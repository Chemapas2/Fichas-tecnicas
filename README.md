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
6. Editar manualmente todos los campos necesarios.
7. Seleccionar nutrientes a incorporar: un selector para FT Comercial y otro para FT Calidad-Operaciones / FT Especificaciones.
8. Si procede, pulsar `Guardar nutrientes` para que esas selecciones salgan por defecto en siguientes productos o sesiones.
9. Elegir los campos que deben incluirse en cada tipo de ficha.
10. Pulsar uno de los cuatro botones:
   - Generar Etiqueta.
   - Generar FT Calidad-Operaciones.
   - Generar FT Comercial.
   - Generar FT Especificaciones.
11. Revisar y editar el texto final.
12. Descargar en:
   - TXT
   - DOCX
   - PDF
   - Excel

## Acumulado masivo

Cada ficha generada puede añadirse al acumulado con el botón `Acumular`.

El acumulado se puede descargar desde la barra lateral como `acumulado_fichas_piensos.xlsx`.

Sirve para ir procesando muchos productos y consolidar todas las fichas calculadas en un único Excel.

## Persistencia de nutrientes

La app dispone de dos perfiles de nutrientes:

- `FT Comercial`: nutrientes que se incorporan a la ficha comercial.
- `FT Calidad-Operaciones y FT Especificaciones`: nutrientes que se incorporan a las fichas técnica-operativa y de especificaciones.

Cada perfil puede tener una selección distinta. Los botones `Guardar nutrientes FT Comercial` y `Guardar nutrientes FT Técnica/Especificaciones` guardan la selección como defecto para siguientes productos y siguientes ejecuciones de la app.

La persistencia se realiza en el fichero local `nutrient_defaults.json`, creado automáticamente junto a `main.py`. Si se despliega en un servidor multiusuario, ese fichero será compartido por los usuarios de la misma instalación salvo que se adapte la app para persistencia por usuario.

Si se pulsa `Borrar información del producto actual`, se limpian los campos editables y documentos generados, pero se conservan las selecciones de nutrientes guardadas y el acumulado.

## Campos editables y textos propuestos

Al seleccionar especie, subespecie, lifestage, opción de etiqueta u opción de beneficio, los campos editables principales se rellenan automáticamente con los textos elegidos arriba:

- tipo de pienso;
- animales de destino;
- modo de empleo;
- precauciones de uso / recomendaciones;
- foco-beneficio;
- beneficio.

Después se pueden modificar manualmente. Si se cambia la opción propuesta, la app vuelve a cargar en esos campos los textos de la nueva opción seleccionada.

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
- selector doble de nutrientes y persistencia de perfiles;
- construcción de documentos;
- exportadores TXT/DOCX/PDF/XLSX;
- interfaz Streamlit.

## Despliegue recomendado

Para uso interno de equipo:

```bash
streamlit run main.py --server.maxUploadSize 500
```

Para uso multiusuario conviene desplegar en un servidor interno con control de acceso, ya que los ficheros de formulación y especificación pueden contener información sensible.
