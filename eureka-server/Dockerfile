FROM robsonoduarte/8-jre-alpine-bash

# 在主机 /var/lib/docker下创建临时文件，连接到容器的/tmp, 因为Spring Boot内嵌的Tomcat使用/tmp作为工作目录
VOLUME /tmp

RUN mkdir -p /home/data/logs

ARG JAR_FILE
ADD ${JAR_FILE} /app/app.jar

ENV TZ=Asia/Shanghai
#ENV JAVA_HOME /home/tools/jdk8
#ENV PATH ${PATH}:${JAVA_HOME}/bin

WORKDIR /app

#设置时区
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo '$TZ' > /etc/timezone

CMD ["--spring.profiles.active=pro"]

ENTRYPOINT ["java", "-Xmx800m", "-jar","app.jar"]

# docker run eureka-server --spring.profiles.active=cluster