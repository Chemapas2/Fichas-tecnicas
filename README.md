# Generador de etiquetas y fichas técnicas de piensos

App Streamlit para generar etiquetas, fichas técnicas de Calidad-Operaciones, fichas comerciales y fichas de especificación a partir de un fichero de formulación y del Excel maestro de textos parametrizados.

## Estructura esperada en GitHub

```text
fichas-tecnicas/
├─ main.py
├─ requirements.txt
├─ README.md
├─ data/
│  └─ Etiquetas_y_beneficios_para_App_animales_destino.xlsx
├─ assets/
│  ├─ Logo1 Nanta.jpg
│  ├─ Solapa rosa.jpg
│  ├─ foto_porcino.jpg
│  ├─ foto_avicultura.jpg
│  ├─ foto_conejos.jpg
│  ├─ foto_ovino.jpg
│  ├─ foto_caprino.jpg
│  ├─ foto_vacuno_leche.jpg
│  ├─ foto_vacuno_carne.jpg
│  └─ foto_caballos.jpg
└─ templates/
   ├─ nanta_corporativa.html
   ├─ etiqueta.html
   ├─ ft_calidad_operaciones.html
   ├─ ft_especificaciones.html
   ├─ comercial_base.html
   ├─ comercial_porcino.html
   ├─ comercial_avicultura.html
   ├─ comercial_conejos.html
   ├─ comercial_ovino.html
   ├─ comercial_caprino.html
   ├─ comercial_vacuno_leche.html
   ├─ comercial_vacuno_carne.html
   └─ comercial_caballos.html
```

## Plantillas HTML

La app puede usar distintas plantillas por tipo de ficha:

- `etiqueta.html` para etiquetas.
- `ft_calidad_operaciones.html` para FT Calidad-Operaciones.
- `ft_especificaciones.html` para FT Especificaciones.
- `comercial_base.html` como plantilla comercial genérica.
- `comercial_*.html` como plantillas comerciales por especie.

En FT Comercial, la app intenta elegir automáticamente la plantilla por especie/subespecie/producto. Si no detecta especie, usa `comercial_base.html`.

También se puede seleccionar manualmente la plantilla en cada ficha generada desde el desplegable **Plantilla HTML/PDF**.

## Fotografías de especie

La app busca estas imágenes en `assets/`:

```text
foto_porcino.jpg
foto_avicultura.jpg
foto_conejos.jpg
foto_ovino.jpg
foto_caprino.jpg
foto_vacuno_leche.jpg
foto_vacuno_carne.jpg
foto_caballos.jpg
```

El paquete incluye imágenes de cabecera de sustitución. Para usar fotografías reales, basta con reemplazar esos archivos por fotografías definitivas con el mismo nombre. Mantener preferiblemente formato horizontal, por ejemplo 1600 x 650 px.

## Marcadores disponibles en plantillas

Las plantillas HTML pueden usar estos marcadores:

```text
{{LOGO_URI}}
{{SOLAPA_URI}}
{{SPECIES_PHOTO_URI}}
{{DOC_TYPE}}
{{TITLE}}
{{PRODUCT}}
{{ESPECIE}}
{{SUBESPECIE}}
{{LIFESTAGE}}
{{SPECIES_LABEL}}
{{SECTIONS}}
{{DATE}}
{{DEFINICION_POSICIONAMIENTO}}
{{CARACTERISTICAS}}
{{BENEFICIOS}}
{{MODO_EMPLEO}}
{{PRECAUCIONES_DE_USO}}
{{NUTRIENTES_HTML}}
{{LIMITES_NUTRIENTES_HTML}}
{{LIMITES_INGREDIENTES_HTML}}
```

Además, cualquier campo generado puede usarse como marcador en formato original, minúsculas o con guiones bajos. Por ejemplo:

```text
{{Nombre comercial}}
{{nombre_comercial}}
{{TIPO_DE_PIENSO}}
{{Animales de destino}}
{{animales_de_destino}}
```

## Descarga HTML/PDF NANTA

La app genera un archivo HTML maquetado. Para convertirlo a PDF:

1. Descargar **HTML/PDF NANTA**.
2. Abrir el archivo `.html` en Chrome o Edge.
3. Pulsar `Ctrl + P`.
4. Seleccionar **Guardar como PDF**.
5. Activar **Gráficos de fondo** si no aparecen los colores o la solapa.

## Canva_Comercial

Los Excel individuales y el Excel acumulado incluyen una hoja `Canva_Comercial`, pensada para Canva Bulk Create o Google Sheets. Cada fila contiene campos planos y versiones cortas de textos para facilitar la maquetación masiva.

## Ejecución local

```bash
pip install -r requirements.txt
streamlit run main.py
```
