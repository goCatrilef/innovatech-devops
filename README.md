# Innovatech - Despligue en AWS

Proyecto desarrollado para la **Evaluación Parcial N°2** de la asignatura **Introducción a Herramientas DevOps**.

El objetivo del proyecto es contenedorizzar una solución compuesta por Frontend y Backend, publicar sus imágenes Docker en **Amazon ECR** y automatizar el despliegue mediante **GitHub Actions** sobre instancias **EC2 en AWS**.

La arquitectura considera:

- Frontend desplegado en una instancia EC2 pública.
- Backend desplegado en una instancia EC2 privada.
- Bases de datos MySQL en contenedores.
- Imágenes Docker almacenadas en Amazon ECR.
- Pipelines CI/CD con GitHub Actions.

---

## Comenzando 🚀

Estas instrucciones permiten entender cómo levantar, construir y desplegar el proyecto en un ambiente local o en AWS.

El proyecto está dividido en tres componentes principales:

```txt
front_despacho/
API-Ventas-SpingBoot/
API-Despachos-SpringBoot/
```

Cada componente cuenta con su respectivo `Dockerfile` para generar imágenes Docker.

---

## Pre-requisitos 📋

Para ejecutar el proyecto se requiere tener instalado:

```txt
Git
Docker
Docker Compose
AWS CLI
Cuenta AWS Academy
GitHub Actions habilitado
```

También se requiere tener configurados los repositorios de Amazon ECR:

```txt
innovatech-frontend
innovatech-ventas-backend
innovatech-despachos-backend
innovatech-mysql
```

---

## Instalación 🔧

Clonar el repositorio:

```bash
git clone <URL_DEL_REPOSITORIO>
cd innovatech-devops
```

Construir imagen del Frontend:

```bash
docker build -t innovatech-frontend:local ./front_despacho
```

Construir imagen del Backend Ventas:

```bash
docker build -t innovatech-ventas-backend:local ./API-Ventas-SpingBoot
```

Construir imagen del Backend Despachos:

```bash
docker build -t innovatech-despachos-backend:local ./API-Despachos-SpringBoot
```

Levantar servicios Backend con Docker Compose:

```bash
cd innovatech-backend
docker-compose up -d
```

Verificar contenedores:

```bash
docker ps
```

---

## Despliegue 📦

El despliegue se realiza utilizando **GitHub Actions** y **Amazon ECR**.

Flujo general del pipeline:

```txt
Push a rama develop/deploy
        ↓
GitHub Actions
        ↓
Build imagen Docker
        ↓
Login a Amazon ECR
        ↓
Push imagen a ECR
        ↓
Deploy en EC2
```

### Frontend

El Frontend se despliega automáticamente en la instancia EC2 pública.

El contenedor se ejecuta en el puerto 80:

```bash
docker run -d \
  --name innovatech-frontend \
  -p 80:80 \
  --restart always \
  699480004844.dkr.ecr.us-east-1.amazonaws.com/innovatech-frontend:latest
```

URL de acceso:

```txt
http://98.92.10.108
```

### Backend

El Backend se ejecuta en una instancia EC2 privada, sin IP pública.

Servicios esperados:

```txt
ventas-backend
despachos-backend
mysql-ventas
mysql-despachos
```

Puertos utilizados:

```txt
8082 → Backend Ventas
8081 → Backend Despachos
3306 → MySQL
```

La comunicación se realiza desde la EC2 Frontend hacia la EC2 Backend usando IP privada.

---

## Variables de entorno 🔐

Los backends utilizan variables de entorno para conectarse a MySQL.

Ejemplo para Ventas:

```txt
DB_ENDPOINT=mysql-ventas
DB_PORT=3306
DB_NAME=ventas_db
DB_USERNAME=ventas_user
DB_PASSWORD=ventas_pass
```

Ejemplo para Despachos:

```txt
DB_ENDPOINT=mysql-despachos
DB_PORT=3306
DB_NAME=despachos_db
DB_USERNAME=despachos_user
DB_PASSWORD=despachos_pass
```

---

## GitHub Secrets 🔑

Los pipelines utilizan GitHub Secrets para proteger credenciales y datos sensibles.

Descripción:

```txt
AWS_ACCESS_KEY_ID      → Access Key de AWS Academy
AWS_SECRET_ACCESS_KEY  → Secret Key de AWS Academy
AWS_SESSION_TOKEN      → Token temporal del laboratorio AWS
AWS_REGION             → Región AWS, us-east-1
AWS_ACCOUNT_ID         → ID de la cuenta AWS
EC2_FRONT_HOST         → IP pública de la EC2 Frontend
EC2_USER               → Usuario SSH, ec2-user
EC2_SSH_KEY            → Contenido de la llave privada .pem
```

---

## Persistencia de datos 💾

La persistencia de MySQL se realiza mediante volúmenes Docker.

Volúmenes utilizados:

```txt
mysql_ventas_data
mysql_despachos_data
```

Estos volúmenes permiten mantener los datos aunque los contenedores sean eliminados o recreados.

Comandos útiles:

```bash
docker volume ls
docker volume inspect mysql_ventas_data
docker volume inspect mysql_despachos_data
```

---

## Seguridad en AWS 🔒

La arquitectura separa la capa pública y privada.

### EC2 Frontend

La instancia Frontend es pública y permite acceso HTTP:

```txt
HTTP 80 desde Internet
SSH 22 para administración
```

### EC2 Backend

La instancia Backend es privada y no tiene IP pública.

Reglas de entrada configuradas:

```txt
8081 desde Security Group del Frontend
8082 desde Security Group del Frontend
22 desde Security Group del Frontend
```

Esto permite que solo el Frontend pueda comunicarse con el Backend.

---


## Construido con 🛠️

Herramientas utilizadas:

- [Docker](https://www.docker.com/) - Contenedorización de servicios.
- [Docker Compose](https://docs.docker.com/compose/) - Orquestación local de contenedores.
- [Amazon EC2](https://aws.amazon.com/ec2/) - Instancias para despliegue.
- [Amazon ECR](https://aws.amazon.com/ecr/) - Registro privado de imágenes Docker.
- [GitHub Actions](https://github.com/features/actions) - Automatización CI/CD.
- [React + Vite](https://vitejs.dev/) - Frontend.
- [Spring Boot](https://spring.io/projects/spring-boot) - Backend.
- [MySQL](https://www.mysql.com/) - Base de datos.
- [Maven](https://maven.apache.org/) - Gestor de dependencias Java.

---

## Versionado 📌

El versionado de imágenes se realiza mediante etiquetas Docker.

Etiqueta principal utilizada:

```txt
latest
```

También se puede utilizar el SHA del commit para versionado:

```yaml
IMAGE_TAG: ${{ github.sha }}
```

Esto permite mayor trazabilidad entre código fuente, pipeline e imagen desplegada.

---

## Autores ✒️

Proyecto desarrollado por:

```txt
Gonzalo Catrilef
Equipo Innovatech DevOps
```



## Licencia 📄

Este proyecto fue desarrollado con fines académicos para la asignatura Introducción a Herramientas DevOps.

---

⌨️ Desarrollado para Evaluación Parcial N°2 - Innovatech DevOps
