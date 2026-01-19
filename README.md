# Mockup Generator

Generador automático de mockups personalizados para leads de outreach. Crea sitios web preview usando templates Astro, genera contenido con Claude AI (via CLI), y produce mensajes de WhatsApp/Email listos para enviar.

## Características

- **Modo CLI y GUI** - Ejecuta con argumentos de línea de comandos o interfaz gráfica con tkinter
- **Procesamiento paralelo** - 1-5 workers simultáneos para procesar múltiples leads
- **Generación de contenido con IA** - Claude CLI para decisiones de diseño y contenido personalizado
- **Selección inteligente de templates** - Claude elige entre múltiples templates disponibles
- **Imágenes de stock por nicho** - Pexels API con fotos curadas para cada nicho (manicure, spa, etc.)
- **Screenshots automáticos** - Playwright captura full-page screenshot + hero section + PDF
- **Mensajes de outreach** - WhatsApp y Email generados automáticamente con copywriting chileno
- **Actualización de Excel** - Tracking de estado en tiempo real

## Requisitos

### Sistema
- Python 3.9+
- Node.js 18+ (para Astro)
- macOS / Linux / Windows
- Claude CLI instalado (`claude --version`)

### Claude CLI
Este proyecto usa **Claude CLI** (no la API directamente) para generar contenido. Debes tener una suscripción Max o Team.

```bash
# Verificar instalación
claude --version
# Debe mostrar algo como: 2.1.12 (Claude Code)
```

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

## Uso

### Modo CLI (recomendado)
```bash
python mockup-generator.py \
  --excel "/path/to/leads.xlsx" \
  --templates "/path/to/templates/" \
  --output "/path/to/output/" \
  --max-leads 5 \
  --workers 3
```

### Modo GUI
```bash
python mockup-generator.py
# Se abre interfaz gráfica para seleccionar archivos
```

### Argumentos CLI

| Argumento | Descripción | Default |
|-----------|-------------|---------|
| `--excel` | Ruta al archivo Excel con leads | (requerido en CLI) |
| `--templates` | Carpeta con templates Astro | (requerido en CLI) |
| `--output` | Carpeta donde guardar mockups | (requerido en CLI) |
| `--max-leads` | Máximo de leads a procesar | Sin límite |
| `--workers` | Workers paralelos (1-5) | 3 |

## Estructura del Excel

El Excel debe tener estas columnas:

| Columna | Descripción | Ejemplo |
|---------|-------------|---------|
| Business Name | Nombre del negocio | "María Bonita Spa" |
| Niche | Nicho/categoría | "Manicuristas" |
| Tier | 1 = sin website, 2 = con website | "1" |
| Website URL | URL del sitio (vacío para Tier 1) | "" |
| Comuna | Comuna (organiza output) | "Providencia" |
| Phone/WhatsApp | Número de teléfono | "+56 9 1234 5678" |
| Instagram/FB Link | Link a Instagram | "https://instagram.com/..." |
| Generate Mockup | "Sí" para procesar | "Sí" |
| Mockup Status | Se actualiza automáticamente | "Completed" |
| Mockup Path | Ruta al mockup generado | "/output/Providencia/..." |
| Mockup Date | Fecha de generación | "2026-01-19" |

### Filtrado de Leads

El script **solo procesa leads que cumplen TODAS estas condiciones**:

1. **Tier = 1** (leads sin sitio web)
2. **Website URL vacío** (verificación adicional)
3. **Generate Mockup = "Sí"** (explícito, no vacío)
4. **Mockup Status ≠ "Completed"** (no reprocesar completados)

> **Nota**: Los leads Tier 2 (con website existente) son ignorados automáticamente.

## Estructura de Templates

El script detecta templates **recursivamente** hasta 4 niveles de profundidad:

```
templates/
├── template-minimalista-elegante/     # Template directo
│   ├── package.json
│   └── src/
├── template-artistica/
│   └── artistica-creativa/            # Template anidado
│       ├── package.json
│       └── src/
└── template-moderna/
    └── moderna-geometrica/            # Template anidado
        ├── package.json
        └── src/
```

Cada template debe tener:
- `package.json` (identifica como template válido)
- `src/config/site.config.ts`
- `src/pages/index.astro`

