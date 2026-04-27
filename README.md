# Generador de etiquetas y fichas técnicas de piensos

Aplicación Streamlit para confeccionar de forma rápida textos y documentos de:

1. Etiquetas.
2. Ficha técnica de Calidad-Operaciones.
3. Ficha técnica Comercial.
4. Ficha técnica de Especificaciones.

La app trabaja con un fichero de formulación y un Excel de textos parametrizados por especie, subespecie y lifestage. El objetivo operativo es seleccionar textos ya preparados, volcarlos a campos editables y generar entregables descargables sin tener que teclear salvo correcciones puntuales.

## Archivos incluidos

- `main.py`: aplicación Streamlit.
- `requirements.txt`: dependencias Python.
- `README.md`: instrucciones de instalación y uso.

## Instalación

```bash
python -m venv .venv
source .venv/bin/activate      # Linux/Mac
# .venv\Scripts\activate       # Windows

pip install -r requirements.txt
```

## Ejecución

```bash
streamlit run main.py
```

La aplicación se abrirá en el navegador.

## Ficheros de entrada

### 1. Fichero de formulación

La app intenta leer formatos habituales:

- Excel: `.xlsx`, `.xlsm`, `.xls`, `.xlsb`, `.ods`.
- Texto/tablas: `.csv`, `.tsv`, `.txt`, `.dat`, `.prn`.
- `.json`.
- `.pdf` y `.docx` con texto extraíble.
- `.parquet`.

Además incorpora un parser específico para salidas de formulación tipo Multi-Mix con bloques de texto que contienen `Specification:`, ingredientes incluidos y sección `ANALYSIS`.

No existe una lectura universal real para cualquier fichero. Cuando el fichero no tiene una estructura reconocible, la app intenta extraer texto plano y detectar productos; si no lo consigue, conviene convertir el origen a Excel/CSV/TXT o revisar los encabezados.

### 2. Excel de etiquetas y beneficios

Debe contener dos hojas equivalentes a:

- `Etiquetas`
- `Beneficios`

Campos esperados en `Etiquetas`:

- Especie.
- Subespecie.
- Animales de destino.
- Opción.
- Tipo de pienso.
- Foco-beneficio, si existe.
- Modo de empleo.
- Precauciones de uso.
- Recomendaciones de manejo en etiquetado, si existen.

La app también reconoce variantes como `Modo 1`, `Modo 2`, `Modo 3`, `Observaciones` o `Recomendaciones`, consolidándolas para que los textos propuestos lleguen a los campos editables.

Campos esperados en `Beneficios`:

- Especie.
- Subespecie.
- Estado productivo / Lifestage.
- Foco beneficio.
- Opción.
- Texto beneficio.

La app tolera variaciones menores de nombre de columna mediante normalización de acentos, espacios y mayúsculas.

## Flujo de uso

1. Cargar el fichero de formulación.
2. Cargar el Excel de etiquetas y beneficios.
3. Seleccionar un producto desde el desplegable.
4. Seleccionar especie, subespecie y lifestage.
5. Elegir una opción de etiqueta y una opción de beneficio.
6. Revisar el panel `Textos propuestos`.
7. Pulsar `Pasar opciones elegidas a campos editables` si se quiere forzar la recarga de los textos seleccionados.
8. Revisar o modificar los campos editables. Por defecto se cargan con el nombre del producto y los textos seleccionados.
9. Seleccionar nutrientes para FT Comercial y para FT Calidad-Operaciones/FT Especificaciones.
10. Guardar nutrientes por defecto cuando se quiera reutilizar esa selección en siguientes usos.
11. Elegir los campos que deben incluirse en cada tipo de ficha.
12. Pulsar uno de los cuatro botones de generación.
13. Revisar el texto final y descargarlo.

## Campos editables y selector de campos

Cada tipo de ficha tiene su propio selector de campos. Las opciones disponibles incluyen:

- todos los campos editables principales;
- todos los campos de la FT Calidad-Operaciones;
- campos comerciales de beneficio;
- campos técnicos de fórmula como código/specification, fuente y coste cuando están disponibles.

El usuario puede incluir o excluir campos antes de generar cada documento.

## Nutrientes

Hay dos selectores independientes:

1. `FT Comercial`.
2. `FT Calidad-Operaciones y FT Especificaciones`.

Cada selector dispone de botón `Guardar nutrientes por defecto`. Al guardarlos, se crea o actualiza el archivo local `nutrient_defaults.json` junto a `main.py`. En siguientes usos de la app, esos nutrientes se cargan como selección inicial cuando existen en el producto seleccionado.

## Informes generados

Los documentos se generan con estructura de entregable:

- título del documento;
- bloques de información seleccionados por el usuario;
- tabla de constituyentes/nutrientes seleccionados;
- en FT Especificaciones, tablas separadas de `Límites de nutrientes` y `Límites de ingredientes`.

Las tablas no repiten `Nutriente:` o `Ingrediente:` en cada línea. Se presentan con encabezados y columnas de valor de fórmula, mínimo, máximo y tipo de límite cuando esos datos están disponibles.

## Formatos de salida

Cada ficha generada puede descargarse como:

- TXT.
- DOCX.
- PDF.
- Excel.

El texto final también es editable antes de la descarga.

## Acumulado masivo

Cada ficha generada puede añadirse al acumulado con el botón `Acumular`.

El acumulado se puede descargar desde la barra lateral como `acumulado_fichas_piensos.xlsx`.

Sirve para procesar muchos productos y consolidar todas las fichas calculadas en un único Excel.

## Botón de borrado

`Borrar información del producto actual` limpia campos editables y documentos generados para empezar con otro producto. Se conservan el acumulado y las selecciones guardadas de nutrientes.

## Limitaciones conocidas

- La lectura de PDF depende de que el PDF contenga texto extraíble. Si es una imagen escaneada, no se leerá salvo que previamente se haga OCR.
- Los ficheros de formulación muy heterogéneos pueden requerir conversión a tabla o ajuste del parser.
- La app no valida el cumplimiento legal/regulatorio de los textos generados. Debe existir revisión técnica y regulatoria antes de usar documentos definitivos.
- La generación PDF usa maquetación simple para entregable rápido; para diseño corporativo final puede usarse el DOCX o el TXT como base de maquetación.

## Estructura técnica

`main.py` está organizado en bloques:

- utilidades de normalización;
- lectura de ficheros;
- normalización de etiquetas y beneficios;
- parser Multi-Mix;
- parser genérico de tablas;
- inferencia básica producto → especie/subespecie/lifestage;
- carga de textos propuestos en campos editables;
- selector y persistencia de nutrientes;
- construcción de documentos;
- exportadores TXT/DOCX/PDF/XLSX;
- interfaz Streamlit.

## Despliegue recomendado

Para uso interno de equipo:

```bash
streamlit run main.py --server.maxUploadSize 500
```

Para uso multiusuario conviene desplegar en un servidor interno con control de acceso, ya que los ficheros de formulación y especificación pueden contener información sensible.
