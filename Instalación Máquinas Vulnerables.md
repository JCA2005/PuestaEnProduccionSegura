# Instalación de máquinas vulnerables: dockerlabs.es

<img width="1000" height="420" alt="image" src="https://github.com/user-attachments/assets/5ed057eb-2ab8-4f34-beb8-79d5430aa488" />



## 1. Introducción y Objetivos

El presente documento detalla el procedimiento de configuración de un entorno de pentesting basado en **WSL (Windows Subsystem for Linux)** y el despliegue de la máquina vulnerable **"Obsession"** de la plataforma DockerLabs. El objetivo es realizar una auditoría de seguridad inicial, abarcando desde la preparación de la infraestructura hasta la fase de enumeración de servicios.

## 2. Preparación del Entorno de Auditoría

Para garantizar la interoperabilidad entre las herramientas de Windows y los contenedores de Linux, se realizaron ajustes específicos en la configuración del subsistema.

### 2.1. Configuración de Red en WSL

Se modificó la configuración global de WSL para establecer el modo de red en **Mirrored** (espejo). Esto permite que las interfaces de red de Linux sean accesibles directamente desde el host Windows. Adicionalmente, se desactivó el **Firewall de Hyper-V** para evitar el bloqueo de paquetes durante los escaneos.

<img width="1920" height="1020" alt="Captura de pantalla 2026-01-20 102146" src="https://github.com/user-attachments/assets/aa1596d7-cee0-461b-8286-fdbf4840af42" />


### 2.2. Despliegue de Kali Linux

Se procedió a la instalación de la distribución **Kali Linux** a través de la terminal de PowerShell, descargando la última versión disponible para WSL.

<img width="1919" height="307" alt="Captura de pantalla 2026-01-20 102356" src="https://github.com/user-attachments/assets/4577d04a-8cf9-4c5e-9cd1-f5e02e4c03cb" />


Una vez inicializado el sistema, se configuró el usuario estándar de operaciones (en este caso, `Javi`) y se establecieron las credenciales de acceso administrativo.

<img width="1920" height="1020" alt="Captura de pantalla 2026-01-20 103520" src="https://github.com/user-attachments/assets/fe4aaeec-decc-4068-b3c1-21748da4646d" />


### 2.3. Instalación y Hardening de Docker

Con el sistema operativo base listo, el siguiente paso fue preparar el motor de contenedores Docker. Primero, se actualizaron los repositorios del sistema para garantizar la integridad de los paquetes.

```bash
sudo apt update && sudo apt upgrade -y
