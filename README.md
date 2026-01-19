# Mockup Generator

Generador automático de mockups personalizados para leads de outreach. Crea sitios web preview usando templates Astro, genera contenido con Claude AI, y produce mensajes de WhatsApp/Email listos para enviar.

## Características

- **GUI interactiva** - Selección de archivos y configuración visual con tkinter
- **Procesamiento paralelo** - 1-5 workers simultáneos para procesar múltiples leads
- **Generación de contenido con IA** - Claude API para decisiones de diseño y contenido personalizado
- **Múltiples fuentes de imágenes** - Instagram → Unsplash → Placeholders locales
- **Screenshots automáticos** - Playwright captura el hero section de cada mockup
- **Mensajes de outreach** - WhatsApp y Email generados automáticamente
- **Actualización de Excel** - Tracking de estado en tiempo real

## Requisitos

### Sistema
- Python 3.10+
- Node.js 18+ (para Astro)
- macOS / Linux / Windows

### API Keys
- **ANTHROPIC_API_KEY** (requerido) - [Obtener en Anthropic Console](https://console.anthropic.com/)
- **UNSPLASH_ACCESS_KEY** (opcional) - [Obtener en Unsplash Developers](https://unsplash.com/developers)

## Instalación

### 1. Clonar el repositorio
```bash
git clone https://github.com/tu-usuario/ahjin-mockup-generator.git
cd ahjin-mockup-generator
```

### 2. Instalar dependencias de Python
```bash
pip install -r requirements.txt
```

### 3. Instalar Playwright browsers
```bash
playwright install chromium
```

### 4. Configurar variables de entorno
```bash
cp .env.example .env
```

Edita `.env` y agrega tus API keys:
```bash
# REQUERIDO
ANTHROPIC_API_KEY=sk-ant-api03-tu-key-aqui

# OPCIONAL - para imágenes de stock
UNSPLASH_ACCESS_KEY=tu-unsplash-key
```

### 5. Agregar imágenes de fallback (opcional pero recomendado)
```bash
# Estructura de carpetas
fallback_images/
├── manicuristas/     # Imágenes para nicho manicuristas
│   ├── 1.jpg
│   ├── 2.jpg
│   └── ...
├── generic/          # Imágenes genéricas para cualquier nicho
│   ├── 1.jpg
│   └── ...
└── README.md
```

## Uso

### Ejecutar el script
```bash
python mockup-generator.py
```

### Flujo de trabajo

1. **Seleccionar archivo Excel** - Diálogo para elegir el archivo con leads
2. **Seleccionar carpeta de templates** - Carpeta con templates Astro
3. **Seleccionar carpeta de output** - Donde se guardarán los mockups
4. **Configurar procesamiento** - Ventana con:
   - Resumen de leads y templates encontrados
   - Slider para número de workers (1-5)
   - Checkbox para habilitar/deshabilitar procesamiento paralelo
5. **Iniciar procesamiento** - El script procesa cada lead

### Estructura del Excel de leads

El Excel debe tener estas columnas (los nombres son flexibles):

| Columna | Descripción |
|---------|-------------|
| Business Name | Nombre del negocio |
| Niche | Nicho/categoría (ej: "manicuristas") |
| País | País |
| Región | Región |
| Ciudad | Ciudad |
| Comuna | Comuna (usado para organizar output) |
| Website URL | URL del sitio actual (vacío = sin sitio) |
| Phone/WhatsApp | Número de teléfono |
| Instagram/FB Link | Link a Instagram |
| Status | Estado del lead |
| Owner Name | Nombre del dueño |
| Tier | Tier del lead (Tier 1 = sin sitio web) |
| Generate Mockup | "Yes" para procesar |
| Mockup Status | Estado del mockup (se actualiza automáticamente) |
| Mockup Path | Ruta al mockup (se llena automáticamente) |
| Mockup Date | Fecha de generación (se llena automáticamente) |

### Estructura de templates

```
templates/
├── manicuristas/                    # Nicho
│   └── template-1-independiente/    # Template
│       ├── package.json
│       ├── astro.config.mjs
│       ├── src/
│       │   ├── config/site.config.ts
│       │   ├── pages/index.astro
│       │   └── components/
│       └── public/
└── otro-nicho/
    └── template-X/
```

Cada template debe tener:
- `package.json`
- `src/config/site.config.ts`
- `src/pages/index.astro`

### Output generado

```
[carpeta_output]/
├── Las_Condes/                      # Organizado por comuna
│   └── Nails_by_Carolina/           # Nombre del negocio (sanitizado)
│       ├── proyecto/                # Proyecto Astro completo
│       │   ├── src/
│       │   │   └── config/site.config.ts  # Generado
│       │   ├── public/images/
│       │   └── package.json
│       ├── mockup.png               # Screenshot del hero (1440x900)
│       ├── mensaje.txt              # WhatsApp + Email listos
│       └── info.json                # Metadatos del proceso
├── Providencia/
│   └── ...
└── mockup-generator.log             # Log de la sesión
```

## Configuración avanzada

### Constantes del script

En `mockup-generator.py` puedes modificar:

```python
# Procesamiento paralelo
DEFAULT_WORKERS = 3      # Workers por defecto
MAX_WORKERS = 5          # Máximo de workers
PORT_POOL = [4321, 4322, 4323, 4324, 4325]  # Puertos Astro

# Timeouts
SERVER_START_TIMEOUT = 120  # Segundos para iniciar servidor
NPM_INSTALL_TIMEOUT = 300   # Segundos para npm install
SCREENSHOT_WAIT = 3000      # Milisegundos antes del screenshot

# Rate limits (segundos entre requests)
RATE_LIMITS = {
    'instagram': 5.0,
    'unsplash': 1.0,
    'claude': 0.5
}
```

### Temas disponibles

El script soporta 4 temas de color que Claude elige automáticamente:

| Tema | Colores | Ideal para |
|------|---------|------------|
| `elegante` | Rosa + Dorado | Negocios premium |
| `fresh` | Mint + Coral | Negocios jóvenes |
| `bold` | Negro + Fucsia | Artistas atrevidos |
| `natural` | Verde Sage + Beige | Eco-friendly |

### Secciones del sitio

Secciones disponibles (Claude decide cuáles mostrar):
- Hero (obligatorio)
- About
- Services
- Gallery
- Testimonials
- Contact (obligatorio)
- Footer (obligatorio)
- WhatsAppButton (obligatorio)

## Troubleshooting

### Error: ANTHROPIC_API_KEY not set
```bash
# Verificar que .env existe y tiene la key
cat .env | grep ANTHROPIC
```

### Error: npm install failed
```bash
# Verificar Node.js instalado
node --version  # Debe ser >= 18

# Limpiar cache de npm
npm cache clean --force
```

### Error: Playwright browser not found
```bash
# Reinstalar browsers
playwright install chromium
```

### Instagram scraping falla
- Cuenta puede ser privada
- Rate limiting de Instagram
- El script automáticamente pasa a Unsplash/placeholders

### Servidor Astro no inicia
- Verificar que el puerto no está en uso
- El script maneja múltiples puertos (4321-4325)

## Arquitectura

```
[Excel Leads] → [GUI Selección] → [Descubrir Templates]
                                         ↓
                 ┌─────────────────────────────────────┐
                 │     PROCESAMIENTO PARALELO          │
                 │   (ThreadPoolExecutor, 3-5 workers) │
                 └───────────────┬─────────────────────┘
                                 ↓
           [Por cada Lead en paralelo]:
                                 ↓
         [Claude: Decisiones de Diseño]
                        ↓
         [Copiar Template] → [Generar site.config.ts]
                                    ↓
         [Descargar Imágenes] → [Modificar index.astro]
                                    ↓
         [npm install] → [Iniciar Astro (puerto único)]
                                    ↓
         [Screenshot Playwright] → [Detener Servidor]
                                    ↓
         [Claude: Mensajes Outreach] → [Guardar Output]
                                    ↓
                  [Actualizar Excel (thread-safe)]
```

## API Usage

Por lead procesado:
- ~3 llamadas a Claude API
- ~5,000-10,000 tokens totales
- Costo estimado: ~$0.05-0.10 USD por lead

## Licencia

MIT

## Autor

Ahjin Agency
