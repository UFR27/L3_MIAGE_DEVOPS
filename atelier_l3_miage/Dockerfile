FROM centos:7.3.1611

# Install EPEL repo
RUN yum -y install epel-release

# Install Nginx
RUN yum -y install nginx

# Install Tools & Dev Compiler
RUN yum -y install wget unzip git libssh2

# Repo needed for PHP 7.2
RUN rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm && \
    wget http://rpms.famillecollet.com/enterprise/remi-release-7.rpm && \
    rpm -Uvh remi-release-7*.rpm && \
    yum-config-manager --enable remi-php72

# Install PHP 7.2 and extensions
# RUN yum -y install php-fpm php-devel php-cli php-opcache php-gd \
# 	php-mbstring php-mcrypt php-mysql php-oci8 php-pdo php-pear php-xml php-xmlrpc \
# 	php-intl php-soap php-pgsql php-pecl-zip php-pecl-xdebug php-pecl-ssh2

# Install PHP 7.2 and extensions
RUN yum -y install php-fpm php-cli php-mysql php-xml

#copy conf and basic web pages
COPY ./conf/nginx.conf /etc/nginx/
COPY ./conf/www.conf /etc/php-fpm.d/
COPY ./pages/index.html /var/www/html/
COPY ./pages/index.php /var/www/html/

RUN  ln -s /usr/share/nginx/html /var/www/html/ && chown -R nginx:nginx /var/www/html/

# Install Composer
RUN curl -sS https://getcomposer.org/installer | php -- \
        --install-dir=/usr/local/bin \
        --filename=composer

# forward request and error logs to docker log collector
RUN ln -sf /dev/stdout /var/log/nginx/access.log \
	&& ln -sf /dev/stderr /var/log/nginx/error.log

# Startup Script to run PHP-FPM and Nginx
COPY ./script/start.sh /tmp/
RUN chmod +x /tmp/start.sh && mkdir /run/php-fpm/


VOLUME /etc/nginx/
VOLUME /var/www/

RUN chmod 777 var/lib/php/session

EXPOSE 80

STOPSIGNAL SIGTERM

CMD ["/tmp/start.sh"]
