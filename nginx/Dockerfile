FROM nginx:latest


WORKDIR /etc/nginx/conf.d

RUN rm default.conf

COPY ./nginx.conf .    

ENTRYPOINT ["nginx", "-g" , "daemon off;"]
