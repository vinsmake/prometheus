FROM prom/prometheus:latest

# Copia el archivo de configuración
COPY prometheus.yml /etc/prometheus/prometheus.yml

# Define la configuración de retención de datos
CMD [ "--config.file=/etc/prometheus/prometheus.yml", "--storage.tsdb.retention.time=30d" ]
