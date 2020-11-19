# airflow-kubernetes
Repo del taller Airflow en Kubernetes para el taller "Airflow en Kubernetes" @ PyConAr 2020

## Setup
Las instrucciones detalladas de instalación para cada sistema operativo se encuentran en [aquí](https://docs.google.com/document/d/1J7irLNi6HgdjX764xW2C2TKJSIz8ACnLDY1Dwlaq3dA/edit)

Para setupear el virtual environment para este taller les recomiendo ejecutar:
```bash
pip install "apache-airflow[async,celery,crypto,jdbc,kubernetes,password,postgres,redis,slack]==1.10.12" --constraint docker/airflow/requirements.txt
docker run -d -p 5000:5000 --restart always --name registry registry:2
```

Para confirmar que todas las herramientas estan correctamente instaladas todos los siguientes comando deberían andar correctamente:
``` bash
docker run hello-world
docker-compose --version
minikube start
kubectl version
helm version
kubectl cluster-info
```

## Imagenes de Docker adicionales para los ejemplos
"pre-descargar" las siguientes imagenes de docker para tenerlas cacheadas en local cuando se vayan autilizar en los ejemplos. Ejecutar los siguientes comandos:
```bash
docker pull redis:5.0.5
docker pull library/postgres:12
docker pull puckel/docker-airflow
docker pull java:8
docker pull python:3
docker pull bitnami/spark
```


## Slides del taller
[aquí](https://drive.google.com/file/d/1R2uvK1TYjkZu0bervaWzFUhV96RfwOLv/view)


## Problemas al construir el docker compose
Es posible que el ```docker-compose up --build``` fallé porque no encuentra el archivo airflow.cfg. En este caso lo más sencillo es simplemente borrar (o comentar) la linea 9 del Dockerfile de la carpeta airflow (docker/airflow/Dockerfile) de este repo. La línea en cuestión es:
```COPY airflow.cfg $AIRFLOW_HOME/airflow.cfg```


## Para que el Kubernetes pod operator funcione
Luego de configurar y levantar el cluster de Kubernetes a utilizar, para efectos de esta prueba es el que se levanta con minikube (con el comando ```minikube start```).
Definir el "contexto" que se va usar (los contextos existentes se pueden revisar abriendo el archivo de configuración (```cat ~/.kube/config```) y buscando el elemento "context" ), en mi caso se llama docker-desktop:
```bash
kubectl config  use-context docker-desktop
```

Después y antes de levantar el docker-compose, copiar la configuración de acceso al cluster de Kubernetes que se vaya a utilizar al source code que se va a builder. Para esto correr (estando en la carpeta raiz de este repo):
```bash
cp ~/.kube/config docker/airflow
```

Finalmente ejecutar el ```docker-compose up --build```


## Para que el kubernetes_multilanguage_dag funcione
Es necesario construir las imagenes de docker de cada uno de los "lenguages" utilizados en el ejemplo (python, java y spark). Basta con ejecutar los siguientes comandos, estando en la carpeta raiz de este repo:
```bash
cd multi-language
./build_images.sh
```


## Para cerrar
Detener el docker-compose levantado y el cluster de kubernetes (minikube)
```bash
docker-compose stop
minikube stop
```
