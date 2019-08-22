FROM frolvlad/alpine-oraclejdk8:slim

# 在主机 /var/lib/docker下创建临时文件，连接到容器的/tmp, 因为Spring Boot内嵌的Tomcat使用/tmp作为工作目录
VOLUME /tmp

RUN mkdir -p /home/data/logs

ARG JAR_FILE

ADD ${JAR_FILE} /app/app.jar

ENV TZ=Asia/Shanghai

EXPOSE 12000

ENTRYPOINT ["java", "-Xmx800m", "-jar","/app/app.jar", "--spring.profiles.active=pro"]