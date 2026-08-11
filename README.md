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



<br>
<br>


## Ejecución

Con el inventario y las variables ya ajustados:


ansible-playbook -i inventory/hosts.yml site.yml --ask-become-pass



("--ask-become-pass" solo hace falta si el usuario SSH necesita contraseña para sudo. Si ya tiene privilegios sin contraseña se puede omitir.)



El playbook:

1. **Configura el servidor de base de datos:** instala MariaDB, crea la base "cumples", la tabla "cumpleanios" con datos iniciales, el usuario de la aplicación, y habilita el firewall (UFW) permitiendo únicamente SSH desde el controlador y MariaDB desde el servidor de aplicación.
2. **Configura el servidor de aplicación:** instala Apache y PHP, despliega "cumple.php" con los datos de conexión inyectados desde las variables, ajusta el contexto de SELinux, y habilita el firewall (firewalld) para tráfico HTTP.


<br>
<br>

## Verificación

El playbook valida al final que la aplicación responda por HTTP (tarea llamada "Validar que la aplicación responda"). También se puede comprobar manualmente usando un curl:


curl http://<IP del servidor de aplicación>/cumple.php

o

curl http://localhost/cumple.php
(esta última en caso de que lo estés ejecutando dentro del servidor de la app).

Debería devolver una tabla HTML con los cumpleaños cargados en la base de datos.

<br>
<br>

## Ejecución idempotente

Correr el playbook una segunda vez reportará algunos cambios referentes al clonado y modificación de archivos del repositorio. Todo el resto de tareas deberían no reportar cambios.


ansible-playbook -i inventory/hosts.yml site.yml

<br>
<br>

## Notas de seguridad

- No se almacenan contraseñas reales ni claves privadas en este repositorio.
- El acceso a MariaDB por red queda restringido únicamente a la IP del servidor de aplicación (regla de UFW + usuario de MariaDB limitado a ese host).
- El acceso HTTP al servidor de aplicación se habilita únicamente a través de firewalld.



