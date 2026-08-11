# Obligatorio Taller de Servidores Linux


Automatización con Ansible del despliegue de una aplicación web con Apache-PHP, distribuida en dos servidores.

<br>
<br>

## Arquitectura

| Servidor | Sistema operativo | Función |
|---|---|---|
| Aplicación | CentOS Stream | Apache, PHP |
| Base de datos | Ubuntu Server | MariaDB |

La aplicación "dbappphp" corre en el servidor CentOS y consulta a través de la red los datos almacenados en MariaDB en el servidor Ubuntu.


<br>
<br>


## Requisitos previos

**En el servidor Ansible:**
- Ansible >= 2.14
- Acceso SSH a ambos servidores.
- Colecciones de Ansible declaradas en requirements.yml:  ansible-galaxy collection install -r requirements.yml
  

**En los servidores administrados:**
- Python 3 instalado (requerido por Ansible)
- CentOS Stream con acceso a repositorios dnf, y Ubuntu Server con acceso a apt


<br>
<br>

## Estructura del repositorio


- site.yml
- requirements.yml             
- inventory/
   - hosts.yml                
- group_vars/
   - all.yml                  
   - app.yml                  
   - db.yml                   
- roles/
   - apache_php/
      - tasks/main.yml       
   - mariadb/
      - tasks/main.yml       
      - handlers/main.yml    



<br>
<br>


## Configuración antes de ejecutar

**1. Inventario** "inventory/hosts.yml". Reemplazar las IPs de ejemplo por las de los servidores reales:

```yaml
app:
  hosts:
    centos:
      ansible_host: <IP del servidor CentOS>

db:
  hosts:
    ubuntu:
      ansible_host: <IP del servidor Ubuntu>
```


<br>

 
**2. Variables** "group_vars/". Ajustar según el entorno:


- **Variable (Archivo): Descripción**


- app_repo (en all.yml): Repositorio Git de la aplicación PHP
- db_name, db_user, db_password (en all.yml): Credenciales de la app en MariaDB
- db_host, db_port (en app.yml): Datos de conexión al servidor de base de datos
- db_allowed_host (en db.yml): IP desde la que se permite conectarse a MariaDB (servidor de aplicación)
- db_bind_address (en db.yml): IP propia del servidor de base de datos, en la que escucha MariaDB
- controller_ip (en db.yml): IP de la máquina de control, habilitada por SSH en el firewall
 


**Importante:** db_password viene con un valor de ejemplo (CAMBIAR_EN_PRODUCCION). Reemplazarlo por una contraseña real antes de desplegar en cualquier entorno que no sea de pruebas (este repositorio no versiona contraseñas reales).












