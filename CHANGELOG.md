# Changelog — Doctoralia

Todas las versiones siguen [Keep a Changelog](https://keepachangelog.com/es/1.1.0/)
y [Versionado Semántico](https://semver.org/lang/es/).

---

## [3.0.1] — 2026-10-12

### Corregido
- La descarga en Word fallaba con «No se pudo cargar una librería»: dependía de una librería externa (docx, desde cdnjs). El .docx se genera ahora dentro de la propia página, sin dependencias, y la política de seguridad ya no admite ningún script externo.

---

## [3.0.0] — 2026-10-12

Rediseño sobre los módulos de la plataforma Forja (interfaz y motor).

### Añadido
- Interfaz en español, inglés y portugués, con selector y `?lang=`; el idioma de la revisión sigue siendo independiente.
- Proveedores de IA: OpenAI, Google Gemini y Anthropic Claude, con la clave de cada persona.
- Ejemplo sin clave ni red, en los tres idiomas (referencias ficticias marcadas `[DEMO]`).
- Aviso previo al envío con muestra y enmascarado de correo, DNI/NIE y teléfono en el tema y las instrucciones.
- Secciones de consulta (Artículo, Auditoría, Huecos, Historial, Agentes y prompts) fuera del recorrido lineal.
- Migración automática desde la v2.1: clave de OpenAI, prompts propios e historial (como archivo de solo descarga).

### Cambiado
- «Citas resueltas» cuenta ahora los marcadores que el modelo emitió y el código eliminó; antes salía siempre 100 % porque las inválidas se borraban antes de medir.
- El coste se calcula con precios que escribe la persona; ya no hay tabla de precios en el código.
- El abstract se muestra al principio del documento.
- Los marcadores de huecos se muestran como «Autor, año» para no confundirlos con la numeración final.

### Retirado
- Los agentes `entropy`, `voice`, `formatter` y `conclusion_writer`, que figuraban en la lista pero nunca se ejecutaban.
- Los proxies públicos de arXiv (sacaban las consultas a terceros); arXiv se consulta solo por DataCite.
- La vista previa en streaming, el sandbox de prompts y el control de reintentos (el módulo de IA reintenta con espera exponencial).

---

## [2.1.0] — 2026-09-24

Versión centrada en la **fiabilidad del acceso a las fuentes**.

### Cambiado
- **Capa de red común** para todas las fuentes: ritmo mínimo por servidor, reintentos con
  espera exponencial (respetando `Retry-After`) ante 429, 5xx y errores de red, y tiempo
  máximo de 20 s por petición.
- **Semantic Scholar**: las peticiones sin clave se serializan (≈1/1,5 s) y se reintentan hasta
  4 veces. El "Failed to fetch" era un 429 sin cabeceras CORS de la cuota pública compartida.
- **arXiv**: se consulta el registro DataCite de arXiv (DOI 10.48550, con CORS, abstract y año)
  en lugar de `export.arxiv.org`, que no admite CORS. Los proxies públicos quedan solo como
  respaldo, con 10 s de tiempo máximo (antes podían bloquear la fase 40 s).
- **OpenAlex**: admite clave propia (`api_key`). Desde 2026 el uso sin clave se limita a unas
  100 búsquedas diarias por conexión; al agotarse se avisa una vez y se omite la fuente.
- **PubMed**: ritmo de 3 peticiones/s y clave NCBI opcional.
- **Cortacircuitos por fuente**: tras fallos repetidos se omiten sus consultas restantes con
  un único aviso y un resumen de fuentes caídas.
- La comprobación de retractaciones ya no informa de "ninguna retractación" cuando OpenAlex
  no ha respondido: indica cuántos DOI quedaron sin comprobar.

---

## [2.0.0] — 2026-08-31

Versión centrada en la **integridad de las citas**. En la v1 el redactor escribía a partir
de un texto generado por otro agente y las referencias se construían aparte, de modo que
nada garantizaba que una cita del cuerpo correspondiera a un paper real. En la v2 las citas
se anclan a un corpus numerado y se verifican en el navegador con código determinista.

### Añadido

**Citación anclada y verificable**
- El corpus recuperado se congela como lista numerada `[1]…[N]` y se pasa íntegro al
  redactor, que solo puede citar por índice.
- Validación determinista en JavaScript: se extraen todos los marcadores del texto, se
  eliminan los que caen fuera de rango y las citas de tipo `[Autor et al., Año]`. La
  comprobación no se delega al modelo.
- Si una sección se genera sin ninguna cita, se reescribe automáticamente exigiéndolas.
- Renumeración por orden de aparición al cerrar el documento. Las referencias finales son
  **solo las efectivamente citadas**, no un top-N arbitrario.
- Formateo real de referencias en IEEE, APA 7, Vancouver, ACM y Chicago. En APA y Chicago
  los marcadores `[n]` se convierten en `(Apellido, año)` usando el registro real del paper.
- Los agentes `narrator` y `polish` se descartan automáticamente si su reescritura destruye
  citas (umbrales del 90 % y 95 % respectivamente).

**Panel de Auditoría (nuevo)**
- Métricas verificables: proporción de citas resueltas, densidad por 1.000 palabras,
  cobertura del corpus, secciones sin citas, referencias con DOI, rango temporal, recencia,
  concentración de venues (HHI), proporción de acceso abierto y referencias retractadas.
- Tabla de citación por sección.
- Hallazgos de `skeptic`, `verifier` y `thread`, que en la v1 se ejecutaban y se descartaban.
- Desglose del consumo real de tokens y coste por agente.

**Fuentes y corpus**
- Comprobación de retractaciones en OpenAlex (un lote por cada 50 DOI, sin coste de tokens)
  con exclusión automática, activada por defecto.
- Deduplicación por DOI normalizado con fusión campo a campo del registro más rico;
  similitud de Jaccard ≥ 0,85 sobre el título como criterio subsidiario.
- Ranking por índice compuesto: percentil de citas por año dentro del corpus, recencia,
  disponibilidad de abstract y confirmación en varias fuentes.
- Búsquedas en paralelo con concurrencia configurable (por defecto 4).
- Importación de corpus propio en BibTeX, RIS o listas de DOI (resueltas contra OpenAlex).

**Trazabilidad y exportación**
- Diagrama de flujo PRISMA en SVG descargable, con los recuentos reales de la ejecución.
- Sección de metodología PRISMA redactada sobre cifras reales, no inventadas por el modelo.
- Exportación a `.docx` OOXML real, generada sin dependencias externas.
- Exportación de las referencias citadas en BibTeX.
- Exportación de `doctoralia_run.json`: prompts efectivos, consultas generadas, corpus con
  DOI, parámetros, métricas y consumo. Permite reproducir y auditar la revisión.

**Interfaz**
- Contador de coste real en vivo durante la ejecución, calculado con los tokens que
  devuelve la API.
- Vista previa en streaming del texto según se genera.
- Nuevos conmutadores: auditoría de contenido, exclusión de retractados, pulido de estilo
  y streaming.

### Cambiado

- **PubMed** pasa de `esummary` a `efetch` en XML: los abstracts ahora se recuperan de
  verdad. En la v1 el campo llegaba siempre vacío pese a anunciarse en la interfaz.
- El límite de abstract sube de 500 a 900–1.200 caracteres en todas las fuentes.
- El historial y el borrador migran de `localStorage` (≈5 MB compartidos, fallo silencioso
  por cuota) a **IndexedDB**, con 20 entradas y su traza de ejecución. Migración automática.
- La exportación a Word deja de ser HTML renombrado a `.doc` y pasa a `.docx` real.
- El orden por defecto del corpus deja de ser «citas brutas», que sesga hacia lo antiguo.
- El `narrator` queda **desactivado por defecto**: era una tercera reescritura del mismo
  texto que encarecía la ejecución y alejaba el resultado de las fuentes.
- El «Quality Score /100» se sustituye por «citas resueltas». El juicio del `referee` se
  mantiene, etiquetado como autoevaluación no calibrada.
- arXiv usa una cadena de reintentos (directo → corsproxy → allorigins) en lugar de
  depender de un único proxy.
- Prompts reescritos en español, con la regla de citación por índice como restricción
  explícita e inviolable.
- Los títulos de sección por defecto y los generados por el `architect` respetan el idioma
  de salida seleccionado.

### Corregido

- **Clave de API de Semantic Scholar expuesta** en el código fuente servido públicamente.
  Eliminada; se sustituye por un campo opcional para la clave propia del usuario.
  *Quien haya desplegado la v1 debe revocar esa clave.*
- **Inyección de HTML** a través de títulos de papers, texto de gaps y tema del usuario, en
  el checkpoint, la tabla de referencias, las tarjetas de gaps y el historial. Todo el
  contenido de terceros se escapa antes de insertarse en el DOM.
- Los agentes `skeptic`, `verifier` y `thread` se invocaban y su respuesta se descartaba
  sin asignar, consumiendo tokens sin efecto alguno.
- El fallo al guardar en `localStorage` por exceso de cuota se silenciaba con un
  `catch` vacío: el usuario creía tener historial sin tenerlo.
- Deduplicación por los primeros 50 caracteres del título, que fusionaba papers distintos
  de la misma serie y no fusionaba el mismo paper con subtítulos discrepantes.
- La estimación de coste era una constante sin relación con el consumo real.

### Seguridad

- Revocar la clave de Semantic Scholar de la v1 es **obligatorio** al actualizar.
- La clave de OpenAI sigue sin salir del navegador; el cierre de la inyección de HTML
  elimina la vía por la que un título malicioso podía leerla desde `localStorage`.

---

## [1.x] — anterior

Pipeline de 20 agentes en 5 fases, 6 fuentes bibliográficas, asistente de 3 pasos,
estimación previa de coste, checkpoint de papers, editor de prompts, autoguardado de
borrador y exportación a Markdown, `.doc`, LaTeX y texto plano.
