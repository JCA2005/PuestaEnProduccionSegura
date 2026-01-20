# Despliegue de HexStrike AI MCP con Gemini CLI en WSL (Kali Linux)

<img width="1536" height="1024" alt="image" src="https://github.com/user-attachments/assets/60202e5d-a734-4ddb-94a2-c6691f7d7e9a" />


HexStrike AI es una plataforma MCP de última generación que integra más de 150 herramientas de ciberseguridad junto con más de 12 agentes de IA autónomos. Su objetivo es permitir que Gemini CLI lleve a cabo auditorías de seguridad, escaneos automatizados, detección de vulnerabilidades y pruebas de penetración de forma inteligente.

---

## Requisitos necesarios

- **WSL2** activo en Windows con **Kali Linux**
  ```bash
  wsl -d kali-linux
  ```
- **Node.js 20 o superior**
- **Python 3.8 o superior**
- **4GB RAM mínimo (8GB recomendado)**
- **20GB de espacio libre**
- **Conexión a internet estable**

---

## Paso 1: Actualizar el sistema

```bash
wsl -d kali-linux
sudo apt update && sudo apt upgrade -y
```

---

## Paso 2: Verificar herramientas en Kali

```bash
which nmap gobuster ffuf sqlmap nikto
```

Si falta alguna:

```bash
sudo apt install nmap masscan gobuster feroxbuster ffuf dirb httpx nikto sqlmap wpscan dnsutils whois curl wget git -y
```

---

## Paso 3: Clonar HexStrike AI

```bash
cd ~
git clone https://github.com/0x4m4/hexstrike-ai.git
cd hexstrike-ai
```

---

## Paso 4: Crear entorno virtual

```bash
python3 -m venv hexstrike-env
source hexstrike-env/bin/activate
```

---

## Paso 5: Instalar dependencias

```bash
pip install -r requirements.txt
```

---

## Paso 6: Verificación

```bash
python3 hexstrike_server.py --help
```

<img width="1920" height="1020" alt="Captura de pantalla 2026-01-20 085335" src="https://github.com/user-attachments/assets/44f4a5a6-504a-404b-b597-3ea778ff2b1f" />


---

## Paso 7: Iniciar servidor MCP

```bash
cd ~/hexstrike-ai
source hexstrike-env/bin/activate
python3 hexstrike_server.py
```

<img width="1920" height="1020" alt="Captura de pantalla 2026-01-20 085510" src="https://github.com/user-attachments/assets/e8e79d68-0d4e-4dc9-bb92-7e0b44a54d79" />


---

## Paso 8: Comprobar estado

```bash
curl http://localhost:8888/health
```

---

## Paso 9: Configurar Gemini CLI

```bash
mkdir -p ~/.config/gemini-cli/
nano ~/.config/gemini-cli/settings.json
```

Verificar ruta:

```bash
ls -la /root/hexstrike-ai/hexstrike_mcp.py
pwd
```

---

## Paso 10: Probar Gemini CLI

```bash
gemini
```

<img width="1920" height="1020" alt="Captura de pantalla 2026-01-20 092211" src="https://github.com/user-attachments/assets/7ee7b0f7-ad34-4087-a4f7-54aef63118d5" />


Dentro:

```
Realiza un escaneo de puertos con nmap en 127.0.0.1 usando hexstrike-ai
```
