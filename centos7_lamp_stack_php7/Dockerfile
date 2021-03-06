FROM centos:centos7
MAINTAINER Prasad Gopale <prasadgopale@gmail.com>
# Install varioius utilities
RUN yum -y install curl wget unzip git vim mlocate \
iproute python-setuptools hostname inotify-tools yum-utils which \
epel-release openssh-server openssh-clients

# Configure SSH
RUN ssh-keygen -b 1024 -t rsa -f /etc/ssh/ssh_host_key \
&& ssh-keygen -b 1024 -t rsa -f /etc/ssh/ssh_host_rsa_key \
&& ssh-keygen -b 1024 -t dsa -f /etc/ssh/ssh_host_dsa_key \
&& sed -ri 's/UsePAM yes/#UsePAM yes/g' /etc/ssh/sshd_config \
&& sed -ri 's/#UsePAM no/UsePAM no/g' /etc/ssh/sshd_config

# Set root password
RUN echo root:docker | chpasswd && yum install -y passwd

# Install Python and Supervisor
RUN yum -y install python-setuptools \
&& mkdir -p /var/log/supervisor \
&& easy_install supervisor

# Install Apache & EXIM
RUN yum -y install httpd mod_ssl exim

#RUN rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
#RUN rpm -Uvh https://mirror.webtatic.com/yum/el7/webtatic-release.rpm

# Install Remi Updated PHP 7
RUN wget http://rpms.remirepo.net/enterprise/remi-release-7.rpm \
&& rpm -Uvh remi-release-7.rpm \
&& yum-config-manager --enable remi-php70 \
&& yum -y install php php-devel php-gd php-pdo php-soap php-xmlrpc php-xml

RUN curl -sS https://getcomposer.org/installer | php -- --version=1.0.3 --install-dir=/usr/local/bin --filename=composer && chown apache /usr/local/bin/composer


# Reconfigure Apache
RUN sed -i 's/AllowOverride None/AllowOverride All/g' /etc/httpd/conf/httpd.conf \
&& chown apache:apache /var/www/html \
&& chmod +x /var/www/html

# Install phpMyAdmin
RUN yum install -y phpMyAdmin \
&& sed -i 's/Require ip 127.0.0.1//g' /etc/httpd/conf.d/phpMyAdmin.conf \
&& sed -i 's/Require ip ::1/Require all granted/g' /etc/httpd/conf.d/phpMyAdmin.conf \
&& sed -i 's/Allow from 127.0.0.1/Allow from all/g' /etc/httpd/conf.d/phpMyAdmin.conf \
&& sed -i "s/'cookie'/'config'/g" /etc/phpMyAdmin/config.inc.php \
&& sed -i "s/\['user'\] .*= '';/\['user'\] = 'root';/g" /etc/phpMyAdmin/config.inc.php \
&& sed -i "/AllowNoPassword.*/ {N; s/AllowNoPassword.*FALSE/AllowNoPassword'] = TRUE/g}" /etc/phpMyAdmin/config.inc.php \
&& sed -i 's/upload_max_filesize = 2M/upload_max_filesize = 512M/g' /etc/php.ini \
&& sed -i 's/post_max_size = 8M/post_max_size = 512M/g' /etc/php.ini \
&& sed -i 's/memory_limit = 128M/memory_limit = 512M/g' /etc/php.ini

# Install MariaDB
COPY MariaDB.repo /etc/yum.repos.d/MariaDB.repo
RUN yum clean all;yum -y install mariadb-server mariadb-client

# Setup Drush
RUN wget http://files.drush.org/drush.phar \
&& chmod +x drush.phar \
&& mv drush.phar /usr/local/bin/drush

#Setup Redis
RUN yum install epel-release -y && \
    yum update -y && \
    yum install redis -y && \
yum clean all


# Setup NodeJS
RUN curl --silent --location https://rpm.nodesource.com/setup_7.x | bash - \
&& yum -y install nodejs gcc-c++ make \
&& npm install -g npm \
&& npm install -g gulp grunt-cli

#Angular Cli
#RUN npm install -g @angular/cli@1.5.5


# UTC Timezone & Networking
RUN ln -sf /usr/share/zoneinfo/UTC /etc/localtime \
	&& echo "NETWORKING=yes" > /etc/sysconfig/network

COPY supervisord.conf /etc/supervisord.conf
EXPOSE 22 25 80 443 3306 6379
CMD ["/usr/bin/supervisord"]

