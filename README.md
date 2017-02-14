# Pandora Framework Architecture <img src="https://avatars1.githubusercontent.com/u/25132340?v=3&s=200" height="75" align="right" alt="" />

This repository contains documentation relating to the components of the Linked Open Data Image Presentation and Annotation Framework.

See v.0.2.0 [diagram](https://github.com/blumenbach/architecture/blob/master/docs/pandora_0.2.0.png)

Currently, these components are documented in the following repositories or locations:

## Repository Layer
* [Fedora 4](https://github.com/fcrepo4/fcrepo4)   
* [Apache Karaf](https://github.com/apache/karaf)
* [Apache Camel](http://camel.apache.org/karaf.html)
* [Fedora Messaging](https://github.com/fcrepo4-exts/fcrepo-camel-toolbox)
* [Apache Jena](https://github.com/apache/jena) 

## Service Layer
* [iipsrv](https://github.com/ruven/iipsrv) 
* [openJPEG](https://github.com/uclouvain/openjpeg)
* [Solr](http://ftp.halifax.rwth-aachen.de/apache/lucene/solr/6.0.0/)
* [Virtuoso](https://github.com/openlink/virtuoso-opensource)
* [eXist-db](https://github.com/eXist-db)
* [wikibase](https://github.com/wikimedia/mediawiki-extensions-Wikibase)

## Middleware
* [Pandora Manifest Service](https://github.com/pan-dora/manifest-service)
* [Pandora Modeller](https://github.com/pan-dora/modeller)
* [Redis](http://redis.io/topics/quickstart)
* [Riak KV](https://github.com/basho/riak_kv)

## Web App Layer
* [IIIF Viewer](https://github.com/blumenbach/iiif-viewer)
* [Metadata Subscriber](https://github.com/blumenbach/metadata-subscriber)
* [Blumenbach TEI Datenbank](https://github.com/blumenbach/Blumenbach-TEI)
* [XForms for TEI](https://github.com/blumenbach/orbeon-bb)
* [Mediafiles Viewer](https://github.com/blumenbach/mediafiles)
* [Sammlung Karte](https://github.com/blumenbach/sammlung-karte)
* [Composite UI](https://github.com/blumenbach/composite-ui)

## Data Model
* The Data Model for all [objects](https://github.com/blumenbach/collection-builder) is based on the [IIIF Presentation API](http://iiif.io/api/presentation/2.1/).
* See [Diagram](https://github.com/blumenbach/architecture/blob/master/data-model/data-model_v.0.2.png) for the class relationships
* See [HOWTO](https://github.com/blumenbach/architecture/blob/master/data-model/HOWTO.md) for implementation specifics.
* See [tei-to-rdf](https://github.com/blumenbach/tei-to-rdf) on prototype method for #LD transformation of TEI. 

## Event Model 
The Event Model of the Web App layer provides realtime dynamic topology via:  
* [socket.io](https://github.com/socketio/socket.io/)  
* [redis pubsub messaging](http://redis.io/topics/pubsub).  

## Session Model
Assuming a load balanced reverse proxy to the worker instances,
"[sticky sessions](https://www.npmjs.com/package/sticky-session)" will be used.  
[Redis](http://redis.io/) (or [Riak KV](https://github.com/basho/riak_kv)) will provide persistent session storage. 


## Key Web App Node.js Libraries
* [socket.io](http://socket.io/)
* [socket.io-redis](https://www.npmjs.com/package/socket.io-redis)
* [redis](http://redis.io/)
* [socket.io-express-session](https://www.npmjs.com/package/express-socket.io-session)
* [openseadragon](http://openseadragon.github.io/)
* [node-rest-client](https://www.npmjs.com/package/node-rest-client)





