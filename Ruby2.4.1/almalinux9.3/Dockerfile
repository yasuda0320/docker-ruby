FROM almalinux:9.3

ENV RUBY_MAJOR 2.4
ENV RUBY_VERSION 2.4.1
ENV RUBY_DOWNLOAD_SHA256 a330e10d5cb5e53b3a0078326c5731888bb55e32c4abfeb27d9e7f8e5d000250
ENV RUBYGEMS_VERSION 2.6.13

ENV OPENSSL_VERSION 1.1.1v
ENV OPENSSL_DOWNLOAD_SHA256 d6697e2871e77238460402e9362d47d18382b15ef9f246aba6c7bd780d38a6b0
ENV OPENSSL_INSTALL_DIR /opt/openssl

RUN mkdir -p /usr/local/etc \
    && { \
        echo 'install: --no-document'; \
        echo 'update: --no-document'; \
    } >> /usr/local/etc/gemrc

RUN set -ex \
    && dnf update -y \
    && dnf install -y \
        autoconf \
        gcc \
        gmp-devel \
        libffi-devel \
        make \
        openssl-devel \
        perl \
        perl-FindBin \
        readline-devel \
        ruby \
        tar \
        zlib-devel

RUN set -ex \
    && curl -O https://www.openssl.org/source/openssl-$OPENSSL_VERSION.tar.gz \
    && echo "$OPENSSL_DOWNLOAD_SHA256 openssl-$OPENSSL_VERSION.tar.gz" | sha256sum -c - \
    && tar -xzvf openssl-$OPENSSL_VERSION.tar.gz \
    && cd openssl-$OPENSSL_VERSION \
    && ./config --prefix=$OPENSSL_INSTALL_DIR shared zlib \
    && make -j "$(nproc)" \
    && make install \
    && cd / \
    && rm -rf /openssl-$OPENSSL_VERSION \
    && ln -s /etc/ssl/cert.pem $OPENSSL_INSTALL_DIR/ssl/cert.pem \
    && echo "$OPENSSL_INSTALL_DIR/lib" > /etc/ld.so.conf.d/openssl-$OPENSSL_VERSION.conf \
    && ldconfig

RUN set -ex \
    && curl -O https://cache.ruby-lang.org/pub/ruby/${RUBY_MAJOR%-rc}/ruby-$RUBY_VERSION.tar.gz \
    && echo "$RUBY_DOWNLOAD_SHA256 ruby-$RUBY_VERSION.tar.gz" | sha256sum -c - \
    && tar -xzvf ruby-$RUBY_VERSION.tar.gz \
    && rm -rf /ruby-$RUBY_VERSION.tar.gz \
    && cd ruby-$RUBY_VERSION \
    && ./configure --disable-install-doc --enable-shared --with-openssl-dir=$OPENSSL_INSTALL_DIR \
    && make -j "$(nproc)" \
    && make install \
    && dnf remove -y ruby \
    && cd / \
    && rm -rf /ruby-$RUBY_VERSION \
    && gem update --system "$RUBYGEMS_VERSION"

CMD ["irb"]
