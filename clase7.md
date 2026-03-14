# Aplicaciones
- Detectar qué apps comparten datos con las mismas empresas
- Relacionar una exfiltraxuon con GPS
- Analizar metadatos de miles de fotos de redes sociales
- Detectar estafas analizando anuncios online
- Analizar cuánto te “perfilan” las webs
- Analizar patrones de escritura
- Detectar relojes inteligentes
- Detectar auriculares inalámbricos
- Analizar nombres curiosos de dispositivos
- Analizar datos de aplicaciones móviles
- Radiografía digital de un día de vida con datos

--------------
--------------

# Radar de dispositivos Bluetooth Low Energy: identificación y análisis de dispositivos cercanos
```Bash
mkdir blue3
cd blue3
python3.12 -m venv blue
source blue/bin/activate
nano code.py
pip3.12 install bleak
python3.12 code.py
deactivate
```

```Python
import asyncio
from bleak import BleakScanner
import math


def estimate_distance(rssi, tx_power=-59):
    if rssi is None:
        return "desconocida"

    ratio = rssi / tx_power

    if ratio < 1:
        return round(pow(ratio, 10), 2)
    else:
        return round((0.89976) * pow(ratio, 7.7095) + 0.111, 2)


def classify_device(name, uuids):

    if name:
        name = name.lower()

        if "iphone" in name or "android" in name:
            return "📱 móvil"

        if "watch" in name:
            return "⌚ smartwatch"

        if "buds" in name or "airpods" in name:
            return "🎧 auriculares"

        if "tv" in name or "samsung" in name:
            return "📺 televisión"

        if "oral" in name or "toothbrush" in name:
            return "🪥 cepillo inteligente"

        if "pet" in name or "dog" in name:
            return "🐶 collar mascota"

    if uuids:
        uuids = str(uuids).lower()

        if "180d" in uuids:
            return "⌚ wearable / pulsómetro"

        if "181a" in uuids:
            return "🌡 sensor ambiental"

    return "❓ dispositivo BLE"


async def scan():

    print("\nRADAR BLE - dispositivos cercanos\n")

    devices = await BleakScanner.discover(return_adv=True)

    for address, (device, adv) in devices.items():

        rssi = adv.rssi
        distance = estimate_distance(rssi)

        device_type = classify_device(device.name, adv.service_uuids)

        print("━━━━━━━━━━━━━━━━━━━━━━━━")
        print("Nombre:", device.name)
        print("ID:", address)
        print("RSSI:", rssi)
        print("Distancia estimada:", distance, "m")
        print("Tipo probable:", device_type)

        if adv.service_uuids:
            print("Servicios BLE:", adv.service_uuids)

        print("━━━━━━━━━━━━━━━━━━━━━━━━")


asyncio.run(scan())
```
