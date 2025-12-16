# Manual de configuración de IA agéntica en WSL

<img width="958" height="480" alt="image" src="https://github.com/user-attachments/assets/58cb5015-cb53-4585-8df1-87425f097fb6" />


Este tutorial explica cómo configurar un entorno Linux en Windows mediante WSL, instalar Node.js y Ollama, y ejecutar un modelo de IA agéntica ligero, incluyendo problemas comunes y soluciones.

---

## 1. Configuración de WSL y Ubuntu

**Qué es WSL:**
Windows Subsystem for Linux (WSL) permite ejecutar Linux dentro de Windows sin instalar un sistema operativo adicional.

**Verificar distribuciones existentes:**

```powershell
wsl -l -v
```

**Instalar Ubuntu (recomendado):**

```powershell
wsl --install -d Ubuntu
```

> Tras la instalación, se pedirá un nombre de usuario y contraseña la primera vez que se abra Ubuntu.

---

## 2. Actualización del sistema Ubuntu

```bash
sudo apt update && sudo apt upgrade -y
```

> Esto asegura que todos los paquetes estén al día.

---

## 3. Instalación de Node.js usando NVM

**Instalar NVM:**

```bash
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash
source ~/.bashrc
```

**Instalar Node.js versión 20:**

```bash
nvm install 20
nvm use 20
```

**Verificar instalación:**

```bash
node -v
npm -v
```

> Node.js es necesario para ejecutar herramientas de IA agéntica basadas en JavaScript. NVM permite gestionar varias versiones de Node.

---

## 4. Instalación de Ollama

Ollama permite ejecutar modelos de pesos abiertos de forma local.

**Instalación:**

```bash
curl -fsSL https://ollama.com/install.sh | sh
```

**Verificar instalación:**

```bash
ollama --version
```

---

## 5. Descargar y ejecutar un modelo de IA

**Intento inicial con modelo grande:**

```bash
ollama pull qwen3-coder
ollama run qwen3-coder
```

**Problema detectado:**

```
Error: 500 Internal Server Error: model requires more system memory (12.9 GiB) than is available (8.8 GiB)
```

> Este error se debe a que el modelo requiere más memoria RAM de la disponible en WSL.

**Solución: usar un modelo más ligero:**

```bash
ollama pull qwen2.5-coder:7b
ollama run qwen2.5-coder:7b
```

**Prueba básica:**

```text
Explica qué es WSL en una frase
```

<img width="1920" height="1020" alt="Captura de pantalla 2025-12-16 202839" src="https://github.com/user-attachments/assets/7edf55b8-7289-4354-a740-35aeaa29c638" />


> El modelo ligero funciona correctamente y no supera la memoria disponible.

---

## 6. Resumen de la configuración

1. **WSL2 con Ubuntu:** Entorno Linux dentro de Windows.
2. **Node.js 20 + npm:** Instalación mediante NVM.
3. **Ollama:** Ejecución de modelos de pesos abiertos.
4. **Modelo ligero `qwen2.5-coder:7b`:** Compatible con la memoria disponible.

> Con esto se dispone de una herramienta de IA agéntica funcional lista para usar en terminal.

---

## 7. Notas importantes

* Siempre verificar la memoria disponible antes de usar modelos grandes.
* Los modelos ligeros son recomendables si WSL tiene menos RAM.
* Todos los comandos deben ejecutarse **dentro de Ubuntu en WSL**, no en PowerShell ni CMD.
* Node.js y Ollama son imprescindibles para el funcionamiento de la IA agéntica.

---
