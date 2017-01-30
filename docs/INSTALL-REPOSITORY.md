## Repository Layer Installation Guide v.0.1


### Prerequisites
on Ubuntu 16.10

    $> apt-get install git-core
    $> apt-get install default-jdk
    
set JAVA_HOME in /etc/environment

    $> apt install maven
    $> apt install apache2
    $> a2enmod proxy_http

setup reverse proxy to localhost:8080

### Tomcat8

    $> apt-get install tomcat8  
    $> apt-get install tomcat8-admin

Configure Java in /etc/default/tomcat8    

Typical Fedora Configuration

    JAVA_OPTS="-Djava.awt.headless=true -Xmx1024M -Xms512M -XX:+UseConcMarkSweepGC"
    JAVA_OPTS="${JAVA_OPTS} -Dfcrepo.home=/mnt/fcrepo4-data -Dfcrepo.modeshape.configuration=file:/etc/fcrepo/repository.json"    
    JAVA_OPTS="${JAVA_OPTS} -Dfile.encoding=UTF8 -Dlog.fcrepo=DEBUG"  

Configure tomcat8 in /var/lib/tomcat8/conf


### FCREPO RC2 4.7.0
Download war from https://github.com/fcrepo4/fcrepo4/releases/download/fcrepo-4.7.0-RC-2/fcrepo-webapp-4.7.0-RC-2.war
create data directory (e.g. /mnt/fcrepo4-data)  
create binary serialization directory (e.g. /mnt/serialization/binaries)  
assign dir permissions to tomcat8  

    $> cp fcrepo-*.war to /var/lib/tomcat8/webapps/fcrepo.war

### Jena Fuseki 2
Clone Jena repo from https://github.com/apache/jena

    $> cd jena-fuseki2
    $> mvn package
create directory /etc/fuseki
assign dir permissions to tomcat8

    $> cp jena-fuseki-war/target/jena-fuseki-war-*-SNAPSHOT.war /var/lib/tomcat8/webapps/fuseki.war

### Solr 6
Download http://ftp-stud.hs-esslingen.de/pub/Mirrors/ftp.apache.org/dist/lucene/solr/6.0.0  
extract

    $> ant ivy-bootstrap 
    $> ant compile
    $> cd solr
    $> ant server
    $> cd bin
    $> chmod 755 solr
    $> ./solr start -p 8983
    $> ./solr create_core -c fcrepo  

### Karaf 4.0.7
Download http://www.apache.org/dyn/closer.lua/karaf/4.0.7/apache-karaf-4.0.7.tar.gz  
extract

    $> cp apache-karaf-* /srv/karaf
    $> cd /srv/karaf/bin
    $> ./karaf
    $> feature:repo-add hawtio 1.4.65    
    $> feature:install hawtio
    $> feature:repo-add mvn:org.fcrepo.camel/toolbox-features/LATEST/xml/features
    $> feature:install fcrepo-indexing-solr
    $> feature:install fcrepo-ldpath
    $> feature:install fcrepo-indexing-triplestore
    $> feature:install fcrepo-fixity
    $> feature:install fcrepo-serialization
    $> feature:install fcrepo-reindexing   


Hawtio URI: http://localhost:8181/hawtio/  
login: karaf pw: karaf  

Configure Karaf in /srv/karaf/etc:  

* org.fcrepo.camel.indexing.triplestore.cfg  
    \# The base URL of the triplestore being used.
Prerequisite: create dataset in Fuseki
triplestore.baseUrl=reverse-proxy-name.org/fuseki/fcrepo/update  

* org.fcrepo.camel.indexing.solr.cfg  
    \# The baseUrl for the Solr server.
Prerequisite: create core in Solr
solr.baseUrl=localhost:8983/solr/fcrepo  

* org.fcrepo.camel.serialization.cfg  
    \# The directory to store the metadata files in.
    serialization.descriptions=/mnt/fcrepo/descriptions  
    
    \# The directory to store the binary files in, if writing them to disk.
    serialization.binaries=/mnt/fcrepo/binaries  

    \# The flag for whether or not to write binaries to disk.
    serialization.includeBinaries=true  

### Trigger Indexing
    $> curl -XPOST localhost:9080/reindexing -H"Content-Type: application/json" -d '["broker:queue:triplestore.reindex"]'