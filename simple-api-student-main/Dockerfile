# Build
# Utilise une image Maven avec Amazon Corretto 17 pour la construction de l'application
FROM maven:3.8.6-amazoncorretto-17 AS myapp-build

# Définit une variable d'environnement pour le répertoire de l'application
ENV MYAPP_HOME /opt/myapp

# Définit le répertoire de travail actif pour cette étape
WORKDIR $MYAPP_HOME

# Copie le fichier de configuration Maven (pom.xml) dans l'image Docker
COPY pom.xml .

# Copie le répertoire source de l'application dans l'image Docker
COPY src ./src

# Exécute le processus de build de l'application avec Maven (crée un JAR)
RUN mvn package -DskipTests

# Run
# Utilise l'image Amazon Corretto 17 pour l'exécution de l'application
FROM amazoncorretto:17

# Redéfinit la variable d'environnement pour le répertoire de l'application
ENV MYAPP_HOME /opt/myapp

# Redéfinit le répertoire de travail actif pour cette étape
WORKDIR $MYAPP_HOME

# Copie le fichier JAR de l'application généré lors de l'étape de build dans l'étape d'exécution
COPY --from=myapp-build $MYAPP_HOME/target/*.jar $MYAPP_HOME/myapp.jar

# Spécifie la commande qui sera exécutée lorsque le conteneur Docker est démarré
ENTRYPOINT java -jar myapp.jar
