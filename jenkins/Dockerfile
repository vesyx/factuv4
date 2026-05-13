FROM jenkins/jenkins
USER root
# Define variables
ENV MAVEN_VERSION=3.9.15
# Update OS and install wget
RUN apt-get update && apt-get install -y wget
# Download Maven
RUN wget --no-verbose https://downloads.apache.org/maven/maven-3/${MAVEN_VERSION}/binaries/apache-maven-${MAVEN_VERSION}-bin.tar.gz -P /tmp/
# Install Maven
RUN tar xzf /tmp/apache-maven-${MAVEN_VERSION}-bin.tar.gz -C /opt/ && \
ln -s /opt/apache-maven-${MAVEN_VERSION} /opt/maven && \
ln -s /opt/maven/bin/mvn /usr/local/bin/mvn && \
rm /tmp/apache-maven-${MAVEN_VERSION}-bin.tar.gz
# Permissions
RUN chown -R jenkins:jenkins /opt/maven
# Environment variables
ENV MAVEN_HOME=/opt/maven
ENV PATH=$MAVEN_HOME/bin:$PATH
USER jenkins