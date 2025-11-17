
# 🎯 Paso 2 — Reiniciar limpio
```
docker compose down -v
docker system prune -f
docker compose up --build
```

# Eliminar node_modules y pnpm-lock.yaml
## Para eliminar node_modules
```
Remove-Item -Recurse -Force .\node_modules
```
-Recurse → borra todo dentro de la carpeta.<br>
-Force → borra incluso archivos ocultos o protegidos.<br>
.\node_modules → indica la carpeta en la ruta actual.

## Para eliminar pnpm-lock.yaml
```
Remove-Item -Force .\pnpm-lock.yaml
```
## Luego, reconstruir dependencias
```
pnpm install
```
___________
# Si hay una modificacion de package.json se debe hacer
## 1️⃣ Borrar node_modules y lockfile dentro del contenedor

Si tu contenedor está corriendo, primero entra o hazlo en un solo comando:
```
docker-compose exec frontend sh -c "rm -rf node_modules pnpm-lock.yaml"
```

Esto borra las dependencias viejas y el lockfile.

## 2️⃣ Instalar todo de nuevo
```
docker-compose exec frontend sh -c "pnpm install"
```
- Esto instalará las dependencias según tu package.json recién modificado.
- pnpm-lock.yaml se regenerará automáticamente.

## 3️⃣ Construir el frontend (opcional, producción)

Si quieres hacer un build de producción en vez de desarrollo:
```
docker-compose exec frontend sh -c "pnpm run build"
```
- Esto generará la carpeta dist lista para servir.
- Luego podrías cambiar el CMD en tu Dockerfile a un servidor estático como serve o nginx.
