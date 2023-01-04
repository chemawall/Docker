#Construccion de REDIS

#----------------------
#Construir una imagen de redis
#Asignar un nombre al contenedor
#Asignar un tag mediante variable
#----------------------


#Variables

REDIS_TAG=3
REDIS_CONTAINER_NAME=redis
REDIS_IMAGE_NAME=redis


#Construccion y despliege de redis

build_redis:
	@echo 'Construccion de redis con el TAG=$(REDIS_TAG)'
	docker pull $(REDIS_IMAGE_NAME):$(REDIS_TAG)
	@echo 'Ejecución de un contenedor de $(REDIS_IMAGE_NAME) con el nombre de $(REDIS_CONTAINER_NAME)'
	docker run -d --name $(REDIS_CONTAINER_NAME) $(REDIS_IMAGE_NAME):$(REDIS_TAG)


#-------------------------------------
#Visualizar todas las imágenes
#Visualizar contenedores activos
#Visualizar contenedores muertos
#--------------------------------------


show_images:
	@echo 'Imagenes de docker'
	docker images -a
	@echo ' Contenedores en Ejecución'
	docker ps --filter status=running
	@echo 'Contenedores fuera de Ejecución'
	docker ps --filter status=exited

#-----------------------------------------
#Eliminar contenedor de REDIS
#Visualizar de nuevo
#-----------------------------------

delete_redis:
	@echo 'Parar redis'
	docker stop $(REDIS_CONTAINER_NAME)
	@echo 'Eliminacion del contenedor'
	docker rm $(REDIS_CONTAINER_NAME)
	@echo 'Eliminación de la imagen'
	docker rmi $(REDIS_IMAGE_NAME):$(REDIS_TAG)
	@echo 'EStadisticas de los contenedores en Ejecución'
	docker stats

#-----------------------------------------
#2.Despliege del monitor
#-----------------------------------------

#Construcción del monitor

build_monitor_prometheus:
	@echo 'Construccion y despliegue de Prometheus'
	cd monitor/prometheus && docker-compose up -d

build_monitor_grafana:
	@echo 'Construccion y despliegue de grafana'
	cd monitor/grafana && docker-compose up -d

build_monitor_redis:
	@echo 'Construccion y despliegue de Redis'
	cd monitor/redis && docker-compose up -d

build_monitor_redis_exporter:
	@echo 'Construccion y despliegue de Redis-Exporter'
	cd monitor/redis-exporter && docker-compose up -d

build_monitor:
	@echo 'Contenedores en ejecucion'
	docker ps -a
	$(MAKE) build_monitor_prometheus
	$(MAKE) build_monitor_grafana
	$(MAKE) build_monitor_redis
	$(MAKE) build_monitor_redis_exporter
	docker  ps -a

 #Parar el servicio de Prometheus
 
stop_monitor_prometheus:      ##Parar el servicio de Prometheus
	@echo 'Parar el servicio de Prometheus'
	cd monitor/prometheus && docker-compose stop prometheus

stop_monitor_grafana:
	@echo 'Parar el servicio de Grafana'
	cd monitor/prometheus && docker-compose stop grafana

stop_monitor_redis:
	@echo 'Parar el servicio de Redis'
	cd monitor/redis && docker-compose stop redis

stop_monitor_redis_exporter:
	@echo 'Parar el servicio de Redis-Exporter'
	cd monitor/redis-exporter && docker-compose stop redis-exporter

stop_monitor:
	@echo 'Contenedores en ejecución'
	docker ps -a
	$(MAKE) stop_monitor_prometheus
	$(MAKE) stop_monitor_grafana
	$(MAKE) stop_monitor_redis
	$(MAKE) stop_monitor_redis_exporter
	docker  ps -a

#Eliminacion de los servicios del monitor

down_monitor_prometheus:
	@echo 'Parar el servicio Prometheus'
	cd monitor/prometheus && docker-compose down prometheus

#etc etc

#Proporciona ayuda
.PHONY: help
help:			##Comando de ayuda
	@fgrep -h "##" $(MAKEFILE_LIST) | fgrep -v fgrep | sed -e 's/\\$$//' | sed -e 's/##//'

#-----------------------------------------
#3.Construccion de CADVISOR
#-----------------------------------------

CADVISOR_IMAGE=google/cadvisor
CADVISOR_TAG=v0.33.0
CADVISOR_CONTAINER=cadvisor_wall
PATH_CADVISOR_IMAGE=/home/jm/programacion/Docker/Docker

build_cadvisor: ##Construccion y despliegue de Cadvisor
	@echo 'Descarga de la imagen de cadvisor'
	docker pull $(CADVISOR_IMAGE):$(CADVISOR_TAG)
	@echo 'Ejecución de $(CADVISOR_IMAGE)'
	docker run -dp 8080:8080 --name $(CADVISOR_CONTAINER) $(CADVISOR_IMAGE):$(CADVISOR_TAG)

save_cadvisor:
	@echo 'Guarda la imagen de Cadvisor'
	docker save $(CADVISOR_IMAGE):$(CADVISOR_TAG) | gzip > $(PATH_CADVISOR_IMAGE)/cadvisor.tar.gz
	
	
load_cadvisor:	
	@echo 'Carga de la imagen'
	docker load --input $(PATH_CADVISOR_IMAGE)/cadvisor.tar.gz
	docker run -dp 8080:8080 --name $(CADVISOR_CONTAINER) $(CADVISOR_IMAGE):$(CADVISOR_TAG)

aws_connection:
	$(MAKE) save_cadvisor
	@echo 'Conexion remote con AWS con SSH'
	$(MAKE) load_cadvisor

delete_cadvisor:
	docker rm -f $(CADVISOR_CONTAINER)
	docker rmi $(CADVISOR_IMAGE):$(CADVISOR_TAG)


