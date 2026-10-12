# Doctoralia

Revisiones del estado del arte con **citas que se pueden comprobar**. Aplicación web de un solo fichero (`index.html`), sin servidor.

**Usar la app:** https://fborrasumh.github.io/doctoralia/

**Idiomas de la interfaz:** español (por defecto), inglés y portugués; selector en la barra superior (o `?lang=en` / `?lang=pt` en la URL). El idioma de la revisión (español, inglés, portugués, francés o alemán) es independiente.

## Qué hace

- Busca en seis bases académicas en paralelo (PubMed, arXiv —a través de DataCite—, Semantic Scholar, OpenAlex, Europe PMC y CrossRef), fusiona los duplicados por DOI (y por título cuando no hay DOI), filtra por años y excluye los papers retractados que OpenAlex o PubMed marcan.
- Permite sembrar la revisión con tu propia bibliografía (BibTeX, RIS o lista de DOI).
- Fija un **corpus numerado** [1]–[N] y le pide al redactor que cite solo por índice. **El código**, no el modelo, extrae todos los marcadores, elimina los fuera de rango y las citas autor-año inventadas, renumera por orden de aparición y deja como referencias solo las realmente citadas (IEEE, APA 7, Vancouver, ACM o Chicago).
- Muestra una auditoría con métricas calculadas en JavaScript, los hallazgos de los agentes validadores, el diagrama PRISMA con los recuentos reales y el consumo real de tokens por agente.
- Antes de gastar en la redacción enseña los papers recuperados y deja decidir si continuar.
- Exporta a Word, Markdown, LaTeX, BibTeX, texto y una **traza JSON** (consultas, prompts, recuentos, corpus, consumo) para que la revisión sea reproducible. Las secciones se pueden editar y reescribir una a una.
- Hay un **ejemplo que funciona sin clave ni red**, en los tres idiomas. Todas sus referencias son ficticias y están marcadas como `[DEMO]`.

## Cómo se usa la IA

Con tu propia clave de **OpenAI, Google Gemini o Anthropic Claude**. La clave se guarda solo en el navegador y va solo a tu proveedor. No hay IA local.

El coste lo calculas tú: escribes el precio por millón de tokens de tu proveedor (cambian y no se fijan en el código) y la app suma los tokens reales que devuelve la API.

## Privacidad

- En tu navegador: revisiones, historial (máximo 20), prompts propios y claves.
- Hacia las bases bibliográficas salen las consultas de búsqueda derivadas del tema (y tu correo o tus claves de esas bases, solo si los escribes).
- Hacia tu proveedor de IA salen el tema, las instrucciones (con correo, DNI/NIE y teléfono enmascarados) y los títulos y resúmenes de los papers recuperados. Antes del primer envío de la sesión ves una muestra y confirmas.

## Límites

- Una cita verificada demuestra que el paper está en el corpus, **no que diga lo que la frase afirma**. El agente verificador es un modelo y puede equivocarse.
- Las puntuaciones del agente `referee` son autoevaluaciones del modelo, no calibradas.
- Cobertura y calidad dependen de lo que devuelvan las bases; sin clave propia, Semantic Scholar y OpenAlex limitan mucho.
- Si editas o reescribes una sección, las métricas siguen siendo las de la versión generada.
- Probada con IA y bases simuladas; **no** se ha probado en esta versión con claves ni bases reales.

## Autoría

Fernando Borrás Rocher (Universidad Miguel Hernández de Elche). ORCID: [0000-0002-5519-4573](https://orcid.org/0000-0002-5519-4573)

## Cómo citar

Borrás Rocher, F. (2026). *Doctoralia* (v3.0.0) [Software]. https://doi.org/10.5281/zenodo.21423293

## Licencia

MIT. Véase [LICENSE](LICENSE).
