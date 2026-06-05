Máster en Ciencia de Datos

Universitat Oberta de Catalunya 
 
Autoría: Tatyana Silchenko
  
# Data Career Atlas (2020–2024)

## Estructura de carpetas: 
```
visdatos_pra2-skills/
│
├── index.html 
│  
├── README.md       
│
├── html/             
│     ├── fig1..html
│     ...
│     └── fig7..html
│
├── notebooks/
│     └── etl-skills_visdatos_pra2.ipynb
│
├── data/
│     ├── raw/
│     │     ├── stackover/..
│     │     ├── stackover_20-21/..
│     │     ├── un_m49_snapshot.parquet
│     │     │     ...
│     │     └── WHR26_Data_Figure_2.1.xlsx
│     ├── processed/
│     │     ├── 1_salaries_country.csv
│     │     │     ...
│     │     └── countries_scores.csv

```
---

# Reproducibilidad
1. clonar el proyecto: Abrir una terminal (CMD, PowerShell, Anaconda Prompt) 
y ejecutar:
```
git clone https://github.com/Sity127001/visdatos_pra2-skills.git
```
2. Situarse en la carpeta del repositorio clonado, 
por ejemplo - si se clona en el disco C:\ reemplasar User:
```
cd C:\Users\User\visdatos_pra2-skills
```
3. Crear el entorno definido en el fichero environment.yml:
```
conda env create -f environment.yml 
```
4. activar el entorno
```
conda activate visdatos-pra2
```
5. Abrir el Jupyter notebook desde Anaconda o desde Visual Studio Code y ejecutar el 
notebook:
```
etl-skills_visdatos_pra2.ipynb
```
La ejecución del notebook genera los ficheros procesados y los gráficos HTML utilizados 
posteriormente para construir la visualización final.

6. Personalización de la visualización
El archivo `index.html` puede editarse mediante Visual Studio Code 
para modificar aspectos visuales como colores, tipografías, estilos o 
distribución de elementos y/o modificación de los mismos gráficos - filtros adicionales etc.

7. Publicación de la visualización

La visualización puede publicarse mediante:

- GitHub Pages, utilizando el archivo `index.html` almacenado en el repositorio.
- Netlify, desplegando directamente el archivo `index.html` o el contenido completo 
del proyecto.

Una vez publicado, se obtiene una URL pública para acceder a la visualización desde 
cualquier navegador.

El enlace generado:
```
https://eclectic-rugelach-c22120.netlify.app/
```
---

## Descripción general

**Data Career Atlas** es un proyecto de análisis y visualización que integra datos económicos,
sociales y laborales para responder dos preguntas clave:

- **Dónde se vive mejor** como profesional de datos (2020–2024).  
- **Qué skills y roles** permiten acceder a esas oportunidades (zoom 2024).

El proyecto se estructura en dos actos:

### Acto 1 — “Dónde se vive bien” (2020–2024)
Salarios, coste de vida, felicidad, educación e índice compuesto.

### Acto 2 — “Qué te lleva ahí” (2024)
Skills, roles y demanda real en job postings.

---

# 1. Procesamiento y limpieza de datos

## 1.1 Normalización global de países

Todos los datasets fueron estandarizados mediante:

- `pycountry` para nombres oficiales  
- UN M49 / ISO 3166 para regiones y códigos de país, obtenido mediante scraping responsable 
y guardado como diccionario local en formato Parquet para evitar dependencias externas.  
  - Corrección manual de variantes lingüísticas y duplicados  

**Fuente oficial:**  
https://unstats.un.org/unsd/methodology/m49/

---

## 1.2 Normalización de skills

- Conversión a minúsculas  
- Limpieza de símbolos y espacios  
- `explode` de listas de skills en job postings  
- Consolidación de **233 skills únicas** en 2024  
- Unificación de categorías profesionales  

---

## 1.3 Filtrado temporal

- Datasets limitados a **2020–2024**  
- Job postings: solo **2024**  
- Stack Overflow:  
  - **2020–2021 → TXT**  
  - **2022–2025 → CSV**  
- Integración mediante carga iterativa y normalización de columnas  

---

## 1.4 Cálculo de salario real

```
salary_real = salary_in_usd / cost_of_living_index
```
---

## 1.5 Normalización min–max

Aplicada a:

