# 🚪 ms-api-gateway

Единая точка входа в систему из 4 микросервисов:

- ms-warehouse  (по умолчанию `http://localhost:8081`)
- ms-delivery   (по умолчанию `http://localhost:8082`)
- ms-customer   (по умолчанию `http://localhost:8083`)
- ms-payment-service (по умолчанию `http://localhost:8084`)

Gateway поднимается на `http://localhost:8080` и проксирует **все ручки** каждого сервиса под отдельным префиксом.

---

## ✅ Как пользоваться (локально, Windows)

1) Запусти все 4 микросервиса как обычно (каждый на своём порту).

2) Запусти gateway:

```bat
.\gradlew clean bootRun
```

---

## 🧭 Маршрутизация (все ручки доступны)

| Сервис | Было | Стало через gateway |
|------:|------|---------------------|
| warehouse | `http://localhost:8081/**` | `http://localhost:8080/warehouse/**` |
| delivery  | `http://localhost:8082/**` | `http://localhost:8080/delivery/**` |
| customer  | `http://localhost:8083/**` | `http://localhost:8080/customer/**` |
| payment   | `http://localhost:8084/**` | `http://localhost:8080/payment/**` |

### Swagger каждого сервиса через gateway

- Warehouse: `http://localhost:8080/warehouse/swagger-ui/index.html`
- Delivery:  `http://localhost:8080/delivery/swagger-ui/index.html`
- Customer:  `http://localhost:8080/customer/swagger-ui/index.html`
- Payment:   `http://localhost:8080/payment/swagger-ui/index.html`

---

## 🧾 Журналирование “из коробки”

Gateway добавляет и прокидывает заголовок `X-Request-Id`:

- если клиент прислал — прокидываем дальше;
- если не прислал — генерируем UUID;
- всегда возвращаем его клиенту в ответе.

Плюс пишет access-log на каждый запрос:

```
requestId=... method=... path=... status=... durationMs=...
```

---

## 📈 Actuator / метрики

- Health: `http://localhost:8080/actuator/health`
- Prometheus: `http://localhost:8080/actuator/prometheus`

---

## ⚙️ Переменные окружения (если нужно переопределить)

| Ключ | По умолчанию |
|-----|--------------|
| `SERVER_PORT` | `8080` |
| `WAREHOUSE_URL` | `http://localhost:8081` |
| `DELIVERY_URL`  | `http://localhost:8082` |
| `CUSTOMER_URL`  | `http://localhost:8083` |
| `PAYMENT_URL`   | `http://localhost:8084` |

---

## 🐳 Docker (опционально)

Если хочешь запускать gateway в Docker, а сервисы — локально (на хосте), можно использовать `host.docker.internal`:

```yaml
WAREHOUSE_URL: http://host.docker.internal:8081
DELIVERY_URL:  http://host.docker.internal:8082
CUSTOMER_URL:  http://host.docker.internal:8083
PAYMENT_URL:   http://host.docker.internal:8084
```
