# Grafana'yı Nginx arkasında çalıştırmak

# v2
"iframe.html" dosyası içerisin verilen JWT ve "grafana.ini" içerisindeki ayarlarla URL'den giriş yapılabildi. Iframe kullanarak Grafana'ya giriş yapılabiliyor ve mevcut sayfa yenilendiği takdirde takdirde giriş ekranına atmak yerine Grafana'ya giriş yapıldığında kullanıcıyı karşılayan ana sayfaya yönlendiriyor. Burada istediğimiz, kullanıcı hangi sayfada sayfayı yeniliyor ise, o sayfanın kalıcı olması, herhangi bir farklı sayfaya yönlendirme yapmaması. 

## JWT Token ile Kullanıcı Girişi Yapmak
`grafana/grafana.ini` Dosyasındaki ayarlar ile kullanıcı girişini JWT üzerinden sağlamak istiyoruz.

### JWT Token Oluşturmak
jwt.io sayfasında aşağıdaki şekilde bir token oluşturup girişi URL üzerinden deniyoruz:
![alt text](.vscode/images/jwt-io-token.png)

```
eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VybmFtZSI6ImNlbS50b3BrYXlhIiwiZW1haWwiOiJjZW0udG9wa2F5YUB1bGFrLmNvbS50ciIsImlhdCI6MTUxNjIzOTAyMn0.K5kppSQuVrgice7ACi1E_KzflG6P1Ihz7G1wIpwl7kwXdqtvWlLQHWZ6rcif2XlspzhKVkoZBJ_G5AA3GGka33wdstm9l4G9ffnk766IUYMJKxUmLiU27LEW_EVJ9ttg4IDXxiypoizRfUxaE1dZnsr8uvhHGCLFwVhYFqnqX-clrY0D6ej_tamrzrbdCujA7rGmh6C2M1v4isaNe7iYfd2s932InQ2hTh6pqsfFh7XCw8Ihqt4KVPGTEOHpQj5tWVKk5URpcCCV8jtOm8OXeQy1u4lAbBUA2gHxOqVfbtELBF1JKy7BVwbq7nxFV88zP9Jrr0zmc3QPu3uAWw-BRoTlPqwJrHey9tMHr5e_BqnQ_UrRIATgUs_Td_uJAq0JXZszDqPGpDg-pRrt1Wkt61FzS50pfrMXF-seeQLpiReNjy1dyjC3VxqMgVcaxbDAmXk42KYO2Le333Zt_tGexS7JmL_-TW-bxJFeJXe7vgEQGQYZ04_7GdBIzQGORdhgM8opsppSmO0TL_W9_9vMvifoxnsQpFblCaHydj4tTetWvzkL-L4_yYNK_qqnrOksTmd8539ZbpMrFx5RyORrwtgAMoZn5Q-XeSgoSSFEYw3LbDUXoPn6CXQ7bfVFvhxWfw-_9V01F0zsy6VcW-_WJ3nCU6ffe43v6cQtDES3Flw
```

Oluşan token ile URL adresinden giriş sağlamayı bekliyoruz:
```
http://localhost/?auth_token=eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9......
```

# v1

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