FROM alpine:3.7.3

RUN mkdir -p /usr/local/etc && \
    { echo 'install: --no-document'; echo 'update: --no-document'; } >> /usr/local/etc/gemrc

ENV RUBY_MAJOR=2.4
ENV RUBY_VERSION=2.4.1
ENV RUBY_DOWNLOAD_SHA256=4fc8a9992de3e90191de369270ea4b6c1b171b7941743614cc50822ddc1fe654
ENV RUBYGEMS_VERSION=2.6.13

RUN set -ex && apk add --no-cache --virtual .ruby-builddeps autoconf bison bzip2 bzip2-dev ca-certificates coreutils dpkg-dev dpkg gcc gdbm-dev glib-dev libc-dev libffi-dev libressl libressl-dev libxml2-dev libxslt-dev linux-headers make ncurses-dev procps readline-dev ruby tar xz yaml-dev zlib-dev && wget -O ruby.tar.xz "https://cache.ruby-lang.org/pub/ruby/${RUBY_MAJOR%-rc}/ruby-$RUBY_VERSION.tar.xz" && echo "$RUBY_DOWNLOAD_SHA256 *ruby.tar.xz" | sha256sum -c - && mkdir -p /usr/src/ruby && tar -xJf ruby.tar.xz -C /usr/src/ruby --strip-components=1 && rm ruby.tar.xz && cd /usr/src/ruby && { echo '#define ENABLE_PATH_CHECK 0'; echo; cat file.c; } > file.c.new && mv file.c.new file.c && autoconf && gnuArch="$(dpkg-architecture --query DEB_BUILD_GNU_TYPE)" && export ac_cv_func_isnan=yes ac_cv_func_isinf=yes && ./configure --build="$gnuArch" --disable-install-doc --enable-shared && make -j "$(nproc)" && make install && runDeps="$( scanelf --needed --nobanner --recursive /usr/local | awk '{ gsub(/,/, "\nso:", $2); print "so:" $2 }' | sort -u | xargs -r apk info --installed | sort -u )" && apk add --virtual .ruby-rundeps $runDeps bzip2 ca-certificates libffi-dev libressl-dev procps yaml-dev zlib-dev && apk del .ruby-builddeps && cd / && rm -r /usr/src/ruby && gem update --system "$RUBYGEMS_VERSION"

ENV BUNDLER_VERSION=1.15.4
RUN gem install bundler --version "$BUNDLER_VERSION"

ENV GEM_HOME=/usr/local/bundle
ENV BUNDLE_PATH=/usr/local/bundle BUNDLE_BIN=/usr/local/bundle/bin BUNDLE_SILENCE_ROOT_WARNING=1 BUNDLE_APP_CONFIG=/usr/local/bundle
ENV PATH=/usr/local/bundle/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
RUN mkdir -p "$GEM_HOME" "$BUNDLE_BIN" && chmod 777 "$GEM_HOME" "$BUNDLE_BIN"

CMD ["irb"]
