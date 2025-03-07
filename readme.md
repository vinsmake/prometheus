


# 📊 Monitorización de Sitios Web con Prometheus, Blackbox Exporter y Grafana

Este proyecto implementa un sistema de **monitorización de sitios web** utilizando **Prometheus**, **Blackbox Exporter** y **Grafana**, permitiendo obtener métricas detalladas sobre la disponibilidad y la seguridad SSL de múltiples sitios web.

## 📌 **Características del Proyecto**
✅ **Monitorización de Disponibilidad HTTP** con Prometheus y Blackbox Exporter.  
✅ **Monitoreo de Certificados SSL** para verificar la expiración de certificados.  
✅ **Visualización en Grafana** con dashboards personalizados.  
✅ **Persistencia de datos** mediante volúmenes en Docker.  
✅ **Configuración de retención de datos** para evitar crecimiento descontrolado.  
✅ **Queries optimizadas en Prometheus** para mostrar alias y eliminar duplicados.  

## 🛠 **Requisitos Previos**
Asegúrate de tener instalado:
- [Docker](https://docs.docker.com/get-docker/) (para ejecutar los contenedores).
- [Docker Compose](https://docs.docker.com/compose/) (para gestionar el `docker-compose.yml`).
- Acceso a internet para descargar imágenes de Docker.

## 🚀 **Instalación y Configuración**
### **1️⃣ Clonar el Repositorio**
```bash
git clone https://cgig-git.jalisco.gob.mx/nuevas-tecnologias/dgitg/prometheus.git
```

### **2️⃣ Configurar los Archivos Necesarios**
El proyecto ya incluye configuraciones listas, pero puedes modificarlas:

#### **📌 `docker-compose.yml` (para levantar los contenedores)**
```yaml
version: '3.8'

services:
  prometheus:
    image: prom/prometheus:latest
    volumes:
      - ./prometheus/prometheus.yml:/etc/prometheus/prometheus.yml
      - ./prometheus/data:/prometheus
    ports:
      - "9090:9090"
    command:
      - '--config.file=/etc/prometheus/prometheus.yml'
      - '--storage.tsdb.retention.time=30d'
    restart: always

  blackbox:
    image: prom/blackbox-exporter:latest
    volumes:
      - ./blackbox/blackbox.yml:/etc/blackbox_exporter/config.yml
    ports:
      - "9115:9115"
    restart: always

  grafana:
    image: grafana/grafana:latest
    volumes:
      - ./grafana:/var/lib/grafana
    environment:
      - GF_SECURITY_ADMIN_USER=admin
      - GF_SECURITY_ADMIN_PASSWORD=admin
    ports:
      - "3000:3000"
    restart: always
```

📌 **Explicación de la Configuración:**
- **Prometheus** (`9090`): Recolecta métricas y las almacena.
- **Blackbox Exporter** (`9115`): Realiza pruebas HTTP/S en los sitios web.
- **Grafana** (`3000`): Visualización de métricas con dashboards personalizados.
- **Retención de Datos**: Se configura para almacenar solo los últimos `30 días`.
- **Persistencia**: Se usan volúmenes (`./grafana`, `./prometheus/data`) para evitar perder datos.

---

#### **📌 `prometheus.yml` (Configuración de Prometheus)**
```yaml
global:
  scrape_interval: 60s # Verificación cada 60 segundos

scrape_configs:
  - job_name: "website_monitoring"
    metrics_path: /probe
    params:
      module: [http_2xx]
    static_configs:
      - targets:
          - "https://ejemplo.com"
          - "https://otro-sitio.com"

    relabel_configs:
      - source_labels: [__address__]
        target_label: __param_target
      - source_labels: [__param_target]
        target_label: instance
      - target_label: __address__
        replacement: "blackbox:9115"

    metric_relabel_configs:
      - source_labels: [probe_http_status_code]
        regex: ".*"
        target_label: http_status
      - source_labels: [__param_target]
        target_label: alias
        replacement: "Mi Alias"
        regex: "https://ejemplo.com"
```

📌 **¿Qué hace esto?**
- **Se monitorean sitios web específicos** (`targets`).
- **Se asignan alias personalizados** (`alias`) para mejor visualización.
- **Se extraen métricas de estado HTTP (`http_status`) y tiempos de respuesta.**

---

## 🚀 **Ejecutar el Proyecto**
### **Para Iniciar los Contenedores**
```bash
docker-compose up -d
```
Esto iniciará Prometheus, Blackbox Exporter y Grafana en segundo plano.

### **Para Detener los Contenedores**
```bash
docker-compose down
```

---

## 📊 **Acceder a las Herramientas**
| Herramienta  | URL de Acceso |
|-------------|---------------|
| **Prometheus** | [http://localhost:9090](http://localhost:9090) |
| **Blackbox Exporter** | [http://localhost:9115](http://localhost:9115) |
| **Grafana** | [http://localhost:3000](http://localhost:3000) |

📌 **Usuario y Contraseña por Defecto en Grafana:** `admin / admin` (puedes cambiarlo en la configuración).

---

## 📌 **Queries Actuales en Prometheus**
Estas queries están optimizadas para **evitar duplicados y asegurar la correcta visualización en Grafana**.

### **🔹 Verificación de Tiempo de Respuesta HTTP**
```promql
label_replace(
  label_replace(
    max by (instance, alias) (
      probe_http_duration_seconds{job="website_monitoring", phase="transfer"}
    ),
    "alias", "Sin Alias", "alias", "^$"
  ),
  "url", "$1", "instance", "(.*)"
)
```
📌 **Evita duplicados y asigna alias correctamente.**

### **🔹 Monitoreo de Expiración de Certificados SSL**
```promql
label_replace(
  (probe_ssl_earliest_cert_expiry{job="website_monitoring"} - time()) / 86400,
  "url", "$1", "instance", "(.*)"
)
```
📌 **Calcula los días restantes antes de que un certificado SSL expire.**

---

## 🛠 **Cómo Modificar las Queries en Grafana**
1️⃣ Ir a **Grafana** → `http://localhost:3000`.  
2️⃣ Crear o editar un **Dashboard**.  
3️⃣ Agregar un **Panel** y elegir **Prometheus como fuente de datos**.  
4️⃣ Copiar y pegar las queries en el editor.  
5️⃣ **Guardar y visualizar las métricas en gráficos o tablas.**  

---

## 📌 **Conclusión**
Este proyecto permite **monitorizar múltiples sitios web** de forma eficiente con **Prometheus, Blackbox Exporter y Grafana**. La configuración está optimizada para garantizar **una gestión eficiente de los datos** y **una visualización clara en Grafana**. 🚀

🔹 **Si necesitas agregar más sitios o modificar la configuración, solo edita `prometheus.yml` y reinicia los contenedores.** 💡

