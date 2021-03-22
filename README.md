# MIchaelangelo-Yash-Assignment3-4
Fixing PHP Blog
Below is the new site.conf file for changing to https
server {
	listen 80 default_server;
	listen [::]:80 default_server;

	listen 443 ssl http2 default_server;	
	listen [::]:443 ssl http2 default_server;

	ssl_certificate /certs/localhost.crt;
	ssl_certificate_key /certs/localhost.key;	

	root /code;

	index index.php; 

	server_name 0.0.0.0;
	charset utf-8;

	location ~ \.php$ {
		try_files $uri =404;
		fastcgi_split_path_info ^(.+\.php)(/.+)$;
		fastcgi_pass php:9000;
		fastcgi_index index.php;
		include fastcgi_params;
		fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
		fastcgi_param PATH_INFO $fastcgi_path_info;
	}
}




Below is the new docker-compose.yml file

version: '2'

services:

  nginx:
    image: nginx:latest
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - ./code:/code
      - ./site.conf:/etc/nginx/conf.d/site.conf
      - ./certs/localhost.crt:/certs/localhost.crt
      - ./certs/localhost.key:/certs/localhost.key
    links:
      - php

  php:
    build: 
      context: ./dockerfiles/php/
      dockerfile: ./Dockerfile
    volumes:
        - ./code:/code

    links:
      - db

  db:
    image: postgres:latest
    restart: always
    ports:
      - "5432:5432"
    volumes:
      - ./init-db.sh:/docker-entrypoint-initdb.d/init-db.sh
      - ./schema.sql:/schema.sql
      - ./data.sql:/data.sql
    environment:
      POSTGRES_PASSWORD: assignment1dbadmin

  adminer:
    image: adminer
    restart: always
    ports:
      - "8081:8080"



