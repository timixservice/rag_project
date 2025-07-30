# 🐳 Kotaemon Docker Deployment Guide

Este directorio contiene todos los archivos necesarios para desplegar Kotaemon en Docker de manera eficiente y escalable.

## 📋 Archivos Incluidos

### Requirements Files
- **`requirements.txt`** - Dependencias completas para producción (recomendado)
- **`requirements-minimal.txt`** - Dependencias mínimas para despliegues básicos

### Docker Configuration
- **`Dockerfile.example`** - Dockerfile optimizado para Kotaemon
- **`docker-compose.example.yml`** - Orquestación completa con servicios opcionales

## 🚀 Opciones de Despliegue

### 1. Despliegue Básico con Docker

```bash
# Construir la imagen
docker build -f Dockerfile.example -t kotaemon:latest .

# Ejecutar el contenedor
docker run \
  -e GRADIO_SERVER_NAME=0.0.0.0 \
  -e GRADIO_SERVER_PORT=7860 \
  -e OPENAI_API_KEY=your_openai_key \
  -e COHERE_API_KEY=your_cohere_key \
  -v ./ktem_app_data:/app/ktem_app_data \
  -p 7860:7860 \
  -it --rm \
  kotaemon:latest
```

### 2. Despliegue con Docker Compose (Recomendado)

```bash
# Copiar archivos de ejemplo
cp docker-compose.example.yml docker-compose.yml
cp .env.example .env

# Editar variables de entorno
nano .env

# Ejecutar stack completo
docker-compose up -d

# Ver logs
docker-compose logs -f kotaemon
```

### 3. Despliegue con Modelos Locales (Ollama)

```bash
# Ejecutar con soporte para modelos locales
docker-compose --profile local-models up -d

# Una vez iniciado, configurar modelos en Ollama
docker exec -it kotaemon-ollama ollama pull llama3.1:8b
docker exec -it kotaemon-ollama ollama pull nomic-embed-text
```

### 4. Despliegue con Elasticsearch

```bash
# Ejecutar con Elasticsearch para almacenamiento avanzado
docker-compose --profile elasticsearch up -d
```

## ⚙️ Configuración de Variables de Entorno

### Variables Obligatorias
```bash
# API Keys principales
OPENAI_API_KEY=sk-proj-your-openai-key
COHERE_API_KEY=your-cohere-key
```

### Variables Opcionales
```bash
# Azure OpenAI
AZURE_OPENAI_ENDPOINT=https://your-endpoint.openai.azure.com/
AZURE_OPENAI_API_KEY=your-azure-key
AZURE_OPENAI_CHAT_DEPLOYMENT=gpt-4
AZURE_OPENAI_EMBEDDINGS_DEPLOYMENT=text-embedding-ada-002

# Modelos Locales
LOCAL_MODEL=qwen2.5:7b
LOCAL_MODEL_EMBEDDINGS=nomic-embed-text

# GraphRAG
USE_NANO_GRAPHRAG=true
USE_LIGHTRAG=false
USE_MS_GRAPHRAG=false

# Otros Proveedores
VOYAGE_API_KEY=your-voyage-key
MISTRAL_API_KEY=your-mistral-key
```

## 🔧 Personalización de Dependencias

### Para Usar GraphRAG

Edita el `Dockerfile.example` y descomenta las líneas correspondientes:

```dockerfile
# Para NanoGraphRAG
RUN pip install nano-graphrag

# Para LightRAG
RUN pip install git+https://github.com/HKUDS/LightRAG.git

# Para Microsoft GraphRAG
RUN pip install "graphrag<=0.3.6" future
```

### Para Procesamiento Avanzado de Documentos

```dockerfile
# Instalar Docling para procesamiento local
RUN pip install docling
```

## 📊 Monitoreo y Logs

### Ver logs en tiempo real
```bash
docker-compose logs -f kotaemon
```

### Verificar salud del contenedor
```bash
docker-compose ps
curl http://localhost:7860/
```

### Acceder al contenedor
```bash
docker-compose exec kotaemon bash
```

## 🛠️ Troubleshooting

### Problema: Error de memoria insuficiente
**Solución:** Incrementar memoria asignada a Docker
```bash
# Verificar uso de memoria
docker stats kotaemon-app
```

### Problema: Puertos en uso
**Solución:** Cambiar puerto en docker-compose.yml
```yaml
ports:
  - "8080:7860"  # Cambiar puerto host
```

### Problema: Permisos de volúmenes
**Solución:** Ajustar permisos de directorios
```bash
sudo chown -R $USER:$USER ./ktem_app_data
chmod -R 755 ./ktem_app_data
```

### Problema: Dependencias faltantes
**Solución:** Usar requirements.txt completo
```dockerfile
COPY requirements.txt .  # En lugar de requirements-minimal.txt
```

## 🔄 Actualizaciones

### Actualizar imagen
```bash
docker-compose down
docker-compose build --no-cache
docker-compose up -d
```

### Actualizar solo código (sin rebuild)
```bash
docker-compose restart kotaemon
```

## 📈 Escalabilidad

### Para despliegues de alta disponibilidad:

1. **Load Balancer:** Usar nginx o traefik
2. **Multiple Replicas:** Escalar servicio con docker-compose
3. **External Database:** Usar PostgreSQL en lugar de SQLite
4. **Redis Cache:** Para sesiones compartidas

```yaml
# Ejemplo de escalado
services:
  kotaemon:
    deploy:
      replicas: 3
```

## 🔒 Seguridad

### Recomendaciones:
- Usar secrets en lugar de variables de entorno para API keys
- Configurar network security groups
- Habilitar HTTPS con certificados SSL
- Implementar rate limiting
- Regular backup de volúmenes

## 📞 Soporte

Si encuentras problemas durante el despliegue:

1. Verifica logs: `docker-compose logs -f`
2. Confirma variables de entorno en `.env`
3. Valida conectividad de red
4. Revisa recursos disponibles (CPU/RAM/Disk)

---

**¡Kotaemon está listo para producción! 🚀**