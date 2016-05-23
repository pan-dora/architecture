# How to create a IIIF Presentation 2.0 API Data Model in Fedora

## Prerequisites:
- fcrepo serialization should be installed in karaf.  
- enable saving binaries to a directory accessible to djatoka (/media/fcrepo).

### 1. Create Containers in Fedora for the edition objects: 
 if it does not exist, create top container ("edition") first
 - root
- canvas
- sequence
- annotation
- list
- range
- layer
- res  
URI Pattern: <http://localhost:8080/fcrepo/rest/edition/{id}/{container}>  
`./containers/create_base_objects.sh {id}`
 
### 2. stage TIFF files in a CLI accessible directory. (/media/staged/{id})
- Create upload script with this process.
- a. ls > tiffs.sh
- b. open tiffs.sh in editor
- c. change file names as required: to remove leading zeros use this regex:
-- Find: (.+?)(\d{3}.tif)
-- Replace: mv $1$2 $2
-- save as mv.sh, chmod 755 mv.sh, ./mv.sh
-- open mv.sh in editor
- d: add curl command with this regex:
-- Find: mv 00000(\d{3}.tif) .+?(tif)
-- Replace: curl -X PUT --upload-file $1 -H"Content-Type: image/jpeg" "http://localhost:8080/fcrepo/rest/edition/{id}/res/$1"
- save as upload.sh, chmod 755 upload.sh

### 3. Upload binaries to Fedora res container
`./upload.sh` 

### 4. Create binary metadata (RDF)
- check image dimensions (i.e. 2112 x 3314)
- make a subdirectory tree under metadata for the ttl files (./metadata/base/res)
- use the script res_metadata.sh by piping in the ids of all of the files.
- This will create 1 .ttl file for each binary.
- Create the batch script:
  - ls *.tif > list.txt
  - open list.txt   
  -- Find: (.+?)(.tif)\n  
  -- Replace:  
   `../../res_metadata.sh $1\n`
  - save as build_res_ttl.sh
  
### 5. Patch binary metadata
- Create the batch script:
  - ls *.ttl > ttl_bin.txt
  - open ttl_bin.txt  
  -- Find: (.+?)(.ttl)\n  
  -- Replace: 
  `curl -X PATCH -H "Content-Type: application/sparql-update" --data-binary "@$1.ttl" "http://localhost:8080/fcrepo/rest/edition/{id}/res/$1.tif/fcr:metadata" \n` 
  - save as update_res_metadata.sh

Note: If a property exists, you must first delete it before you use SPARQL update!

Example:  
```sparql
DELETE { 
<> <http://www.w3.org/2003/12/exif/ns#height> \"2000\"^^<http://www.w3.org/2001/XMLSchema#integer> .
<> <http://www.w3.org/2003/12/exif/ns#width> \"1500\"^^<http://www.w3.org/2001/XMLSchema#integer> .
} WHERE {}" > "$id.ttl"
```

### 6. Create xml file descriptors
- file descriptor has one element  
```xml 
<id>{edition_id}_001</id>
```  
 that is used by the IIIF server for the URI
- use build_descriptors.sh then move the descriptors into the binary directory (/media/fcrepo/edition/{edition_id}/res)

### 7. Ingest images into Djatoka
`curl -s 'http://localhost:8888/ingest?unattended=true'`

### 8. Create list objects
- Each list object will eventually contain the metadata about an image resource (perhaps a pointer to the TEI).
- The lists must preceed the creation of the canvas.  
Example:
`curl -i -X PUT "http://localhost:8080/fcrepo/rest/edition/{id}/list/l001"`  
URI pattern:  
<http://localhost:8080/fcrepo/rest/edition/{id}/list/l{list_id}>
- Use create_list_objects.sh script
`./containers/create_lists.sh {$start} {$end}`

### 9. Create canvas objects
- This is the same as the list creation.  
URI pattern:   
<http://localhost:8080/fcrepo/rest/edition/{edition_id}/canvas/c{canvas_id}>  
`./build/containers/create_canvases.sh {$start} {$end}`

### 10. create canvas metadata (RDF)
- same as step 4 but use 
`../../canvas_metadata.sh {$id}` 
- save as build_canvas_ttl.sh

### 11. Patch canvas metadata
 `update_canvas_metadata.sh {$start} {$end}`  
Example:  
`curl -X PATCH -H "Content-Type: application/sparql-update" --data-binary "@002.ttl" "http://localhost:8080/fcrepo/rest/edition/{id}/canvas/c002"`

### 12. Create sequence metadata (RDF)
- Use the script build/sequence_metadata.php  
`php ./metadata/sequence_metadata.php {$start} {$end}`
- Note: Prefixes and SPARQL INSERT are appended before the sequence chain   
Example:    
```sparql
INSERT { 
 <> <http://iiif.io/api/presentation/2#hasCanvases> _:c000 .
```

### 13. Create and Patch sequence object
- default sequence has path <http://localhost:8080/fcrepo/rest/edition/{id}/sequence/normal>  
Example:    
`curl -X PATCH -H "Content-Type: application/sparql-update" --data-binary "@sequence.ttl" "http://localhost:8080/fcrepo/rest/edition/{id}/sequence/normal"`

### 14. Create Ranges
- a manual process that involves partitioning the canvases based on the textual divisions (chapters, etc.)
- sequence.ttl can be reused for this purpose because the node chain is the same.

### 15. Create collection and subcollection objects
- `curl -i -X PUT "http://localhost:8080/fcrepo/rest/collection"`
- `curl -i -X PUT "http://localhost:8080/fcrepo/rest/collection/top"`
- `curl -i -X PUT "http://localhost:8080/fcrepo/rest/collection/blumenbach-editions"`
- `curl -i -X PUT "http://localhost:8080/fcrepo/rest/collection/blumenbach-objects"`

### 16. Create Manifest Object
`curl -i -X PUT "http://localhost:8080/fcrepo/rest/edition/{id}/manifest"`

### 17. Edit collection metadata (RDF) template
- template file is `./metadata/collection.ttl`

### 18. Patch collection metadata
- `curl -X PATCH -H "Content-Type: application/sparql-update" --data-binary "@collection.ttl" "http://localhost:8080/fcrepo/rest/collection/top"`

### 19. Edit manifest metadata (RDF) template
- template file is `./metadata/manifest.ttl`

### 20. Patch manifest metadata (RDF)
`curl -X PATCH -H "Content-Type: application/sparql-update; charset=utf-8" --data-binary "@manifest.ttl" "http://localhost:8080/fcrepo/rest/edition/{id}/manifest"`


