📝 Traefik Docker 設定重點整理

概念,說明,配置方式
靜態配置,  Traefik 啟動時的基礎設定。
  定義監聽端口、啟用 Provider 等。
    traefik-compose.yml 檔案中的 command 和 ports 區塊。
動態配置,
  應用程式的路由規則。
  Traefik 在運行中自動發現並更新。
    應用程式 docker-compose.yml 檔案中的 Docker Labels。
Entrypoint (入口點),
  Traefik 監聽流量的端口（例如：web 監聽 80，websecure 監聽 443，或自定義的 custom_port 監聽 17000）。,
    靜態配置 (--entrypoints.web.address=:80)。
Router (路由器),
  根據規則（例如域名 Host 或路徑 Path）將流量從 Entrypoint 導向 Service。,
    動態配置 (traefik.http.routers.name.rule=Host(...))。
Service (服務),
  Traefik 如何連接到實際運行的應用程式容器（需要知道容器內部的端口）。,
    動態配置 (traefik.http.services.name.loadbalancer.server.port=9999)。

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
