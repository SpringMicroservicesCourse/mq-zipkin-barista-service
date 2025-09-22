# Spring Cloud 微服務鏈路追蹤系統 ⚡

[![Java](https://img.shields.io/badge/Java-21-orange.svg)](https://www.oracle.com/java/)
[![Spring Boot](https://img.shields.io/badge/Spring%20Boot-3.4.5-brightgreen.svg)](https://spring.io/projects/spring-boot)
[![Spring Cloud](https://img.shields.io/badge/Spring%20Cloud-2024.0.2-blue.svg)](https://spring.io/projects/spring-cloud)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

## 專案介紹

本專案是一個基於 Spring Cloud 的微服務鏈路追蹤系統，透過 Zipkin 和 RabbitMQ 實現分散式系統的請求追蹤與監控。系統模擬咖啡店訂單處理流程，包含客戶下單、服務員處理、咖啡師製作等完整業務流程。

> 💡 **為什麼選擇此專案？**
> - 完整的微服務架構實作範例
> - 整合 Zipkin 分散式追蹤系統
> - 使用 RabbitMQ 進行服務間通訊
> - 支援服務鏈路可視化監控

### 🎯 專案特色

- **分散式追蹤**：透過 Zipkin 實現完整的請求鏈路追蹤
- **訊息驅動架構**：使用 RabbitMQ 進行異步訊息處理
- **微服務架構**：包含客戶服務、服務員服務、咖啡師服務
- **可視化監控**：提供 Zipkin UI 進行鏈路分析
- **服務發現**：整合 Consul 進行服務註冊與發現

## 技術棧

### 核心框架
- **Spring Boot 3.4.5** - 微服務基礎框架
- **Spring Cloud 2024.0.2** - 微服務生態系統
- **Spring Cloud Stream** - 訊息驅動微服務
- **Spring Data JPA** - 資料持久層

### 追蹤與監控
- **Zipkin** - 分散式追蹤系統
- **Micrometer Tracing** - 追蹤指標收集
- **Brave** - Zipkin 客戶端實作

### 訊息與服務發現
- **RabbitMQ** - 訊息代理
- **Consul** - 服務發現與配置管理

### 資料庫
- **MariaDB** - 關聯式資料庫
- **Hibernate** - ORM 框架

### 開發工具與輔助
- **Lombok** - 減少樣板程式碼
- **Maven** - 專案建置管理
- **Docker** - 容器化部署

## 專案結構

```
Chapter 16 服務鏈路追蹤/
├── mq-zipkin-barista-service/          # 咖啡師服務
│   ├── src/main/java/
│   │   └── tw/fengqing/spring/springbucks/barista/
│   │       ├── BaristaServiceApplication.java
│   │       ├── integration/
│   │       │   ├── OrderListener.java      # 訂單處理監聽器
│   │       │   └── Waiter.java            # 訊息介面定義
│   │       ├── model/
│   │       │   ├── CoffeeOrder.java       # 咖啡訂單實體
│   │       │   └── OrderState.java        # 訂單狀態枚舉
│   │       └── repository/
│   │           └── CoffeeOrderRepository.java
│   ├── src/main/resources/
│   │   └── application.properties        # 服務配置
│   └── pom.xml
├── sleuth-customer-service/             # 客戶服務
│   ├── src/main/java/
│   │   └── tw/fengqing/spring/springbucks/customer/
│   │       ├── controller/
│   │       │   └── CustomerController.java
│   │       ├── integration/
│   │       │   └── NotificationListener.java  # 通知監聽器
│   │       ├── model/
│   │       ├── service/
│   │       └── CustomerServiceApplication.java
│   └── src/main/resources/
│       └── application.properties
├── sleuth-waiter-service/               # 服務員服務
│   ├── src/main/java/
│   │   └── tw/fengqing/spring/springbucks/waiter/
│   │       ├── controller/
│   │       │   ├── CoffeeController.java
│   │       │   └── CoffeeOrderController.java
│   │       ├── integration/
│   │       ├── model/
│   │       └── WaiterServiceApplication.java
│   └── src/main/resources/
│       └── application.properties
└── README.md
```

## 快速開始

### 前置需求
- Java 21 或以上版本
- Maven 3.6 或以上版本
- Docker 和 Docker Compose
- MariaDB 資料庫
- RabbitMQ 訊息代理

### 安裝與執行

1. **啟動基礎設施服務：**
```bash
# 啟動 RabbitMQ 和 Zipkin
docker run -d --name rabbitmq-spring-course \
  -p 5672:5672 -p 15672:15672 \
  -e RABBITMQ_DEFAULT_USER=spring \
  -e RABBITMQ_DEFAULT_PASS=spring \
  rabbitmq:4.1.4-management

docker run -d --name rabbit-zipkin-spring-course \
  -p 9411:9411 \
  -e RABBIT_ADDRESSES=rabbitmq:5672 \
  -e RABBIT_USER=spring \
  -e RABBIT_PASSWORD=spring \
  --link rabbitmq-spring-course:rabbitmq \
  openzipkin/zipkin:3-arm64
```

2. **設定資料庫：**
```sql
-- 建立資料庫
CREATE DATABASE springbucks;
CREATE USER 'springbucks'@'localhost' IDENTIFIED BY 'springbucks';
GRANT ALL PRIVILEGES ON springbucks.* TO 'springbucks'@'localhost';
```

3. **編譯專案：**
```bash
# 編譯咖啡師服務
cd mq-zipkin-barista-service
mvn clean compile

# 編譯客戶服務
cd ../sleuth-customer-service
mvn clean compile

# 編譯服務員服務
cd ../sleuth-waiter-service
mvn clean compile
```

4. **執行應用程式：**
```bash
# 啟動客戶服務 (端口 8090)
cd sleuth-customer-service
mvn spring-boot:run

# 啟動服務員服務 (端口 8080)
cd ../sleuth-waiter-service
mvn spring-boot:run

# 啟動咖啡師服務 (隨機端口)
cd ../mq-zipkin-barista-service
mvn spring-boot:run
```

## 進階說明

### 環境變數
```properties
# 資料庫設定
DB_URL=jdbc:mariadb://localhost:3306/springbucks
DB_USERNAME=springbucks
DB_PASSWORD=springbucks

# RabbitMQ 設定
RABBITMQ_HOST=localhost
RABBITMQ_PORT=5672
RABBITMQ_USERNAME=spring
RABBITMQ_PASSWORD=spring

# Zipkin 設定
ZIPKIN_ENDPOINT=http://localhost:9411/api/v2/spans
```

### 設定檔說明
```properties
# application.properties 主要設定

# 服務基本設定
spring.application.name=barista-service
server.port=0

# 追蹤設定
management.tracing.sampling.probability=1.0
management.zipkin.tracing.sender.type=rabbitmq

# 資料庫設定
spring.datasource.url=jdbc:mariadb://localhost:3306/springbucks
spring.datasource.username=springbucks
spring.datasource.password=springbucks

# RabbitMQ 設定
spring.rabbitmq.host=localhost
spring.rabbitmq.port=5672
spring.rabbitmq.username=spring
spring.rabbitmq.password=spring

# Spring Cloud Stream 函數式編程模型配置
spring.cloud.function.definition=newOrders
spring.cloud.stream.bindings.newOrders-in-0.destination=newOrders
spring.cloud.stream.bindings.finishedOrders-out-0.destination=finishedOrders
```

### 服務間通訊流程

1. **客戶下單**：客戶服務接收訂單請求
2. **服務員處理**：服務員服務處理訂單並發送訊息
3. **咖啡師製作**：咖啡師服務接收訊息並製作咖啡
4. **狀態更新**：完成後更新訂單狀態為 BREWED
5. **客戶通知**：客戶服務接收通知並更新狀態為 TAKEN

## API 端點

### 客戶服務 (端口 8090)
- `POST /order` - 建立新訂單
- `GET /order/{id}` - 查詢訂單詳情

### 服務員服務 (端口 8080)
- `POST /order` - 處理訂單
- `GET /order/{id}` - 查詢訂單狀態
- `GET /coffee` - 查詢咖啡選單

## 監控與追蹤

### Zipkin UI
- 訪問 `http://localhost:9411` 查看追蹤資訊
- 支援服務依賴圖分析
- 提供請求鏈路詳細資訊

### 重要日誌
```log
# 訂單處理日誌
INFO [barista-service] Order 1 is READY.
INFO [customer-service] Order 1 is READY, I'll take it.

# 追蹤日誌
INFO [customer-service,68d0c8688c35a5afcb2554688de7b05c,cb2554688de7b05c]
```

## 參考資源

- [Spring Cloud 官方文件](https://spring.io/projects/spring-cloud)
- [Zipkin 官方文件](https://zipkin.io/)
- [Spring Cloud Stream 文件](https://spring.io/projects/spring-cloud-stream)
- [RabbitMQ 官方文件](https://www.rabbitmq.com/documentation.html)

## 注意事項與最佳實踐

### ⚠️ 重要提醒

| 項目 | 說明 | 建議做法 |
|------|------|----------|
| 服務端口 | 避免端口衝突 | 使用不同端口或隨機端口 |
| 訊息路由 | 確保主題配置正確 | 統一使用 finishedOrders 主題 |
| 資料庫連線 | 使用連線池 | 配置 HikariCP 連線池 |
| 追蹤採樣 | 生產環境調整採樣率 | 設定適當的 sampling.probability |

### 🔒 最佳實踐指南

- **程式碼註解**：在重要的程式碼區塊添加清楚註解，方便團隊成員理解與維護
- **專業用語**：使用台灣常用的專業用語，確保溝通順暢且符合本地習慣
- **錯誤處理**：實作完整的錯誤處理機制
- **日誌記錄**：使用結構化日誌記錄重要操作
- **配置管理**：使用環境變數管理敏感資訊

### 🚨 常見問題

1. **訂單狀態停留在 BREWED**
   - 檢查 Customer Service 是否正在運行
   - 確認 RabbitMQ 連線正常
   - 驗證訊息路由配置

2. **服務啟動失敗**
   - 檢查端口是否被佔用
   - 確認資料庫連線設定
   - 驗證 RabbitMQ 服務狀態

3. **追蹤資訊不完整**
   - 確認 Zipkin 服務正常運行
   - 檢查追蹤採樣設定
   - 驗證服務間通訊

## 授權說明

本專案採用 MIT 授權條款，詳見 LICENSE 檔案。

## 關於我們

我們主要專注在敏捷專案管理、物聯網（IoT）應用開發和領域驅動設計（DDD）。喜歡把先進技術和實務經驗結合，打造好用又靈活的軟體解決方案。

## 聯繫我們

- **FB 粉絲頁**：[風清雲談 | Facebook](https://www.facebook.com/profile.php?id=61576838896062)
- **LinkedIn**：[linkedin.com/in/chu-kuo-lung](https://www.linkedin.com/in/chu-kuo-lung)
- **YouTube 頻道**：[雲談風清 - YouTube](https://www.youtube.com/channel/UCXDqLTdCMiCJ1j8xGRfwEig)
- **風清雲談 部落格**：[風清雲談](https://blog.fengqing.tw/)
- **電子郵件**：[fengqing.tw@gmail.com](mailto:fengqing.tw@gmail.com)

---

**📅 最後更新：2025-09-22**  
**👨‍💻 維護者：風清雲談團隊**
