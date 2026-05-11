Análisis de Ineficiencias en el Mercado de Apuestas de LaLiga
Asignatura: Introducción a los Sistemas Big Data — Valor de la Información
Universidad: Universidad Francisco de Vitoria (UFV)
Curso: 2025–2026
Integrantes: Pablo Peredo · Rodrigo Urrutia · Ricardo Sada · Nicolás Quetglas

Descripción del proyecto
Este proyecto analiza cinco temporadas de LaLiga española (2020/21 – 2024/25) para detectar ineficiencias sistemáticas en el mercado de apuestas deportivas. A partir de datos históricos de cuotas de Bet365 y Pinnacle, se construye un pipeline ETL completo que culmina en un análisis OLAP con PySpark.
La pregunta central es: ¿las casas de apuestas cometen errores de valoración consistentes en ciertos equipos, meses o tipos de resultado? Donde hay un patrón de error, hay una ineficiencia de mercado.

Fuente de datos
FuenteFormatoContenidoTemporadasfootball-data.co.ukCSVResultados y cuotas históricas de LaLiga (Bet365, Pinnacle)2020/21 – 2024/25

Estructura del repositorio
bigdata-apuestas-ufv/
├── data/
│   ├── raw/                  ← CSVs originales sin modificar de football-data.co.uk
│   │   ├── SP1 (3).csv       (temporada 2020/21)
│   │   ├── SP1 (4).csv       (temporada 2021/22)
│   │   ├── SP1 (5).csv       (temporada 2022/23)
│   │   ├── SP1 (6).csv       (temporada 2023/24)
│   │   └── SP1 (7).csv       (temporada 2024/25)
│   └── final/                ← Tablas del modelo estrella exportadas
│       ├── dim_equipo.csv
│       ├── dim_tiempo.csv
│       └── fact_partidos.csv
├── notebooks/
│   └── proyecto_final_pyspark.ipynb   ← Cuaderno PySpark (Google Colab)
└── README.md

Pipeline ETL
El pipeline sigue estas etapas en orden:

Extracción — Lectura de los 5 CSVs con unionByName(allowMissingColumns=True) para manejar columnas inconsistentes entre temporadas.
Análisis de calidad — Perfil completo por fuente: registros, variables, nulos, blancos, duplicados, errores de casteo y estadísticos básicos.
Transformación y limpieza — Casteo de tipos, normalización de fechas, derivación de temporada, cálculo de probabilidades implícitas y margen Bet365.
Modelado estrella — Construcción de Dim_Equipo, Dim_Tiempo y Fact_Partidos con claves sustitutas.
Carga SQL — Exportación a base de datos SQLite con DDL completo (PK, FK, NOT NULL).
Análisis OLAP con PySpark — Cubo dimensional con operaciones Roll-up, Drill-down, Slice & Dice, Pivot, Window Functions y 5 preguntas de negocio.


Modelo estrella
TablaTipoDescripciónFact_PartidosHechosResultado, goles, cuotas B365/Pinnacle, probabilidades implícitas, margenDim_EquipoDimensiónCatálogo de equipos con clave sustitutaDim_TiempoDimensiónFecha, mes, año, trimestre, día de semana, temporada

Operaciones OLAP implementadas
OperaciónDescripciónRoll-upEvolución de cuotas promedio por temporada y mesDrill-downMargen de error por equipo individualSlice & DicePartidos con discrepancia > 3% entre Bet365 y PinnaclePivot + HeatmapError medio cruzado por equipo × temporadaWindow FunctionsMedia móvil de 5 partidos del margen Bet365

Preguntas de negocio respondidas
#PreguntaQ1¿Cuál es la evolución anual del overround de Bet365 en LaLiga?Q2¿Qué equipos tienen mayor sesgo sistemático entre cuota implícita y rendimiento real?Q3¿En qué mes de la temporada el mercado comete más errores de valoración?Q4¿Cuáles son los 10 partidos con mayor discrepancia entre Bet365 y Pinnacle?Q5¿Existe diferencia en el error de Bet365 según el tipo de resultado (local, empate, visitante)?

Cómo ejecutar el notebook

Abre Google Colab
Sube el archivo notebooks/proyecto_final_pyspark.ipynb
Sube los 5 CSVs de data/raw/ al entorno de Colab (/content/)
Ejecuta Runtime → Run all

No se requiere ninguna instalación adicional. El notebook instala PySpark automáticamente en la primera celda.

Tecnologías utilizadas

Python 3.10+
PySpark 3.x (Google Colab)
pandas, matplotlib
SQLite3
GitHub (control de versiones y almacenamiento de datos)
