## 🔧 **Overview**

This Docker Compose file sets up a complete IoT infrastructure stack for messaging, monitoring, and observability using:

* **VerneMQ** as the MQTT message broker
* **Kafka (KRaft mode)** for high-throughput data streaming
* **Kafka UI** for Kafka topic inspection and management
* **Prometheus** for metrics collection
* **Grafana** for visualization and dashboarding

All services are connected via a single Docker bridge network (`iot`), enabling communication between containers.

---

## 🧱 **Services Breakdown**

### 1. **VerneMQ**

* **Role:** MQTT broker (supports MQTT v3, v5, etc.)
* **Key Configs:**

  * Allows anonymous clients for development use
  * Advertises itself as `vernemq1.local`
  * Exposes MQTT on port `1883`, and HTTP API on `8888`
  * Data, config, and logs are persisted via Docker volumes
* **Usage:** Publish/Subscribe communication for IoT devices

---

### 2. **Kafka (KRaft Mode)**

* **Role:** Distributed event streaming platform
* **Key Configs:**

  * Runs in **KRaft mode** (no Zookeeper)
  * Uses both internal (`29092`) and external (`9092`) listeners
  * External listener uses host IP for outside-Docker connectivity
  * Supports automatic topic creation
* **Usage:** Reliable and scalable message queue for sensor/event data

---

### 3. **Kafka UI**

* **Role:** Web-based Kafka management UI
* **Key Configs:**

  * Loads dynamic configuration from `dynamic_config.yaml`
  * Connects to Kafka via internal listener (`kafka:29092`)
  * Exposed on port `8080`
* **Usage:** Inspect topics, messages, consumers, and broker status

---

### 4. **Prometheus**

* **Role:** Metrics collector and time-series database
* **Key Configs:**

  * Uses a mounted custom `prometheus.yml` configuration file
  * Stores metric data in a persistent volume
  * Exposed on port `9090`
* **Usage:** Pulls metrics from supported services (like Kafka)

---

### 5. **Grafana**

* **Role:** Metrics visualization and dashboarding tool
* **Key Configs:**

  * Admin user/password set via environment variables
  * Allows pre-configuration via provisioning (optional, commented)
  * Data volume persists dashboards and settings
  * Exposed on port `3000`
* **Usage:** Displays dashboards using data from Prometheus

---

## 🌐 **Network**

All services share a custom bridge network:

```yaml
networks:
  iot:
    driver: bridge
```

This allows for DNS-based communication between containers (e.g., `kafka:29092`, `vernemq1.local:1883`).

---

## 💾 **Volumes**

Persistent data is stored for the following:

* VerneMQ: `/vernemq/data`, `/vernemq/log`, `/vernemq/etc`
* Kafka: `/bitnami/kafka`
* Prometheus: `/prometheus`
* Grafana: `/var/lib/grafana`

---

## 💾 **Starting Container**
```bash
# change to directory server
$ cd ~/Iot-class-2025-server

# build and start container
$ docker compose up --build 

```
|Option	|Default	|Description|
|--|--|--|
|--build		| |Build images before starting containers|

---


## 💾 **Stop and remove containers, networks**
```bash
# change to directory server
$ cd ~/Iot-class-2025-server

# build and start container
$ docker compose down --volumes --remove-orphans --rmi local

```

|Option	|Default	|Description|
|--|--|--|
|--remove-orphans		| |Remove containers for services not |defined in the Compose file|
|--rmi		| |Remove images used by services. "local" remove |only images that don't have a custom tag ("local"||"all")|
|-t, --timeout		| |Specify a shutdown timeout in seconds|
|-v, --volumes		| |Remove named volumes declared in the |"volumes" section of the Compose file and anonymous |volumes attached to containers|

---