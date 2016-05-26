## Repository Layer Installation Guide v.0.1

### Tomcat8

`$> apt-get install tomcat8  
$> apt-get install tomcat8-admin`

Configure Java in /etc/default/tomcat8    

Typical Fedora Configuration  
`JAVA_OPTS="-Djava.awt.headless=true -Xmx1024M -Xms512M -XX:+UseConcMarkSweepGC"    
JAVA_OPTS="${JAVA_OPTS} -Dfcrepo.home=/mnt/fcrepo4-data -Dlog.fcrepo=DEBUG"    
JAVA_OPTS="${JAVA_OPTS} -Dfile.encoding=UTF8"`  

Configure tomcat8 in /var/lib/tomcat8/conf


### FCREPO 4.5.1 Plus
Download war from https://github.com/fcrepo4-exts/fcrepo-webapp-plus
create data directory (e.g. /mnt/fcrepo4-data)  
create binary serialization directory (e.g. /media/fcrepo)  
assign dir permissions to tomcat8  

`$> cp fcrepo-*.war to /var/lib/tomcat8/webapps/fcrepo.war`

#### Default Transform Program
note: default transform must be created before solr-indexer will work
`"@prefix fedora : <http://fedora.info/definitions/v4/repository#>
id      = . :: xsd:string ;
title = rdfs:label :: xsd:string;
uuid = fedora:uuid :: xsd:string ;" > post.txt`  

`$> curl -i -X PUT -H "Content-Type: application/rdf+ldpath" --data-binary "@post.txt" http://localhost:8080/fcrepo/rest/fedora:system/fedora:transform/fedora:ldpath/default/fedora:Container`
 

### Jena Fuseki 2
Clone Jena repo from https://github.com/apache/jena
`$> cd jena-fuseki
$> mvn package`
create directory /etc/fuseki
assign dir permissions to tomcat8
`$> cp jena-fuseki-war/target/jena-fuseki-war-*-SNAPSHOT.war /var/lib/tomcat8/webapps/fuseki.war`

### Solr 6
Download http://ftp-stud.hs-esslingen.de/pub/Mirrors/ftp.apache.org/dist/lucene/solr/6.0.0  
extract   
`$> ant ivy-bootstrap   
$> ant compile  
$> cd solr  
$> ant server  
$> cd bin  
$> chmod 755 solr  
$> ./solr start -p 8983  
$> ./solr create_core -c fcrepo`  

### Karaf 4.0.5
Download http://www.apache.org/dyn/closer.lua/karaf/4.0.5/apache-karaf-4.0.5.tar.gz  
extract  
`$> cp apache-karaf-* /srv/karaf  
$> cd /srv/karaf/bin  
$> ./karaf  
$> feature:repo-add hawtio 1.4.65  
$> feature:install hawtio  
$> feature:repo-add mvn:org.fcrepo.camel/toolbox-features/4.5.2/xml/features  
$> feature:install fcrepo-indexing-solr  
$> feature:install fcrepo-indexing-triplestore  
$> feature:install fcrepo-audit-triplestore  
$> feature:install fcrepo-fixity  
$> feature:install fcrepo-serialization` 
Note: (as of 26.05.2016 complete toolbox release 4.5.3 had not been provisioned in Maven)  
`$> feature:repo-add mvn:org.fcrepo.camel/toolbox-features/latest/xml/features
$> feature:install fcrepo-reindexing  
$> feature:install fcrepo-service-activemq ` 

Hawtio URI: http://localhost:8181/hawtio/  
login: karaf pw: karaf  

Configure Karaf in /srv/karaf/etc:  

* org.fcrepo.camel.indexing.triplestore.cfg  
`# The base URL of the triplestore being used.`
Prerequisite: create dataset in Fuseki  
triplestore.baseUrl=localhost:8080/fuseki/blume/update  

* org.fcrepo.camel.indexing.solr.cfg  
`# The baseUrl for the Solr server.`
Prerequisite: create core in Solr  
solr.baseUrl=localhost:8983/solr/fcrepo  

* org.fcrepo.camel.serialization.cfg   
`# The directory to store the metadata files in.`  
serialization.descriptions=/media/fcrepo/descriptions  

`# The directory to store the binary files in, if writing them to disk.`  
serialization.binaries=/media/fcrepo/binaries  

`# The flag for whether or not to write binaries to disk.`  
serialization.includeBinaries=true  

### Trigger Indexing
`curl -XPOST localhost:9080/reindexing -H"Content-Type: application/json" -d '["broker:queue:triplestore.reindex"]'`