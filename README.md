# Remote Climate Control System (Raspberry Pi + Node-RED + RuuviTag + Midea AC)

Este proyecto implementa un sistema completo de **medición**, **control inteligente** y **automatización local** para un aire acondicionado Midea, utilizando:

- **Raspberry Pi**
- **Node-RED**
- **Sensor Bluetooth RuuviTag**
- **Control LAN de AC Midea (msmart-ng + midea-beautiful-air)**
- **Control PID/Histerésis**
- **Python en entornos virtuales (node-red-contrib-python-venv)**

Incluye:
✔ Lectura de temperaturas vía Bluetooth  
✔ Control PID/histéresis adaptable  
✔ Ajuste autónomo de temperatura del AC  
✔ Control de velocidad del ventilador  
✔ Control de swing  
✔ Control Turbo  
✔ Key/Token LAN vía msmart-ng  
✔ Comandos locales vía midea-beautiful-air-cli  
✔ Automatización periódica  
✔ Entorno modular y mantenible  

---

# 📌 1. Instalación del entorno RuuviTag

Basado en:  
https://github.com/ttu/ruuvitag-sensor  
Instalación en Raspberry Pi:  
https://github.com/ttu/ruuvitag-sensor/blob/master/install_guide_pi.md  

### 🔧 Dependencias del sistema
```bash
sudo apt update
sudo apt install python3-setuptools python3-pip bluetooth bluez libbluetooth-dev
```

### 🔧 Crear entorno virtual
```bash
python3 -m venv ~/venv-ruuvi
source ~/venv-ruuvi/bin/activate
pip install ruuvitag-sensor
deactivate
```

### 🔍 Lectura de prueba
```bash
~/venv-ruuvi/bin/python3 -m ruuvitag_sensor -g AA:BB:CC:DD:EE:FF
```

---

# 📌 2. Instalación del entorno Midea (LAN control)

Usamos:

- **msmart-ng** → Descubre key/token  
- **midea-beautiful-air** → Control LAN  
- **midea-beautiful-air-cli** → Testing  

### 🔧 Crear entorno virtual
```bash
python3 -m venv ~/venv-midea
source ~/venv-midea/bin/activate
pip install msmart-ng
pip install midea-beautiful-air
pip install midea-beautiful-air-cli
deactivate
```

---

# 📌 3. Obtener token y key (msmart-ng)

```bash
source ~/venv-midea/bin/activate
msmart-ng discover
```

Ejemplo:
```json
{
  "id": "150633094208661",
  "ip": "192.168.1.4",
  "token": "63f9a8e6b4...",
  "key": "c81d2a71b9..."
}
```

---

# 📌 4. Probar control desde CLI (midea-beautiful-air-cli)

### 🔍 Descubrir equipo (cloud)
```bash
midea-beautiful-air-cli discover --account "email" --password "pass" --app MSmartHome
```

### 🔧 Leer estado LAN
```bash
midea-beautiful-air-cli status --ip 192.168.1.4 --token TOKEN --key KEY
```

### ❄ Cambiar temperatura
```bash
midea-beautiful-air-cli set --ip 192.168.1.4 --target-temperature 23 --token TOKEN --key KEY
```

### 🔄 Encender/Apagar
```bash
midea-beautiful-air-cli set --ip 192.168.1.4 --power on
```

### 🌬 Velocidad del ventilador
```bash
midea-beautiful-air-cli set --ip 192.168.1.4 --fan-speed 60
```

### ↕ Swing
```bash
midea-beautiful-air-cli set --ip 192.168.1.4 --vertical-swing on
```

### ⚡ Turbo
```bash
midea-beautiful-air-cli set --ip 192.168.1.4 --turbo on
```

---

# 📌 5. Node-RED – Flujo completo

El flujo contiene:

✔ Lectura periódica del RuuviTag  
✔ Control inteligente por histéresis  
✔ Ajuste autónomo del AC  
✔ Controles manuales (temperatura, modo, swing, turbo, fan speed)  
✔ Scripts Python encapsulados  
✔ Tokens globales  
✔ Entorno Python independiente por venv  

El archivo `.json` del flujo completo se incluye por separado.

---

# 📌 6. Lógica de control automático

Basado en histéresis:

- evita oscilaciones  
- protege el compresor  
- mantiene estabilidad térmica  
- controla fan speed opcional  

Incluye:
- deadband configurable  
- tiempo mínimo entre acciones  
- límites mínimo/máximo  

---

# 📌 7. Scripts Python incluidos en Node-RED

### ✔ Lectura RuuviTag  
### ✔ Obtener key/token Midea  
### ✔ Control LAN del AC  

---

# 📌 8. Acceso Remoto – VNC (Escritorio remoto)

### 🔧 Activar VNC desde interfaz oficial
```bash
sudo raspi-config
```

Ruta:
```
Interface Options → VNC → Enable
```

### 🔧 Activar VNC automáticamente (CLI)
```bash
sudo raspi-config nonint do_vnc 0
```

### 🔧 Forzar inicio con escritorio gráfico
```bash
sudo raspi-config
```

Ruta:
```
System Options → Boot / Auto Login → Desktop Autologin
```

---

# 📌 9. Acceso Remoto Seguro con Tailscale (SSH + VNC)

Basado en la guía oficial:  
https://tailscale.com/learn/how-to-ssh-into-a-raspberry-pi

### 🔧 Instalar Tailscale
```bash
curl -fsSL https://tailscale.com/install.sh | sh
```

### 🔧 Iniciar Tailscale
```bash
sudo tailscale up
```

Abrir el enlace provisto y autorizar el dispositivo.

### 🟦 Obtener IP Tailscale
```bash
tailscale ip -4
```

### 🟦 SSH vía Tailscale
```bash
ssh pi@IP_TAILSCALE
```

### 🟦 VNC vía Tailscale
Usar **VNC Viewer** → conectar a:
```
IP_TAILSCALE
```

### 🔧 Arranque automático
```bash
sudo systemctl enable tailscaled
sudo systemctl start tailscaled
```

---

# 📌 10. Créditos

- RuuviTag Sensor Library  
- msmart-ng  
- midea-beautiful-air  
- node-red-contrib-python-venv  

---

# 📌 11. Licencia

MIT
