
# 🐾 Spring PetClinic with Postgres, Prometheus & Grafana

This project demonstrates running the **Spring PetClinic** application with a **Postgres database**, monitored by **Prometheus** and **Grafana**, all orchestrated using **Docker Compose**.

---

## 📚 Table of Contents
1. [Overview](#-overview)  
2. [Architecture](#-architecture)  
3. [Docker Compose Setup](#-docker-compose-setup)  
4. [Services](#-services)  
5. [Accessing the Apps](#-accessing-the-apps)  
6. [Screenshots](#-screenshots)  
7. [Summary](#-summary)  

---

## 🔎 Overview
- **PetClinic** → Sample Spring Boot app connected to Postgres, with Actuator + Prometheus metrics enabled.  
- **Postgres** → Database backend for PetClinic.  
- **Prometheus** → Scrapes PetClinic metrics exposed at `/actuator/prometheus`.  
- **Grafana** → Visualizes metrics and provides JVM + HTTP monitoring dashboards.  

---

## 🏗️ Architecture
```mermaid
graph TD
  A[Spring PetClinic] -->|JDBC| B[(Postgres DB)]
  A -->|/actuator/prometheus| C[Prometheus]
  C --> D[Grafana Dashboards]
````

---

## 📦 Docker Compose Setup

Here’s the `docker-compose.yml` file:

```yaml
services:
  petclinic:
    image: spring-petclinic:latest
    container_name: petclinic
    ports:
      - "8081:8080"
    environment:
      - SPRING_PROFILES_ACTIVE=postgres
      - MANAGEMENT_ENDPOINTS_WEB_EXPOSURE_INCLUDE=*
      - MANAGEMENT_ENDPOINT_PROMETHEUS_ENABLED=true
      - MANAGEMENT_METRICS_EXPORT_PROMETHEUS_ENABLED=true
      - SPRING_DATASOURCE_URL=jdbc:postgresql://postgres:5432/petclinic
      - SPRING_DATASOURCE_USERNAME=petclinic
      - SPRING_DATASOURCE_PASSWORD=petclinic
    depends_on:
      - postgres

  postgres:
    image: postgres:17.5
    container_name: postgres
    environment:
      POSTGRES_USER: petclinic
      POSTGRES_PASSWORD: petclinic
      POSTGRES_DB: petclinic
    ports:
      - "5432:5432"

  prometheus:
    image: prom/prometheus:latest
    container_name: prometheus
    ports:
      - "9090:9090"
    volumes:
      - ./prometheus.yml:/etc/prometheus/prometheus.yml
    depends_on:
      - petclinic

  grafana:
    image: grafana/grafana:latest
    container_name: grafana
    ports:
      - "3000:3000"
    environment:
      - GF_SECURITY_ADMIN_USER=admin
      - GF_SECURITY_ADMIN_PASSWORD=admin
    depends_on:
      - prometheus
```

---

## ⚙️ Services

| Service        | Port | Description                               |
| -------------- | ---- | ----------------------------------------- |
| **PetClinic**  | 8081 | Spring Boot app exposing Actuator metrics |
| **Postgres**   | 5432 | Database for PetClinic                    |
| **Prometheus** | 9090 | Scrapes metrics from PetClinic            |
| **Grafana**    | 3000 | Visualizes metrics via dashboards         |

---

## 🚀 Running the Stack

1. Start the services:

   ```bash
   docker-compose up -d
   ```

2. Access the apps:

   * PetClinic → [http://localhost:8081](http://localhost:8081)
   * Prometheus → [http://localhost:9090](http://localhost:9090)
   * Grafana → [http://localhost:3000](http://localhost:3000) *(user: `admin`, pass: `admin`)*

---

## 📸 Screenshots

### 🐶 PetClinic

![Spring PetClinic UI](spring.png)

### 📊 Grafana Dashboards

![Grafana JVM Dashboard](dashboard1.png)
![Grafana JVM Memory](dashboard2.jpg)

### ⚙️ Actuator Metrics

![Spring Actuator](actuator.png)

### 🎯 Prometheus Target

![Prometheus Target](target.png)

### 🔍 Prometheus Query

![Prometheus Query](query.png)

---

## ✅ Summary

This setup includes:

* **PetClinic** app (with Postgres backend).
* **Prometheus** scraping metrics from `/actuator/prometheus`.
* **Grafana** dashboards to visualize application & JVM metrics.

👉 Ideal for learning **Spring Boot monitoring** with Docker, Prometheus & Grafana.

تحب أزود كمان مثال لـ **prometheus.yml** جوه الـ README بحيث يبقى كل حاجة self-contained ولا تخليه ملف منفصل؟
```
