Excenergy-AirConditioner-Control
================================

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
~/venv-ruuvi/bin/python3 -m ruuvitag_sensor -g E6:45:04:1B:1F:76
```

---

# 📌 2. Instalación del entorno Midea (LAN control)

Usamos la combinación:

- **msmart-ng** → Descubre key/token
- **midea-beautiful-air** → Control LAN estable
- **midea-beautiful-air-cli** → Testing y comandos manuales

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

Ejemplo de salida:
```json
{
  "id": "150633094208661",
  "ip": "192.168.1.4",
  "token": "63f9a8e6b4...",
  "key": "c81d2a71b9..."
}
```

Ese token/key se guarda luego en variables globales de Node-RED.

---

# 📌 4. Probar control desde CLI (midea-beautiful-air-cli)

### 🔍 Descubrir equipo con credenciales en cloud
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

### 🌬 Cambiar velocidad del ventilador
```bash
midea-beautiful-air-cli set --ip 192.168.1.4 --fan-speed 60
```

### ↕ Activar swing
```bash
midea-beautiful-air-cli set --ip 192.168.1.4 --vertical-swing on
```

### ⚡ Turbo
```bash
midea-beautiful-air-cli set --ip 192.168.1.4 --turbo on
```

---

# 📌 5. Node-RED – Flujo completo

El flujo de Node-RED incluye:

✔ Lectura periódica del RuuviTag  
✔ Control inteligente por histéresis  
✔ Ajuste autónomo del AC  
✔ Cambios manuales (temperatura, modo, swing, turbo, fan speed)  
✔ Entorno Python por venv para RuuviTag  
✔ Entorno Python por venv para Midea  

En este archivo se incluye:

- Instalación de dependencias automáticas
- Tokens guardados globalmente
- Scripts Python encapsulados
- Lógica de control automático
- Comandos individualizados

**El export del flujo completo está en el archivo `.json` entregado previamente.**

---

# 📌 6. Lógica de control automático (JS Control System)

Basado en histéresis:

- reduce cambios innecesarios
- evita oscilaciones
- protege compresor
- mantiene estabilidad térmica

Incluye:
- deadband configurable  
- tiempo mínimo entre acciones  
- límites mínimo/máximo  
- fan speed opcional  

---

# 📌 7. Scripts Python incluidos en Node-RED

### ✔ Lectura RuuviTag  
(Asíncrono, obtiene último paquete válido)

### ✔ Obtener key/token Midea  
(usando msmart-ng)

### ✔ Control LAN  
(set temperature, mode, fan speed, swing, turbo…)

---

# 📌 8. Créditos

- RuuviTag Sensor Library  
- msmart-ng  
- midea-beautiful-air  
- node-red-contrib-python-venv  

---

# 📌 9. Licencia

MIT  
