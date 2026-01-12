docker run -it -d -p 8080:8080 -e DYNAMIC_CONFIG_ENABLED=true provectuslabs/kafka-ui

- open on your browser [http://<your-ip>:8080/]
- Click configure new cluster
- add your cluster like this

docker-compose.yaml
```
version: '3.3'

services:
  kafka-ui:
    ports:
      - "8080:8080"
    environment:
      - DYNAMIC_CONFIG_ENABLED=true
    image: provectuslabs/kafka-ui
    extra_hosts:
      - "kafka1:10.30.14.33"
      - "kafka2:10.30.14.34"
      - "kafka3:10.30.14.35"
```

có authe
```
version: '3.3'

services:
  kafka-ui:
    ports:
      - "8080:8080"
    environment:
      - DYNAMIC_CONFIG_ENABLED=true
      - AUTH_TYPE=LOGIN_FORM
      - SPRING_SECURITY_USER_NAME=admin
      - SPRING_SECURITY_USER_PASSWORD=secret

    image: provectuslabs/kafka-ui

    extra_hosts:
      - "kafka1:10.30.9.31"
      - "kafka2:10.30.9.32"
      - "kafka3:10.30.9.33"
```

Chỉnh end point
```
version: '3.3'

services:
  kafka-ui:
    ports:
      - "8080:8080"
    environment:
      - DYNAMIC_CONFIG_ENABLED=true
      - AUTH_TYPE=LOGIN_FORM
      - SPRING_SECURITY_USER_NAME=admin
      - SPRING_SECURITY_USER_PASSWORD=admin
      - SERVER_SERVLET_CONTEXT_PATH=/kafka-ui
    image: provectuslabs/kafka-ui

    extra_hosts:
      - "kafka1:10.30.9.31"
      - "kafka2:10.30.9.32"
      - "kafka3:10.30.9.33"

```

Thêm volumn
```
version: '3.3'

services:
  kafka-ui:
    ports:
      - "8083:8080"
    environment:
      - DYNAMIC_CONFIG_ENABLED=true
      - AUTH_TYPE=LOGIN_FORM
      - SPRING_SECURITY_USER_NAME=admin
      - SPRING_SECURITY_USER_PASSWORD=admin
      - SERVER_SERVLET_CONTEXT_PATH=/kafka-ui
    image: provectuslabs/kafka-ui
    volumes:
      - /opt/kafka-ui:/logs
    extra_hosts:
      - "kafka1:10.0.12.134"
      - "kafka2:10.0.12.135"
      - "kafka3:10.0.12.136"
      - "kafka4:10.0.12.137"
```

Nginx
```
server {
    if ($host = edu-monitor.xxx.vn) {
        return 301 https://$host$request_uri;
    } 


    listen 80;
    server_name edu-monitor.xxx.vn;
    return 404; # managed by Certbot
}

server {
  listen 443 ssl;
  server_name edu-monitor.xxx.vn;
  ssl_certificate /etc/nginx/ssl/xxx/xxx.cer; 
  ssl_certificate_key /etc/nginx/ssl/xxx/private.key;
  ssl_protocols TLSv1.2 TLSv1.3;
  access_log  /opt/nginx/logs/edu-monitor_access.log;
  error_log  /opt/nginx/logs/edu-monitor_error.log;

  location /kafka-ui {
    proxy_pass http://xxx:8083;
    proxy_set_header   Host $host;
    proxy_http_version 1.1;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection "upgrade";
    proxy_set_header   X-Real-IP $remote_addr;
    proxy_set_header   X-Forwarded-Host $host;
    proxy_set_header   X-Forwarded-For $remote_addr;
  }
}
```
