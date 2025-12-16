# Procedimiento

## 1. Introducción
Desplegar N8N con traefik HTTPS usando docker y con dominio.

## 2. Requisitos Previos
- Servidor bare metal o VPS
- Linux Ubuntu Server 24.04
- Dominio
- Internet
 

## 3. Pasos

### Paso 1: Preparación

[Installar Docker](https://docs.docker.com/engine/install/ubuntu/)


### Paso 2: Ejecución
Configurar dominio.
En mi caso use DonDominio como proveedor y como mi servidor está detrás de un router domestico necesitaba que la IP fuera dinámica por si la cambiaba mi proveedor actualizara de forma automática en mi dominio para ello Dondominio ofrece una guía.
[Guía Dondominio](https://dondominio.dev/es/dondns/docs/linux/)

En la configuración hay que agregar los parámetros en "dondomcli.conf"

```bash
DDUSER=""   # 👈 pone su nombre de usuario
DDPASSWORD=""  # 👈 pone su clave DonDNS
DDHOST=""  # 👈 pone su host o hosts separados por coma
```
y habilitar un crontab (en mi caso edito con vim)

```bash
crontab -e
```
agregar la línea
```bash
*/5 * * * *  /path/dondomcli.sh -c /path/dondomcli.conf
```

### Paso 3: Traefik
En este paso configuramos el docker compose para la aplicacion de traefik con su dashborad en una primera etapa para entrar por http y puerto 8080 se deja los parametros de la siguiente manera:

```bash
- "--api.insecure=true"  
- "--api.dashboard=true"
```
Y se comenta las lineas relacionadas con el Dashboard Traefik

Editar y ejecutar el contenedor

```bash
vi docker-compose.yaml
docker compose up -d 
```
## 4. N8N
Tener en cuenta las variables  y configuraciones para que traefik pueda leer el contenedor de N8N y publicarlo

Editar y ejecutar el contenedor
```bash
vi docker-compose.yaml
docker compose up -d 
```

## 5. Fail2ban
```bash
apt install fail2ban
```
Por defecto la ruta se encuentra en `/etc/faail2ban/`
Para la configuracion de fail2ban se crea el archivo `jail.local`, que es donde se modifican los jail que a su vez tienen prioridad sobre el archivo principal `jail.conf`

En mi caso agregue 2 filtros especificos para los jails 404 y 429, el primero para evitar los bost de escaneos de URls en mi servidor y el 2do para bloquear intentos de sesion recurrentes que fueran  fallidos.   
`/etc/fail2ban/filter.d/`
`traefik-404.conf`
`traefik-4429.conf`

```bash
systemctl restart fail2ban
```



