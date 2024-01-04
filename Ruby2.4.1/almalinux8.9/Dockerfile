FROM almalinux:8.9

RUN mkdir -p /usr/local/etc \
    && { \
        echo 'install: --no-document'; \
        echo 'update: --no-document'; \
    } >> /usr/local/etc/gemrc

ENV RUBY_MAJOR 2.4
ENV RUBY_VERSION 2.4.1
ENV RUBY_DOWNLOAD_SHA256 a330e10d5cb5e53b3a0078326c5731888bb55e32c4abfeb27d9e7f8e5d000250
ENV RUBYGEMS_VERSION 2.6.13

RUN set -ex \
    && dnf update -y \
    && dnf install -y \
        autoconf \
        gcc \
        gmp-devel \
        libffi-devel \
        make \
        openssl-devel \
        readline-devel \
        ruby \
        tar \
    && dnf clean all

RUN set -ex \
    && curl -O https://cache.ruby-lang.org/pub/ruby/${RUBY_MAJOR%-rc}/ruby-$RUBY_VERSION.tar.gz \
    && echo "$RUBY_DOWNLOAD_SHA256 ruby-$RUBY_VERSION.tar.gz" | sha256sum -c - \
    && tar -xzvf ruby-$RUBY_VERSION.tar.gz \
    && rm -rf /ruby-$RUBY_VERSION.tar.gz \
    && cd ruby-$RUBY_VERSION \
    && ./configure --disable-install-doc --enable-shared \
    && make -j "$(nproc)" \
    && make install \
    && dnf remove -y ruby \
    && dnf clean all \
    && cd / \
    && rm -rf /ruby-$RUBY_VERSION \
    && gem update --system "$RUBYGEMS_VERSION"

CMD ["irb"]
