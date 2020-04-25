# Oracle instant client
FROM ubuntu:18.04

MAINTAINER neo <neo@kfsyscc.org>

# Oracle-instantclient
ADD oracle-instantclient12.2-basic-12.2.0.1.0-1.x86_64.rpm /tmp/
ADD oracle-instantclient12.2-sqlplus-12.2.0.1.0-1.x86_64.rpm /tmp/
ADD oracle-instantclient12.2-devel-12.2.0.1.0-1.x86_64.rpm /tmp/

# Oracle Environment
#ENV NLS_LANG=AMERICAN_AMERICA.ZHT16MSWIN950
ENV NLS_LANG=AMERICAN_AMERICA.AL32UTF8
ENV ORACLE_HOME=/usr/lib/oracle/12.2/client64
ENV PATH=$PATH:$ORACLE_HOME/bin
ENV LD_LIBRARY_PATH=$LD_LIBRARY_PATH:$ORACLE_HOME/lib
ENV TNS_ADMIN=$ORACLE_HOME/network/admin

# Install Oracle
RUN apt-get update && \
    apt-get install -y python3-pip python3-dev && \
    cd /usr/local/bin && \
    ln -s /usr/bin/python3 python && \
    pip3 install --upgrade pip && \
    apt-get install -y --no-install-recommends apt-utils && \
    apt-get install -y sqlite3 && \
    apt-get -y install alien libaio1 && \
    apt-get -y install locales locales-all && \
    alien -i /tmp/oracle-instantclient12.2-basic-12.2.0.1.0-1.x86_64.rpm && \
    alien -i /tmp/oracle-instantclient12.2-sqlplus-12.2.0.1.0-1.x86_64.rpm && \
    alien -i /tmp/oracle-instantclient12.2-devel-12.2.0.1.0-1.x86_64.rpm && \
    mkdir -p /opt/oracle/network && \
    mkdir -p /opt/app && \
    ln -snf /usr/lib/oracle/12.2/client64 /opt/oracle && \
    ln -snf /etc/oracle /opt/oracle/network/admin && \
    apt-get install --no-install-recommends -y redis-server && \
    apt-get clean && rm -rf /var/cache/apt/* /var/lib/apt/lists/* /tmp/* /var/tmp/* && \
    update-rc.d -f redis-server disable && \
    sed -i 's/^\(bind .*\)$/# \1/' /etc/redis/redis.conf && \
    sed -i 's/^\(daemonize .*\)$/# \1/' /etc/redis/redis.conf

#Set oracle DSN
COPY ./app/tnsnames.ora $ORACLE_HOME/network/admin/tnsnames.ora

# Locale 
ENV LC_ALL=en_US.UTF-8
ENV LANG=en_US.UTF-8
ENV LANGUAGE=en_US.UTF-8

# Working Area
WORKDIR /opt/app
ADD ./app /opt/app

# Install Flask & GUNICON & SQLAlchemy
RUN apt-get update && \
    apt-get install -y && \
    pip install -r /opt/app/requirements.txt && \  
    ln -snf /opt/app/tnsnames.ora $ORACLE_HOME/network/admin/tnsnames.ora && \ 
    ln -snf /opt/app/redis.conf /etc/redis/redis.conf

ENV DOCKER_CONTAINER=1
EXPOSE 5000
EXPOSE 8000

VOLUME ["/var/lib/redis"]
EXPOSE 6379

# CMD specifcies the command to execute to start the server running.
CMD ["/opt/app/start.sh"]
# done