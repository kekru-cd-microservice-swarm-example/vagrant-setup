FROM jenkins:2.19.4
USER root
RUN apt-get update && apt-get install -y \
    tree \
    jq

RUN echo "Europe/Berlin" > /etc/timezone && dpkg-reconfigure -f noninteractive tzdata
USER jenkins
