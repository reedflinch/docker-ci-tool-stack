#
# Creates a docker container with SonarQube, incl. several plugins
# Since the original Dockerfile does not support plugins, I
# had to extend the Dockerfile
#
# Original: https://hub.docker.com/_/sonarqube/
#

FROM java:openjdk-8u45-jdk

MAINTAINER Marcel Birkner <marcel.birkner@codecentric.de>

ENV SONARQUBE_HOME /opt/sonarqube

# Http port
EXPOSE 9000

# H2 Database port
#EXPOSE 9092
# PostGres Database port
EXPOSE 5432

# Database configuration
# Defaults to using H2
ENV SONARQUBE_JDBC_USERNAME sonar
ENV SONARQUBE_JDBC_PASSWORD sonar
#ENV SONARQUBE_JDBC_URL jdbc:h2:tcp://localhost:9092/sonar
#ENV SONARQUBE_JDBC_URL jdbc:postgresql:tcp://localhost:5432/sonar

ENV SONAR_VERSION 5.1.2

ENV SONAR_DOWNLOAD_URL https://sonarsource.bintray.com/Distribution

# pub   2048R/D26468DE 2015-05-25
#      Key fingerprint = F118 2E81 C792 9289 21DB  CAB4 CFCA 4A29 D264 68DE
# uid       [ unknown] sonarsource_deployer (Sonarsource Deployer) <infra@sonarsource.com>
# sub   2048R/06855C1D 2015-05-25
RUN gpg --keyserver ha.pool.sks-keyservers.net --recv-keys F1182E81C792928921DBCAB4CFCA4A29D26468DE

RUN set -x \
	&& cd /opt \
	&& curl -o sonarqube.zip -fSL http://downloads.sonarsource.com/sonarqube/sonarqube-$SONAR_VERSION.zip \
	&& curl -o sonarqube.zip.asc -fSL http://downloads.sonarsource.com/sonarqube/sonarqube-$SONAR_VERSION.zip.asc \
	&& gpg --verify sonarqube.zip.asc \
	&& unzip sonarqube.zip \
	&& mv sonarqube-$SONAR_VERSION sonarqube \
        && rm sonarqube.zip* \
	&& rm -rf $SONARQUBE_HOME/bin/*

# Installing Plugins
RUN cd /opt/sonarqube/extensions/plugins/ \
  && curl -o sonar-java-plugin-3.5.jar -fSL $SONAR_DOWNLOAD_URL/sonar-java-plugin/sonar-java-plugin-3.5.jar \
  && curl -o sonar-web-plugin-2.4.jar -fSL $SONAR_DOWNLOAD_URL/sonar-web-plugin/sonar-web-plugin-2.4.jar \
  && curl -o sonar-scm-git-plugin-1.1.jar -fSL http://downloads.sonarsource.com/plugins/org/codehaus/sonar-plugins/sonar-scm-git-plugin/1.1/sonar-scm-git-plugin-1.1.jar \
  && curl -o sonar-javascript-plugin-2.10.jar -fSL https://sonarsource.bintray.com/Distribution/sonar-javascript-plugin/sonar-javascript-plugin-2.10.jar

VOLUME ["$SONARQUBE_HOME/data", "$SONARQUBE_HOME/extensions"]

WORKDIR $SONARQUBE_HOME
COPY sonar.properties $SONARQUBE_HOME/conf/
COPY run.sh $SONARQUBE_HOME/bin/
RUN chmod +x $SONARQUBE_HOME/bin/run.sh

# install sonar-runner
#RUN wget https://sonarsource.bintray.com/Distribution/sonar-scanner-cli/sonar-scanner-2.5.zip
#RUN unzip sonar-scanner-2.5.zip
#RUN rm sonar-scanner-2.5.zip
#RUN export SONAR_RUNNER_HOME=$SONARQUBE_HOME/sonar-scanner-2.5
#RUN export PATH=$PATH:$SONAR_RUNNER_HOME/bin

ENTRYPOINT ["./bin/run.sh"]
