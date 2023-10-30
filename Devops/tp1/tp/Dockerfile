# Utilise l'image officielle de PostgreSQL version 14.1 avec la variante Alpine Linux
FROM postgres:14.1-alpine

# Copie les fichiers SQL du répertoire local "/sql" vers le répertoire "/docker-entrypoint-initdb.d" de l'image
COPY /sql /docker-entrypoint-initdb.d

# Définit des variables d'environnement pour la configuration de la base de données PostgreSQL
ENV POSTGRES_DB=db \
   POSTGRES_USER=usr \
   POSTGRES_PASSWORD=pwd

# Utilisez une image Java JRE comme base
#FROM openjdk:17-jre

# Copiez les fichiers .class (compiled Java) dans le conteneur
#COPY javatp/Main.class /app/Main.clas

# Exécutez l'application Java en utilisant la commande "java Main"
#CMD ["java", "/app/Main"]




