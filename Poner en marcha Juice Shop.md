# <h1 align="center">Poner en marcha Juice Shop</h1>

<p align="center">
  <img width="400" height="480" alt="image" src="https://github.com/user-attachments/assets/9a0c70bf-caa3-4f22-b7a0-cd349a4f94e1" />
</p>


## 1. Introducción y Objetivos

**OWASP Juice Shop** es una aplicación web intencionalmente vulnerable diseñada como plataforma de entrenamiento para la identificación, análisis y explotación de vulnerabilidades web comunes. El objetivo de este ejercicio es:

- Desplegar exitosamente la aplicación en un contenedor Docker
- Familiarizarse con la plataforma de entrenamiento
- Identificar y catalogar vulnerabilidades del sistema
- Documentar hallazgos de seguridad

---

## 2. Requisitos Previos

- **Docker** instalado y funcionando en WSL o en el sistema host
- **Puerto 3000** disponible en el host
- **Conexión a Internet** (solo para descargar la imagen)
- **Mínimo 512MB de RAM** libre
- **Acceso administrativo** para ejecutar comandos Docker

---

## 3. Paso 1: Descargar la Imagen Docker de Juice Shop

El primer paso consiste en descargar la imagen Docker oficial de OWASP Juice Shop desde el registro de contenedores.

```bash
docker pull bkimmirich/juice-shop
```
<img width="1919" height="1008" alt="Captura de pantalla 2026-02-02 121709" src="https://github.com/user-attachments/assets/d98291a6-1543-4ef8-b601-323f166d97e4" />

Este comando descargará automáticamente las capas necesarias de la imagen, incluyendo el sistema operativo base, Node.js y todas las dependencias de la aplicación.



---

## 4. Paso 2: Verificar la Imagen Descargada

Una vez completada la descarga, se puede verificar que la imagen está disponible localmente:

```bash
docker images | grep juice-shop
```

O simplemente listar todas las imágenes:

```bash
docker images
```

---

## 5. Paso 3: Ejecutar el Contenedor de Juice Shop

Ahora procedemos a lanzar un contenedor basado en la imagen descargada, exponiendo el puerto 3000:

```bash
docker run --rm -p 3000:3000 bkimmirich/juice-shop
```

**Explicación de parámetros:**
- `--rm`: Elimina automáticamente el contenedor al detenerlo
- `-p 3000:3000`: Mapea el puerto 3000 del contenedor al puerto 3000 del host
- `bkimmirich/juice-shop`: Nombre de la imagen Docker

<img width="927" height="463" alt="Captura de pantalla 2026-02-02 121852" src="https://github.com/user-attachments/assets/d6c395f8-bf78-49b1-8fb8-d177ed0044f2" />

Cuando veas el mensaje **"Server listening on port 3000"**, la aplicación estará lista para usar.

---

## 6. Paso 4: Acceder a la Aplicación

Abre tu navegador web e ingresa a:

```
http://localhost:3000
```

Se desplegará la página principal de OWASP Juice Shop con el catálogo de productos.

<img width="1919" height="865" alt="Captura de pantalla 2026-02-02 121956" src="https://github.com/user-attachments/assets/b788e517-0771-4d67-968c-5e6728d703a2" />

La aplicación incluye:
- Catálogo de productos con descripciones
- Funcionalidad de búsqueda
- Carrito de compras
- Sistema de usuarios y autenticación
- Panel de administración
- Otras funcionalidades típicas de una tienda online

---

## 7. Detección y Catalogación de Vulnerabilidades

### 7.1. Vulnerabilidad #1: Cross-Site Scripting (XSS)

#### 7.1.1. Descripción
Una vulnerabilidad **Stored XSS** (Cross-Site Scripting Almacenado) fue identificada en el campo de búsqueda de la aplicación. Esta vulnerabilidad permite inyectar código JavaScript malicioso que se ejecutará en el navegador de otros usuarios.

#### 7.1.2. Pasos para Reproducir

1. Dirígete a la página principal de Juice Shop
2. Localiza la barra de búsqueda en la parte superior
3. Ingresa el siguiente payload de prueba:

```javascript
<img src=x onerror="alert('XSS')">
```

4. Presiona Enter o haz clic en el botón de búsqueda

#### 7.1.3. Evidencia

<img width="1919" height="969" alt="Captura de pantalla 2026-02-02 122129" src="https://github.com/user-attachments/assets/78791486-43e6-4f51-bcb9-345913a1455f" />


El navegador ejecuta el código JavaScript inyectado, mostrando un alert (ventana emergente) con el mensaje "XSS". Esta es una prueba de concepto que demuestra la falta de sanitización de entrada.

#### 7.1.4. Análisis Técnico

| Aspecto | Detalles |
|--------|----------|
| **Tipo de Vulnerabilidad** | Reflected/Stored XSS |
| **Severidad** | Alta (CVSS 7.1) |
| **Vector de Ataque** | Campo de búsqueda |
| **Causa Raíz** | Falta de validación y sanitización de entrada |
| **Impacto** | Ejecución de código arbitrario en el navegador |
| **OWASP Top 10** | A03:2021 – Injection |

#### 7.1.5. Impacto Potencial

Un atacante podría:
- Robar cookies de sesión (hijacking de sesión)
- Capturar credenciales de usuarios
- Redirigir a sitios maliciosos
- Modificar contenido de la página
- Distribuir malware
- Realizar acciones en nombre del usuario