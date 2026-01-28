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
```

<img width="1919" height="456" alt="Captura de pantalla 2026-01-20 114359" src="https://github.com/user-attachments/assets/ebe16f3b-efe7-481f-bc8e-b73d42b24597" />

### 2.3.2. Instalación de Docker

Para instalar el motor de contenedores, utilizamos el paquete docker.io, que incluye todos los componentes necesarios.

```bash
sudo apt install docker.io -y
```

<img width="1920" height="1020" alt="Captura de pantalla 2026-01-20 114447" src="https://github.com/user-attachments/assets/b154a172-b1cd-4a08-91a7-203b4ec077d9" />

Comprobamos que la instalación se realizó correctamente verificando la versión instalada:

```bash
docker --version
```

<img width="1919" height="279" alt="Captura de pantalla 2026-01-20 114521" src="https://github.com/user-attachments/assets/4ac358e3-ae15-4388-8ccc-8a461dcd1398" />
### 2.3.3. Gestión del Servicio Docker

Iniciamos el servicio de Docker y lo configuramos para que se ejecute automáticamente al iniciar el sistema:

```bash
sudo systemctl start docker
sudo systemctl enable docker
```

Para comprobar que Docker está operativo, ejecutamos:

```bash
docker ps
```

<img width="1915" height="425" alt="Captura de pantalla 2026-01-20 114613" src="https://github.com/user-attachments/assets/26e85eb3-64b3-4262-8776-29f2f66b6bb1" />
### 2.3.4. Configuración de Permisos de Usuario

Para evitar el uso constante de sudo, añadimos el usuario Javi al grupo docker:

```bash
sudo usermod -aG docker javi
```

<img width="1919" height="629" alt="Captura de pantalla 2026-01-20 114733" src="https://github.com/user-attachments/assets/080dda51-0845-4f7e-9dfd-a6a704f3a643" />

## 3. Despliegue de la Máquina Vulnerable "Obsession"
### 3.1. Descarga del Laboratorio

Descargamos el archivo comprimido de la máquina Obsession desde el enlace oficial proporcionado por DockerLabs (alojado en MEGA).

<img width="1919" height="868" alt="Captura de pantalla 2026-01-20 115158" src="https://github.com/user-attachments/assets/a228595e-4061-41e6-925d-7c3e349a72f5" />
### 3.2. Preparación del Script de Despliegue

Nos ubicamos en el directorio de descargas y descomprimimos el laboratorio. Posteriormente, revisamos el script de despliegue auto_deploy.sh utilizando el editor nano:

```bash
nano auto_deploy.sh
```

<img width="1919" height="435" alt="Captura de pantalla 2026-01-20 120517" src="https://github.com/user-attachments/assets/b3fee272-856f-43f3-a075-de3820e910a0" />

Revisar este script es fundamental para comprender cómo se construye la imagen Docker y cómo se asigna la red al contenedor.

<img width="1920" height="1020" alt="Captura de pantalla 2026-01-20 121001" src="https://github.com/user-attachments/assets/402b54cf-4f68-466c-9fb7-72365d2a6ae3" />

### 3.3. Ejecución del Despliegue

Ejecutamos el script con permisos de superusuario para iniciar el despliegue de la máquina vulnerable:

```bash
sudo ./auto_deploy.sh obsession.tar
```

<img width="1920" height="1020" alt="Captura de pantalla 2026-01-20 121124" src="https://github.com/user-attachments/assets/1af4a14c-1aca-4242-9ff5-dd0c177acca0" />

El proceso finaliza correctamente, indicando que la máquina está activa en la siguiente dirección IP:

```bash
172.17.0.2
```

<img width="780" height="611" alt="Captura de pantalla 2026-01-28 090005" src="https://github.com/user-attachments/assets/f534163d-ae12-492d-9985-d61f8a195aea" />

## 4. Fase de Reconocimiento y Enumeración
### 4.1. Comprobación de Conectividad

Para verificar que la máquina es accesible, enviamos paquetes ICMP mediante el siguiente comando:

```bash
ping -c 2 172.17.0.2
```

El valor TTL = 64 confirma que el sistema operativo objetivo es Linux.

<img width="772" height="423" alt="Captura de pantalla 2026-01-28 090100" src="https://github.com/user-attachments/assets/da634f5d-31c3-43e0-be44-d76f936f74b3" />

### 4.2. Escaneo de Puertos con Nmap

Para identificar los servicios expuestos, realizamos un escaneo completo de puertos utilizando nmap:

```bash
nmap -p- -sVC --min-rate 5000 -n -Pn 172.17.0.2
```

Resultados obtenidos:

- Puerto 21 (FTP): Acceso anónimo habilitado y presencia de archivos sospechosos (chat-gonza.txt, pendientes.txt)

- Puerto 22 (SSH): Servicio abierto

- Puerto 80 (HTTP): Servidor Apache activo

<img width="839" height="674" alt="Captura de pantalla 2026-01-28 091328" src="https://github.com/user-attachments/assets/968b1d6f-859b-4a0b-91a0-d9e51785129d" />

### 4.3. Enumeración Web

Para identificar tecnologías web, utilizamos la herramienta whatweb mediante el siguiente comando:

```bash
whatweb http://172.17.0.2
```

Se identifica el título del sitio como "Russoski Coaching" y se detecta el correo electrónico expuesto:

russoski@dockerlabs.es

<img width="1919" height="331" alt="Captura de pantalla 2026-01-28 091440" src="https://github.com/user-attachments/assets/7440ed81-38d4-42ff-bdb3-d65d4575059a" />

Finalmente, accedemos al sitio web desde el navegador para realizar una inspección visual.

<img width="1920" height="1020" alt="Captura de pantalla 2026-01-28 092905" src="https://github.com/user-attachments/assets/1385d347-4ebe-4ad4-bda9-c877daa08f7d" />

Aunque la web es principalmente informativa, la vulnerabilidad crítica identificada se encuentra en el servicio FTP, que será el principal vector de ataque en fases posteriores.


