# Generador de etiquetas y fichas técnicas de piensos

Aplicación Streamlit para confeccionar de forma rápida textos y entregables de:

1. Etiquetas.
2. Ficha técnica de Calidad-Operaciones.
3. Ficha técnica Comercial.
4. Ficha técnica de Especificaciones.

La app trabaja con un fichero de formulación y con un Excel maestro de textos parametrizados por especie, subespecie y lifestage.

## Archivos incluidos

- `main.py`: aplicación Streamlit.
- `requirements.txt`: dependencias Python.
- `README.md`: instrucciones de instalación, uso y despliegue.

## Archivo maestro de etiquetas y beneficios en GitHub

Para no tener que cargar el Excel de etiquetas y beneficios en cada uso, colócalo dentro del repositorio con esta estructura:

```text
fichas-tecnicas/
├─ main.py
├─ requirements.txt
├─ README.md
└─ data/
   └─ Etiquetas_y_beneficios_para_App_animales_destino.xlsx
```

La app busca automáticamente este archivo:

```python
MASTER_TEXTS_PATH = "data/Etiquetas_y_beneficios_para_App_animales_destino.xlsx"
```

Si el archivo existe, se carga al arrancar la app. Si no existe o falla la lectura, aparece el cargador manual en la barra lateral. También hay una opción para usar otro Excel manualmente si se necesita sustituir el maestro de forma puntual.

## Instalación local

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

## Ficheros de entrada

### 1. Fichero de formulación

La app intenta leer formatos habituales:

- Excel: `.xlsx`, `.xlsm`, `.xls`, `.xlsb`, `.ods`.
- Texto/tablas: `.csv`, `.tsv`, `.txt`, `.dat`, `.prn`.
- `.json`.
- `.pdf` y `.docx` con texto extraíble.
- `.parquet`.

Además incorpora un parser específico para salidas de formulación tipo Multi-Mix con bloques de texto que contienen `Specification:`, ingredientes incluidos y sección `ANALYSIS`.

No existe una lectura literalmente universal de cualquier fichero. Cuando el fichero no tiene una estructura reconocible, la app intenta extraer texto plano y detectar productos; si no lo consigue, se debe convertir el origen a Excel/CSV/TXT o ajustar el parser.

### 2. Excel de etiquetas y beneficios

Debe contener dos hojas equivalentes a:

- `Etiquetas`.
- `Beneficios`.

Campos esperados en `Etiquetas`:

- Especie.
- Subespecie.
- Animales de destino.
- Opción.
- Tipo de pienso.
- Foco-Beneficio, si aplica.
- Modo de empleo.
- Precauciones de uso.
- Recomendaciones de manejo en etiquetado, si aplica.

Campos esperados en `Beneficios`:

- Especie.
- Subespecie.
- Estado productivo / Lifestage.
- Foco-beneficio.
- Opción.
- Texto beneficio.

La app tolera variaciones menores de nombre de columna mediante normalización de acentos, espacios y mayúsculas. También consolida columnas equivalentes, por ejemplo `Modo 1`, `Modo 2`, `Modo 3` o columnas repetidas normalizadas al mismo campo.

## Flujo de uso

1. Cargar el fichero de formulación.
2. Seleccionar un producto desde el desplegable.
3. Confirmar especie, subespecie y lifestage.
4. Elegir una opción de etiqueta y una opción de beneficio.
5. Pulsar `Pasar opciones elegidas a campos editables` si se desea forzar la copia de las propuestas.
6. Revisar o editar los campos principales.
7. Revisar los campos adicionales de Calidad-Operaciones y Especificaciones.
8. Seleccionar nutrientes para:
   - FT Comercial.
   - FT Calidad-Operaciones y FT Especificaciones.
9. Guardar nutrientes por defecto si se desea reutilizar esa selección.
10. Elegir los campos que deben incluirse en cada tipo de ficha.
11. Generar la ficha deseada.
12. Revisar el texto final y descargarlo en TXT, DOCX, PDF o Excel.

## Persistencia de campos adicionales

En el bloque `Campos adicionales de Calidad-Operaciones y Especificaciones` existe el botón:

```text
Guardar campos adicionales por defecto
```

Ese botón guarda la información en:

```text
quality_operation_defaults.json
```

Así, en los siguientes productos o al volver a usar la app, los campos adicionales aparecen ya rellenos y solo hay que editar lo que cambie.

El botón `Borrar información del producto actual` no borra:

- campos adicionales guardados;
- nutrientes guardados;
- acumulado de fichas.

## Persistencia de nutrientes

Hay dos selectores independientes:

- `Nutrientes para FT Comercial`.
- `Nutrientes para FT Calidad-Operaciones y FT Especificaciones`.

Cada uno dispone de su botón `Guardar nutrientes`. La selección se guarda en:

```text
nutrient_defaults.json
```

En el siguiente uso, esos nutrientes se proponen por defecto si existen en el producto seleccionado.

## Presentación de nutrientes y límites

Los informes muestran nutrientes, límites de nutrientes e ingredientes como tablas legibles, por ejemplo:

```text
LÍMITES DE INGREDIENTES
Ingrediente | % fórmula | Mínimo | Máximo
----------- | --------- | ------ | ------
CEBADA      | 18        | 10     | 25
TRIGO       | 12        |        | 30
```

En DOCX y PDF, esas tablas se exportan como tablas reales siempre que las dependencias estén instaladas.

## Salidas generadas

Cada ficha puede descargarse en:

- TXT.
- DOCX.
- PDF.
- Excel.

Además, cada ficha puede añadirse al acumulado mediante `Acumular`. El acumulado se descarga desde la barra lateral como:

```text
acumulado_fichas_piensos.xlsx
```

## Despliegue en Streamlit Cloud

1. Sube `main.py`, `requirements.txt` y `README.md` al repositorio.
2. Crea la carpeta `data` y sube dentro el Excel maestro.
3. En Streamlit Cloud selecciona el repositorio y `main.py` como archivo principal.
4. Reinicia la app tras subir cambios importantes.

## Limitaciones conocidas

- La lectura de PDF depende de que el PDF contenga texto extraíble. Si es una imagen escaneada, no se leerá salvo OCR previo.
- Los ficheros de formulación muy heterogéneos pueden requerir conversión a tabla o ajuste del parser.
- La app no valida cumplimiento legal/regulatorio de los textos generados. Debe existir revisión técnica y regulatoria antes de usar documentos definitivos.
- En Streamlit Cloud, los JSON de persistencia se conservan en el entorno de ejecución mientras la app esté desplegada, pero no sustituyen a un sistema corporativo de base de datos si se necesita persistencia multiusuario formal.
