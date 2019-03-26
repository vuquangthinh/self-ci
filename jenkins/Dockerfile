FROM jenkins/jenkins:lts

MAINTAINER Jared De La Cruz <jared@jareddlc.com>

ARG USER_JENKINS=jenkins
ARG USER_ROOT=root
ARG GROUP_DOCKER=docker
ARG GROUP_DOCKER_GID=1001

# Switch to root
USER ${USER_ROOT}

# Create docker group and add user jenkins to the group
RUN addgroup -gid ${GROUP_DOCKER_GID} ${GROUP_DOCKER} \
	&& addgroup ${USER_JENKINS} ${GROUP_DOCKER}

# Install docker
# From https://github.com/docker-library/docker/blob/5a539ed6ab5d91c5d8ccf0ccbc583500740199a2/17.09/Dockerfile

ENV DOCKER_CHANNEL stable
ENV DOCKER_BUCKET download.docker.com
ENV DOCKER_VERSION 17.09.0-ce
ENV DOCKER_ARCH x86_64
ENV DOCKER_SHA256 a9e90a73c3cdfbf238f148e1ec0eaff5eb181f92f35bdd938fd7dab18e1c4647

RUN set -ex; \
	apt-get update; \
	apt-get install -y \
		curl \
		tar \
	&& curl -fSL -o docker.tgz "https://${DOCKER_BUCKET}/linux/static/${DOCKER_CHANNEL}/${DOCKER_ARCH}/docker-${DOCKER_VERSION}.tgz" \
	&& echo "${DOCKER_SHA256} *docker.tgz" | sha256sum -c - \
	&& tar --extract --file docker.tgz --strip-components 1 --directory /usr/local/bin/ \
	&& rm docker.tgz \
	&& dockerd -v \
	&& docker -v

RUN curl -L "https://github.com/docker/compose/releases/download/1.23.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
RUN chmod +x /usr/local/bin/docker-compose
RUN ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose
RUN docker-compose --version

# Add user jenkins to list of sudoers
RUN echo "jenkins ALL=(ALL) NOPASSWD: ALL" >> /etc/sudoers

# Switch to user jenkins

COPY docker-socket.sh /docker-socket.sh
RUN chmod +x /docker-socket.sh

USER ${USER_JENKINS}

ENTRYPOINT ["/docker-socket.sh"]