- salario real  
- felicidad  
- educación  

---

## 1.6 Índice compuesto (`country_score`)

Ponderación:

- **50%** salario real normalizado  
- **30%** felicidad normalizada  
- **20%** educación normalizada  



\[
country\_score = 
0.5 \cdot salary\_real\_norm +
0.3 \cdot happiness\_norm +
0.2 \cdot education\_norm
\]



---

# 2. Datasets finales generados

## 2.1 Salaries (country–year–usd, 2020–2024)
`salaries_country`  
Columnas: `region`, `country`, `year`, `salary_in_usd`.

## 2.2 Salaries por rol (2020–2024)
`salaries_roles`  
Columnas: `year`, `country`, `region`, `role_category`, `salary_in_usd`.

## 2.3 Skills por rol (job postings 2024)
`skills_by_role_final`  
Columnas: `country`, `year`, `role_category`, `skill_clean`, `count`.

## 2.4 Top skills en job postings (2024)
`top_skills_jobs`  
Columnas: `country`, `year`, `job_skills`, `count_jobs`.

## 2.5 Cost of Living Index (2020–2024)
`cost_clean`  
Columnas: `country`, `year`, `cost_of_living_index`.

## 2.6 Stack Overflow skills (2020–2024)
`skills_year`  
Columnas: `year`, `country`, `skills`, `responders`, `salary_mean`.

## 2.7 Happiness (2020–2024)
`happy_clean`  
Columnas: `country`, `happiness_score`, `year`.

## 2.8 Education Index (2020–2024)
`edu_clean`  
Columnas: `year`, `country`, `education_index`.

## 2.9 Dataset unificado (country-year)
`country_metrics`  
Columnas:  
`region`, `country`, `year`, `salary_in_usd`, `cost_of_living_index`,  
`salary_real`, `happiness_score`, `education_index`, `country_score`.

---

# 3. Fuentes originales

## 3.1 Acto 1 — “Dónde se vive bien” (2020–2024)

### Data Salaries (2020–2024)
Fuente: https://www.kaggle.com/datasets/willianoliveiragibin/data-jobs-salaries  
Formato: TXT  
Descripción: salarios por país, rol y experiencia.

### Cost of Living Index (2018–2026)
Fuente: https://www.kaggle.com/datasets/naifnoor/global-cost-of-living-index-by-country-20182026  
Formato: CSV  
Descripción: índice comparativo de coste de vida.

### World Happiness Report (2011–2025)
Fuente: https://www.worldhappiness.report/data-sharing/  
Formato: Excel  
Descripción: bienestar subjetivo y variables explicativas.

### Human Development Index (2020–2023, extendido a 2024)
Fuente: https://hdr.undp.org/data-center/documentation-and-downloads  
Formato: CSV  

Justificación de imputación 2024:

- HDI es estable y de baja volatilidad  
- Se aplicó *forward fill* desde 2023  
- Se evitó usar valores antiguos para no distorsionar tendencias  

---

## 3.2 Acto 2 — “Qué te lleva ahí” (2024)

### Job Postings (2024)
Fuente:  
https://www.kaggle.com/datasets/abubakerasiel/global-data-science-and-analytics-job-dataset  
Formato: Excel  
Descripción: ofertas reales de empleo en data science y analytics.

### Stack Overflow Developer Survey (2020–2025)
Fuente: https://survey.stackoverflow.co/  
Formato:  
- 2020–2021 → TXT  
- 2022–2025 → CSV  
Descripción: tecnologías usadas, salarios, experiencia y contexto laboral.

---

# 4. Justificación del uso de Kaggle

- Muchos datasets tecnológicos no están disponibles en formato abierto  
- Kaggle ofrece versiones estructuradas y limpias  
- Se garantiza trazabilidad hacia fuentes oficiales cuando existe correspondencia  
- Reduce el coste de scraping y procesamiento inicial  

---

# 5. Enlace entre actos

> No basta con saber dónde se vive mejor; también es necesario entender qué habilidades permiten acceder a esos contextos.

Acto 1 define el destino.  
Acto 2 define la ruta profesional.

---

# 6. Visualización final

El dashboard HTML **Data Career Atlas · 2020–2024** presenta:

- Acto 1: evolución 2020 - 2024 y cierre 2024  
- Acto 2: skills, roles y demanda laboral 2024  




