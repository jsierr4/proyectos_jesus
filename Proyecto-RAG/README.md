# Práctica RAG con LangChain + ChromaDB + Ollama + Chainlit

Sistema de Generación Aumentada por Recuperación (RAG) sobre un documento PDF, con interfaz de chat.

## Estructura del proyecto

```
rag_practica/
├── practica_rag.ipynb   <- Notebook con toda la lógica explicada paso a paso
├── app.py               <- Interfaz de chat con Chainlit
├── documento.pdf        <- El PDF que queremos consultar (poner aquí)
├── chroma_db/           <- Se genera al ejecutar (vectores persistidos)
└── README.md
```

## Requisitos

1. Tener Ollama instalado y corriendo: https://ollama.com
2. Descargar los modelos:
   ```bash
   ollama pull llama3.1
   ollama pull nomic-embed-text
   ```
3. Instalar dependencias de Python:
   ```bash
   pip install langchain langchain-community langchain-ollama langchain-chroma chromadb pypdf langgraph chainlit
   ```
4. Colocar el PDF en la raíz del proyecto con el nombre `documento.pdf` (o cambiar `PDF_PATH` en el código).

## Cómo ejecutar

### Opción A: Notebook (paso a paso)

Abrir `practica_rag.ipynb` en Jupyter o VS Code y ejecutar las celdas en orden. La primera vez tardará un poco porque hay que indexar el PDF.

### Opción B: Interfaz de chat con Chainlit

```bash
chainlit run app.py -w
```

Se abre el navegador en `http://localhost:8000` con una interfaz de chat. La primera vez se indexa el PDF; las siguientes reutiliza la base de datos guardada en `chroma_db/`.

## Diagrama de flujo

```
                    +---------------------+
                    |  Usuario escribe    |
                    |  una pregunta       |
                    +----------+----------+
                               |
                               v
                    +---------------------+
                    |   Agente (LLM)      |
                    |   decide si necesita|
                    |   buscar            |
                    +----------+----------+
                               |
                               v
                    +---------------------+
                    | Herramienta:        |
                    | buscar_en_documento |
                    +----------+----------+
                               |
                               v
                    +---------------------+
                    |  Embeddings de la   |
                    |  pregunta           |
                    | (nomic-embed-text)  |
                    +----------+----------+
                               |
                               v
                    +---------------------+
                    |  ChromaDB busca     |
                    |  los TOP_K=3        |
                    |  chunks más cercanos|
                    +----------+----------+
                               |
                               v
                    +---------------------+
                    |  Devuelve fragmentos|
                    |  con número de      |
                    |  página             |
                    +----------+----------+
                               |
                               v
                    +---------------------+
                    |   LLM (llama3.1)    |
                    |  redacta respuesta  |
                    |  citando la página  |
                    +----------+----------+
                               |
                               v
                    +---------------------+
                    |   Respuesta al      |
                    |   usuario           |
                    +---------------------+
```

### Fase de indexación (se hace una sola vez)

```
documento.pdf
     |
     v
PyPDFLoader  -->  RecursiveCharacterTextSplitter  -->  OllamaEmbeddings  -->  ChromaDB
   (carga)         (chunks 800/100)                    (vectoriza)         (guarda)
```

## Despliegue

Opciones según el enunciado de la práctica 4.8.4, ordenadas de menor a mayor complejidad:

- **Hugging Face Spaces:** soporta Chainlit. Hay que subir el repo y dejarlo público. Tier gratuito limitado en hardware.
- **Streamlit Cloud:** solo para apps Streamlit (no aplica aquí).
- **VPS con Docker (DigitalOcean, Hetzner):** control total, pero hay que configurar el servidor, dominio, HTTPS, etc.

Nota importante: si se despliega en la nube hay que cambiar Ollama (que corre en local) por un proveedor con API (Claude, GPT, Gemini), porque los servidores gratuitos no llevan modelos LLM cargados.

## Justificación de parámetros

Ver la última celda del notebook.
