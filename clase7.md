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
- Analizar cómo correlacionar horarios de actividad para identificar a un posible autor de un ataque.
- Estudiar cómo pequeñas filtraciones de información pueden revelar la identidad de un usuario anónimo.
- Analizar patrones de conexión para detectar uso de redes de anonimato.
- Comparar estilos de escritura en mensajes para identificar autores (análisis estilométrico).
- Analizar cómo la actividad online puede revelar la zona horaria de un atacante.
- Detectar qué idioma utiliza realmente un atacante analizando errores lingüísticos.
- Analizar patrones de actividad que revelan horarios laborales del atacante.
- Detectar reutilización de nombres de usuario en distintos servicios.
- Analizar cómo pequeñas pistas en código o comentarios pueden revelar la identidad del desarrollad

--------------
--------------

# Enviar señales de apagado
* jesusninoc.com/09/02/prueba-de-comandos-para-apagar-luces-inteligentes-con-python-y-bleak-envio-de-comandos-a-leds-con-valores-hex/

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

-------------
-------------

# Dispositivos cercanos (vecinos, gatos, la gente por la calle)
```Python
import asyncio
from bleak import BleakScanner

seen_devices = set()


def estimate_people(num_devices):

    if num_devices == 0:
        return "🟢 Parece que estás solo"

    if num_devices <= 2:
        return "🟡 Puede haber una persona cerca"

    if num_devices <= 5:
        return "🟠 Varias personas cerca"

    return "🔴 Muchas personas cerca"


async def scan():

    global seen_devices

    print("\nRADAR BLE - detección de personas cercanas\n")

    while True:

        devices = await BleakScanner.discover(return_adv=True)

        current_devices = set()

        for address, (device, adv) in devices.items():
            current_devices.add(address)

        new_devices = current_devices - seen_devices

        print("\n━━━━━━━━━━━━━━━━━━━━━━")
        print("Dispositivos detectados:", len(current_devices))
        print(estimate_people(len(current_devices)))

        if new_devices:
            print("\n🆕 Nuevos dispositivos detectados:")

            for address, (device, adv) in devices.items():

                if address in new_devices:

                    name = device.name if device.name else "desconocido"
                    rssi = adv.rssi

                    print("Nombre:", name)
                    print("ID:", address)
                    print("RSSI:", rssi)

        seen_devices = current_devices

        print("━━━━━━━━━━━━━━━━━━━━━━")

        await asyncio.sleep(5)


asyncio.run(scan())
```
