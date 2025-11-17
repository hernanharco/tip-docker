
# Opción 1: El Comando Todo-en-Uno (Recomendado)
Este comando detiene, elimina el contenedor anterior, fuerza la reconstrucción completa de la imagen y levanta el nuevo contenedor del servicio frontend.
```
docker-compose up -d --build --force-recreate --no-cache frontend
```
arte del comando	Propósito
docker-compose up -> Comando base para construir, (re)crear e iniciar los servicios.
-d ->	Ejecuta los contenedores en modo detached (segundo plano).
--build -> Fuerza la construcción de la imagen antes de iniciar.
--force-recreate ->	Destruye el contenedor anterior y crea uno nuevo, incluso si no hubo cambios en la configuración.
--no-cache ->	CLAVE: Ignora la caché existente para la imagen, forzando la ejecución de todos los pasos del Dockerfile (incluyendo la instalación de pnpm install con las nuevas dependencias de package.json).
frontend ->	Especifica que solo se aplique a este servicio.

________

# Opción 2: Pasos Explícitos (Borrar Imagen Primero)
Si deseas ser explícito y asegurarte de que la imagen local anterior ya no exista antes de la nueva compilación, puedes usar estos pasos:

Paso 1: Detener y eliminar el Contenedor
Detiene y elimina el contenedor asociado al servicio frontend.

```
docker-compose rm -s -f frontend
```

# Paso 2: Eliminar la Imagen Local Stale (Opcional, pero recomendado)
Busca la imagen que fue construida para tu servicio y elimínala. El nombre de tu imagen sería probablemente frontend_harcoPortfolio_frontend o similar, pero es más seguro eliminarla por el nombre que Docker le da:


-> Nota: La imagen se nombra generalmente con el nombre del directorio y el nombre del servicio.
-> Usa este si sabes el nombre:
```
docker rmi frontend_harcoportfolio_frontend
```

# Paso 3: Reconstruir y Cargar el Servicio
Reconstruye la imagen sin caché y levanta el servicio:

```
docker-compose up -d --build --no-cache frontend
```
___________
# ➡️ Comando para Desarrollo
Para ejecutar:
```
docker compose -f docker-compose.yml -f docker-compose.dev.yml up -d
```
# ➡️ Comando para Producción
Para ejecutar (o construir primero y luego ejecutar):
```
# Opcional: Construir todas las imágenes de producción
docker compose -f docker-compose.yml -f docker-compose.prod.yml build 

# Ejecutar los contenedores en modo producción
docker compose -f docker-compose.yml -f docker-compose.prod.yml up -d
```
___
La clave es que Docker Compose fusiona los archivos en el orden que los especificas.
____
# 💻 1. Prueba del Entorno de Desarrollo
El objetivo aquí es verificar que se active el modo de desarrollo, usando volúmenes para que puedas modificar el código localmente y ver los cambios al instante.

Comando a Ejecutar
Ejecuta el entorno de desarrollo y luego verifica el estado:
```
# 1. Ejecutar la configuración de desarrollo
docker compose -f docker-compose.yml -f docker-compose.dev.yml up -d

# 2. Verificar que los contenedores estén corriendo y usando los puertos correctos
docker compose ps
```

## ➡️ Verificaciones Clave
Estado: En la salida de docker compose ps, verifica que los contenedores backend_auth y frontend_auth estén en estado running y que los puertos expuestos coincidan con los de tu .dev.yml (e.g., 0.0.0.0:5176->9002/tcp).

