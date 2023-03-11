# REFERENCE

- [https://github.com/docker/awesome-compose/tree/master/fastapi](https://github.com/docker/awesome-compose/tree/master/fastapi)
- [Special-Problems-in-ComNet'22-CPE-RMUTT](https://youtube.com/playlist?list=PLJz1XVERx6ACV-vTC6eG7HSMdBUR0dZId)
- [https://github.com/nantawatCharoenrat](https://github.com/nantawatCharoenrat/swarm02/blob/master/README.md)

# SWARM-DEPLOY-FASTAPI-SWARM02-SPCN17

[ruangrit17swarm02-fastapi.xops.ipv9.me/](https://ruangrit17swarm02-fastapi.xops.ipv9.me/)

# WAKATIME-SWARM02-SPCN17
[WAKATIME](https://wakatime.com/@spcn17/projects/zxmyimmgjb?start=2023-03-03&end=2023-03-09)

# Setup-Linux
- คำสั่งที่ใช้ในการ อัพเดทและอัพเกรดแพ็คเกจ
```
sudo apt update; sudo apt upgrade -y
```
- Set timezone
```
sudo time datectl set-timezone Asia/Bangkok
```
 - คำสั่งที่ใช้ในการ Set ชื่อ Hostname
```
sudo hostnamectl set-hostname **ชื่อที่ต้องการจะตั้ง**
```
- คำสั่งที่ใช้ในการเปลี่ยน Machine-ID 
```
rm /var/lib/dbus/machine-id
echo -n > /etc/machine-id
cat /etc/machine-id
ln -s /etc/machine-id /var/lib/dbus/machine-id
```

# INSTALL-DOCKER
- คำสั่งที่ใช้ในการ Install Docker
```
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```
- คำสั่งตรวจสอบเวอร์ชั่น Docker
```
sudo docker version
```
- คำสั่งตรวจสอบการใช้งาน Docker
```
sudo docker run hello-world
```
# BUILD-IMAGE & TAG
- คำสั่งการ Build image
```
sudo docker compose "django/compose.yaml" up -d --build
```
- คำสั่งการ Tag
```
sudo docker tag SOURCE_IMAGE[:TAG] TARGET_IMAGE[:TAG]
```

# PUSH IMAGE TO DOCKER HUB 
- คำสั่งเข้าสู่ระบบ Docker ใน VSCODE
```
docker login
```
- คำสั่ง Push Image To Docker Hub
```
docker push TARGET_IMAGE[:TAG]
```

# CREATE STACK DEPLOY
- สร้างไฟล์ docker-compose.yaml
```
version: '3.7'
services:
  api:
    image: TARGET_IMAGE[:TAG]
    networks:
     - webproxy
    ports:
     - "8808:8000"
    environment:
     PORT: 8000
    logging:
      driver: json-file
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - app:/app
    deploy:
      replicas: 1

volumes:
  app:          
networks:
  webproxy:
    external: true
```
- นำ docker-compose.yaml ไป Stack Deploy on local

# SWARM CLUSTER
- สร้างไฟล์ docker-compose-RevProxy.yaml
```
version: '3.7'
services:
  api:
    image: TARGET_IMAGE[:TAG]
    networks:
     - webproxy
    environment:
     PORT: 8000
    logging:
      driver: json-file
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - app:/app
    deploy:
      replicas: 1
      labels:
        - traefik.docker.network=webproxy
        - traefik.enable=true
        - traefik.http.routers.${APPNAME}-https.entrypoints=websecure
        - traefik.http.routers.${APPNAME}-https.rule=Host("${APPNAME} URL ")
        - traefik.http.routers.${APPNAME}-https.tls.certresolver=default
        - traefik.http.services.${APPNAME}.loadbalancer.server.port=8000

volumes:
  app:          
networks:
  webproxy:
    external: true
```

![image](https://user-images.githubusercontent.com/119097836/224471620-83025224-b541-43d1-ba6b-c2dbffffad9d.png)
