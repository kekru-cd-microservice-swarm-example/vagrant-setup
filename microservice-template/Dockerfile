//Template fuer eine Spring Boot Anwendung
//Basis ist OpenJDK 8
FROM openjdk:8
//Zeitzone auf Berlin setzen
RUN echo "Europe/Berlin" > /etc/timezone && dpkg-reconfigure -f noninteractive tzdata

//Jar File in Image kopieren
ADD app.jar /data
WORKDIR /data

//Healthcheck definieren
HEALTHCHECK --interval=20s --timeout=5s --retries=3 CMD curl -f http://localhost:8080/ || exit 1

//Startkommando
CMD java -jar app.jar

//Ports, die verfügbar sein sollen
EXPOSE 8080
