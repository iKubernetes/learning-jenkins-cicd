# Author: MageEdu <mage@magedu.com>
# Site: http://www.magedu.com
FROM jenkins/jenkins:jdk11
ADD ./sources.list /etc/apt/sources.list
USER root
RUN apt-get update && \
    apt-get -y install maven python3 docker.io golang gradle && \
    # npm, php and ruby not installed
    apt-get clean
