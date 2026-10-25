FROM php:7.4-fpm

RUN echo 'deb http://snapshot.debian.org/archive/debian/20260901T000000Z/ bullseye main' > /etc/apt/sources.list && \
    echo 'deb http://snapshot.debian.org/archive/debian-security/20260901T000000Z/ bullseye-security main' >> /etc/apt/sources.list

RUN apt-get -o Acquire::Check-Valid-Until=false update && apt-get install -y \
    curl \
	build-essential \
	autoconf \
	pkg-config \
	libargon2-dev \
	libcurl4-openssl-dev \
	libonig-dev \
	libreadline-dev \
	libsodium-dev \
	libsqlite3-dev \
	libssl-dev \
	zlib1g-dev \
    libicu-dev \
    libzip-dev \
    libjpeg-dev \
    libfreetype6-dev \
    libpng-dev \
    libxml2-dev \
    default-mysql-client \
    nodejs \
    npm \
    && apt-get clean -y && rm -rf /var/lib/apt/lists/*

RUN curl -fsSL "https://github.com/php/php-src/commit/712e60f9edfd03f0960fc4fc3c2c375f42b90aa2.diff" -o /tmp/CVE-109618-109619.patch

RUN docker-php-source extract && cd /usr/src/php && patch -p1 < /tmp/CVE-109618-109619.patch && \
./configure $(php-config --configure-options) && make -j$(nproc) && make install

RUN docker-php-ext-configure gd \
    --with-freetype \
    --with-jpeg

RUN docker-php-ext-install \
    pdo_mysql \
    intl \
    zip \
    curl \
    opcache \
    gd \
    mbstring \
    xml

COPY --from=composer:1 /usr/bin/composer /usr/bin/composer
COPY docker/entrypoint.sh /entrypoint.sh
WORKDIR /var/www/html/
RUN chmod +x /entrypoint.sh
ENTRYPOINT ["/entrypoint.sh"]
CMD ["php-fpm"]