# Generador de etiquetas y fichas técnicas de piensos

Aplicación Streamlit para confeccionar de forma rápida textos de:

1. Etiquetas.
2. Ficha técnica de Calidad-Operaciones.
3. Ficha técnica Comercial.
4. Ficha técnica de Especificaciones.

La app está pensada para trabajar con un fichero de formulación y un Excel de textos parametrizados por especie, subespecie y lifestage.

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

Además incorpora parsers específicos para salidas de formulación en texto:

- bloques tipo Multi-Mix con `Specification:`, ingredientes incluidos y sección `ANALYSIS`;
- bloques tipo Single-Mix/SP con líneas `SP:`, secciones `INCLUDED RAW MATERIALS`, `REJECTED RAW MATERIALS` y `NUTRIENT ANALYSIS`.

En los ficheros Excel de una sola columna generados como listado de formulación, la app ya no interpreta cada materia prima como producto: detecta cada bloque `SP:` como producto.

No existe una lectura realmente universal de cualquier fichero. Cuando el fichero no tiene una estructura reconocible, la app intenta extraer texto plano y detectar productos; si no lo consigue, se debe convertir el origen a Excel/CSV/TXT o revisar los encabezados.

### 2. Excel de etiquetas y beneficios

Si existe el archivo maestro en el repositorio, la app lo cargará automáticamente y no será necesario subirlo en cada uso:

```text
data/Etiquetas_y_beneficios_para_App_animales_destino.xlsx
```

Desde la barra lateral se puede marcar la opción de cargar otro Excel si se quiere sustituir temporalmente el maestro.

Debe contener dos hojas equivalentes a:

- `Etiquetas`
- `Beneficios`

Campos esperados en `Etiquetas`:

- Especie
- Subespecie
- Animales de destino
- Opción
- Tipo de pienso
- Foco-Beneficio
- Modo de empleo
- Precauciones de uso

Campos esperados en `Beneficios`:

- Especie
- Subespecie
- Estado productivo / Lifestage
- Foco beneficio
- Opción
- Texto beneficio

La app tolera pequeñas variaciones de nombre de columna mediante normalización de acentos, espacios y mayúsculas.

## Flujo de uso

1. Cargar el fichero de formulación.
2. Cargar el Excel de etiquetas y beneficios.
3. Seleccionar un producto desde el desplegable.
4. Seleccionar especie, subespecie y lifestage.
5. Elegir una opción de etiqueta y una opción de beneficio.
6. Editar manualmente todos los campos necesarios.
7. Seleccionar nutrientes a incorporar.
8. Elegir los campos que deben incluirse en cada tipo de ficha.
9. Pulsar uno de los cuatro botones:
   - Generar Etiqueta.
   - Generar FT Calidad-Operaciones.
   - Generar FT Comercial.
   - Generar FT Especificaciones.
10. Revisar y editar el texto final.
11. Descargar en:
   - TXT
   - DOCX
   - PDF
   - Excel

## Acumulado masivo

Cada ficha generada puede añadirse al acumulado con el botón `Acumular`.

El acumulado se puede descargar desde la barra lateral como `acumulado_fichas_piensos.xlsx`.

Sirve para ir procesando muchos productos y consolidar todas las fichas calculadas en un único Excel.

## Persistencia de nutrientes

La selección de nutrientes se mantiene en la sesión de Streamlit para reutilizarla en:

- siguientes productos;
- otros tipos de ficha del mismo producto.

Si se pulsa `Borrar información del producto actual`, se limpian los campos editables y documentos generados, pero se conserva la selección global de nutrientes y el acumulado.

## Salidas generadas

### Etiqueta

Por defecto incluye:

- Nombre comercial.
- Tipo de pienso.
- Especie.
- Subespecie.
- Animales de destino.
- Modo de empleo.
- Precauciones de uso / recomendaciones.
- Presentación.
- Peso del producto.
- EAN.

### FT Calidad-Operaciones

Por defecto incluye los campos operativos internos:

- fecha, versión, código, EAN;
- tipo de pienso, especie, subespecie y destino;
- modo de empleo y precauciones;
- envase, palé, presentación;
- parámetros de durabilidad y finos;
- fábrica y datos operativos;
- nutrientes seleccionados.

### FT Comercial

Por defecto incluye:

- posicionamiento;
- características;
- beneficio;
- modo de empleo;
- campos de etiqueta;
- nutrientes seleccionados.

### FT Especificaciones

Por defecto incluye lo anterior y añade:

- límites de nutrientes;
- límites de ingredientes;
- restricciones detectadas en el fichero de fórmula.

La calidad de esos límites depende de que el fichero de formulación contenga esa información de forma parseable.

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
- selector de nutrientes;
- construcción de documentos;
- exportadores TXT/DOCX/PDF/XLSX;
- interfaz Streamlit.

## Despliegue recomendado

Para uso interno de equipo:

```bash
streamlit run main.py --server.maxUploadSize 500
```

Para uso multiusuario conviene desplegar en un servidor interno con control de acceso, ya que los ficheros de formulación y especificación pueden contener información sensible.


## Persistencia de selecciones

La app guarda selecciones repetitivas en archivos JSON locales del despliegue:

- `nutrient_defaults.json`: nutrientes por defecto para FT Comercial y para FT Calidad-Operaciones / FT Especificaciones.
- `quality_operation_defaults.json`: campos adicionales de Calidad-Operaciones / Especificaciones definidos por el usuario.

Estos archivos permiten evitar teclear datos repetidos. En Streamlit Cloud permanecen mientras el entorno de la app conserve esos archivos; si se redepliega desde cero, se pueden volver a generar usando los botones de guardar.
