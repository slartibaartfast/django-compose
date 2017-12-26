
Docker compose for django, django rest framework and mysql

If you build python from `python:3.5-alpine`, and if you want to install `uwsgi` and some scientific computering packages, like `numpy`/ `matplotlib` / `pandas`, you can write your Dockerfile like this:

```
# Version: 0.0.1
FROM python:3.5-alpine
MAINTAINER Xin Xiong "xiongxin20008@126.com"
ENV PYTHONUNBUFFERED 1
RUN set -e; \
        apk add --no-cache --virtual .build-deps \
                gcc \
                libc-dev \
                linux-headers \
                mariadb-dev \
                python3-dev \
                postgresql-dev \
                freetype-dev \
                libpng-dev \
                g++ \
        ;
RUN mkdir /djcode
WORKDIR /djcode
ENV REFRESHED_AT 2017-12-25
ADD requirements.txt /djcode/
RUN pip install --no-cache-dir -r /djcode/requirements.txt
RUN pip install uwsgi
ADD . /djcode/  # copy . to /djcode/
EXPOSE 6001
```

And you need to change `docker-compose.yml` file a little:

From
```
    ...
    command: bash -c "python check_db.py --service-name mysql --ip db --port 3306 &&
                      python manage.py migrate &&
                      python manage.py runserver 0.0.0.0:8000"
   ...
```

To

```
    ...
    command: /bin/sh -c "python check_db.py --service-name mysql --ip db --port 3306 &&
                      python manage.py migrate &&
                      python manage.py runserver 0.0.0.0:8000"
   ...
```
