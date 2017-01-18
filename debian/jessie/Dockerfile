FROM debian:jessie

MAINTAINER joserprieto "talktome@joserprieto.es"

# Arguments for the build of the image; right of "=" are the default values.

ARG TIMEZONE="Europe/Madrid"
ARG LOCALE_LANG_COUNTRY="es_ES"
ARG LOCALE_CODIFICATION="UTF-8"
ARG LOCALE_CODIFICATION_ENV="utf8"

# Set enviroment variable for allow terminal output:

ENV TERM="xterm"

## Install needed packages and add support for the locale and timezone:

# We will localize to es-ES (default; other langs/timezone, use --build-arg on docker build) our Debian image:
#   Notes:
#       Installing the 'apt-utils' package gets rid of the
#           'debconf: delaying package configuration, since apt-utils is not installed'
#        error message when installing any other package with the apt-get package manager.
#       The
#           set -x
#        avoid the errors:
#           debconf: unable to initialize frontend: Dialog
#           ...
#           debconf: unable to initialize frontend: Readline
#           ...
#       But, the noninteractive frontend has no requirements but it does not ask any questions
#        see: http://www.gnu.org/software/bash/manual/bashref.html#The-Set-Builtin
#       (which may or may not be what you want).
#       Suggested packages: packages that will be very useful; feel free to ommit the

RUN set -x \
	&& apt-get update \
	&& apt-get upgrade -y \
	&& echo "=> Needed packages:" \
    && apt-get install -y --no-install-recommends apt-utils curl ca-certificates tar openssl xz-utils \
    && echo "=> Suggested packages:" \
    && apt-get install -y --no-install-recommends tree nano \
    && echo "=> Configuring and installing timezone (${TIMEZONE}):" \
    && echo ${TIMEZONE} > /etc/timezone \
    && dpkg-reconfigure -f noninteractive tzdata \
    && echo "=> Configuring and installing locale (${LOCALE_LANG_COUNTRY}.${LOCALE_CODIFICATION}):" \
    && apt-get install -y locales \
    && rm -rf /var/lib/apt/lists/* \
    && localedef -i ${LOCALE_LANG_COUNTRY} -c -f ${LOCALE_CODIFICATION} -A /usr/share/locale/locale.alias ${LOCALE_LANG_COUNTRY}.${LOCALE_CODIFICATION}

# Set enviroment variables:

ENV LANG=${LOCALE_LANG_COUNTRY}.${LOCALE_CODIFICATION_ENV}

RUN groupadd --gid 1000 node \
  && useradd --uid 1000 --gid node --shell /bin/bash --create-home node

# gpg keys listed at https://github.com/nodejs/node
RUN set -ex \
    && for key in \
        9554F04D7259F04124DE6B476D5A82AC7E37093B \
        94AE36675C464D64BAFA68DD7434390BDBE9B9C5 \
        0034A06D9D9B0064CE8ADF6BF1747F4AD2306D93 \
        FD3A5288F042B6850C66B31F09FE44734EB7990E \
        71DCFD284A79C3B38668286BC97EC7A07EDE3FC1 \
        DD8F2338BAE7501E3DD5AC78C273792F7D83545D \
        B9AE9905FFD7803F25714661B63B535A4C206CA9 \
        C4F0DFFF4E8C1A8236409D08E73BC641CC11F4C8 \
    ; do \
        gpg --keyserver ha.pool.sks-keyservers.net --recv-keys "$key"; \
    done

ENV NPM_CONFIG_LOGLEVEL info
ENV NODE_VERSION 7.4.0

RUN curl -SLO "https://nodejs.org/dist/v$NODE_VERSION/node-v$NODE_VERSION-linux-x64.tar.xz" \
  && curl -SLO "https://nodejs.org/dist/v$NODE_VERSION/SHASUMS256.txt.asc" \
  && gpg --batch --decrypt --output SHASUMS256.txt SHASUMS256.txt.asc \
  && grep " node-v$NODE_VERSION-linux-x64.tar.xz\$" SHASUMS256.txt | sha256sum -c - \
  && tar -xJf "node-v$NODE_VERSION-linux-x64.tar.xz" -C /usr/local --strip-components=1 \
  && rm "node-v$NODE_VERSION-linux-x64.tar.xz" SHASUMS256.txt.asc SHASUMS256.txt \
  && ln -s /usr/local/bin/node /usr/local/bin/nodejs

CMD [ "node" ]