## Output Generado

```
[output]/
├── Providencia/                        # Por comuna
│   └── Maria_Bonita_Spa/              # Por negocio
│       ├── proyecto/                   # Proyecto Astro completo
│       │   ├── src/config/site.config.ts
│       │   ├── public/images/
│       │   └── package.json
│       ├── mockup.png                  # Screenshot full-page
│       ├── mockup-hero.png             # Screenshot solo hero
│       ├── mockup.pdf                  # PDF del sitio
│       ├── mensaje.txt                 # WhatsApp + Email
│       └── info.json                   # Metadatos
├── Las_Condes/
│   └── ...
└── mockup-generator.log
```

## Flujo de Procesamiento

```
[Excel con leads]
       ↓
[Descubrir Templates] ← Búsqueda recursiva (4 niveles)
       ↓
[Por cada lead]:
       ↓
[Claude CLI] → Elegir template + tema + secciones
       ↓
[Pexels API] → Descargar 6 imágenes del nicho
       ↓
[Claude CLI] → Generar contenido personalizado
       ↓
[Copiar Template] → proyecto/
       ↓
[Generar site.config.ts] ← Datos del lead
       ↓
[Modificar index.astro] ← Reordenar/ocultar secciones
       ↓
[npm install] + [Astro dev server]
       ↓
[Playwright] → Screenshots + PDF
       ↓
[Claude CLI] → Generar mensajes outreach
       ↓
[Actualizar Excel] ← Status + Path + Date
```

## Temas de Color

Claude elige automáticamente entre 4 temas:

| Tema | Colores | Ideal para |
|------|---------|------------|
| `elegante` | Rosa suave + Oro rosado | Spas premium, elegantes |
| `fresh` | Verde salvia + Terracota | Negocios frescos, naturales |
| `bold` | Burgundy + Dorado (fondo negro) | Estilo sofisticado y atrevido |
| `natural` | Verde oliva + Arena cálida | Eco-friendly, orgánico |

## Imágenes de Stock

El script usa **Pexels** con IDs curados por nicho:

- **Manicuristas**: Fotos de nail art, manicure, manos con esmalte
- **Más nichos**: Se pueden agregar en `_get_pexels_ids_for_niche()`

Las imágenes se descargan sin API key usando URLs directas de Pexels CDN.

## Secciones del Sitio

Claude decide qué secciones mostrar y en qué orden:

| Sección | Obligatoria | Descripción |
|---------|-------------|-------------|
| Hero | Sí | Header principal con CTA |
| About | No | Información del negocio |
| Services | No | Lista de servicios con precios |
| Gallery | No | Galería de trabajos |
| Testimonials | No | Testimonios de clientes |
| Contact | Sí | Info de contacto + ubicación |
| Footer | Sí | Footer con redes sociales |
| WhatsAppButton | Sí | Botón flotante de WhatsApp |

## Troubleshooting

### Claude CLI no encontrado
```bash
# Verificar instalación
which claude
claude --version

# Si no está, instalar desde:
# https://claude.ai/download
```

### npm install falla
```bash
# Verificar Node.js
node --version  # >= 18

# Limpiar cache
npm cache clean --force
```

### Playwright no encuentra browser
```bash
playwright install chromium
```

### Mapa no aparece en screenshot
El template usa un **card elegante de ubicación** en lugar de iframe de Google Maps (que no renderiza en Playwright). El card muestra la dirección y un botón "Ver en Google Maps".

### Imágenes no cargan
El script usa Pexels CDN que es confiable. Si fallan, se usan placeholders locales en `fallback_images/`.

## Arquitectura Técnica

- **Python**: Orquestación, GUI, procesamiento paralelo
- **Claude CLI**: Decisiones de diseño, contenido, mensajes
- **Astro**: Framework de templates (SSG)
- **Playwright**: Screenshots y PDF
- **Pexels CDN**: Imágenes de stock sin API key
- **pandas/openpyxl**: Manejo de Excel

## Costos

- **Claude CLI**: Incluido en suscripción Max/Team
- **Pexels**: Gratis (sin API key)
- **Playwright**: Gratis (open source)

## Licencia

MIT

## Autor

Ahjin Agency - Diseño web para negocios locales en Chile
