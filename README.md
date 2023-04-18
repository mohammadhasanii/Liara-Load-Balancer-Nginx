
# Liara-Load-Balancer-Nginx

This container allows you to distribute all your requests in Liara.ir internal network between 2 copies of the application container on your server side




![Logo](https://www.f5.com/content/dam/f5-com/page-assets-en/home-en/products/nginx/2020/nginx-product-icon-open-source_1100x481.jpg)


## Usage/Examples
# nginx.conf
```conf

user nginx;
worker_processes auto;

pid /tmp/nginx.pid;

events {

	worker_connections 1024;
}

http {

	client_body_temp_path /tmp/client_temp;
	proxy_temp_path /tmp/proxy_temp_path;
	fastcgi_temp_path /tmp/fastcgi_temp;
	uwsgi_temp_path /tmp/uwsgi_temp;
	scgi_temp_path /tmp/scgi_temp;

	include /etc/nginx/mime.types;
	default_type application/octet-stream;

	log_format main '$request_method $status $http_x_forwarded_for "$request_uri" "$http_referer" "$http_user_agent"';
      log_format alogformat "$time_local $scheme://$host:$server_port$request_uri";
	server_tokens off;
	sendfile on;
	keepalive_timeout 65;
	gzip on;
	access_log /dev/stdout;
	error_log stderr;



#All the backend servers that are deployed in Liara.ir, just enter the application name along with the port
#Just change this part
  upstream backend {

    server backend1:5000;
    server backend2:5000;
  }

	server {


		resolver 127.0.0.11 ipv6=off valid=5s;
		listen 80;
		location / {
                  add_header X-debug-message "A static file was served" always;
			proxy_set_header Host $host;
			proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
			proxy_pass http://backend;
		}
	}
}



}

```

# Dockerfile
```conf

FROM nginx

COPY nginx.conf /etc/nginx/nginx.conf


```




## Deploy to Liara.ir



```bash
  liara deploy --platform=docker --port=80
```
    