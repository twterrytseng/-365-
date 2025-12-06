##  video   https://www.youtube.com/watch?v=CV3pi8KQEy0  ;   https://www.youtube.com/watch?v=KWJQoDZLSWY ##

Traefik 路由概念流程圖

Request ->  Entrypoint/Port -> Middleware/Rules -> Route/Rules -> Service/Internet Port -> Application/Container


. Entrypoint (入口點) - 交通的起點

    定義： 外部流量進入 Traefik 代理的第一個端口。

    角色： 告訴 Traefik 應該監聽哪些主機端口。

    範例： 監聽 Web 流量的 80 端口，或監聽安全 Web 流量的 443 端口。

    配置方式： 屬於靜態配置（Traefik 啟動時就要知道）。

2. Router (路由器) - 交通的指揮官

    定義： 根據請求的內容（規則）決定將流量導向哪個服務。

    角色： 匹配傳入請求的條件。

    關鍵規則：

        Host 規則： 根據域名判斷（例如 Host(\app1.local`)`）。

        Path 規則： 根據 URL 路徑判斷（例如 PathPrefix(\/api`)`）。

    配置方式： 主要透過動態配置（Docker Labels）。

3. Service (服務) - 交通的終點

    定義： 實際接收請求的應用程式容器。

    角色： 告訴 Traefik 如何連接到容器內部的 IP 和端口。

    重點： 必須指定容器內部的端口（例如 8000、9999），而不是主機暴露的端口。

    配置方式： 主要透過動態配置（Docker Labels）。

4. Middleware (中間件) - 交通的檢查站

    定義： 在請求到達服務之前或之後，對請求或響應進行操作。

    角色： 實現額外的功能，例如安全、重定向、限流。

    常見範例：

        Redirect： 將 HTTP 自動跳轉到 HTTPS。

        BasicAuth： 要求用戶名和密碼驗證。

    配置方式： 主要透過動態配置（Docker Labels），然後將中間件附加到 Router 上。

