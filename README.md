# Obligatorio Taller de Servidores Linux


Automatización con Ansible del despliegue de una aplicación web con Apache-PHP, distribuida en dos servidores.


## Arquitectura

| Servidor | Sistema operativo | Función |
|---|---|---|
| Aplicación | CentOS Stream | Apache, PHP |
| Base de datos | Ubuntu Server | MariaDB |

La aplicación "dbappphp" corre en el servidor CentOS y consulta a través de la red los datos almacenados en MariaDB en el servidor Ubuntu.

