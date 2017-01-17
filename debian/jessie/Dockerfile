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
    && apt-get install -y --no-install-recommends apt-utils curl ca-certificates build-essential libssl-dev \
    && echo "=> Suggested packages:" \
    && apt-get install -y --no-install-recommends tree nano tar \
    && echo "=> Configuring and installing timezone (${TIMEZONE}):" \
    && echo ${TIMEZONE} > /etc/timezone \
    && dpkg-reconfigure -f noninteractive tzdata \
    && echo "=> Configuring and installing locale (${LOCALE_LANG_COUNTRY}.${LOCALE_CODIFICATION}):" \
    && apt-get install -y locales \
    && rm -rf /var/lib/apt/lists/* \
    && localedef -i ${LOCALE_LANG_COUNTRY} -c -f ${LOCALE_CODIFICATION} -A /usr/share/locale/locale.alias ${LOCALE_LANG_COUNTRY}.${LOCALE_CODIFICATION}

# Set enviroment variables:

ENV LANG=${LOCALE_LANG_COUNTRY}.${LOCALE_CODIFICATION_ENV}

# Set versions of nvm (Node.js version manager), and Node.js:

ENV NVM_VERSION 0.32.0
ENV NPM_CONFIG_LOGLEVEL info
ENV NODE_VERSION 7.4.0

# Download nvm, install it, install Node.js, and remove unnecesary files and packages:

RUN set -x \
	&& echo "=> Downloading nvm version (${NVM_VERSION}):" \
    && curl -sL https://raw.githubusercontent.com/creationix/nvm/v${NVM_VERSION}/install.sh -o install_nvm.sh \
    && echo "=> Installing nvm version (${NVM_VERSION}):" \
    && bash install_nvm.sh \
    && echo "=> Configuring and loading in bash nvm version (${NVM_VERSION}):" \
    && export NVM_DIR="/root/.nvm" \
    && [ -s "$NVM_DIR/nvm.sh" ] && . "$NVM_DIR/nvm.sh" \  # This loads nvm
    && echo "=> Installing node version (${NODE_VERSION}):" \
    && nvm install ${NODE_VERSION} \
    && echo "=> Cleaning...:" \
    && rm install_nvm.sh \
    && apt-get purge -y --auto-remove apt-utils curl ca-certificates build-essential libssl-dev

CMD [ "node" ]