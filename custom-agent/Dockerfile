FROM jetbrains/teamcity-minimal-agent:2025.07.1-linux


ARG JDK_URL=https://github.com/adoptium/temurin17-binaries/releases/download/jdk-17.0.11+9/OpenJDK17U-jdk_x64_linux_hotspot_17.0.11_9.tar.gz
ARG JDK_SHA256=aa7fb6bb342319d227a838af5c363bfa1b4a670c209372f9e6585bd79da6220c

ARG MAVEN_VERSION=3.9.11
ARG MAVEN_BASEURL=https://downloads.apache.org/maven/maven-3/${MAVEN_VERSION}/binaries
ARG MAVEN_ARCH=apache-maven-${MAVEN_VERSION}-bin.tar.gz

ARG GIT_BOT_NAME="tc-release-notes-bot"
ARG GIT_BOT_EMAIL="tc-release-notes-bot@github.com"


ENV LANG=C.UTF-8 LC_ALL=C.UTF-8 TZ=UTC
ENV JAVA_HOME=/opt/jdk
ENV MAVEN_HOME=/opt/maven
ENV PATH=${JAVA_HOME}/bin:${MAVEN_HOME}/bin:${PATH}

USER root
RUN set -eux; \
  apt-get update; \
  DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends \
    ca-certificates curl tar git \
    openssh-client \
    rsync \
    jq; \
  rm -rf /var/lib/apt/lists/*





ENV HOME=/opt/buildagent
RUN mkdir -p /opt/buildagent/.ssh && chown -R buildagent:buildagent /opt/buildagent/.ssh


RUN mkdir -p /etc/ssh \
 && ssh-keyscan -t rsa,ecdsa,ed25519 github.com >> /etc/ssh/ssh_known_hosts \
 && chmod 644 /etc/ssh/ssh_known_hosts

# JDK (verify checksum)
RUN curl -fsSL "$JDK_URL" -o /tmp/jdk.tgz; \
  echo "${JDK_SHA256}  /tmp/jdk.tgz" | sha256sum -c -; \
  mkdir -p /opt; \
  tar -xzf /tmp/jdk.tgz -C /opt; \
  ln -s /opt/$(tar -tzf /tmp/jdk.tgz | head -1 | cut -d/ -f1) /opt/jdk; \
  rm -f /tmp/jdk.tgz

# Maven (verify sha512)
RUN curl -fsSL "${MAVEN_BASEURL}/${MAVEN_ARCH}.sha512" -o /tmp/maven.sha512; \
  curl -fsSL "${MAVEN_BASEURL}/${MAVEN_ARCH}" -o /tmp/maven.tgz; \
  sha512sum -c /tmp/maven.sha512; \
  tar -xzf /tmp/maven.tgz -C /opt; \
  ln -s /opt/apache-maven-${MAVEN_VERSION} /opt/maven; \
  rm -f /tmp/maven.tgz /tmp/maven.sha512




USER buildagent



# ---- Git global defaults ----
# Identity + CI defaults.
RUN git config --global user.name  "${GIT_BOT_NAME}" \
 && git config --global user.email "${GIT_BOT_EMAIL}" \
 && git config --global commit.gpgsign false \
 && git config --global fetch.prune true \
 && git config --global push.default simple \
 && git config --global pull.rebase false \
 && git config --global init.defaultBranch main \
 && git config --global core.autocrlf input \
 && git config --global advice.detachedHead false