Volúmenes: En tu navegador, accede a la URL de desarrollo (probablemente http://localhost:5176).

HMR (Hot Module Replacement): Una vez que la página cargue, modifica un texto pequeño en un archivo de tu proyecto (e.g., en el componente de login del frontend). Si el texto se actualiza en el navegador casi al instante sin reiniciar el contenedor, significa que los volúmenes están funcionando y la configuración de desarrollo es correcta.
___

# 🏭 2. Prueba del Entorno de Producción
El objetivo aquí es verificar que se cree una imagen autónoma y optimizada que ejecute el servidor de producción.

Comando a Ejecutar
Para esta prueba, debes detener y limpiar el entorno de desarrollo y luego ejecutar el de producción.

```
# 1. Limpiar el entorno de desarrollo (muy importante para evitar conflictos)
docker compose -f docker-compose.yml -f docker-compose.dev.yml down

# 2. Construir e iniciar la configuración de producción
docker compose -f docker-compose.yml -f docker-compose.prod.yml up -d --build
```
El --build aquí es crucial porque fuerza la creación de la imagen final de producción.

## ➡️ Verificaciones Clave
1. Puertos y Estado: En la salida de docker compose ps, verifica que los contenedores estén en estado running y que los puertos expuestos coincidan con los de tu .prod.yml (e.g., el frontend puede estar en 0.0.0.0:443->9002/tcp si usaste esa configuración de ejemplo).

2. Aislamiento: Vuelve a intentar modificar el texto en el archivo de tu proyecto. El cambio NO debe reflejarse en el navegador, porque la aplicación ahora se está sirviendo desde la imagen estática compilada, no desde un volumen.

3. Comando: Puedes verificar los logs para asegurarte de que se esté ejecutando el comando de producción (e.g., pnpm start) en lugar del comando de desarrollo:

```
docker compose -f docker-compose.yml -f docker-compose.prod.yml logs frontend
```
Si estas verificaciones pasan para ambos entornos, sabrás que tus tres archivos de configuración de Docker Compose se están fusionando y aplicando correctamente
____
resconstruir la imagen del docker sin que haya cambios en las migraciones ni en superusuario
```
docker-compose up --build -d
```
```
docker-compose build frontend
```
# tip-docker
# 🛠️ Flujo completo: instalar react-router-dom con pnpm y reconstruir
## Paso 1: Instala pnpm en tu máquina local (solo para gestionar package.json)
```bash
# En tu terminal local (fuera de Docker)
npm install -g pnpm
```
- Esto te permite usar pnpm para actualizar package.json y pnpm-lock.yaml.

## Paso 2: Agrega la dependencia en tu máquina local
```bash
cd frontend
pnpm add react-router-dom
```

# 🧹 Suponiendo que usas docker-compose.yml
## 1. Detener y eliminar contenedores, redes y volúmenes (opcional)
⚠️ Cuidado: si usas -v, se borrarán los volúmenes (pero en desarrollo frontend, normalmente no guardas datos importantes ahí).

### Detener y eliminar todo (excepto volúmenes)
```bash
docker-compose down
```

### O si quieres también borrar volúmenes (útil para forzar reinstalación de node_modules)
```bash
docker-compose down -v
```

## 2. Reconstruir las imágenes
Esto ejecutará de nuevo el Dockerfile, incluyendo npm install (con tu package.json actualizado).

### Reconstruir solo el servicio frontend
```bash
docker-compose build frontend
```
### O reconstruir todos los servicios
```bash
docker-compose build
```

✅ Asegúrate de que ya hayas actualizado tu package.json local con:

## 3. Levantar todo de nuevo o reiniciar las imagenes
```bash
docker-compose up
```
O en modo "detached" (fondo): ***mas utilizado
```bash
docker-compose up -d
```

# 🐳 Si NO usas docker-compose (solo docker)

## 1. Detener y eliminar el contenedor
```bash
# Lista los contenedores
docker ps -a

# Detén el contenedor (reemplaza <nombre> o <id>)
docker stop <nombre>

# Elimínalo
docker rm <nombre>
```
## 2. Eliminar la imagen antigua (opcional pero recomendado)
```bash
# Lista las imágenes
docker images

# Elimina la imagen (reemplaza <nombre-imagen>)
docker rmi <nombre-imagen>
```
## 3. Reconstruir e iniciar
```bash
# Construir nueva imagen
docker build -t mi-frontend ./frontend

# Ejecutar
docker run -p 5173:5173 -v ${PWD}/frontend:/app -v /app/node_modules mi-frontend
```
En Windows PowerShell, usa ${PWD} para la ruta actual. 






