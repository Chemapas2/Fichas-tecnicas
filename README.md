# Generador de etiquetas y fichas técnicas de piensos

Aplicación Streamlit para confeccionar de forma rápida textos de:

1. Etiquetas.
2. Ficha técnica de Calidad-Operaciones.
3. Ficha técnica Comercial.
4. Ficha técnica de Especificaciones.

La app trabaja con un fichero de formulación y un Excel de textos parametrizados por especie, subespecie y lifestage. El objetivo es que el usuario seleccione opciones y solo tenga que teclear cuando quiera modificar el texto propuesto.

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


## Corrección incluida en esta versión

Esta versión corrige el fallo que podía aparecer en el bloque `3. Nutrientes` en Streamlit Cloud. El selector de nutrientes ya no depende de estado antiguo de ejecución ni de claves persistidas de productos previos. Además, si el fichero `nutrient_defaults.json` no existe, está vacío o no se puede leer, la app sigue funcionando y usa una selección inicial automática.

## Ficheros de entrada

### 1. Fichero de formulación

La app intenta leer formatos habituales:

- Excel: `.xlsx`, `.xlsm`, `.xls`, `.xlsb`, `.ods`.
- Texto/tablas: `.csv`, `.tsv`, `.txt`, `.dat`, `.prn`.
- `.json`.
- `.pdf` y `.docx` con texto extraíble.
- `.parquet`.

Además incorpora un parser específico para salidas de formulación tipo Multi-Mix con bloques de texto que contienen `Specification:`, ingredientes incluidos y sección `ANALYSIS`.

No existe una lectura realmente universal de cualquier fichero. Cuando el fichero no tiene una estructura reconocible, la app intenta extraer texto plano y detectar productos; si no lo consigue, se debe convertir el origen a Excel/CSV/TXT o revisar los encabezados.

### 2. Excel de etiquetas y beneficios

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
6. Pulsar `Pasar opciones elegidas a campos editables` cuando se quiera copiar la propuesta a los campos editables.
7. Revisar los campos editables. Salen rellenos por defecto con el nombre limpio del producto y con los textos seleccionados.
8. Seleccionar nutrientes en los dos perfiles disponibles:
   - FT Comercial.
   - FT Calidad-Operaciones y FT Especificaciones.
9. Guardar nutrientes por defecto en cada perfil si se quieren reutilizar en siguientes sesiones.
10. Elegir los campos que deben incluirse en cada tipo de ficha.
11. Pulsar uno de los cuatro botones de generación.
12. Revisar y editar el texto final.
13. Descargar en TXT, DOCX, PDF o Excel.

## Campos editables

Los campos principales se rellenan automáticamente con:

- nombre comercial limpio del producto seleccionado;
- tipo de pienso;
- animales de destino;
- modo de empleo;
- precauciones de uso / recomendaciones;
- foco-beneficio;
- texto de beneficios.

Si se cambia la opción de etiqueta o de beneficio, el botón `Pasar opciones elegidas a campos editables` actualiza los campos editables sin necesidad de copiar y pegar.

## Selector de campos por ficha

Cada tipo de ficha tiene su propio selector de campos. Las opciones incluyen todos los campos editables y los campos de la FT de Calidad-Operaciones.

Los valores por defecto son distintos para:

- Etiqueta.
- FT Calidad-Operaciones.
- FT Comercial.
- FT Especificaciones.

## Nutrientes

Hay dos selectores independientes:

1. `FT Comercial`.
2. `FT Calidad-Operaciones y FT Especificaciones`.

Cada selector tiene un botón `Guardar nutrientes`. La selección se guarda en `nutrient_defaults.json` y se recupera en siguientes usos de la app.

Las fichas muestran los nutrientes como tabla, sin repetir `Nutriente:` antes de cada línea.

## Límites de nutrientes e ingredientes

En la FT de Especificaciones se añaden, si están disponibles en el fichero de formulación:

- `LÍMITES DE NUTRIENTES`.
- `LÍMITES DE INGREDIENTES`.

Ambas secciones se presentan como tablas con columnas de valor, restricción, mínimo y máximo cuando esos datos existen.

## Acumulado masivo

Cada ficha generada puede añadirse al acumulado con el botón `Acumular`.

El acumulado se puede descargar desde la barra lateral como `acumulado_fichas_piensos.xlsx`.

Sirve para ir procesando muchos productos y consolidar todas las fichas calculadas en un único Excel.

## Salidas generadas

### Etiqueta

Por defecto incluye campos de etiqueta: nombre comercial, tipo de pienso, especie, subespecie, animales de destino, modo de empleo, precauciones/recomendaciones, presentación, peso y EAN.

### FT Calidad-Operaciones

Por defecto incluye campos operativos internos, modo de empleo, precauciones, envase, palé, presentación, parámetros de durabilidad/finos, fábrica, datos operativos y nutrientes seleccionados para este perfil.

### FT Comercial

Por defecto incluye posicionamiento, características, beneficio, modo de empleo, campos de etiqueta y nutrientes seleccionados para el perfil comercial.

### FT Especificaciones

Por defecto incluye datos de especificación, nutrientes seleccionados para el perfil técnico y límites de nutrientes e ingredientes detectados en la fórmula.

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
- selectores de nutrientes persistentes;
- construcción de documentos;
- exportadores TXT/DOCX/PDF/XLSX;
- interfaz Streamlit.

## Despliegue recomendado

Para uso interno de equipo:

```bash
streamlit run main.py --server.maxUploadSize 500
```

Para uso multiusuario conviene desplegar en un servidor interno con control de acceso, ya que los ficheros de formulación y especificación pueden contener información sensible.
