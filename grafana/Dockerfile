FROM grafana/grafana:latest

# Copia el archivo de configuración de Grafana
COPY grafana.ini /etc/grafana/grafana.ini

# Define las variables de entorno para el usuario admin
ENV GF_SECURITY_ADMIN_USER=admin
ENV GF_SECURITY_ADMIN_PASSWORD=admin

# Expone el puerto 3000
EXPOSE 3000

# Inicia Grafana
CMD [ "/run.sh" ]
