# 🐾 Spring PetClinic with Postgres, Prometheus & Grafana

This project demonstrates running the **Spring PetClinic** application with a **Postgres database**, monitored by **Prometheus** and **Grafana**, all orchestrated using **Docker Compose**.

---

## 📚 Table of Contents
1. [Overview](#-overview)  
2. [Architecture](#-architecture)   
3. [Services](#-services)  
4. [Accessing the Apps](#-accessing-the-apps)  
5. [Screenshots](#-screenshots)  
6. [Summary](#-summary)  

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


## 🐳 Docker Compose Services

This project uses **Docker Compose** to run multiple services together.  
Here’s a breakdown of each service defined in the `docker-compose.yml` file:

---

### 1. 🟢 PetClinic (Spring Boot App)
```yaml
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
````

* Runs the **Spring PetClinic** app.
* Exposed on [http://localhost:8081](http://localhost:8081).
* Uses **Postgres** as its database.
* Exposes **Actuator + Prometheus metrics** for monitoring.
* Depends on `postgres` (it must be running first).

---

### 2. 🟦 Postgres (Database)

```yaml
  postgres:
    image: postgres:17.5
    container_name: postgres
    environment:
      POSTGRES_USER: petclinic
      POSTGRES_PASSWORD: petclinic
      POSTGRES_DB: petclinic
    ports:
      - "5432:5432"
```

* Provides a **PostgreSQL database** for the PetClinic app.
* Credentials:

  * Username: `petclinic`
  * Password: `petclinic`
  * Database: `petclinic`
* Exposed locally on port **5432** (can connect using pgAdmin, DBeaver, etc.).

---

### 3. 📊 Prometheus (Monitoring)

```yaml
  prometheus:
    image: prom/prometheus:latest
    container_name: prometheus
    ports:
      - "9090:9090"
    volumes:
      - ./prometheus.yml:/etc/prometheus/prometheus.yml
    depends_on:
      - petclinic
```

* Collects metrics from **PetClinic** (`/actuator/prometheus`).
* Uses a config file `prometheus.yml` to define scrape targets.
* Accessible at [http://localhost:9090](http://localhost:9090).
* Depends on `petclinic`.

---

### 4. 📈 Grafana (Dashboards)

```yaml
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

* Provides visualization for metrics collected by Prometheus.
* Default login:

  * Username: `admin`
  * Password: `admin`
* Accessible at [http://localhost:3000](http://localhost:3000).
* Depends on `prometheus`.

---

## ⚡ How Everything Connects

* **PetClinic** ↔️ uses **Postgres** as its DB.
* **Prometheus** ↔️ scrapes metrics from **PetClinic**.
* **Grafana** ↔️ connects to **Prometheus** to visualize metrics.

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

➡️ All services are started together using:

```bash
docker-compose up -d
```


2. Access the apps:

   * PetClinic → [http://localhost:8081](http://localhost:8081)
   * Prometheus → [http://localhost:9090](http://localhost:9090)
   * Grafana → [http://localhost:3000](http://localhost:3000)

---

## 📸 Screenshots

### 🐶 PetClinic

![Spring PetClinic UI](spring.png)

### 📊 Grafana Dashboards

![Grafana JVM Dashboard](dashboard1.png)

![Grafana JVM Memory](dashboard2.png)

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


