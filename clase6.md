# Reconocer emociones en imágenes de rostros
* https://www.jesusninoc.com/04/12/reconocer-emociones-de-las-caras-de-las-personas-que-hay-en-una-imagen-utilizando-el-modelo-fer-facial-expression-recognition/

--------
--------

# ¿Dónde está una persona?
* https://blog.palantir.com/the-future-of-drone-navigation-7236075fdedf
* GPS
* % batería
* https://www.jesusninoc.com/12/08/radar-ble-en-terminal-visualizador-de-proximidad-en-tiempo-real/
* https://github.com/jesusninoc/Shadowbroker
* https://www.jesusninoc.com/08/29/escribir-y-enviar-un-mensaje-a-un-dispositivo-bluetooth-desde-python/
* https://www.jesusninoc.com/08/27/escanear-dispositivos-bluetooth-caracteristicas-escribibles-y-el-atributo-handle-de-cada-caracteristicas-desde-python/

-----------
-----------

# Shadowbroker
Global Threat Intercept — Real-Time Geospatial Intelligence Platform
```Bash
git clone https://github.com/BigBodyCobain/Shadowbroker.git
cd Shadowbroker
./compose.sh up -d
```

--------
--------

# Deepfake de cámara

```Bash
mkdir face
cd face
python -m venv faceswap_env    
source faceswap_env/bin/activate
pip3.12 install insightface
pip3.12 install opencv-python
pip3.12 install numpy
pip3.12 install onnxruntime
ls ~/.insightface/models
```

```Python
import cv2
import insightface
import numpy as np
import os

# -----------------------------
# Cargar modelo de detección facial
# -----------------------------

app = insightface.app.FaceAnalysis(name="buffalo_l")
app.prepare(ctx_id=0, det_size=(640, 640))

# -----------------------------
# Ruta del modelo FaceSwap
# -----------------------------

model_path = os.path.expanduser(
    "~/.insightface/models/inswapper_128.onnx"
)

if not os.path.exists(model_path):
    print("No se encuentra el modelo:", model_path)
    print("Comprueba que existe en ~/.insightface/models/")
    exit()

# -----------------------------
# Cargar modelo de swap
# -----------------------------

swapper = insightface.model_zoo.get_model(
    model_path,
    providers=["CPUExecutionProvider"]
)

# -----------------------------
# Cargar imagen origen
# -----------------------------

source_img = cv2.imread("cara.jpg")

if source_img is None:
    print("No se pudo cargar cara.jpg")
    exit()

source_faces = app.get(source_img)

if len(source_faces) == 0:
    print("No se detectó ninguna cara en la imagen")
    exit()

source_face = source_faces[0]

# -----------------------------
# Abrir webcam
# -----------------------------

cap = cv2.VideoCapture(0)

if not cap.isOpened():
    print("No se pudo abrir la webcam")
    exit()

print("FaceSwap iniciado. Pulsa ESC para salir.")

# -----------------------------
# Loop principal
# -----------------------------

while True:

    ret, frame = cap.read()

    if not ret:
        break

    faces = app.get(frame)

    for face in faces:

        frame = swapper.get(
            frame,
            face,
            source_face,
            paste_back=True
        )

    cv2.imshow("FaceSwap Live", frame)

    if cv2.waitKey(1) & 0xFF == 27:
        break

# -----------------------------
# Cerrar
# -----------------------------

cap.release()
cv2.destroyAllWindows()
```
