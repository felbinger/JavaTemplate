FROM adoptopenjdk:15-jdk-hotspot-bionic AS builder

RUN set -o errexit -o nounset \
    && apt-get update \
    && apt-get install git -y

COPY . /home/src
WORKDIR /home/src

RUN chmod +x ./gradlew \
    && ./gradlew installDist --stacktrace --no-daemon

FROM adoptopenjdk:15-jre-hotspot-bionic

ARG APP_USER=app
ARG APP_GROUP=app

ENV APP_HOME /opt/app
ENV DATA_DIR /data

RUN set -o errexit -o nounset \
    && groupadd --system --gid 1000 ${APP_GROUP} \
    && useradd --system --gid ${APP_GROUP} --uid 1000 --shell /bin/bash --create-home ${APP_USER} \
    && mkdir -p ${DATA_DIR} \
    && chown --recursive ${APP_USER}:${APP_GROUP} ${DATA_DIR} \
    && chown --recursive ${APP_USER}:${APP_GROUP} /home/${APP_USER}

WORKDIR ${DATA_DIR}
COPY --from=builder --chown=${APP_USER}:${APP_GROUP} /home/src/javatemplate/build/install/javatemplate/ ${APP_HOME}/

RUN ln --symbolic ${APP_HOME}/bin/JavaTemplate /usr/bin/javatemplate \
  && chmod +x /usr/bin/javatemplate
USER ${APP_USER}

ENTRYPOINT ["javatemplate"]