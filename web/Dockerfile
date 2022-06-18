FROM python:3.8-buster
USER root
# uWSGI:
RUN pip install uwsgi
# Copy the base uWSGI ini file to enable default dynamic uwsgi process number
COPY ./uwsgi.ini /etc/uwsgi/
# Which uWSGI .ini file should be used, to make it customizable
ENV UWSGI_INI /etc/uwsgi/uwsgi.ini
# By default, run 2 processes
ENV UWSGI_CHEAPER 2
# By default, when on demand, run up to 16 processes
ENV UWSGI_PROCESSES 16
# CRON:
USER root
ARG INSTALL_CRON
RUN if [ ${INSTALL_CRON} = true ]; then \
    apt-get update -yqq ; \
    apt-get -y install cron \
    ;fi
# SUPERVISOR:
USER root
ARG INSTALL_SUPERVISOR
RUN if [ ${INSTALL_SUPERVISOR} = true ]; then \
    apt-get update -yqq ; \
    apt-get -y install supervisor \
    ;fi

# NPM:
USER root
ARG INSTALL_NPM
ARG NODE_VERSION
RUN if [ ${INSTALL_NPM} = true ]; then \
    apt-get update -yqq ; \
    apt-get install -y gnupg2 ; \
    curl -sL https://deb.nodesource.com/setup_${NODE_VERSION}.x | bash - ; \
    apt-get install nodejs npm ; \
    apt-get -y install nodejs \
    ;fi

# GIT:
USER root
ARG INSTALL_GIT
RUN if [ ${INSTALL_GIT} = true ]; then \
    apt-get -y install git \
    ;fi
# ZIP:
USER root
ARG INSTALL_ZIP
RUN if [ ${INSTALL_ZIP} = true ]; then \
    apt-get -y install zip unzip \
    ;fi

# Image optimizers:
USER root
ARG INSTALL_IMAGE_OPTIMIZERS
RUN if [ ${INSTALL_IMAGE_OPTIMIZERS} = true ]; then \
    apt-get install -y jpegoptim optipng pngquant gifsicle \
    ;fi
# pgsql client
ARG INSTALL_PG_CLIENT
RUN if [ ${INSTALL_PG_CLIENT} = true ]; then \
    # Create folders if not exists (https://github.com/tianon/docker-brew-debian/issues/65)
    mkdir -p /usr/share/man/man1 && \
    mkdir -p /usr/share/man/man7 && \
    # Install the pgsql client
    apt-get install -y postgresql-client \
    ;fi

COPY ./requirements.txt /opt/
RUN pip install -r /opt/requirements.txt
ARG WORK_DIR=/var/www
RUN usermod -u 1000 www-data
WORKDIR ${WORK_DIR}
CMD [ "uwsgi" ]
EXPOSE 9000
