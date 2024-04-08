# Grafana'yı Nginx arkasında çalıştırmak
Bu haliyle proxy olarak çalışan Nginx'in arkasında Grafana çalıştı

`nginx/default.conf`
```
server {
  listen 80;
  server_name     localhost;

  location / {
    proxy_set_header Host $host;
    proxy_pass http://grafana;
  }
}

upstream grafana {
  server grafana:3000;
}
```

```dockerfile
volumes:
  prometheus_data: {}
  grafana_data: {}
  postgres_data: {}

services:
  nginx:
    container_name: ng
    image: docker.io/nginx:mainline
    volumes:
      - ./nginx:/etc/nginx/conf.d
    ports:
      - 80:80

  grafana:
    container_name: grafana
    image: docker.io/grafana/grafana:latest
    restart: unless-stopped
    volumes:
      - grafana_data:/var/lib/grafana
    ports:
      - 3090:3000
```