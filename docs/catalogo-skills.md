# Catálogo de skills

Skills en `/mnt/skills/examples` y `/mnt/skills/public` con `SKILL.md` (41 en total). Los paquetes `.skill` no se cuentan.

| Categoría | Skills |
|---|---|
| [documentos y archivos](#documentos-y-archivos) | 8 |
| [diseño y arte](#diseño-y-arte) | 7 |
| [navegador y trámites](#navegador-y-trámites) | 13 |
| [trabajo y comunicación](#trabajo-y-comunicación) | 6 |
| [desarrollo](#desarrollo) | 2 |
| [skills y agentes](#skills-y-agentes) | 4 |
| [otro](#otro) | 1 |

## Documentos y archivos

| Nombre | Ruta | Qué hace | Cuándo se activa | Recursos |
|---|---|---|---|---|
| docs | `/mnt/skills/examples/docs` | Crea y edita documentos compartidos con soporte para comentarios y gráficos. | Usuario solicita crear o editar documento, memo, especificación o runbook. | ninguno |
| docx | `/mnt/skills/public/docx` | Crea, lee, edita y manipula documentos Word (.docx) y plantillas (.dotx) | Cuando el usuario necesita crear, leer o editar documento Word | `scripts/` |
| file-reading | `/mnt/skills/public/file-reading` | Router que indica qué herramienta usar para leer cada tipo de archivo subido | Cuando un archivo fue subido y necesita ser leído desde /mnt/user-data/uploads/ | ninguno |
| google-workspace | `/mnt/skills/examples/google-workspace` | Guía para editar Google Docs, Sheets, Slides; maneja rangos y formatos | Usuario crea o edita archivos en Google Drive | `references/`, `scripts/` |
| pdf | `/mnt/skills/public/pdf` | Crear leer editar dividir fusionar rotar páginas marcar agua cifrar y OCR en PDF. | Cualquier operación con archivos PDF: lectura creación fusión división rotación cifrado. | `scripts/` |
| pdf-reading | `/mnt/skills/public/pdf-reading` | Lectura inspección y extracción de contenido de archivos PDF desde disco. | Leer inspeccionar o extraer contenido de PDF: texto tablas imágenes formularios. | ninguno |
| pptx | `/mnt/skills/public/pptx` | Crear leer editar presentaciones PowerPoint diapositivas plantillas notas del orador. | Crear leer editar presentaciones PowerPoint pptx potx con cualquier propósito. | `scripts/` |
| xlsx | `/mnt/skills/public/xlsx` | Crear leer editar hojas de cálculo fórmulas formato gráficos limpieza de datos. | Operar con archivos xlsx xlsm csv tsv crear editar limpiar convertir formato. | `scripts/` |

## Diseño y arte

| Nombre | Ruta | Qué hace | Cuándo se activa | Recursos |
|---|---|---|---|---|
| algorithmic-art | `/mnt/skills/examples/algorithmic-art` | Crea arte algorítmico generativo con p5.js y parámetros interactivos | Cuando el usuario pide arte visual, arte generativo, campos de flujo, sistemas de partículas | `templates/` |
| brand-guidelines | `/mnt/skills/examples/brand-guidelines` | Aplica colores y tipografía oficial de Anthropic a artefactos y documentos | Cuando se necesita aplicar guías de marca, colores o estilos corporativos | ninguno |
| canvas-design | `/mnt/skills/examples/canvas-design` | Crea arte visual y diseño en PNG y PDF usando filosofía de diseño. | El usuario pide crear un póster, obra de arte, diseño u obra estática | `canvas-fonts/` |
| frontend-design | `/mnt/skills/public/frontend-design` | Asesoramiento para diseño visual distintivo e intencional en UI nuevas o existentes. | Cuando construir o remodelar UI con dirección estética y tipografía específica. | ninguno |
| paint | `/mnt/skills/examples/paint` | Pinta imágenes originales en estilo acuarela escribiendo código Python | Usuario pide dibujar, pintar, bosquejar o crear imagen sin generador | `examples/`, `paintkit/` |
| slack-gif-creator | `/mnt/skills/examples/slack-gif-creator` | Crea GIFs animados optimizados para Slack con validación y herramientas | Cuando el usuario pide GIFs animados para Slack | `core/` |
| theme-factory | `/mnt/skills/examples/theme-factory` | Aplica temas de colores y fuentes consistentes a artifacts | Cuando el usuario quiere estilizar un artifact con tema visual profesional | `themes/` |

## Navegador y trámites

| Nombre | Ruta | Qué hace | Cuándo se activa | Recursos |
|---|---|---|---|---|
| benepass-reimbursement | `/mnt/skills/examples/benepass-reimbursement` | Automatiza reembolsos de gastos en Benepass: login, selección, carga de recibos, envío | Cuando el usuario necesita reembolsar un gasto en Benepass | ninguno |
| built-in-browser | `/mnt/skills/examples/built-in-browser` | Referencia para usar el navegador integrado del escritorio de Claude | Antes de usar herramientas del navegador integrado en la app de escritorio | ninguno |
| call-to-book | `/mnt/skills/examples/call-to-book` | Realiza llamadas para reservar citas, consulta calendario, confirma y agrega a calendario | Cuando el usuario quiere reservar una cita o reservación por teléfono | ninguno |
| cancel-unsubscribe | `/mnt/skills/examples/cancel-unsubscribe` | Cancela suscripciones o servicios, encuentra contacto y maneja cancelación. | El usuario pide cancelar una suscripción o servicio | ninguno |
| chrome-browser | `/mnt/skills/examples/chrome-browser` | Referencia para usar la extensión Claude in Chrome que actúa en Chrome real. | Antes de usar Claude in Chrome por primera vez o cuando se usen herramientas de la extensión | ninguno |
| computer-use | `/mnt/skills/examples/computer-use` | Permite a Claude tomar capturas de pantalla y controlar la computadora del usuario. | Antes de hacer algo en una app de la computadora, ver la pantalla o para control de escritorio | ninguno |
| file-expenses | `/mnt/skills/examples/file-expenses` | Gestiona gastos y reembolsos detectando plataforma, buscando recibos y verificando. | Usuario necesita presentar gasto o solicitar reembolso en plataforma. | ninguno |
| file-form | `/mnt/skills/examples/file-form` | Gestiona trámites burocráticos: jurado, multas, renovaciones, permisos gubernamentales. | Usuario debe completar trámite administrativo o formulario gubernamental con plazo. | ninguno |
| grocery-shopping | `/mnt/skills/examples/grocery-shopping` | Ayuda a pedir compras de supermercado con flujo concierge, lista, presupuesto y carrito | Usuario pide ayuda para pedir el supermercado a domicilio | ninguno |
| hire-help | `/mnt/skills/examples/hire-help` | Busca y reserva proveedores de servicios en plataformas web | Usuario pide ayuda para encontrar y contratar un proveedor | ninguno |
| meal-delivery | `/mnt/skills/examples/meal-delivery` | Ordena comida para llegada exacta, trabaja hacia atrás, sugiere restaurantes y monitorea | Cuando el usuario quiere ordenar comida para una hora específica | ninguno |
| prescription-refill | `/mnt/skills/examples/prescription-refill` | Gestiona recarga de medicamentos en farmacias por teléfono u online | Usuario necesita refill de medicamento o solicita gestión de recarga | ninguno |
| return-refund | `/mnt/skills/examples/return-refund` | Procesa devoluciones y reembolsos navegando políticas y trámites de envío | Usuario quiere devolver artículos u obtener reembolsos de minoristas | ninguno |

## Trabajo y comunicación

| Nombre | Ruta | Qué hace | Cuándo se activa | Recursos |
|---|---|---|---|---|
| deep-research | `/mnt/skills/examples/deep-research` | Coordina subagentes para investigar un tema y sintetizar en un reporte narrativo. | Cuando el usuario necesita investigar un tema y sintetizar en un reporte completo | `references/` |
| doc-coauthoring | `/mnt/skills/examples/doc-coauthoring` | Guía estructurada para co-redactar documentación con procesos iterativos y verificación. | Usuario solicita escribir documentación, propuestas, especificaciones o contenido estructurado. | ninguno |
| event-planning | `/mnt/skills/examples/event-planning` | Planifica eventos desde cenas hasta bodas con gestión de venue y presupuesto. | Usuario necesita organizar evento social, corporativo o familiar. | ninguno |
| financial-calculator | `/mnt/skills/examples/financial-calculator` | Realiza cálculos y comparaciones financieras con escenarios | Usuario pide cálculos financieros o análisis de escenarios | ninguno |
| internal-comms | `/mnt/skills/examples/internal-comms` | Ayuda a escribir comunicaciones internas con formatos estandarizados | Cuando el usuario pide escribir reportes, newsletters, actualizaciones o comunicaciones internas | `examples/` |
| morning | `/mnt/skills/examples/morning` | Renderiza resumen matutino con calendario, tareas pendientes y contexto de reuniones | Cuando invoca /morning explícitamente o pide ver/configurar su resumen diario | `assets/` |

## Desarrollo

| Nombre | Ruta | Qué hace | Cuándo se activa | Recursos |
|---|---|---|---|---|
| mcp-builder | `/mnt/skills/examples/mcp-builder` | Guía para crear servidores MCP que integren LLMs con servicios externos | Cuando construye servidores MCP en Python o TypeScript/Node | `reference/`, `scripts/` |
| web-artifacts-builder | `/mnt/skills/examples/web-artifacts-builder` | Construye artifacts HTML complejos con React, Tailwind CSS y shadcn/ui | Cuando se necesita artifact HTML complejo con estado, ruteo o componentes avanzados | `scripts/` |

## Skills y agentes

| Nombre | Ruta | Qué hace | Cuándo se activa | Recursos |
|---|---|---|---|---|
| import-memory | `/mnt/skills/examples/import-memory` | Importa memorias de otro asistente IA a la memoria de Claude | Usuario quiere traer memorias de otro asistente IA | ninguno |
| product-self-knowledge | `/mnt/skills/public/product-self-knowledge` | Referencia verificable de productos Anthropic Claude Code Claude API Claude.ai. | Respuestas con hechos sobre productos Anthropic SDK pricing modelos características. | ninguno |
| setup-writing-style | `/mnt/skills/examples/setup-writing-style` | Aprende estilo personal del usuario y crea perfil de voz para borradores | Usuario quiere capturar su voz o que los borradores suenen como su propia voz | `scripts/` |
| skill-creator | `/mnt/skills/examples/skill-creator` | Crea nuevas skills, mejora existentes y mide rendimiento con evals | Usuario quiere crear, editar u optimizar una skill o evaluar desempeño | `agents/`, `assets/`, `eval-viewer/`, `references/`, `scripts/` |

## Otro

| Nombre | Ruta | Qué hace | Cuándo se activa | Recursos |
|---|---|---|---|---|
| learn | `/mnt/skills/examples/learn` | Tutor que alterna explicación directa, descubrimiento guiado, ejemplos resueltos, visuales y material de estudio | Cuando el usuario quiere entender cómo o por qué funciona algo | ninguno |
