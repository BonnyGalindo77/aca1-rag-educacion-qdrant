# ACA 1 · Computación en la Nube — RAG de permanencia estudiantil sobre Qdrant Cloud

[![Abrir en Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/BonnyGalindo77/aca1-rag-educacion-qdrant/blob/main/ACA1_RAG_Educacion_Qdrant.ipynb)

**CUN · Especialización en Inteligencia Artificial** · Docente: Hamilton Rivera Flor
**Equipo:** Bonny Darhyll Brayan Galindo Santos · Camilo José Rodríguez Ayubi · Andrés Alberto López Herrera
**Pilar:** 2 — Educación (consulta académica, normatividad institucional y permanencia)

Este proyecto implementa la ingesta, el *chunking*, la vectorización y la indexación semántica de un corpus sobre permanencia estudiantil en **Qdrant Cloud**. Combina tres documentos normativos colombianos con el dataset **UCI Predict Students' Dropout and Academic Success**, y deja lista la búsqueda Top-k que alimentará el sistema RAG.

| Capa | Herramienta |
|---|---|
| Base vectorial | Qdrant Cloud (free tier 1 GB), métrica coseno, vectores nombrados `gemini` (768 d) y `e5` (384 d) |
| Embeddings | Gemini `gemini-embedding-2` (principal) · `intfloat/multilingual-e5-small` (línea base) |
| Capa SQL | SQLite (agregaciones del dataset → fichas analíticas y metadatos) |
| Chunking | `RecursiveCharacterTextSplitter` (LangChain) · 1.000 caracteres · 15 % de solapamiento |
| Entorno | Google Colab / Jupyter |

## Estructura

```
├── ACA1_RAG_Educacion_Qdrant.ipynb   # notebook reproducible (pasos 1 a 5)
├── requirements.txt
├── .env.example                      # plantilla de credenciales (el .env real no se versiona)
├── corpus/                           # PDFs (los descarga el notebook)
├── datos/                            # CSV del dataset
└── resultados/                       # métricas, figuras y resumen JSON para el póster
```

## Cómo ejecutarlo (unos 15 minutos la primera vez)

### 1. Crear el clúster en Qdrant Cloud (gratis)
1. Entre a <https://cloud.qdrant.io> y regístrese con su cuenta de Google o GitHub.
2. **Create cluster → Free**. Proveedor: GCP o AWS, la región más cercana disponible (por ejemplo `us-east`). Nombre: `aca1-cun`.
3. Cuando el estado esté en verde, copie la **Cluster URL**: termina en `:6333`.
4. En **API Keys → Create**, genere una llave con permiso de escritura y cópiela de inmediato (solo se muestra una vez).

### 2. Obtener la API key de Gemini (gratis)
1. Entre a <https://aistudio.google.com/apikey> con una cuenta de Google.
2. **Create API key**, en un proyecto nuevo o existente. Cópiela.

### 3. Configurar las credenciales
Copie `.env.example` como `.env` y pegue los tres valores. En Colab hay dos opciones:
- subir el `.env` al panel de archivos (ícono de carpeta), o
- registrar `QDRANT_URL`, `QDRANT_API_KEY` y `GOOGLE_API_KEY` en **🔑 Secretos** y activar el acceso del notebook.

### 4. Ejecutar
Abra el notebook con el botón **Abrir en Colab** de arriba, active **GPU T4** (opcional, acelera E5) y use **Entorno de ejecución → Ejecutar todas**. La primera corrida tarda entre 10 y 20 minutos, sobre todo por las pausas que respetan la cuota gratuita de Gemini. Las siguientes son mucho más rápidas porque los embeddings quedan en caché.

Si una descarga de PDF falla, súbalo manualmente a `corpus/` con el nombre que indica la celda y vuelva a ejecutarla.

## Seguridad
- Las llaves se leen únicamente desde variables de entorno (`.env` o Secretos de Colab) y el notebook nunca imprime su valor.
- `.env` está en `.gitignore`. Antes de publicar, verifique con `git status` que no aparezca.
- Si una llave queda expuesta por error, revóquela desde la consola de Qdrant o de AI Studio y genere otra.

## Resultados
Al terminar, `resultados/` contiene `resumen_aca1.json`, `validacion_detalle.csv` y las figuras usadas en el póster: deserción por factor, selección del tamaño de chunk, Top-k de ejemplo y validación.

## Referencias principales
- Lewis, P., et al. (2020). Retrieval-augmented generation for knowledge-intensive NLP tasks. *NeurIPS, 33*, 9459–9474.
- Ministerio de Educación Nacional. (2015). *Guía para la implementación del modelo de gestión de permanencia y graduación estudiantil en IES*.
- Ministerio de Educación Nacional. (2016). *Lineamientos de política de bienestar para instituciones de educación superior*.
- Realinho, V., Vieira Martins, M., Machado, J., & Baptista, L. (2021). *Predict students' dropout and academic success* [Conjunto de datos]. UCI. https://doi.org/10.24432/C5MC89
- Wang, L., et al. (2024). *Multilingual E5 text embeddings: A technical report*. arXiv:2402.05672.
