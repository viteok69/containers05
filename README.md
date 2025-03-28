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




