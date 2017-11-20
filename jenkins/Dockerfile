FROM jenkins/jenkins:lts-alpine

MAINTAINER Mark Zhou "topurce@me.com"

ENV NODE_VERSION 8.9.1
ENV SBT_VERSION=1.0.3 \
    SBT_HOME=/usr/local/sbt
ENV PATH=${SBT_HOME}/bin:${PATH}

# Switch to root user
USER root

RUN apk add --no-cache \
        libstdc++ \
    && apk add --no-cache --virtual .build-deps \
        binutils-gold \
        g++ \
        gcc \
        libgcc \
        linux-headers \
        make \
        python \
    && cd /tmp \
    && curl -SLO "https://nodejs.org/dist/v$NODE_VERSION/node-v$NODE_VERSION.tar.xz" \
    && tar -xf "node-v$NODE_VERSION.tar.xz" \
    && cd "node-v$NODE_VERSION" \
    && ./configure \
    && make -j$(getconf _NPROCESSORS_ONLN) \
    && make install \
    && apk del .build-deps \
    && cd .. \
    && rm -Rf "node-v$NODE_VERSION" \
    && rm "node-v$NODE_VERSION.tar.xz"

ENV YARN_VERSION 1.3.2

RUN curl -fSL -o /usr/local/bin/yarn "https://github.com/yarnpkg/yarn/releases/download/v$YARN_VERSION/yarn-$YARN_VERSION.js" \
    && chmod +x /usr/local/bin/yarn

RUN npm config set registry https://registry.npm.taobao.org


RUN apk add --no-cache git bc && \
    curl -sL /tmp/sbt-${SBT_VERSION}.tgz "https://github.com/sbt/sbt/releases/download/v${SBT_VERSION}/sbt-${SBT_VERSION}.tgz" | \
    gunzip | tar -x -C /usr/local

USER jenkins
COPY fake-project $JENKINS_HOME/fake-project
RUN cd $JENKINS_HOME/fake-project && \
    sbt -Dsbt.log.noformat=true clean +compile

USER root
RUN rm -rf $JENKINS_HOME/fake-project

USER jenkins

