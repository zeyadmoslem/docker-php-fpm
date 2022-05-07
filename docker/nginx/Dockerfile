FROM nginx:stable-alpine


WORKDIR /var/www/src

# change src directory permissions
RUN chmod a+rwx -R /var/www

ADD ./config/default.conf /etc/nginx/conf.d/default.conf
ADD ./certs /etc/nginx/certs/self-signed
