## Nginx Server w/ Unicorn and Assets

#### Run Rails application container with Unicorn
```
$ docker run -d --name [container_name] [docker_image]
```

#### Run Nginx container with link to rails app
```
$ docker build -t nginx .
$ docker run -d -p 80:80 --link [container_name]:app --volumes-from [container_name] --name nginx nginx
```

#### Docker Hub
```
$ docker pull dangerous/nginx-unicorn
```

### nginx.conf
```nginx
http {
  upstream unicorn {
    server app:8080;
  }

  server {
    listen 80;

    location ~ ^/assets/ {
      root /var/www/app/public;
      gzip_static on;
      expires max;
      add_header Cache-Control public;
      include /etc/nginx/mime.types;
    }

    location / {
      proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
      proxy_set_header Host $http_host;
      proxy_redirect off;
      proxy_pass http://unicorn;
    }
  }
}

events {
  worker_connections 1024;
}
```
