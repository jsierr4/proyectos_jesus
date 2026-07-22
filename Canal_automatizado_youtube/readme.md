Esto es un proyecto que pensé y diseñé yo solo con ayuda de Claude.

Consiste en un flujo completo para generar, editar y subir videos a youtube lo que me permite crear canales totalmente automatizados que generan ingresos.
Aunque llevo pocos meses con ello aún esta en fase de pruebas pero ya esta funcionando bien.


Para ejecutar este proyecto necesitas:

1. client_secrets.json — OAuth 2.0 credentials de Google Cloud Console
   (YouTube Data API v3, tipo Desktop)

2. youtube_token.pickle — se genera automáticamente al autenticarse
   por primera vez

3. Variables de entorno o archivo .env con:
   - ANTHROPIC_API_KEY
   - ELEVENLABS_API_KEY
   - PEXELS_API_KEY
   - PIXABAY_API_KEY