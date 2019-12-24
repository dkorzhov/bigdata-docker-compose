FROM alpine:3.10.2

# curl and unzip: download and extract Hive, Hadoop, Spark etc.
# bash: Hadoop is not compatible with Alpine's `ash` shell
# openjdk8: Java
# coreutils: Spark launcher script relies on GNU implementation of `nice`
# procps: Hadoop needs GNU `ps` utility
# findutils: Spark needs GNU `find` to run jobs (weird but true)
# ncurses: so that you can run `yarn top`
RUN apk add --no-cache \
    curl=7.66.0-r0 \
    unzip=6.0-r4 \
    openjdk8=8.222.10-r0 \
    bash=5.0.0-r0 \
    coreutils=8.31-r0 \
    procps=3.3.15-r0 \
    findutils=4.6.0-r1 \
    ncurses=6.1_p20190518-r0

# https://github.com/hadolint/hadolint/wiki/DL4006
SHELL ["/bin/bash", "-o", "pipefail", "-c"]


# Flume
ARG FLUME_VERSION=1.9.0
ENV FLUME_HOME /usr/flume
RUN curl --progress-bar -L --retry 3 \
  "http://apache-mirror.rbc.ru/pub/apache/flume/${FLUME_VERSION}/apache-flume-${FLUME_VERSION}-bin.tar.gz" \
  | gunzip \
  | tar -x -C /usr/ \
 && mv /usr/apache-flume-${FLUME_VERSION}-bin ${FLUME_HOME} \
 && chown -R root:root ${FLUME_HOME}
COPY conf/flume-sources-1.0-SNAPSHOT.jar ${FLUME_HOME}/lib/ 
COPY conf/flume-env.sh ${FLUME_HOME}/conf/
COPY conf/flume.conf ${FLUME_HOME}/conf/

# Common settings
ENV JAVA_HOME /usr/lib/jvm/java-1.8-openjdk
ENV PATH="${PATH}:${JAVA_HOME}/bin"

# If both YARN Web UI and Spark UI is up, then returns 0, 1 otherwise.
HEALTHCHECK CMD curl -f http://host.docker.internal:8080/ \
    && curl -f http://host.docker.internal:8088/ || exit 1

# Entry point: start all services and applications.
#COPY entrypoint.sh /
#RUN chmod +x /entrypoint.sh
#ENTRYPOINT ["/entrypoint.sh"]

