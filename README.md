
# 🎯 Reiniciar limpio
```
docker compose down -v
docker system prune -f
docker compose up --build
```

# 🐋 Flujo de Trabajo Esencial en Docker Compose

Esta secuencia de comandos se utiliza típicamente en entornos de desarrollo para **reiniciar completamente un proyecto basado en Docker Compose**, asegurando una **limpieza exhaustiva** de los datos y una **reconstrucción total** de las imágenes con el código más reciente.

---

## 1. `docker compose down -v`

Detiene y elimina los servicios definidos en `docker-compose.yml`.  
La opción `-v` es crucial para realizar una limpieza profunda.

### 🎯 Propósito Principal
Detener el entorno de forma segura y **eliminar cualquier dato persistente** de ejecuciones anteriores.

### Componentes

| Componente              | Función |
|-------------------------|---------|
| `docker compose down`   | Detiene y elimina contenedores, redes y volúmenes creados por `docker compose up`. |
| `-v` / `--volumes`      | **Elimina también los volúmenes de datos** asociados a los contenedores. |

---

## 2. `docker system prune -f`

Comando de limpieza global para Docker.

### 🎯 Propósito Principal
Liberar espacio en disco eliminando recursos y capas que ya no están en uso.

### Componentes

| Componente             | Función |
|------------------------|---------|
| `docker system prune`  | Elimina datos de Docker no utilizados. |
| `-f` / `--force`       | Ejecuta la limpieza sin pedir confirmación. |

### 🧹 ¿Qué se elimina?

- Contenedores detenidos  
- Redes no utilizadas  
- Imágenes colgantes (*dangling images*)  
- Caché de construcción (*build cache*)

---

## 3. `docker compose up --build`

Inicia el proyecto forzando una **reconstrucción completa** de las imágenes.

### 🎯 Propósito Principal
Crear el entorno desde cero usando la versión más reciente del código y de los Dockerfiles.

### Componentes

| Componente              | Función |
|-------------------------|---------|
| `docker compose up`     | Crea o levanta los servicios definidos en `docker-compose.yml`. |
| `--build`               | Fuerza a reconstruir todas las imágenes antes de iniciar los contenedores. |

---

# 🚀 Resumen del Flujo Completo

Ejecutar la siguiente secuencia garantiza un entorno **limpio y actualizado**:

```
docker compose down -v    # Limpieza profunda del proyecto (incluye datos)
docker system prune -f    # Limpieza global de imágenes y contenedores obsoletos
docker compose up --build # Reconstrucción de imágenes e inicio del nuevo entorno
```

_________

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
