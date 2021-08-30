FROM nginx:latest


WORKDIR /etc/nginx

RUN rm nginx.conf

COPY ./nginx.conf .    

ENTRYPOINT ["nginx", "-g" , "daemon off;"]
