# Lucrarea 5: Rularea site-ului într-un container

> Realizat de studentul: Badia Victor \
> Grupa: I2301
> \
> Verificat de Mihail Croitor

## Scopul Lucrării

Dupa executarea acestei lucrări, studentul va fi capabil să pregătească un container pentru a rula un site web bazat pe Apache HTTP Server + PHP (mod_php) + MariaDB.

## Sarcina

Creați un fișier Dockerfile pentru a construi o imagine a containerului care va conține un site web bazat pe Apache HTTP Server + PHP (mod_php) + MariaDB. Baza de date MariaDB trebuie să fie stocată într-un volum montat. Serverul trebuie să fie disponibil pe portul 8000.

Instalați site-ul WordPress. Verificați funcționarea site-ului.

## Pregatire

Pentru a efectua această lucrare, trebuie să aveți instalat pe computer Docker.

Pentru a efectua această lucrare, trebuie să aveți experiență în efectuarea lucrării de laborator nr. 3.

## Executare

Creați un depozit de cod sursă containers05 și clonați-l pe computerul dvs.

![image](https://github.com/user-attachments/assets/317b76f8-13af-4174-8127-4ee5503fa35f)

## Extragerea fișierelor de configurare apache2, php, mariadb din container

Creați în directorul containers05 un director files, precum și
- directorul files/apache2 - pentru fișierele de configurare apache2;
- directorul files/php - pentru fișierele de configurare php;
- directorul files/mariadb - pentru fișierele de configurare mariadb.

![image](https://github.com/user-attachments/assets/d1722e08-af2f-4078-a2d0-401439014d2c)
![image](https://github.com/user-attachments/assets/dcd9c770-1dda-4992-9a12-8cb5efae8966)

Creați în directorul containers05 fișierul Dockerfile cu următorul conținut:

```bash
# create from debian image
FROM debian:latest

# install apache2, php, mod_php for apache2, php-mysql and mariadb
RUN apt-get update && \
    apt-get install -y apache2 php libapache2-mod-php php-mysql mariadb-server && \
    apt-get clean
```
![image](https://github.com/user-attachments/assets/4c4da172-bada-4271-a577-913d2ec1c7a7)

Construiți o imagine a containerului cu numele apache2-php-mariadb.

```bash
docker build -t apache2-php-mariadb .
```
![image](https://github.com/user-attachments/assets/9193dfaa-2d83-4be5-92c5-7f8d6e02d695)

Creați un container apache2-php-mariadb din imaginea apache2-php-mariadb și porniți-l în modul de fundal cu comanda bash.
![image](https://github.com/user-attachments/assets/8863bbf3-800f-47f7-bc47-4b9d4fa7e8d1)

Copiați din container fișierele de configurare apache2, php, mariadb în directorul files/ de pe computer. Pentru a face acest lucru, în contextul proiectului, executați comenzile:

```bash
docker cp apache2-php-mariadb:/etc/apache2/sites-available/000-default.conf files/apache2/
docker cp apache2-php-mariadb:/etc/apache2/apache2.conf files/apache2/
docker cp apache2-php-mariadb:/etc/php/8.2/apache2/php.ini files/php/
docker cp apache2-php-mariadb:/etc/mysql/mariadb.conf.d/50-server.cnf files/mariadb/
```
![image](https://github.com/user-attachments/assets/52ba8d96-9d44-4ef9-ae14-c66dd09af7df)


După executarea comenzilor, în directorul files/ ar trebui să apară fișierele de configurare apache2, php, mariadb. Verificați dacă acestea există. Opriți și ștergeți containerul apache2-php-mariadb.
Oprim si stergem containerul apache2-php-mariadb:

```bash
docker stop apache2-php-mariadb
docker rm apache2-php-mariadb
```
![image](https://github.com/user-attachments/assets/68cfaf7d-1cec-4720-9f3b-fe554e657772)

##Configurarea
Fișierul de configurare apache2

Deschideți fișierul files/apache2/000-default.conf, găsiți linia #ServerName www.example.com și înlocuiți-o cu ServerName localhost.
![image](https://github.com/user-attachments/assets/064bb54a-1ee8-4633-be63-588351f74770)
Găsiți linia ServerAdmin webmaster@localhost și înlocuiți adresa de e-mail cu a dvs.
![image](https://github.com/user-attachments/assets/b8e0eaf3-52b2-44f8-9d13-8edd5872f7b3)
După linia DocumentRoot /var/www/html adăugați următoarea linie:

```bash
DirectoryIndex index.php index.html
```
![image](https://github.com/user-attachments/assets/c3228046-fa02-48f6-8efe-5abb4712b428)

La sfârșitul fișierului files/apache2/apache2.conf adăugați următoarea linie:

```bash
ServerName localhost
```
![image](https://github.com/user-attachments/assets/82f9b4af-1223-4286-ae71-f21f71f299b3)

##Fișierul de configurare php

Deschideți fișierul files/php/php.ini, găsiți linia ;error_log = php_errors.log și înlocuiți-o cu error_log = /var/log/php_errors.log.
![image](https://github.com/user-attachments/assets/d142a537-0b8c-4ca7-857c-83c422529a9e)

Setați parametrii memory_limit, upload_max_filesize, post_max_size și max_execution_time astfel:

memory_limit = 128M
upload_max_filesize = 128M
post_max_size = 128M
max_execution_time = 120

##Fișierul de configurare mariadb

Deschideți fișierul files/mariadb/50-server.cnf, găsiți linia #log_error = /var/log/mysql/error.log și eliminați # din fața ei.
![image](https://github.com/user-attachments/assets/5c31d1bc-cf40-447b-bcb2-0b80b3636379)

##Crearea scriptului de pornire

In directorul files creati directorul supervisor si fisierul supervisord.conf cu urmatorul continut:

```bash
[supervisord]
nodaemon=true
logfile=/dev/null
user=root

# apache2
[program:apache2]
command=/usr/sbin/apache2ctl -D FOREGROUND
autostart=true
autorestart=true
startretries=3
stderr_logfile=/proc/self/fd/2
user=root

# mariadb
[program:mariadb]
command=/usr/sbin/mariadbd --user=mysql
autostart=true
autorestart=true
startretries=3
stderr_logfile=/proc/self/fd/2
user=mysql
```
![image](https://github.com/user-attachments/assets/d392b669-7564-4ce3-af8a-e5f32db64641)

##Crearea Dockerfile

Deschiți fișierul Dockerfile și adăugați următoarele linii:
după instrucția FROM ... adăugați montarea volumelor:

```bash
# mount volume for mysql data
VOLUME /var/lib/mysql

# mount volume for logs
VOLUME /var/log
```
![image](https://github.com/user-attachments/assets/c018c680-313e-46b7-ab0c-ca41a1cb9bb0)

în instrucția RUN ... adăugați instalarea pachetului supervisor.
![image](https://github.com/user-attachments/assets/a0a1bba6-cddb-4d4a-b710-3dd662af3203)

după instrucția RUN ... adăugați copierea și dezarhivarea site-ului WordPress:
![image](https://github.com/user-attachments/assets/d5ddff57-05f2-441d-a14b-d153c9ec9d02)

după copierea fișierelor WordPress adăugați copierea fișierelor de configurare apache2, php, mariadb, așa cum și a scriptului de pornire:
![image](https://github.com/user-attachments/assets/39b8ee31-2fe7-475a-8e22-dae97aa7ebaf)

pentru functionarea mariadb creati directorul /var/run/mysqld si setati permisiile pe el:
![image](https://github.com/user-attachments/assets/dc7f735e-e04b-407e-a84b-b287646aaf5b)

deschideti portul 80.
adăugați comanda de pornire supervisord:
![image](https://github.com/user-attachments/assets/197aaf0d-c1e8-4f16-bc89-09b7cc2b8d92)

Creati imaginea containerului cu numele apache2-php-mariadb și porniți containerul apache2-php-mariadb din imaginea apache2-php-mariadb. Verificați dacă site-ul WordPress este disponibil la adresa http://localhost/. Verificați dacă in directorul /var/www/html/ există fișierele site-ului WordPress. Verificați dacă fișierele de configurare apache2, php, mariadb sunt modificate.
![image](https://github.com/user-attachments/assets/fcab8013-a91a-42ba-a6cc-6fbd397f20ed)
![image](https://github.com/user-attachments/assets/52188c7e-1f73-47fc-84f0-9df9474e493d)

Verificăm dacă site-ul WordPress este disponibil

##Crearea bazelor de date și a utilizatorului pentru WordPress

Crearea bazelor de date și a utilizatorului pentru WordPress se face în containerul apache2-php-mariadb. Pentru a face acest lucru, conectați-vă la containerul apache2-php-mariadb și executați comenzile:

```bash
mysql
CREATE DATABASE wordpress;
CREATE USER 'wordpress'@'localhost' IDENTIFIED BY 'wordpress';
GRANT ALL PRIVILEGES ON wordpress.* TO 'wordpress'@'localhost';
FLUSH PRIVILEGES;
EXIT;
```

![image](https://github.com/user-attachments/assets/a75958d2-7f2e-4cf3-8beb-5ca78ce6f30b)

##Crearea fișierului de configurare WordPress

Deschideți în browser site-ul WordPress la adresa http://localhost/ și urmați instrucțiunile pentru instalarea site-ului WordPress. La pasul 2, specificați următoarele date:























