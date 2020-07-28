## Spring Boot Docker

Simple Dockerfile:

	FROM openjdk:8-jdk-alpine
	VOLUME /tmp
	ARG JAR_FILE
	COPY  app.jar
	ENTRYPOINT ["java","-jar","/app.jar"]
	
Build dockerfile:
	
	docker build -t jay83/spring-boot-app .
	
Run docker container:

	docker run -p 8080:8080 myorg/myapp

Run in interactive mode:

	docker run -ti --entrypoint /bin/sh myorg/myapp
	


udZh1GTAb1b7