# Tipos-de-redes-Docker
Escenario:

Tienes 3 aplicaciones que necesitan diferentes configuraciones de red.

<Tareas:>


# 2.1 - Crea una red bridge personalizada:

```bash

# Crea una red llamada "frontend" con subnet 172.20.0.0/16
docker network create --driver bridge --subnet 172.20.0.0/16 frontend

# Verifica que se creó correctamente:
docker network ls
docker network inspect frontend

# ¿Qué subnet tiene?
# ¿Qué gateway se asignó automáticamente?

```

# 2.2 - Ejecuta containers en diferentes redes:

```bash

# Container 1: En la red por defecto
docker run -d --name web1 nginx

# Container 2: En tu red personalizada
docker run -d --name web2 --network frontend nginx

# Container 3: En modo host
docker run -d --name web3 --network host nginx

# Pregunta: ¿Cuál container NO tiene su propia IP aislada?
# A) web1
# B) web2
# C) web3 <- no tiene ip aislada 
# D) Todos tienen IP aislada

```

# 2.3 - Inspecciona las IPs:

```bash

# Obtén la IP de web1
docker inspect web1 --format='{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}'

# Obtén la IP de web2
docker inspect web2 --format='{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}'

# Intenta obtener la IP de web3 (host mode)
docker inspect web3 --format='{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}'

# ¿Qué observas en web3?
No hay ip valida
# ¿Qué subnets tienen web1 y web2?
web 1 
172.17.0.2

web 2
172.17.0.2
```

# 2.4 - Prueba la comunicación:

```bash

# ¿Pueden web1 y web2 comunicarse directamente?
docker exec web1 ping -c 2 web2

# ¿Qué resultado esperas? ¿Por qué?
Esperaba un error de conexion, pero obtuve un error de ejecución "ping", se soluciona ingresando al contenedor origen y actualizando e instalando los paquetes

-> docker exec -it web1 sh
-> apt update && apt install -y iputils-ping

# Conecta web1 a la red frontend
docker network connect frontend web1

# Ahora intenta de nuevo:
docker exec web1 ping -c 2 web2

# ¿Ahora funciona? ¿Por qué?
 No pueden comunicarse inicialmente (redes diferentes); después de connect, sí pueden

```

# 2.5 - Limpieza:

```bash

docker stop web1 web2 web3
docker rm web1 web2 web3
docker network rm frontend
docker system prune -a (opcional)

```