FROM --platform=linux/x86_64 php:8.2-fpm
# FROM --platform=linux/x86_64 php:7.4-fpm

# 引数を受け取ってコンテナ内で環境変数を定義
ARG NODEJS_VERSION
ARG LARAVEL_VERSION
ARG PROJECT_NAME
ARG PASSWORD
ARG USER

ENV LARAVEL_VERSION=${LARAVEL_VERSION}
ENV NODEJS_VERSION=${NODEJS_VERSION}
ENV PROJECT_NAME=${PROJECT_NAME}
ENV USER=${USER}
ENV PASSWORD=${PASSWORD}

# ロケール設定
ENV LANG ja_JP.UTF-8
ENV LANGUAGE ja_JP:jp
ENV LC_ALL ja_JP.UTF-8

# タイムゾーンを設定
ENV TZ=Asia/Tokyo

# ローカルからDocker環境にファイルをコピー
COPY php.ini /usr/local/etc/php/php.ini

# パッケージのインストール前に追加
SHELL ["/bin/bash", "-c"]

# パッケージのインストール
RUN apt-get clean && apt-get update \
    && apt-get install -y \
    libwebp-dev \
    locales \
    procps \
    git \
    zip \
    unzip \
    vim \
    sudo \
    libfreetype6-dev \
    libzip-dev \
    libjpeg62-turbo-dev \
    libmcrypt-dev \
    libpng-dev \
    libfontconfig1 \
    libxrender1 \
    libicu-dev \
    && echo "ja_JP.UTF-8 UTF-8" > /etc/locale.gen \
    && locale-gen ja_JP.UTF-8 \
    && rm -rf /var/lib/apt/lists/*

RUN docker-php-ext-configure gd --with-freetype --with-jpeg --with-webp
RUN docker-php-ext-install gd
RUN docker-php-ext-install bcmath
RUN docker-php-ext-install pdo_mysql mysqli exif intl zip
RUN docker-php-ext-configure pcntl --enable-pcntl \
  && docker-php-ext-install pcntl
RUN PHP_OPENSSL=yes

# composerのインストール
RUN cd /usr/bin && curl -s http://getcomposer.org/installer | php && ln -s /usr/bin/composer.phar /usr/bin/composer

# nodejs インストール
RUN curl -sL https://deb.nodesource.com/setup_${NODEJS_VERSION} | bash -
RUN apt-get install -y nodejs

# npm 経由でyarnをインストール
RUN npm install -g yarn

# 一般権限のユーザーを追加
RUN useradd -m -s /bin/bash ${USER} --uid 1000
RUN gpasswd -a ${USER} sudo
RUN echo "${USER}:${PASSWORD}" | chpasswd

# sudo実行時のパスワード入力を省略
RUN echo "${USER} ALL=(ALL) NOPASSWD:ALL" > /etc/sudoers.d/${USER}

# .bashrcをコピー
COPY .bashrc /home/${USER}/.bashrc
# ユーザーの切替
USER ${USER}

# 作業ディレクトリに移動
WORKDIR /var/www/html

# bashに切り替え
RUN /bin/bash
