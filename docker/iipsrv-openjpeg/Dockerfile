## iipsrv Installation Guide v.0.1

RUN apt-get update -y && apt-get install -y build-essential wget cmake make git apache2 libapache2-mod-fcgid openssl libssl-dev autoconf libfcgi0ldbl libtool libjpeg-turbo8 libjpeg-turbo8-dev libtiff5-dev libpng12-0 libpng12-dev libmemcached-dev memcached liblcms2-2 liblcms2-dev libgomp1 libpthread-stubs0-dev liblzma5 liblzma-dev libjbig-dev libjbig0 libz80ex1 libz80ex-dev pkg-config zlib1g-dev libopenjp2-7

### Download and compile openjpeg2.1.2 (master)
WORKDIR /mnt/openjpeg
RUN git clone https://github.com/uclouvain/openjpeg.git ./
RUN cmake . && make && make install

### download and compile Ruven's iipsrv
WORKDIR /tmp/iip
RUN https://github.com/ruven/iipsrv ./
RUN chmod +x autogen.sh && sleep 2 && ./autogen.sh
RUN chmod +x configure && sleep 2 && ./configure --with-openjpeg=/mnt/openjpeg && sleep 2 && make && make install

### make www dir and copy iip binary into fcgi bin
RUN mkdir -p /srv/iipsrv/fcgi-bin
RUN cp src/iipsrv.fcgi /srv/iipsrv/fcgi-bin

### copy over apache2.conf for apache
COPY /conf/iipsrv.conf /etc/apache2/sites-available/iipsrv.conf

### enable fcgid mod
RUN a2enmod fcgid
RUN a2enmod headers
RUN a2enmod rewrite

### enable site conf
RUN sudo a2ensite iipsrv.conf