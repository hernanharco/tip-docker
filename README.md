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

## 3. Levantar todo de nuevo
```bash
docker-compose up
```
O en modo "detached" (fondo):
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






