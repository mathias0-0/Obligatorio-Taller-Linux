# Obligatorio Taller de Servidores Linux


Automatización con Ansible del despliegue de una aplicación web con Apache-PHP, distribuida en dos servidores.


## Arquitectura

| Servidor | Sistema operativo | Función |
|---|---|---|
| Aplicación | CentOS Stream | Apache, PHP |
| Base de datos | Ubuntu Server | MariaDB |

La aplicación "dbappphp" corre en el servidor CentOS y consulta a través de la red los datos almacenados en MariaDB en el servidor Ubuntu.





## Requisitos previos

**En el servidor Ansible:**
- Ansible >= 2.14
- Acceso SSH a ambos servidores.
- Colecciones de Ansible declaradas en requirements.yml:  ansible-galaxy collection install -r requirements.yml
  

**En los servidores administrados:**
- Python 3 instalado (requerido por Ansible)
- CentOS Stream con acceso a repositorios dnf, y Ubuntu Server con acceso a apt





