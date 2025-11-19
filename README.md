Pontia MLOps Tutorial - Ane Martínez

Este repositorio incluye la evaluación de la asignatura introducción a DevOps. 

El objetivo de este trabajo ha sido construir un flujo de MLOps, aplicando buenas prácticas de DevOps para un proyecto de ML basado en datos.

Arquitectura del proyecto:
El repositorio está dividido en 3 fases que se ejecutan mediante pipelines:
* Integration (test unitarios)
* Build (entrenamiento, tests del modelo, registro en MLflow)
* Deploy (construcción y despliegue de la imagen)

Estructura del repositorio
.
├── .github/workflows/     # Pipelines de CI/CD
│   ├── integration.yml
│   ├── build.yml
│   └── deploy.yml
│
├── data/raw/              # Dataset usado en el entrenamiento
├── deployment/            # Dockerfile y API para inferencia
├── model_tests/           # Tests sobre el modelo entrenado
├── models/                # Artefactos generados localmente
├── scripts/               # Scripts auxiliares (registro, consultas)
├── src/                   # Código principal de ML
├── unit_tests/            # Tests unitarios
├── requirements.txt
└── README.md

Variables de entorno utilizadas:
* MLFFLOW_URL, EXPERIMENT_NAME, MODEL_NAME, AZURE_STORAGE_CONNECTION_STRING.
* Para el despliegue: AZURE_CREDENTIALS, ACR_NAME, ACR_USERNAME, ACR_PASSWORD, AZURE_RESOURCE_GROUP, AZURE_REGION, AZURE_CONTAINER_NAME, IMAGE_NAME, MODEL_ALIAS
Estas variables se configuran como secretos en Github para ser utilizadas por los workflows.

Pipelines
1. integration.yml: valida que el proyecto funciona correctamente. Instala las dependencias, ejecuta los test unitarios. La rama main está en cierto modo protegida, por tanto requiere que el código que se quiera poner en la rama main pase este workflow.
2. build.yml: automatiza el proceso de entrenamiento del modelo. Descarga el dataset, ejecuta el archivo main.py (en la carpeta src) para entrenar el modelo, guarda los artefactos (model.pkl), ejecuta el test del modelo y registra el modelo usando el archivo register_model.py (scripts).
3. deploy.yml: construye y configura la imagen en azure. Hace login en azure y ACR. Build/push de la imagen usando el Dockerfile.

Entrenamiento del modelo (archivo main.py)
Este scripy carga y procesa los datos, entrena el modelo y guarda los artefactos en la carpeta models. Para registrar el modelo se utiliza el archivo register_model.py

Tests:
* Unitarios: unit_tests (se ejecutan en la integración)
* Test del modelo: model_tests (se ejecutan en el build).

