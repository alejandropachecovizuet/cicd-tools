FROM jenkins/jenkins:lts

# LABEL jenkins.interware.mx.version="2.168"
# LABEL jenkins.interware.mx.tag="interware/jenkins:2.168"

# LABEL creator "Gerardo Cortés <gcortes@interware.com.mx>"
# LABEL maintainer "Gerardo Cortés <gcortes@interware.com.mx>"

USER root

RUN mkdir -p /var/log/jenkins & \
    mkdir -p /var/cache/jenkins & \
    mkdir -p /var/jenkins_home/.ssh    
RUN chown -R jenkins:jenkins /var/log/jenkins & \
    chown -R jenkins:jenkins /var/cache/jenkins

ADD get-key /bin
COPY id_rsa /var/jenkins_home/.ssh
COPY id_rsa.pub /var/jenkins_home/.ssh
RUN chmod 755 /bin/get-key && \
    chmod 700 /var/jenkins_home/.ssh

RUN apt-get update -qq && \
    apt-get install -qqy apt-transport-https ca-certificates curl gnupg2 software-properties-common && \
    apt-get install sshpass -y && \
    apt-get install iputils-ping -y && \
    apt-get install telnet -y && \
    apt-get install vim -y
     
RUN curl -fsSL https://download.docker.com/linux/debian/gpg | apt-key add -
RUN add-apt-repository \
    "deb [arch=amd64] https://download.docker.com/linux/debian \
    $(lsb_release -cs) \
    stable"
RUN apt-get update  -qq && \
    apt-get install docker-ce -y && \
    rm /usr/bin/python && \
    ln -s /usr/bin/python3 /usr/bin/python && \
    apt install python3-pip -y && \
    ln -s /usr/bin/pip3 /usr/bin/pip && \
    pip install ansible
    
RUN usermod -aG docker jenkins

RUN echo "$(ip route show | awk '/default/ {print $3}') kubehost" >> /etc/hosts

USER jenkins

ENV JENKINS_OPTS="--handlerCountMax=300 --logfile=/var/log/jenkins/jenkins.log"

