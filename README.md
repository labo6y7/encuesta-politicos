# README: Metodología y Estructura de Datos — Encuesta de Percepción de Rostros

> **Propósito de este documento:** Proveer una descripción completa, estructurada e inambigua de la metodología experimental, las variables registradas, el diseño muestral y la arquitectura de datos de la encuesta. Diseñado para ser interpretado por modelos de lenguaje (LLMs) previo al análisis estadístico y descriptivo de los resultados.

---

## 1. Visión General del Estudio

* **Institución / Laboratorio:** Laboratorio de Neurociencia de la Universidad Torcuato Di Tella (UTDT), Buenos Aires, Argentina.
* **Objetivo:** Evaluar la relación entre la postura/identificación política de los participantes y sus reacciones afectivas y explícitas ante la observación de rostros de figuras políticas argentinas.
* **Población Objetivo:** Adultos de 18 años o más residentes o votantes en Argentina.
* **Modalidad de Participación:** Online, anónima, voluntaria y autoadministrada mediante un flujo dinámico por lotes (modelos/grupos de imágenes).

---

## 2. Diseño Experimental y Estímulos

### 2.1. Figuras Políticas (Estímulos)
El estudio evalúa a **5 figuras políticas argentinas**:
1. Javier Milei (`milei`)
2. Patricia Bullrich (`bullrich`)
3. Cristina Fernández de Kirchner (`kirchner`)
4. Sergio Massa (`massa`)
5. Axel Kicillof (`kicillof`)

### 2.2. Condiciones y Emociones
Cada figura política posee fotografías clasificadas bajo **3 expresiones/emociones esperadas**:
* Positiva (`positiva` / `pos`)
* Negativa (`negativa` / `neg`)
* Neutra (`neutra` / `neu`)

### 2.3. Estructura de Modelos (Diseño Between/Within Subject Mixto)
* **Total de imágenes en el banco:** 60 fotografías únicas (12 fotografías por político: 4 positivas, 4 negativas, 4 neutras). Ninguna foto se repite entre modelos.
* **Modelos o Grupos:** Las fotografías están repartidas en **3 modelos/bloques disjuntos** de 20 fotografías cada uno (Modelo 1, Modelo 2, Modelo 3).
* **Composición por modelo:** Cada modelo contiene 20 fotografías = 5 políticos × 4 fotos por político.
  * La distribución de emociones por político dentro de cada modelo es 2-1-1 (dos fotos de una emoción y una foto de cada una de las otras dos), rotando qué emoción se duplica entre cada modelo para asegurar balance completo al consolidar la muestra.
* **Bloque voluntario incremental:**
  * Al ingresar, el sistema asigna dinámicamente al usuario el modelo que posea el **menor número de respuestas registradas en la base de datos** al momento del inicio (mecanismo de balanceo automático de carga).
  * Tras completar un bloque de 20 imágenes, el participante puede decidir finalizar la encuesta o continuar respondiendo un bloque adicional (sin repetir bloques previa o actualmente completados).

### 2.4. Secuenciación y Restricciones de Aleatorización (Trials)
Para evitar sesgos de respuesta o facilitación priming:
1. **Consecutividad:** No se presentan dos fotografías consecutivas de la misma identidad/figura política (siempre que la muestra tenga $>1$ identidad activa).
2. **Separación intra-sujeto:** Si existen ítems de prueba/repetidos, se fuerzan distancias mínimas de separación dentro de la secuencia.

---

## 3. Flujo de la Encuesta y Variables Recolectadas

El experimento consta de las siguientes pantallas/pasos secuenciales:

### Paso 1: Consentimiento Informado (`p-consent`)
* Filtro de edad ($\ge 18$ años) y aceptación voluntaria.

