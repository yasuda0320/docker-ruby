FROM almalinux:8.9

RUN mkdir -p /usr/local/etc \
    && { \
        echo 'install: --no-document'; \
        echo 'update: --no-document'; \
    } >> /usr/local/etc/gemrc

ENV RUBY_MAJOR 2.4
ENV RUBY_VERSION 2.4.1
ENV RUBY_DOWNLOAD_SHA256 4fc8a9992de3e90191de369270ea4b6c1b171b7941743614cc50822ddc1fe654
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
        tar

RUN set -ex \
    && curl -O https://cache.ruby-lang.org/pub/ruby/2.4/ruby-2.4.1.tar.gz \
    && tar -xzvf ruby-2.4.1.tar.gz \
    && cd ruby-2.4.1 \
    && ./configure --disable-install-doc --enable-shared \
    && make -j "$(nproc)" \
    && make install

RUN set -ex \
    && rm -rf /ruby-2.4.1.tar.gz /ruby-2.4.1 \
    && dnf remove -y ruby

CMD ["irb"]
