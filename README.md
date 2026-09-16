# Laboratorio 4: Docker Compose y Microservicios

Este repositorio contiene cuatro ejercicios prácticos sobre Docker Compose. Cada uno desarrolla un concepto distinto: servicios web, comunicación entre contenedores, persistencia de datos y configuración mediante variables de entorno con comprobaciones de salud.

## Requisitos

- Docker Desktop instalado y en ejecución.
- Docker Compose v2 disponible mediante `docker compose version`.
- Un navegador web para validar los ejercicios 1 y 2.

## Estructura del repositorio

```text
Laboratorio4-DN/
├── lab-ejercicio1/  # Nginx y contenido HTML estático
├── lab-ejercicio2/  # Node.js y PostgreSQL
├── lab-ejercicio3/  # MySQL con volumen persistente
└── lab-ejercicio4/  # .env y healthchecks
```

## Ejercicio 1: servicio web con Nginx

Levanta un servidor Nginx que publica el archivo `html/index.html`. El directorio local se monta en el contenedor, por lo que los cambios al HTML se reflejan al recargar el navegador.

```powershell
cd lab-ejercicio1
docker compose up -d
docker compose ps
```

Abre [http://localhost:8080](http://localhost:8080). Para detener el servicio:

```powershell
docker compose down
```

## Ejercicio 2: aplicación Node.js y PostgreSQL

Inicia dos servicios en una misma red interna de Docker Compose:

- `app`: servidor Node.js expuesto en el puerto `3000`.
- `db`: instancia de PostgreSQL accesible desde la aplicación mediante el host `db`.

```powershell
cd lab-ejercicio2
docker compose up -d
docker compose ps
docker compose logs app
```

Valida la respuesta en [http://localhost:3000](http://localhost:3000). También puedes abrir una sesión de PostgreSQL:

```powershell
docker compose exec db psql -U admin -d miapp
```

Finaliza los servicios con `docker compose down`.

## Ejercicio 3: persistencia con volúmenes

Ejecuta una base de datos MySQL y almacena sus datos en el volumen nombrado `datos_mysql`. El volumen conserva la información aunque se eliminen los contenedores con `docker compose down`.

```powershell
cd lab-ejercicio3
docker compose up -d
docker compose logs db
docker compose exec db mysql -uroot -prootpass tienda
```

Para comprobar el volumen creado:

```powershell
docker volume ls
```

Usa `docker compose down` para conservar los datos. Usa `docker compose down -v` solo si deseas eliminar también el volumen y toda la información de MySQL.

## Ejercicio 4: variables de entorno y healthchecks

Este ejercicio usa el archivo `.env` para configurar PostgreSQL y la aplicación, y configura un `healthcheck` para que `app` espere a que `db` esté lista.

```powershell
cd lab-ejercicio4
docker compose config
docker compose up -d
docker compose ps
```

El archivo `docker-compose.yml` está configurado actualmente con `test: ["CMD-SHELL", "false"]`, que simula un healthcheck fallido. Antes de ejecutar el flujo normal, reemplázalo por:

```yaml
test: ["CMD-SHELL", "pg_isready -U ${DB_USER} -d ${DB_NAME}"]
```

Así, el servicio `app` iniciará únicamente cuando PostgreSQL alcance el estado `healthy`. El archivo `.env` está excluido por `.gitignore`; no publiques credenciales reales en el repositorio.

## Comandos útiles

```powershell
# Estado de los servicios del ejercicio actual
docker compose ps

# Registros de todos los servicios
docker compose logs

# Detener y eliminar contenedores del ejercicio actual
docker compose down
```

Cada ejercicio usa nombres de proyecto y recursos propios de Docker Compose, por lo que se recomienda trabajar desde la carpeta correspondiente.