### Paso 2: Datos Demográficos (`p-demo`)
1. `edad`: Variable numérica entera ($18 - 99$).
2. `genero`: Categoría seleccionada.
   * Valores: `mujer` (Mujer), `varon` (Hombre), `no_binario` (No binario), `otro` (Otro), `ns_nc` (Prefiero no responder).
   * *Nota metodológica de diseño:* La encuesta adapta gramaticalmente los textos de los ensayos (arousal/partidos) según el género seleccionado (`f`, `m` o neutro/reformulado `x`).
3. `educacion`: Máximo nivel educativo alcanzado.
   * Valores: `secundario_inc`, `secundario`, `terciario`, `universitario_inc`, `universitario`, `posgrado`.

### Paso 3: Identificación Política (`p-politica`)
1. `ideologia`: Autoubicación en el eje izquierda-derecha.
   * Escala ordinal/continua de **0 a 10** (0 = Extrema izquierda, 10 = Extrema derecha).
2. `partido`: Espacio político con el que más se identifica.
   * Opciones presentadas en orden aleatorizado (salvo fijos): `lla` (La Libertad Avanza), `fp` (Fuerza Patria / UxP), `pro` (PRO), `fit` (Frente de Izquierda), `otro` (Otro), `ninguno` (Ninguno / No me identifico).
3. `orden_partidos`: Cadena de texto que registra el orden exacto en el que fueron mostradas las opciones de partidos (control de sesgo de orden).
4. `interes_politico`: Nivel de interés en la política.
   * Escala ordinal/continua de **0 a 10** (0 = Ningún interés, 10 = Extremo interés).

### Paso 4: Tarea Experimental / Ensayos (`p-trial`)
Por cada imagen del modelo activo (20 imágenes por bloque) se evalúan 3 dimensiones:
1. `valencia`: Evaluación afectiva subjetiva ante la imagen.
   * Escala tipo Likert de **1 a 7** (1 = Totalmente desagradable, 4 = Ni agradable ni desagradable, 7 = Totalmente agradable).
2. `arousal`: Grado de activación o calma provocado por la imagen.
   * Escala tipo Likert de **1 a 7** (1 = Totalmente relajado/a / Calma total, 4 = Moderado/a, 7 = Totalmente activado/a / Activación total).
3. `expresion`: Categorización categórica explícita de la expresión percibida en el rostro presentado.
   * Valores categóricos: `negativa`, `neutra`, `positiva`.
4. `rt_ms`: Tiempo de reacción en milisegundos transcurrido desde la presentación de la imagen hasta la confirmación de la respuesta.

### Paso 5: Termómetro de Sentimiento (`p-termo`)
* Evaluaciones globales por figura política, independientes de las fotografías presentadas.
* `termometro`: Objeto/Diccionario tipo Key-Value donde la clave es el nombre de la figura política y el valor es un entero entre **0 y 100** (0 = Sentimiento totalmente negativo, 50 = Indiferencia, 100 = Sentimiento totalmente positivo).
* `comentario`: Campo de texto libre opcional (máximo 1000 caracteres).

---

## 4. Estructura de Almacenamiento e Interfaz de Datos (Supabase)

Los datos resultantes se persisten en dos tablas relacionales vinculadas por la clave `sesion_id` (UUID único generado por sesión de usuario).

### Tabla A: `participantes` (Nivel Sujeto / Sesión)
Almacena 1 fila por sesión/participante completado.

| Columna | Tipo de Dato | Descripción / Valores |
| :--- | :--- | :--- |
| `sesion_id` | String / UUID | Identificador único global de la sesión. |
| `edad` | Integer | Edad en años ($18-99$). |
| `genero` | String | `mujer`, `varon`, `no_binario`, `otro`, `ns_nc`. |
| `educacion` | String | Nivel educativo máximo alcanzado. |
| `ideologia` | Integer | Escala $0-10$ ($0 = \text{Izq}, 10 = \text{Der}$). |
| `interes_politico`| Integer | Escala $0-10$ ($0 = \text{Sin interés}, 10 = \text{Extremo}$). |
| `partido` | String | Opción de partido elegida (`lla`, `fp`, `pro`, `fit`, `otro`, `ninguno`). |
| `orden_partidos` | String | Lista separada por comas con el orden de presentación de opciones. |
| `termometro` | JSON / Map | Diccionario con los puntajes $0-100$ para cada político evaluado. |
| `comentario` | String | Feedback cualitativo opcional. |
| `duracion_ms` | Integer | Tiempo total acumulado de la sesión en ms. |
| `n_trials` | Integer | Cantidad total de imágenes respondidas ($20, 40, 60$). |
| `modelos_respondidos`| String | Lista de modelos completados en la sesión (ej. `"1,3"`). |
| `modo_prueba` | Boolean | `true` si se corrió en modo testing, `false` si es dato de campo real. |
| `ancho_pantalla` | Integer | Resolución horizontal en píxeles del dispositivo. |
| `user_agent` | String | Cadena User-Agent del navegador. |

