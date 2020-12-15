FROM cloudsuite/hhvm:3.30
LABEL maintainer="Mark Sutherland <mark.sutherland@epfl.ch>"
ENV DEBIAN_FRONTEND noninteractive
USER root

RUN apt-get update && \
	apt-get install -y \
	build-essential \
	git \
	nginx \ 
	wget

RUN wget https://packages.sury.org/php/apt.gpg \
    && apt-key add apt.gpg \
    && echo "deb https://packages.sury.org/php/ $(lsb_release -sc) main" | tee /etc/apt/sources.list.d/php7.list \
    && apt update \
    && apt-get -y install php7.3 php7.3-cgi php7.3-fpm \
    && apt-get -y install \
	php7.3-mysql \
	php7.3-curl \
	php-memcache  

# Increase the open file limit
COPY files/limits.conf.append /tmp/
RUN cat /tmp/limits.conf.append >> /etc/security/limits.conf && rm -f /tmp/limits.conf.append

# Checkout the Elgg installation
COPY files/elgg_installation /usr/share/nginx/html/elgg

WORKDIR /usr/share/nginx/html
# Copy over the settings.php
COPY files/settings.php elgg/engine/settings.php

# Make the Elgg data directory
RUN mkdir /elgg_data
RUN chmod a+rw /elgg_data

# Copy over the Nginx Server configuration
COPY files/nginx_sites_avail.append /tmp/

# Copy over the Nginx HHVM Server configuration
COPY files/nginx_sites_avail_hhvm.append /tmp/
COPY files/configure_hhvm.sh /tmp/

RUN service nginx restart

RUN service php7.3-fpm restart

ADD bootstrap.sh /etc/bootstrap.sh
RUN chown root:root /etc/bootstrap.sh
RUN chmod 700 /etc/bootstrap.sh

EXPOSE 8080

CMD ["/etc/bootstrap.sh", "-d"]
