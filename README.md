# tip-docker
#🧹 Suponiendo que usas docker-compose.yml
##1. Detener y eliminar contenedores, redes y volúmenes (opcional)
⚠️ Cuidado: si usas -v, se borrarán los volúmenes (pero en desarrollo frontend, normalmente no guardas datos importantes ahí).

### Detener y eliminar todo (excepto volúmenes)
```bash
docker-compose down
```

### O si quieres también borrar volúmenes (útil para forzar reinstalación de node_modules)
```bash
docker-compose down -v
```

##2. Reconstruir las imágenes
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