### Tabla B: `valoraciones` (Nivel Ensayos / Trial)
Almacena $N$ filas por participante (donde $N = n_{trials}$, típicamente 20, 40 o 60 filas por sujeto).

| Columna | Tipo de Dato | Descripción / Valores |
| :--- | :--- | :--- |
| `sesion_id` | String / UUID | Clave foránea referenciando a `participantes.sesion_id`. |
| `modelo` | Integer | Identificador del bloque asignado ($1, 2, 3$). |
| `modo_prueba` | Boolean | Indicador de ensayo de prueba (`true`/`false`). |
| `orden` | Integer | Posición secuencial en la que se presentó la foto ($1$ a $N$). |
| `estimulo_id` | String | ID del archivo (ej. `milei_pos_1`, `kicillof_neu_4`). |
| `categoria` | String | Categoría del estímulo (`politico`). |
| `identidad` | String | Nombre del político (`Javier Milei`, `Axel Kicillof`, etc.). |
| `emocion_esperada`| String | Emocionalidad teórica de la foto (`positiva`, `negativa`, `neutra`). |
| `repeticion` | Boolean | `true` si el trial corresponde a una repetición intra-sujeto de control. |
| `valencia` | Integer | Escala $1-7$ ($1 = \text{Muy desagradable}, 7 = \text{Muy agradable}$). |
| `arousal` | Integer | Escala $1-7$ ($1 = \text{Muy relajado/Calma}, 7 = \text{Muy activado}$). |
| `expresion` | String | Categorización explícita (`positiva`, `negativa`, `neutra`). |
| `rt_ms` | Integer | Tiempo de respuesta del trial en milisegundos. |

---

## 5. Directrices y Recomendaciones para el Análisis por LLM

Al realizar análisis estadísticos, agrupamientos o cruces con este set de datos, considerar las siguientes pautas:

1. **Filtrado de Sesiones de Prueba:**
   * Filtrar siempre por `modo_prueba == false` para eliminar registros generados durante la fase de depuración o testeo del código.
2. **Estructura Jerárquica / Multinivel:**
   * Las valoraciones están anidadas en sujetos (`sesion_id`). Para analizar `valencia`, `arousal`, `expresion` o `rt_ms`, emplear modelos lineales mixtos (LMM/GLMM) o agregaciones agrupadas por sujeto para evitar la inflación del error Tipo I.
3. **Cruces de Incongruencia Partidaria:**
   * Se pueden calcular variables sintéticas de congruencia política definiendo si la figura política presentada (`identidad`) pertenece al mismo espacio político (`partido`) o ideología (`ideologia`) con el que se identifica el participante.
4. **Validación de Percepción (Congruencia Perceptual vs. Esperada):**
   * Comparar la variable categórica `expresion` (expresión percibida) contra `emocion_esperada` (clasificación teórica del estímulo) para evaluar sesgos de reconocimiento afectivo guiados por la ideología.
5. **Control de Tiempos de Reacción (`rt_ms`):**
   * Se sugiere aplicar criterios de descarte o transformación (ej. corte por debajo de 300 ms o por encima de 3 desviaciones estándar) para mitigar el efecto de respuestas atípicas o distracciones durante la tarea.
