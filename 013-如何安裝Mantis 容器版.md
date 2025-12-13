目錄架構
 - mantisbt
   - docker-compose.yml
   - .env
   - mantis_files
     - config
       - config 相關
     - custom
    

image 來源  https://github.com/xlrl/docker-mantisbt

Browser
  |
  | HTTPS (443)
  v
Traefik (Reverse Proxy)
  |
  v
MantisBT Container
  |
  v
MariaDB Container


#version: "3.8"

services:
  mantisbt:
    image: xlrl/mantisbt:2.27.3
    container_name: mantisbt
    restart: unless-stopped
    environment:
      MANTIS_TIMEZONE: Asia/Taipei
      MANTIS_ENABLE_ADMIN: 0   ## 設定 1  才能進入安裝畫面 ; 完成後改成 0 關閉安裝設定
      MANTIS_DATABASE_HOST: db
      MANTIS_DATABASE_NAME: ${MANTIS_DB_NAME}
      MANTIS_DATABASE_USER: ${MANTIS_DB_USER}
      MANTIS_DATABASE_PASSWORD: ${MANTIS_DB_PASSWORD}
    volumes:
      - ./mantis_files/config:/var/www/html/config
      - ./mantis_files/custom:/var/www/html/custom
    labels:
      - "traefik.enable=true"
      - "traefik.docker.network=webproxy"

      - "traefik.http.routers.mantis.rule=Host(`${MANTIS_DOMAIN}`)"
      - "traefik.http.routers.mantis.entrypoints=websecure"
      - "traefik.http.routers.mantis.tls=true"
      - "traefik.http.routers.mantis.tls.certresolver=myresolver"

      - "traefik.http.services.mantis.loadbalancer.server.port=80"
    networks:
      - webproxy
      - internal
    depends_on:
      - db

  db:
    image: mariadb:10.11
    container_name: mantisbt-db
    restart: unless-stopped
    environment:
      MYSQL_DATABASE: ${MANTIS_DB_NAME}
      MYSQL_USER: ${MANTIS_DB_USER}
      MYSQL_PASSWORD: ${MANTIS_DB_PASSWORD}
      MYSQL_ROOT_PASSWORD: ${MARIADB_ROOT_PASSWORD}
    volumes:
      - db_data:/var/lib/mysql
    networks:
      - internal

volumes:
  mantis_data:
  db_data:

networks:
  webproxy:
    external: true
  internal:
    driver: bridge

  internal:
    driver: bridge
