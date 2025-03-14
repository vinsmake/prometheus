FROM prom/blackbox-exporter:latest

# Copia el archivo de configuración
COPY blackbox.yml /etc/blackbox_exporter/config.yml

# Expone el puerto 9115 para recibir requests
EXPOSE 9115

# Ejecuta el Blackbox Exporter
CMD [ "/bin/blackbox_exporter", "--config.file=/etc/blackbox_exporter/config.yml" ]
