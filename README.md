# Generador de etiquetas y fichas técnicas de piensos

Aplicación Streamlit para generar etiquetas, FT Calidad-Operaciones, FT Comercial y FT Especificaciones a partir de un fichero de formulación y un Excel maestro de textos parametrizados.

## Ejecución local

```bash
pip install -r requirements.txt
streamlit run main.py
```

## Estructura recomendada en GitHub

```text
fichas-tecnicas/
├─ main.py
├─ requirements.txt
├─ README.md
├─ data/
│  └─ Etiquetas_y_beneficios_para_App_animales_destino.xlsx
├─ assets/
│  ├─ Logo1 Nanta.jpg
│  └─ Solapa rosa.jpg
└─ templates/
   └─ nanta_corporativa.html
```

La app carga automáticamente el Excel maestro desde `data/`. Si no existe, permite cargarlo manualmente desde la barra lateral.

## Plantilla corporativa NANTA

La descarga **HTML/PDF NANTA** usa `templates/nanta_corporativa.html`. Esta plantilla no debe contener textos fijos de producto; debe contener estructura visual y marcadores.

Marcadores admitidos:

```text
{{LOGO_URI}} o {{logo_uri}}
{{SOLAPA_URI}} o {{solapa_uri}}
{{DOC_TYPE}} o {{tipo_ficha}}
{{TITLE}} o {{titulo}}
{{PRODUCT}} o {{producto}}
{{ESPECIE}} o {{especie}}
{{SUBESPECIE}} o {{subespecie}}
{{LIFESTAGE}} o {{lifestage}}
{{SECTIONS}} o {{secciones}}
{{DATE}} o {{fecha}}
```

También se pueden usar marcadores de campos concretos. Ejemplos:

```text
{{Nombre comercial}}
{{modo_empleo}}
{{beneficios}}
{{precauciones_de_uso}}
{{nutrientes_seleccionados_html}}
{{limites_nutrientes_html}}
{{limites_ingredientes_html}}
```

El marcador `{{SECTIONS}}` es el más robusto: inserta automáticamente tarjetas con definición, características, beneficios, modo de empleo, precauciones, nutrientes y límites, según la información disponible.

## Flujo de uso

1. Cargar el fichero de formulación.
2. Seleccionar producto.
3. Seleccionar especie, subespecie, lifestage y textos propuestos.
4. Pulsar **Pasar opciones elegidas a campos editables**.
5. Revisar/editar los campos.
6. Seleccionar nutrientes por tipo de ficha y guardarlos si procede.
7. Generar la ficha deseada.
8. Descargar en TXT, DOCX, PDF texto, HTML/PDF NANTA o Excel.
9. Usar **Acumular** para preparar el Excel masivo con la hoja `Canva_Comercial`.

## Canva_Comercial

Cada Excel individual y el Excel acumulado incluyen una hoja `Canva_Comercial` con una fila por producto/ficha. Está preparada para subirla a Google Sheets o a Canva Bulk Create.

Columnas principales:

```text
tipo_documento, producto, especie, subespecie, lifestage, tipo_pienso,
animales_destino, definicion_posicionamiento, beneficio_corto,
modo_empleo_corto, precauciones_cortas, nutrientes_texto,
limites_nutrientes_texto, limites_ingredientes_texto
```

## Notas técnicas

- El PDF generado por el botón **PDF texto** es una salida sobria a partir del texto estructurado.
- La salida maquetada corporativa es **HTML/PDF NANTA**. Se abre en navegador y desde ahí puede imprimirse o guardarse como PDF.
- La app detecta salidas de formulación tipo `Specification:` y también listados tipo `SP:` / Single-Mix.
