#
# PHP-CLI 7 Dockerfile
#
# https://github.com/phalcon/dockerfiles
#

# Pull base image
FROM phalconphp/ubuntu:14.04.4

MAINTAINER Serghei Iakovlev <serghei@phalconphp.com>

# Adding repositories
RUN curl -s https://packagecloud.io/install/repositories/phalcon/stable/script.deb.sh | bash && \
    apt-add-repository -y ppa:chris-lea/libsodium && \
    LC_ALL=en_US.UTF-8 add-apt-repository -y ppa:ondrej/php && \
    apt-get update -y && \
    apt-get upgrade -y

# Install PHP
RUN apt-get install -yq --no-install-suggests --no-install-recommends \
    php7.0-apcu \
    php-apcu-bc \
    php7.0-bcmath \
    php7.0-bz2 \
    php7.0-cli \
    php7.0-common \
    php7.0-curl \
    php7.0-dev \
    php7.0-dba \
    php7.0-gd \
    php7.0-gearman \
    php7.0-gettext \
    php7.0-gmp \
    php7.0-imagick \
    php7.0-imap\
    php7.0-intl \
    php7.0-memcache \
    php7.0-memcached \
    php7.0-mongodb \
    php7.0-msgpack \
    php7.0-mbstring \
    php7.0-mcrypt  \
    php7.0-mysql \
    php7.0-mysqli \
    php-pear \
    php7.0-odbc \
    php7.0-pgsql \
    php7.0-phalcon \
    php7.0-pspell \
    php7.0-readline \
    php7.0-redis \
    php7.0-recode \
    php7.0-soap \
    php7.0-sqlite3 \
    php7.0-ssh2 \
    php7.0-tidy \
    php7.0-xmlrpc \
    php7.0-xsl \
    php7.0-xml \
    php7.0-zip \
    libcurl4-openssl-dev \
    libreadline6-dev \
    libgearman-dev \
    libevent-dev \
    librecode-dev \
    libsqlite3-dev \
    libssl-dev \
    libxml2-dev \
    librecode0 \
    libsodium-dev \
    libssh2-1-dev \
    libsqlite3-0 \
    libxml2 \
    libc-dev \
    libmagickwand-dev \
    libmemcached-dev \
    libjpeg-dev \
    libpng12-dev \
    libfreetype6-dev \
    libmcrypt-dev \
    libtidy-dev \
    libxslt1-dev \
    libyaml-dev \
    libfreetype6-dev \
    libxpm-dev \
    libvpx-dev

RUN pecl channel-update pecl.php.net && \
    pecl config-set preferred_state beta

# Add installers
ADD installers/*.sh /installers/

RUN bash /installers/aerospike.sh && \
    bash /installers/handlersocketi.sh && \
    bash /installers/pinba.sh

# Install ext-yaml
RUN printf "\n" | pecl install yaml-2.0.0 && \
    echo 'extension=yaml.so' | tee /etc/php/7.0/mods-available/yaml.ini && \
    ln -s /etc/php/7.0/mods-available/yaml.ini /etc/php/7.0/cli/conf.d/20-yaml.ini

# Install ext-libsodium
RUN printf "\n" | pecl install -a libsodium && \
    echo 'extension=libsodium.so' | tee /etc/php/7.0/mods-available/libsodium.ini && \
    ln -s /etc/php/7.0/mods-available/libsodium.ini /etc/php/7.0/cli/conf.d/20-libsodium.ini

# Install ext-xdebug
RUN pecl install xdebug && \
    echo '[Xdebug]' >  /etc/php/7.0/mods-available/xdebug.ini && \
    echo 'zend_extension = /usr/lib/php/20151012/xdebug.so' >> /etc/php/7.0/mods-available/xdebug.ini && \
    echo "xdebug.remote_enable = 1" >> /etc/php/7.0/mods-available/xdebug.ini && \
    echo "xdebug.remote_host = 0.0.0.0" >> /etc/php/7.0/mods-available/xdebug.ini && \
    echo "xdebug.remote_port = 9001" >> /etc/php/7.0/mods-available/xdebug.ini && \
    echo 'xdebug.remote_handler = "dbgp"' >> /etc/php/7.0/mods-available/xdebug.ini && \
    echo "xdebug.remote_connect_back = 1" >> /etc/php/7.0/mods-available/xdebug.ini && \
    echo "xdebug.cli_color = 1" >> /etc/php/7.0/mods-available/xdebug.ini && \
    echo 'xdebug.idekey = "PHPSTORM"' >> /etc/php/7.0/mods-available/xdebug.ini

# Fix PHP warnings
RUN find /etc/php/7.0/mods-available/ -name "*.ini" -exec sed -i -re 's/^(\s*)#(.*)/\1;\2/g' {} \;

# Tune up PHP-CLI
RUN TIMEZONE=`cat /etc/timezone`; sed -i "s|;date.timezone =.*|date.timezone = ${TIMEZONE}|" /etc/php/7.0/cli/php.ini && \
    sed -i "s|memory_limit =.*|memory_limit = -1|" /etc/php/7.0/cli/php.ini && \
    sed -i 's|short_open_tag =.*|short_open_tag = On|' /etc/php/7.0/cli/php.ini && \
    sed -i 's|error_reporting =.*|error_reporting = -1|' /etc/php/7.0/cli/php.ini && \
    sed -i 's|display_errors =.*|display_errors = On|' /etc/php/7.0/cli/php.ini && \
    sed -i 's|display_startup_errors =.*|display_startup_errors = On|' /etc/php/7.0/cli/php.ini && \
    sed -i -re 's|^(;?)(session.save_path) =.*|\2 = "/tmp"|g' /etc/php/7.0/cli/php.ini && \
    sed -i -re 's|^(;?)(phar.readonly) =.*|\2 = off|g' /etc/php/7.0/cli/php.ini && \
    echo "apc.enable_cli = 1" | tee -a /etc/php/7.0/mods-available/apcu.ini && \
    echo 'extension=memcache.so' | tee /etc/php/7.0/mods-available/memcache.ini

RUN phpenmod gearman redis memcache xdebug

# Cleanup package manager
RUN apt-get autoremove -y && \
    apt-get autoclean -y && \
    apt-get clean -y && \
    rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* /etc/php5 /etc/php/5* /usr/lib/php/20121212 /usr/lib/php/20131226

# Set workdir
WORKDIR /app

# Define mountable directories
VOLUME ["/app"]

# Set up the command arguments
ENTRYPOINT ["php"]
CMD ["-v"]
