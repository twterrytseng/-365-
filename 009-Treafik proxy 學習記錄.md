📝 Traefik Docker 設定重點整理

## 靜態配置
Treafik.yml  註意 port , command 這2個區塊


services:
  traefik:
    image: traefik:v3.6
    container_name: traefik
    restart: unless-stopped
    ports:
      - "80:80"       # HTTP 入口點
      - "8080:8080"   # 儀表板
      - "17000:17000"
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock:ro
    command:
      - --entrypoints.web.address=:80
      - --entrypoints.custom_port.address=:17000
      - --api.insecure=true
      - --api.dashboard=true
      - --providers.docker=true
      - --providers.docker.exposedbydefault=false
      - --providers.docker.network=webproxy  # 告訴 Traefik 只在這個網絡中尋找服務
    networks:
      - webproxy

networks:
  webproxy:
    external: true


## 動態配置
應用程式   web-compose.yml 使用 docker lable 設定

services:
  # ----------------------------------------------------
  # 服務一：web-1 (內部端口 9999, Host: web1.local)
  # ----------------------------------------------------
  web-1:
    image: traefik/whoami # 使用 Traefik 官方測試鏡像，默認監聽 80 端口。
    container_name: web-1
    # 由於 whoami 默認監聽 80，為了模擬您的 9999 需求，我們需要傳入參數
    command: ["--port", "9999"]

    labels:
      - traefik.enable=true
      # Router for web-1
      - traefik.http.routers.web-1-router.entrypoints=custom_port
      - traefik.http.routers.web-1-router.rule=Host(`web1.local`)
      # Service for web-1: **重點在這裡！** 指定容器內部監聽的 9999 端口
      - traefik.http.services.web-1-service.loadbalancer.server.port=9999

    networks:
      - webproxy # 只連接到這個單一的網路

  # ----------------------------------------------------
  # 服務二：web-2 (內部端口 10000, Host: web2.local)
  # ----------------------------------------------------
  web-2:
    image: traefik/whoami
    container_name: web-2
    # 為了模擬您的 10000 需求，我們傳入參數
    command: ["--port", "10000"]

    labels:
      - traefik.enable=true
      # Router for web-2
      - traefik.http.routers.web-2-router.entrypoints=web
      - traefik.http.routers.web-2-router.rule=Host(`web2.local`)
      # Service for web-2: **重點在這裡！** 指定容器內部監聽的 10000 端口
      - traefik.http.services.web-2-service.loadbalancer.server.port=10000

    networks:
      - webproxy # 只連接到這個單一的網路

networks:
  webproxy:
    external: true
---

網路與多服務部署最佳實踐

    單一網路原則： Traefik 和所有被代理的應用程式容器必須連接到同一個 Docker 網路，Traefik 才能「看見」它們。

        在 Traefik 靜態配置中，使用 --providers.docker.network=webproxy 指定 Traefik 只在該網絡中搜尋服務。

    最佳網路實踐 (外部網路)：

        方法： 提前手動創建網路 (docker network create webproxy)。

        配置： 在所有相關的 docker-compose.yml 檔案中使用 external: true 標記網路。

        優勢： 實現服務解耦，防止 docker compose down 誤刪共享網路。

    多網路架構 (安全考量)：

        用途： 為了安全，將服務同時連接到 webproxy (外部) 和 internal_db_net (內部) 兩個網路。

        優勢： 確保外部流量只能到達 Web 服務，無法直接接觸資料庫等後端服務。
