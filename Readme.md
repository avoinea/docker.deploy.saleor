# Saleor eCommerce Platform Docker Orchestration

[Saleor](https://getsaleor.com/) - A GRAPHQL-FIRST ECOMMERCE PLATFORM FOR PERFECTIONISTS

## Install

    $ git clone https://github.com/avoinea/docker.deploy.saleor mystore.com
    $ cd mystore.com
    $ cp .env.example .env
    $ vim .env

## SSL certificates

* Obtain your SSL certificates from letsencrypt

      $ docker run --rm \
                   -p 80:80 \
                   -p 443:443 \
                   -v certs:/etc/letsencrypt \
               certbot/certbot certonly

* If you already have SSL certificates

      $ docker run -it --rm -v certs:/etc/letsencrypt -v /path/my/certs:/backup alpine sh
      $ mkdir -p /etc/letsencrypt/live/www.myblog.com/
      $ cd /backup
      $ cp cert.pem privkey.pem fullchain.pem /etc/letsencrypt/live/www.mystore.com/
      $ exit

## Run

### Start

    $ docker-compose pull
    $ docker-compose up -d

### First run

* Setup PostgreSQL

      $ docker-compose exec db gosu postgres psql -d saleor -c "ALTER USER saleor WITH SUPERUSER"
      $ docker-compose exec saleor python manage.py migrate

* Setup ElasticSearch

      $ docker-compose exec saleor python manage.py search_index --rebuild

* Create admin user

      $ docker-compose exec saleor python manage.py createsuperuser

## Start selling your products

    https://www.mystore.com/

## Renew SSL certificates

### First run:

      $ docker-compose stop
      $ docker run --name=letsentrypt -p 80:80 -p 443:443 -v certs:/etc/letsencrypt certbot/certbot renew
      $ docker-compose up -d

### Next run:

      $ docker-compose stop
      $ docker start letsencrypt
      $ docker-compose up -d
