# Projekt Blumenbach Online Architecture <img src="https://avatars0.githubusercontent.com/u/15832405?v=3&s=200" width="75" height="75" align="right" alt="" />

This repository contains documentation relating to the components of the Projekt Blumenbach Online 
Digital Repository.

See v.0.3 [diagram](https://github.com/blumenbach/architecture/blob/master/docs/architecture.png)

Currently, these components are documented in the following repositories or locations:

## Repository Layer
* [Fedora 4](https://github.com/fcrepo4/fcrepo4)   
* [Apache Karaf](https://github.com/apache/karaf)
* [Apache Camel](http://camel.apache.org/karaf.html)
* [Fedora Messaging](https://github.com/fcrepo4-exts/fcrepo-camel-toolbox)
* [Apache Jena](https://github.com/apache/jena)
* [eXist-db](https://github.com/eXist-db) 

## Service Layer
* [Djatoka](https://github.com/blumenbach/freelib-djatoka) 
* [Solr](http://ftp.halifax.rwth-aachen.de/apache/lucene/solr/6.0.0/)
* [Virtuoso](https://github.com/openlink/virtuoso-opensource)

## Middleware
* [Redis](http://redis.io/topics/quickstart)
* [Riak KV](https://github.com/basho/riak_kv)
* [Linked Data Fragments Server](https://github.com/LinkedDataFragments/Server.js/)

## Web App Layer
* [IIIF Viewer](https://github.com/blumenbach/iiif-viewer)
* [Metadata Subscriber](https://github.com/blumenbach/metadata-subscriber)
* [Blumenbach TEI Datenbank](https://github.com/blumenbach/Blumenbach-TEI)
* [XForms for TEI](https://github.com/blumenbach/orbeon-bb)
* [Mediafiles Viewer](https://github.com/blumenbach/mediafiles)
* [Sammlung Karte](https://github.com/blumenbach/sammlung-karte)

## Data Model
* The Data Model for all [objects](https://github.com/blumenbach/collection-builder) is based on the [IIIF Presentation API](http://iiif.io/api/presentation/2.1/).
* See [HOWTO](https://github.com/blumenbach/architecture/blob/master/data-model/HOWTO.md) for specifics.

## Event Model 
The Event Model of the Web App layer provides dynamic topology via [redis pubsub messaging](http://redis.io/topics/pubsub).

## Session Model
Assuming a load balanced reverse proxy to the worker instances,
"[sticky sessions](https://www.npmjs.com/package/sticky-session)" are needed to maintain client state with socket.io.  
Redis (or Riak) will provide persistent session storage. 


## Key Web App Node.js Libraries
* [socket.io](http://socket.io/)
* [socket.io-redis](https://www.npmjs.com/package/socket.io-redis)
* [redis](http://redis.io/)
* [socket.io-express-session](https://www.npmjs.com/package/express-socket.io-session)
* [openseadragon](http://openseadragon.github.io/)
* [node-rest-client](https://www.npmjs.com/package/node-rest-client)





