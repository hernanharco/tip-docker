
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
