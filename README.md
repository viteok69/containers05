# Lucrarea 6: Interacțiunea containerelor

> Realizat de studentul: Badia Victor \
> Grupa: I2301
> \
> Verificat de Mihail Croitor

## Scopul Lucrării

După finalizarea acestei lucrări studentul va fi capabil să gestioneze interacțiunea între mai multe containere.

## Sarcina

Creați o aplicație PHP pe baza a două containere: nginx, php-fpm.

## Pregatire

Pentru efectuarea acestei lucrări este necesar să aveți instalat pe computer Docker.

Pentru efectuarea lucrării este necesar să aveți experiență în efectuarea lucrării de laborator №3.

## Executare

Creați un depozit de cod sursă containers05 și clonați-l pe computerul dvs.

![image](https://github.com/user-attachments/assets/02877156-18a7-45e3-bcad-69b612401aee)


În directorul containers05 creați directorul mounts/site. În acest director copiați site-ul PHP creat în cadrul cursului Programare PHP.

![image](https://github.com/user-attachments/assets/1794ba3f-0147-49b6-b61f-dcc3fee00b19)


Creați fișierul .gitignore în rădăcina proiectului și adăugați următoarele linii:

```bash
# Ignore files and directories
mounts/site/*
```

![image](https://github.com/user-attachments/assets/544e390c-79e6-49c2-8456-f8fde41cd829)
![image](https://github.com/user-attachments/assets/7cf1aca2-634f-4520-9c04-71d6e150993c)


Creați în directorul containers05 fișierul nginx/default.conf cu următorul conținut:

```bash
server {
    listen 80;
    server_name _;
    root /var/www/html;
    index index.php;
    location / {
        try_files $uri $uri/ /index.php?$args;
    }
    location ~ \.php$ {
        fastcgi_pass backend:9000;
        fastcgi_index index.php;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        include fastcgi_params;
    }
}
```

![image](https://github.com/user-attachments/assets/09b3287f-2fb2-47fc-bab5-90eaf75610cf)
![image](https://github.com/user-attachments/assets/cfcac6b8-275e-4a72-ab29-91cd5fbdafab)

## Pornirea și testarea

Creați rețeaua internal pentru containere.

![image](https://github.com/user-attachments/assets/93ca69bf-9db1-469d-aa0b-bdc1982ca4e9)


Creați containerul backend cu următoarele proprietăți:
- pe baza imaginii php:7.4-fpm;
- directorul mounts/site este montat în /var/www/html;
- funcționează în rețeaua internal.

![image](https://github.com/user-attachments/assets/c0de5ade-0591-4d66-ad83-5b55a9f959f0)


Creați containerul frontend cu următoarele proprietăți:
- pe baza imaginii nginx:1.23-alpine;
- directorul mounts/site este montat în /var/www/html;
- fișierul nginx/default.conf este montat în /etc/nginx/conf.d/default.conf;
- portul 80 al containerului este expus pe portul 80 al calculatorului gazdei;
- funcționează în rețeaua internal.

![image](https://github.com/user-attachments/assets/c802c208-ee7d-4cd9-a06a-3b2ecb3e9e5f)

Pentru a testa funcționarea site-ului, deschideți site-ul în browser, trecând la adresa http://localhost. Dacă este afișată pagina de pornire nginx, atunci reîncărcați pagina.









