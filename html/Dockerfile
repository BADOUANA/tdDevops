# Utilise l'image officielle d'Apache HTTP Server version 2.4
FROM httpd:2.4

# Copie le fichier "index.html" depuis le répertoire local vers le répertoire "/usr/local/apache2/htdocs/" de l'image
COPY index.html /usr/local/apache2/htdocs/

# Copie le fichier "httpd.conf" depuis le répertoire local vers le répertoire "/usr/local/apache2/conf/" de l'image
COPY httpd.conf /usr/local/apache2/conf/httpd.conf
