FROM nginx

ADD nginx.conf /etc/nginx/
ADD default.conf /etc/nginx/conf.d/
ADD upstream.conf /etc/nginx/conf.d/

RUN usermod -u 1000 www-data

#COPY ssl/server.crt /etc/nginx/certs/server.crt
#COPY ssl/server.key /etc/nginx/certs/server.key

RUN mkdir /app

CMD ["nginx"]

EXPOSE 80 443