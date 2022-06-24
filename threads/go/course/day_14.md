# Golang - Day 14

## Deploy Golang Service

Docker
- Khi chạy máy vật lí thì ko thể chạy 1 service để máy lên 100%CPU được vì có giới hạn kernel -> cần chạy ảo hóa.
- Container - công nghệ ảo hóa của Linux, ko phải thuộc về Docker
- Có thể chia sẻ hệ điều hành
- Ảo hóa ở tầng Application
- Ảo hóa cả network, storage, isolate app
- Docker khi cài đặt -> có DOCKER_HOST + Client (cli)
- Image alpine
- Entrypoint = điểm bắt đầu khi run docker run
- Build Golang: CGO_ENABLED=0 GOOS=linux go build -a -installsuffix cgo -o app
- Các options chạy docker run:
    - -d: chạy detach
    - —name
    - -e key=value: gắn biến môi trường
- Docker network
- Run nginx-proxy:
```bash
docker run -d -p 80:80 -p 443:443  --network my-net  --name nginx-proxy  -e ENABLE_IPV6=true  --privileged=true  -v ~/nginx/vhost.d:/etc/nginx/vhost.d  -v ~/nginx-certs:/etc/nginx/certs:ro  -v ~/nginx-conf:/etc/nginx/conf.d  -v ~/nginx-logs:/var/log/nginx  -v /usr/share/nginx/html  -v /var/run/docker.sock:/tmp/docker.sock:ro  --label nginx_proxy  jwilder/nginx-proxy
```
- Run nginx-let-encrypts
```bash
docker run -d --network my-net -v ~/nginx/vhost.d:/etc/nginx/vhost.d -v ~/nginx-certs:/etc/nginx/certs:rw -v /var/run/docker.sock:/var/run/docker.sock:ro --volumes-from nginx-proxy --privileged=true jrcs/letsencrypt-nginx-proxy-companion
```

Namecheap - digital ocean - cloudflare

Với micro services thì xài sidecar proxy cho mỗi service

Multi stage build
