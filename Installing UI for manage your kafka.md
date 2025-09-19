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
