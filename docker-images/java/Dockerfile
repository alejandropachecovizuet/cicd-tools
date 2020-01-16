FROM interware/alpine-java:3.9

#LABEL interware/cicd-demo:0.1.0

RUN mkdir -p /opt/spring-cloud/lib && \
    mkdir -p /opt/spring-cloud/bin
    
RUN curl -o /opt/spring-cloud/lib/cicd-demo-0.1.0.jar \
    "http://$(ip route show | awk '/default/ {print $3}'):8081/repository/devops-release/mx/interware/cicd-demo/0.1.0/cicd-demo-0.1.0.jar" 

COPY server-entrypoint.sh /opt/spring-cloud/bin/

ENTRYPOINT ["/usr/bin/java"]
CMD ["-jar", "/opt/spring-cloud/lib/cicd-demo-0.1.0.jar"]
VOLUME /var/lib/spring-cloud/config-repo
EXPOSE 8090
