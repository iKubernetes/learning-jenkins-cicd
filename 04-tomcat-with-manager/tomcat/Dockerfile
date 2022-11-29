FROM tomcat:jdk11
RUN mv /usr/local/tomcat/webapps.dist/manager /usr/local/tomcat/webapps/
ADD ./context.xml /usr/local/tomcat/webapps/manager/META-INF/context.xml
