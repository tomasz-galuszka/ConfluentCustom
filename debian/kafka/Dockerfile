#
# Copyright 2016 Confluent Inc.
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
# http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.

FROM confluentinc/cp-base

ARG COMMIT_ID=unknown
LABEL io.confluent.docker.git.id=$COMMIT_ID
ARG BUILD_NUMBER=-1
LABEL io.confluent.docker.build.number=$BUILD_NUMBER

MAINTAINER partner-support@confluent.io
LABEL io.confluent.docker=true

# allow arg override of required env params
ARG KAFKA_ZOOKEEPER_CONNECT
ENV KAFKA_ZOOKEEPER_CONNECT=${KAFKA_ZOOKEEPER_CONNECT}
ARG KAFKA_ADVERTISED_LISTENERS
ENV KAFKA_ADVERTISED_LISTENERS=${KAFKA_ADVERTISED_LISTENERS}

ENV COMPONENT=kafka
ENV COMPONENT_2=schema-registry

# primary
EXPOSE 9092
EXPOSE 8081

RUN echo "===> installing ${COMPONENT}..." \
    && apt-get update && apt-get install -y confluent-kafka-${SCALA_VERSION}=${KAFKA_VERSION}${CONFLUENT_PLATFORM_LABEL}-${CONFLUENT_DEB_VERSION} \
    \
    && echo "===> clean up ..."  \
    && apt-get clean && rm -rf /tmp/* /var/lib/apt/lists/* \
    \
		&& echo "===> Setting up ${COMPONENT} dirs..." \
   	&& mkdir -p /var/lib/${COMPONENT}/data /etc/${COMPONENT}/secrets\
   	&& chmod -R ag+w /etc/${COMPONENT} /var/lib/${COMPONENT}/data /etc/${COMPONENT}/secrets


RUN echo "===> installing ${COMPONENT_2}..." \
    && apt-get update && apt-get install -y \
        confluent-${COMPONENT_2}=${CONFLUENT_VERSION}${CONFLUENT_PLATFORM_LABEL}-${CONFLUENT_DEB_VERSION} \
        confluent-control-center=${CONFLUENT_VERSION}${CONFLUENT_PLATFORM_LABEL}-${CONFLUENT_DEB_VERSION} \
        confluent-security=${CONFLUENT_VERSION}${CONFLUENT_PLATFORM_LABEL}-${CONFLUENT_DEB_VERSION} \
    \
    && echo "===> clean up ..."  \
    && apt-get clean && rm -rf /tmp/* /var/lib/apt/lists/* \
    \
    && echo "===> Setting up ${COMPONENT_2} dirs" \
    && mkdir -p /etc/${COMPONENT_2}/secrets\
    && chmod -R ag+w /etc/${COMPONENT_2} /etc/${COMPONENT_2}/secrets


VOLUME ["/var/lib/${COMPONENT}/data", "/etc/${COMPONENT}/secrets", "/etc/${COMPONENT_2}/secrets"]

COPY include/etc/confluent/docker /etc/confluent/docker

COPY include/etc/confluent/schema_registry/docker /etc/confluent/schema_registry/docker

COPY include/etc/confluent/run /etc/confluent/run

RUN chmod +x /etc/confluent/run
CMD ["/etc/confluent/run"]
