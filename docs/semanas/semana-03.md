# Semana 3 — Propiedad intelectual, marca y vigilancia tecnológica

**Equipo:** José Carlos y Yael  
**Concepto:** GPIO Génesis — videojuego de simulación de una fábrica donde se programa en C++ real, con enfoque en sistemas embebidos (ESP32 / Raspberry Pi Pico 2)  
**Fecha de las búsquedas:** 23 de septiembre de 2026

!!! abstract "De qué trata esta semana"
    En semana 2 encontramos una oportunidad deseable. Esta semana la pregunta es si **nos la podemos apropiar**: ¿podemos registrar el nombre GPIO Génesis?, ¿alguien ya patentó lo que queremos construir?, ¿qué tan libre está el espacio tecnológico donde vamos a trabajar? Todo lo que hicimos —prompts, respuestas de la IA, búsquedas y decisiones— está en esta misma página.

    Las entrevistas (Paso 6) se documentan en [Semana 4](canvas-mercado-semana4.md), porque ahí es donde se usaron.

## Learning outcomes

Al terminar esta semana podemos:

- Distinguir los instrumentos de propiedad intelectual (marca, patente, modelo de utilidad, diseño industrial, derechos de autor y secreto industrial) y decidir **cuál proteger primero** en GPIO Génesis.
- Diseñar prompts propios para generar, evaluar y verificar un nombre de marca a partir de las indicaciones del profesor, y comparar nuestro resultado con lo que se pedía.
- Hacer e interpretar una **búsqueda fonética en el IMPI** por clase de Niza, y leer qué significa cada antecedente para nuestra marca.
- Seguir un protocolo de **vigilancia tecnológica** (términos + códigos IPC → México → LATAM → global → familias y citas → reclamos → conclusión FTO).
- Leer los **reclamos** de una patente y decidir si nuestro concepto cae dentro o fuera de lo protegido.
- **Auditar** lo que dice la IA con fuentes primarias en vez de copiarlo tal cual.

---

## Paso 2 — ¿Qué propiedad intelectual le aplica a GPIO Génesis?

Del panorama de PI que vimos en clase, así queda cada instrumento aplicado a nuestro proyecto:

| Instrumento | ¿Aplica a GPIO Génesis? | Qué protegería | Prioridad |
|---|---|---|---|
| **Marca registrada** | Sí | El nombre "GPIO Génesis" y su logo en clases 9 (software/videojuegos) y 41 (educación/entretenimiento) | **1 — ya** |
| **Derechos de autor** | Sí | El código fuente del juego, el arte, la música, los niveles y el texto. En México se registra en INDAUTOR | **1 — ya** |
| **Secreto industrial** | Sí | El modelo de diagnóstico que traduce errores de C++ a comportamiento del hardware, y los datos con que se entrene | **1 — NDA desde el día 1** |
| **Modelo de utilidad** | Tal vez, después | La estación de pruebas física (ESP32/Pico 2 + sensores + actuadores) si llega a tener una mejora de forma o función | 2 — con tracción |
| **Diseño industrial** | Tal vez, después | La forma del gabinete de la estación de pruebas | 2 — con tracción |
| **Patente de invención** | Difícil | El software por sí mismo no es patentable en México, y en el Paso 5 encontramos antecedentes que le quitan novedad al diagnóstico con IA | 3 — solo con asesoría |

**Respuesta al checklist de salida — ¿qué PI proteger primero?** La **marca**, junto con el registro de derechos de autor del juego. Es lo más barato, lo más rápido y, según lo que vimos en clase, lo que más duele no tener: si otro registra "GPIO Génesis" antes que nosotros, el nombre es legalmente suyo.

### Licencias open source que tocan nuestro stack

Como el juego va a compilar C++ y a simular microcontroladores, revisamos qué licencias podríamos "heredar" sin darnos cuenta (la trampa del `#include` que vimos en clase):

| Componente que podríamos usar | Licencia | Riesgo para nosotros |
|---|---|---|
| ESP-IDF (SDK de Espressif para ESP32) | Apache 2.0 | ✅ Uso comercial libre |
| Pico SDK (Raspberry Pi) | BSD-3-Clause | ✅ Uso comercial libre |
| Clang / LLVM (para compilar el C++ del jugador) | Apache 2.0 con excepción LLVM | ✅ Se puede embeber en el juego |
| Arduino core para ESP32 | LGPL 2.1 | ✅ Se puede enlazar sin contaminar nuestro código |
| GCC como compilador embebido | GPL v3 | ⚠️ Distribuirlo obliga a entregar su código fuente; no hay que enlazar nuestro código a él |
| QEMU (fork de Espressif para emular ESP32) | GPL v2 | ⚠️ Si lo metemos dentro del juego, todo lo que se enlace tendría que publicarse |

!!! warning "Por verificar"
    Esta tabla la armamos con lo que conocemos de cada proyecto. Antes de usar cualquiera de estos componentes hay que leer el archivo `LICENSE` de la versión exacta que se descargue.

---

## Paso 3 — Nombre de marca con IA

El profesor pidió tres prompts: **(1)** generar nombres y elegir 3, **(2)** evaluar a los 3 finalistas y **(3)** hacer una verificación digital de la marca, justificando qué IA usamos en cada uno.

### Qué IA usamos en cada prompt y por qué

| Prompt | IA | Por qué |
|---|---|---|
| 1 — Generación | **Claude** | Es la IA que indicó el profesor para esta parte. Sigue bien instrucciones de formato (categorías, tablas, justificación por nombre) y entiende el contexto técnico de C++ y embebidos, así que los juegos de palabras salen con sentido y no genéricos. |
| 2 — Evaluación | **Claude** | Aquí lo que importa es el razonamiento con criterios explícitos (una rúbrica), no buscar en internet. Usar la misma IA que generó los nombres mantiene el contexto del juego. |
| 3 — Verificación digital | **Claude con búsqueda web + verificación manual** | Esta parte necesita datos reales y actuales (dominios, otros productos con el mismo nombre, marcas en el IMPI). Por eso la IA solo guía la búsqueda, y nosotros comprobamos cada dato con fuentes primarias (DNS de los dominios y la búsqueda fonética del Paso 4). |

### 3.1 Prompt 1 — Generación de nombres

**Nuestro prompt:**

```text
Rol: Actúa como un director creativo de videojuegos y experto en "naming".
Tarea: Genera una lista de nombres atractivos, originales y memorables para un nuevo
videojuego de puzzle, simulación y automatización.
Contexto del juego: El jugador asume el rol de un ingeniero que debe construir, gestionar
y optimizar una fábrica inteligente. La mecánica principal consiste en programar las
máquinas, brazos robóticos y procesos industriales escribiendo código real en C++. El
juego tiene un fuerte enfoque en sistemas embebidos, control de hardware a bajo nivel
(similar a programar microcontroladores como la serie ESP32 o Pico), gestión de memoria,
lectura de sensores y optimización de rutinas.
Instrucciones de formato: Divide tus propuestas en las siguientes 4 categorías,
entregando 5 ejemplos por cada una junto con una breve justificación (1 línea) de por qué
el nombre funciona y captura la esencia del juego:

- Estilo "Tycoon" Industrial: Nombres que evoquen eficiencia, cadenas de montaje y
  manufactura a gran escala (al estilo de Factorio o Satisfactory).
- Sintaxis y Hardware (Geek): Nombres que hagan juegos de palabras inteligentes con la
  sintaxis de C++ (#include, void, ++, punteros, clases) o con términos de
  electrónica/embebidos (GPIO, RTOS, registros, I2C).
- Minimalistas (De Marca): Nombres cortos (una o dos palabras máximo), contundentes,
  modernos y que funcionen bien como un logotipo comercial.
- Narrativos / Corporativos: Nombres que suenen a la megacorporación tecnológica que te
  contrató o al nombre del proyecto industrial confidencial que debes sacar adelante.
```

**Resultado (20 nombres):**

=== "Tycoon industrial"

    | Nombre | Por qué funciona (según la IA) |
    |---|---|
    | Fundición Lógica | Une la imagen de una fundición de metal con "lógica" de programación: fabricación + razonamiento. |
    | Cadena Crítica | Doble lectura: "cadena de montaje" industrial y "ruta crítica" de gestión de procesos. |
    | Autómata Total | Suena a escala masiva (estilo Factorio) y remite directamente a la automatización. |
    | Forja de Procesos | "Forja" da peso industrial/artesanal; "procesos" ancla el juego en la optimización de rutinas. |
    | Línea Cero | Evoca la línea de producción desde su origen ("build 0"): arrancar un imperio desde nada. |

=== "Sintaxis y hardware (geek)"

    | Nombre | Por qué funciona (según la IA) |
    |---|---|
    | Factory++ | Fusiona "Factory" con el operador de incremento de C++; comunica género y lenguaje al instante. |
    | Void Main() | Juega con la función de entrada de C++ y con el "vacío" que el jugador debe llenar de industria. |
    | Puntero Maestro | "Puntero" remite a memoria en C++, pero también suena a "maestro guía". |
    | **GPIO Génesis** | **Combina el acrónimo real de control de pines embebidos con "génesis", el nacimiento de tu fábrica.** |
    | Overflow Industries | "Buffer overflow" reinterpretado como nombre corporativo: una producción que desborda sus límites. |

=== "Minimalistas (de marca)"

    | Nombre | Por qué funciona (según la IA) |
    |---|---|
    | Nexus | Corto, abstracto y tecnológico; sugiere la red de conexiones entre máquinas. |
    | Ferrum | Latín para "hierro": robusto, industrial y funciona como wordmark internacional. |
    | Loom | "Telar" en inglés: metáfora clásica de la automatización textil. |
    | Axioma | Remite a las reglas fundamentales de la lógica, con sonoridad premium. |
    | Vertex | Término geométrico/técnico; transmite precisión en una sola palabra. |

=== "Narrativos / corporativos"

    | Nombre | Por qué funciona (según la IA) |
    |---|---|
    | Omnicore Industries | Megacorporación omnipresente y algo inquietante, típica del "empleador". |
    | Proyecto Prometeo | Codename mitológico (el fuego robado = tecnología prohibida). |
    | Meridian Dynamics | Nombre corporativo de ciencia ficción para el logo de tu terminal. |
    | Directiva Ónix | Suena a operación clasificada de alto nivel. |
    | Helios Automation Corp. | Grandiosidad (dios del sol) + la función literal de la empresa. |

La recomendación de la IA fue **Ferrum** o **Axioma**, "cortas, pronunciables en cualquier idioma". Nosotros no la seguimos (ver 3.5).

**Los 3 finalistas que elegimos:** GPIO Génesis, Factory++ y Ferrum. Tomamos uno de cada estilo que nos convencía: el más geek y específico (GPIO Génesis), el más fácil de entender a la primera (Factory++) y el mejor calificado por la propia IA como marca (Ferrum).

### 3.2 Nuestro prompt vs. las indicaciones del profesor

Para este paso el profesor **no dio un prompt ya hecho**: dio indicaciones para que cada equipo armara el suyo. La indicación para el primer prompt fue *"Obtén 12 nombres de marca (Evocadores, Compuestos, Inventados y Disruptivos) y elige los 3 que más resuenen"*, junto con la tabla de criterios de un buen nombre vista en clase. Aquí comparamos qué pedía esa indicación y cómo la resolvimos en nuestro prompt.

| Aspecto | Lo que pedía la indicación | Cómo lo hicimos | Qué efecto tuvo |
|---|---|---|---|
| Cantidad | 12 nombres (3 por categoría) | Pedimos 20 (5 por categoría) | Más opciones, pero más trabajo para filtrar. Con 12 habría bastado. |
| Categorías | Por **forma** del nombre: evocadores, compuestos, inventados, disruptivos | Las cambiamos por **estilo del juego**: tycoon, geek, minimalista, corporativo | Salieron nombres más pegados al género, pero casi ningún nombre **inventado**, que son los más fáciles de registrar. Nos faltó esa categoría. |
| Contexto | No especifica cuánto contexto dar | Dimos mucho: C++, ESP32/Pico, sensores, memoria | Gracias a eso salió "GPIO Génesis": sin mencionar embebidos, la IA no habría llegado a GPIO. |
| Rol | No especifica | "Director creativo de videojuegos y experto en naming" | Las justificaciones salieron en lenguaje de videojuegos (Factorio, Satisfactory), no de marca en general. |
| Elegir 3 finalistas | Elegir los 3 que más resuenen | No se lo pedimos a la IA; los elegimos nosotros | La IA solo recomendó Ferrum/Axioma y nosotros nos fuimos por otro. |
| Criterios de un buen nombre | Tabla de clase: pronunciable, corto, sin significado negativo, registrable, dominio, emoción | No los metimos en este prompt | La IA no revisó registrabilidad ni dominio aquí. Eso lo cubrimos con los prompts 2 y 3. |

**Conclusión de la comparación:** al cambiar las categorías y dar tanto contexto técnico conseguimos un nombre con **identidad**, pero nos alejamos de lo que ayuda a que sea **registrable** (la categoría de inventados y los criterios de marca). Si lo hiciéramos otra vez, mantendríamos nuestro rol y contexto y usaríamos las 4 categorías que pedía el profesor.

### 3.3 Prompt 2 — Evaluación de los 3 finalistas

**Nuestro prompt:**

```text
Rol: Actúa como consultor de branding y propiedad intelectual para estudios indie de
videojuegos en México.
Contexto: Estamos nombrando un videojuego de simulación de una fábrica donde el jugador
programa las máquinas escribiendo C++ real, con enfoque en microcontroladores (ESP32,
Raspberry Pi Pico 2). Público: estudiantes de ingeniería de 18 a 25 años en México y
LATAM que juegan Minecraft y simuladores. Se venderá en Steam a 199–299 MXN.
Finalistas: GPIO Génesis, Factory++, Ferrum.
Tarea: Evalúa cada finalista del 1 al 5 con estos 6 criterios:
1. Pronunciable en español e inglés
2. Corto (2–3 sílabas)
3. Sin significado negativo o confuso
4. Registrable ante el IMPI (no genérico ni descriptivo)
5. Probabilidad de tener dominio .com o .mx disponible
6. Coherencia con la emoción de usarlo (cómo se siente, no qué hace)
Entrega una tabla con la calificación y una línea de justificación por celda, el total,
el riesgo principal de cada nombre y tu recomendación final. Marca como "hipótesis"
todo lo que no puedas comprobar.
```

**Resultado:**

| Criterio | GPIO Génesis | Factory++ | Ferrum |
|---|:---:|:---:|:---:|
| Pronunciable ES/EN | **3** — "GPIO" se deletrea (ge-pe-i-o / gee-pee-eye-oh), pero el público técnico ya lo dice así | **4** — "factory plus plus" se entiende en ambos idiomas | **5** — dos sílabas limpias |
| Corto | **2** — 7 sílabas; se va a acortar solo, probablemente a "GPIO" | **3** — 5 sílabas | **5** — 2 sílabas |
| Sin significado negativo | **4** — nada negativo; "Génesis" tiene carga religiosa y recuerda a la consola SEGA Genesis | **5** — nada negativo | **4** — neutral; puede sonar a marca de acero o de herramientas |
| Registrable ante el IMPI | **4** — la combinación es distintiva; "GPIO" es técnico pero no describe un videojuego *(hipótesis, se comprueba en el Paso 4)* | **2** — "Factory" es genérico para un juego de fábricas y el "++" no se pronuncia, así que la parte registrable es casi solo la palabra genérica | **3** — palabra común en latín, probablemente ya usada en varias clases *(hipótesis, no buscada en IMPI)* |
| Dominio disponible | **4** — combinación rara, alta probabilidad de estar libre *(hipótesis, se comprueba en 3.4)* | **2** — "factoryplusplus" o similares probablemente ocupados o incómodos *(hipótesis)* | **1** — ferrum.com casi seguro está ocupado *(hipótesis)* |
| Coherencia emocional | **5** — "tu fábrica nace de tus pines": suena a crear algo desde cero con hardware real | **4** — divertido e ingenioso, pero se siente más broma que marca | **2** — robusto, pero frío y sin relación con programar |
| **Total (de 30)** | **22** | **20** | **20** |

**Riesgo principal de cada uno:**

- **GPIO Génesis:** la longitud y la palabra "Génesis", muy saturada (y asociada a SEGA en videojuegos).
- **Factory++:** difícil de registrar por ser casi descriptivo; además, los buscadores ignoran el "++".
- **Ferrum:** no comunica nada del juego; habría que invertir mucho en construir significado.

**Recomendación de la IA:** GPIO Génesis, con dos condiciones: registrarlo siempre como marca **compuesta** (nunca "Génesis" solo) y usar "GPIO" como el elemento distintivo del logotipo.

### 3.4 Prompt 3 — Verificación digital de pertinencia

**Nuestro prompt:**

```text
Rol: Actúa como consultor senior de estrategia de marca y propiedad intelectual digital.
Objetivo: Verificar la pertinencia y viabilidad digital del nombre "GPIO Génesis" para un
videojuego de simulación de fábrica que enseña C++ real con microcontroladores (ESP32 /
Raspberry Pi Pico 2), dirigido a estudiantes de ingeniería en México y LATAM.
Tareas:
1. Huella digital: ¿qué aparece hoy al buscar "GPIO Genesis", "GPIO Génesis" y
   "Genesis" + juego de fábrica/programación? ¿Hay productos, juegos o empresas con el
   mismo nombre o uno muy parecido?
2. Dominios: revisa si .com, .mx, .com.mx, .io, .dev, .games y .net están libres, además
   de variantes como gpio-genesis.com y playgpiogenesis.com.
3. Confusión de marca: ¿con qué marcas famosas se podría confundir en las clases de
   Niza 9 (software, videojuegos) y 41 (educación, entretenimiento)?
4. Veredicto: pertinencia digital baja / media / alta y qué ajustes recomiendas.
Separa lo que comprobaste con una fuente de lo que es suposición.
```

**Resultado (comprobado):**

| Verificación | Hallazgo | Fuente |
|---|---|---|
| Búsqueda exacta "GPIO Genesis" | **No existe ningún producto, juego ni empresa con ese nombre.** Los resultados son proyectos para conectar controles de **SEGA Genesis** a los pines GPIO de una Raspberry Pi (foros de RetroPie, repositorios de GitHub). | Búsqueda web, 23-sep-2026 |
| "Genesis" + juego de fábrica | Existe **"Genesis Factory"**, un demo de juego de fábrica en itch.io. Es una colisión directa con la palabra "Genesis" sola, en el mismo género. | itch.io (qm-mofan.itch.io/genesis-factory) |
| Dominios | `gpiogenesis.com`, `.mx`, `.com.mx`, `.io`, `.dev`, `.games`, `.net`, `gpio-genesis.com` y `playgpiogenesis.com`: **ninguno existe en el DNS** (respuesta NXDOMAIN). Es muy probable que todos estén disponibles. | Consulta DNS (Google Public DNS), 23-sep-2026 |
| Confusión en clase 9 | SEGA tiene registradas en México marcas con GENESIS para videojuegos: *SEGA GENESIS COLLECTION*, *SONIC'S ULTIMATE GENESIS COLLECTION*, *PHANTASY STAR ONLINE NEW GENESIS*. Nintendo tiene una solicitud de *XENOBLADE GENESIS*. | Búsqueda fonética IMPI (Paso 4) |
| Confusión en clase 41 | 6 registros de "GENESIS" solo y una solicitud de *GENESIS STUDIOS*. Ninguno con "GPIO". | Búsqueda fonética IMPI (Paso 4) |

**Veredicto: pertinencia digital MEDIA-ALTA.** "GPIO Génesis" completo está libre en buscadores, dominios e IMPI; lo saturado es "Génesis" solo. La asociación con SEGA Genesis + Raspberry Pi no es negativa para nuestro público (es justo la cultura *maker* y retro de los estudiantes de ingeniería), pero nos obliga a que **GPIO** sea la parte que se vea y se diga primero.

### 3.5 Auditoría de lo que nos dijo la IA

| Lo que dijo la IA | ¿Lo comprobamos? | Resultado |
|---|---|---|
| "GPIO es el acrónimo real de control de pines embebidos" | Sí | Correcto: *General-Purpose Input/Output*, es lo primero que se programa en un ESP32 o un Pico. |
| Recomendó Ferrum o Axioma como la mejor marca | Lo evaluamos con el Prompt 2 | No lo seguimos: son cortas, pero no comunican nada del juego y probablemente estén saturadas. La IA optimizó "sonar a marca" y no "conectar con el público". |
| En el Prompt 2, "GPIO Génesis" probablemente es registrable | Sí, con el IMPI (Paso 4) | Se confirma: no hay ninguna marca con GPIO ni parecida fonéticamente en las clases 9 y 41. |
| En el Prompt 2, "Génesis" está saturado | Sí, con el IMPI (Paso 4) | Se confirma, y es más grave de lo que dijo: SEGA tiene "Genesis" en videojuegos. |
| Dominios probablemente disponibles | Sí, por DNS | Se confirma para las 9 variantes revisadas. Falta confirmar en un registrador antes de comprar. |

### 3.6 Decisión del nombre

**Nos quedamos con GPIO Génesis** como marca compuesta, con estas reglas:

1. Se registra como **"GPIO GÉNESIS"** completo (denominación + logo), nunca "Génesis" solo.
2. En el logo, **"GPIO"** es el elemento principal y "Génesis" el secundario.
3. Hay que comprar ya `gpiogenesis.com` y `gpiogenesis.mx` (cuestan poco y hoy están libres).
4. El segundo candidato evaluado queda como respaldo: **Factory++**, aunque sabemos que es más difícil de registrar.

Esto responde también al checklist de salida: **sí tenemos 2 nombres candidatos evaluados** (GPIO Génesis y Factory++, más Ferrum como tercero).

---

## Paso 4 — Búsqueda fonética en el IMPI

### Cómo la hicimos

Buscamos en **Marcanet** (búsqueda fonética del IMPI) tres denominaciones en dos clases de Niza, en total **6 búsquedas**:

- **Clase 9:** software, videojuegos descargables, aparatos electrónicos. Es donde vive el juego como producto.
- **Clase 41:** educación, formación y entretenimiento. Es donde vive el juego como servicio educativo.

Buscamos "GPIO" y "Genesis" por separado, además del nombre completo, para saber cuál de las dos palabras genera el riesgo.

### Resumen de resultados

| Búsqueda | Clase | Resultados fonéticos | Marcas con "GPIO" | "GENESIS" exacto (con número de registro) | Antecedente más relevante |
|---|:---:|:---:|:---:|:---:|---|
| GPIO | 9 | 274 | **0** | — | GPI (GPI S.p.A.) y GP (**Gameplanet**, tienda de videojuegos), ambas registradas |
| GPIO | 41 | 268 | **0** | — | GP (Gameplanet) y varias GPI, registradas |
| Genesis | 9 | 279 | 0 | 10 (4) | **SEGA GENESIS COLLECTION** y SUPER GENESIS 32 (SEGA); XENOBLADE GENESIS (Nintendo, solicitud) |
| Genesis | 41 | 278 | 0 | 6 (6) | GENESIS (varios titulares), GENESIS STUDIOS (solicitud), PHANTASY STAR ONLINE NEW GENESIS (SEGA) |
| GPIO Genesis | 9 | 268 | **0** | 10 (4) | Los mismos de "Genesis" en clase 9 |
| GPIO Genesis | 41 | 276 | **0** | 6 (6) | Los mismos de "Genesis" en clase 41 |

<small>Cómo leer la tabla: en el reporte de búsqueda fonética, un número en la columna "Registro" significa que la marca **está registrada**. Si esa columna está vacía, es una **solicitud** que puede estar en trámite, abandonada o negada; el reporte no dice cuál. Para las que nos preocupan habría que abrir el expediente en Marcanet.</small>

### Capturas de la búsqueda

Primera página de cada reporte (consulta del 23-09-2026). Los PDF completos se pueden descargar debajo de cada captura.

=== "GPIO · Clase 9"

    ![Búsqueda fonética IMPI: GPIO, clase 9](../recursos/imgs/semana3/impi-gpio-clase9.png)

    [📄 Reporte completo (PDF, 16 páginas)](../recursos/archivos/semana3/impi-gpio-clase9.pdf)

=== "GPIO · Clase 41"

    ![Búsqueda fonética IMPI: GPIO, clase 41](../recursos/imgs/semana3/impi-gpio-clase41.png)

    [📄 Reporte completo (PDF, 17 páginas)](../recursos/archivos/semana3/impi-gpio-clase41.pdf)

=== "Genesis · Clase 9"

    ![Búsqueda fonética IMPI: Genesis, clase 9](../recursos/imgs/semana3/impi-genesis-clase9.png)

    [📄 Reporte completo (PDF, 16 páginas)](../recursos/archivos/semana3/impi-genesis-clase9.pdf)

=== "Genesis · Clase 41"

    ![Búsqueda fonética IMPI: Genesis, clase 41](../recursos/imgs/semana3/impi-genesis-clase41.png)

    [📄 Reporte completo (PDF, 18 páginas)](../recursos/archivos/semana3/impi-genesis-clase41.pdf)

=== "GPIO Genesis · Clase 9"

    ![Búsqueda fonética IMPI: GPIO Genesis, clase 9](../recursos/imgs/semana3/impi-gpio-genesis-clase9.png)

    [📄 Reporte completo (PDF, 16 páginas)](../recursos/archivos/semana3/impi-gpio-genesis-clase9.pdf)

=== "GPIO Genesis · Clase 41"

    ![Búsqueda fonética IMPI: GPIO Genesis, clase 41](../recursos/imgs/semana3/impi-gpio-genesis-clase41.png)

    [📄 Reporte completo (PDF, 18 páginas)](../recursos/archivos/semana3/impi-gpio-genesis-clase41.pdf)

### Antecedentes que nos importan

| Expediente | Marca | Titular | Clase(s) | Estado en el reporte | Por qué importa |
|---|---|---|---|---|---|
| 1173729 | SEGA GENESIS COLLECTION | SEGA Corporation | 9 y otras | Registrada (1237529) | "Genesis" en videojuegos es de SEGA; es una marca muy conocida en el mismo giro. |
| 200177 | SUPER GENESIS 32 | Kabushiki Kaisha Sega Enterprises | 9, 28 | Registrada (473509) | Mismo titular y mismo giro. |
| 2493693 / 2493694 | PHANTASY STAR ONLINE NEW GENESIS | SEGA Corporation | 9 / 41 | Registradas | SEGA protege "Genesis" dentro de nombres compuestos, igual que haríamos nosotros. |
| 3705962 | XENOBLADE GENESIS | Nintendo Co., Ltd. | 9 | Solicitud | Otra empresa grande de videojuegos usando "Genesis" en un compuesto. |
| 3582729 | GENESIS STUDIOS | Oscar Eduardo Medellín Castañeda | 41 | Solicitud | Un estudio en la clase de educación/entretenimiento. |
| 1936325 / 1936330 | GP | Gameplanet, S.A. de C.V. | 9 / 41 | Registradas | Es lo más parecido fonéticamente a "GPIO", y es del sector videojuegos. Riesgo bajo: "GP" y "GPIO" suenan distinto. |

### Lectura y decisión

- **"GPIO" está limpio.** En ninguna de las dos clases hay una marca con GPIO; lo más cercano son siglas de dos o tres letras (GP, GPI, GPE) que suenan distinto. **GPIO es lo que hace registrable a la marca.**
- **"Genesis" está saturado**, y en videojuegos es de SEGA. Si registramos solo "Génesis" nos lo negarían o nos harían oposición. Que SEGA y Nintendo tengan marcas **compuestas** con Genesis (PHANTASY STAR ONLINE NEW GENESIS, XENOBLADE GENESIS) indica que un compuesto distintivo sí puede convivir.
- **Decisión:** el nombre se confirma como **GPIO GÉNESIS**, solicitado como marca **mixta** (palabra + logo) en las clases **9 y 41**. Si después ofrecemos la plataforma en línea o la app de diagnóstico como servicio, conviene agregar la **clase 42** (servicios tecnológicos), que no buscamos.

!!! note "Limitación"
    La búsqueda fonética no es una opinión legal ni garantiza el registro (el propio reporte lo aclara). Tampoco incluye marcas internacionales que designan a México por el Protocolo de Madrid; para eso habría que revisar la base de la OMPI.

---

## Paso 5 — Vigilancia tecnológica

**Qué queremos saber:** si alguien ya protegió (1) un **videojuego/simulador** donde se programa código real para controlar una fábrica o un hardware, (2) la **conexión entre un simulador y un microcontrolador físico** (ESP32 / Pi Pico 2) que ejecuta el mismo código, o (3) un **diagnóstico con IA** que explica un error de código en términos de lo que hace el hardware.

### Paso 5.1 — Preparación: Prompt 1 (términos y códigos IPC)

**Prompt del profesor (plantilla):**

```text
Actúa como especialista en vigilancia tecnológica para startups
de hardware + software en mercados emergentes.
Concepto: [nombre + descripción]
Mecanismo técnico: [sensores, procesamiento, comunicación]
Componente de IA: [qué hace y dónde corre]
Entrega:
- Términos en ES y EN (principales + sinónimos + combinaciones AND)
- Códigos IPC relevantes (3–5 con descripción)
- Secuencia: IMPI → LATIPAT → Lens.org
```

**Nuestro prompt:**

```text
Actúa como especialista en vigilancia tecnológica para startups de hardware + software
educativo en México y LATAM.
Concepto: GPIO Génesis — videojuego de simulación de una fábrica donde el jugador
programa máquinas y brazos robóticos escribiendo C++ real. Público: estudiantes de
ingeniería de 18–25 años.
Mecanismo técnico: (a) simulador en PC que compila y ejecuta el C++ del jugador sobre un
modelo virtual de microcontrolador (GPIO, PWM, ADC, I2C, temporizadores); (b) a futuro,
una estación de pruebas física con ESP32 o Raspberry Pi Pico 2, sensores y actuadores,
que recibe el mismo código ya probado en el simulador y lo ejecuta en hardware real.
Componente de IA: un copiloto de depuración que compara lo que el código debería hacer
con lo que hizo el hardware (virtual o real) y explica el error en lenguaje natural y
en términos físicos ("el motor recibe pulsos irregulares porque tu delay bloquea el
bucle"). Corre en la nube o en la PC, no en el microcontrolador.
Entrega:
- Términos en ES y EN para cada una de las 3 partes (a, b y el copiloto), con
  sinónimos y combinaciones AND listas para usar en Google Patents
- 5–7 códigos IPC/CPC con descripción y a cuál de las 3 partes corresponde cada uno
- Qué base usar en cada capa (México → LATAM → global) y qué filtros aplicar
- Qué NO debería considerarse riesgo (por ejemplo, patentes de microcontroladores en sí)
```

**En qué se diferencia del prompt del profesor:** separamos el concepto en **tres partes que se pueden proteger por separado** (juego, estación física, copiloto IA). Con un solo bloque de términos, las búsquedas regresaban miles de patentes de videojuegos que no tenían nada que ver (la primera búsqueda amplia dio 119,676 resultados). También pedimos que la IA dijera **qué no es riesgo**, para no perder tiempo con patentes del propio ESP32. Y pedimos términos listos para Google Patents, porque ahí es donde podemos filtrar por país.

**Resultado — términos de búsqueda:**

| Parte | Español | Inglés | Combinaciones AND usadas |
|---|---|---|---|
| (a) Juego/simulador | videojuego educativo, enseñanza de programación, simulador, código fuente, juego serio | programming game, educational game, teaching programming, source code, serious game | `programming AND game AND learn*` · `video game AND code AND learn*` |
| (b) Virtual ↔ físico | microcontrolador, robot educativo, gemelo virtual, kit didáctico, emulador | microcontroller, educational robot, virtual robot, physical toy, emulator, development board | `microcontroller AND simulat* AND education*` · `virtual AND physical AND program*` |
| (c) Copiloto IA | diagnóstico de errores, retroalimentación de código, modelo de lenguaje, depuración | error feedback, debugging, compiler error, large language model, explanation | `error AND source code AND explanation AND language model` · `compiler error AND student*` |

**Resultado — códigos IPC/CPC:**

| Código | Qué cubre | Parte |
|---|---|---|
| **G09B 19/0053** | Enseñanza de computación, p. ej. programación | (a) y (c) |
| **G09B 23/18**, **23/183**, **23/186** | Modelos didácticos de electricidad y electrónica; circuitos; electrónica digital y microprocesadores | (b) |
| **G09B 5/06** | Aparatos educativos audiovisuales/electrónicos con presentación simultánea | (a) |
| **A63F 13/00** | Videojuegos | (a) |
| **A63H 13/00, 33/00** | Juguetes/robots programables | (b) |
| **G06F 11/36** | Prevención de errores por prueba o depuración de software | (c) |
| **G05B 19/042** | Control por programa con procesadores digitales (controladores programables) | (b) |

**Qué NO consideramos riesgo:** patentes de los microcontroladores en sí (Espressif, Raspberry Pi), de compiladores en general y de motores de videojuego. Las usamos como clientes, no competimos con ellas.

### Pasos 5.2 a 5.4 — Búsqueda por capas

| Capa | Base | Qué hicimos | Resultado |
|---|---|---|---|
| México | **IMPI/SIGA** | Intentamos buscar en SIGA 3.1 (siga.impi.gob.mx). SIGA busca **dentro de las gacetas**: pide elegir área, gaceta y fecha, y no acepta una consulta de texto libre en todas las patentes a la vez. | No pudimos hacer ahí una búsqueda útil. Cubrimos México con Google Patents filtrando `country=MX`, que indexa las publicaciones del IMPI. |
| México | Google Patents, `country=MX` | 4 búsquedas: programación + educación (91 resultados), videojuego + programación (24), microcontrolador (545), programación + enseñanza (18) | **Ninguna patente mexicana cubre un juego de programación ni un diagnóstico de código.** Lo más cercano: un robot educativo programable (MX2014015820A) y un sistema didáctico de control automático del CINVESTAV-IPN (MX337633B). |
| LATAM | Google Patents, `country=BR,AR,CO,CL,PE` (en lugar de LATIPAT) | Programación + juego + aprendizaje (3 resultados); microcontrolador + enseñanza (10 en MX/AR/CO/CL/PE); programación + microcontrolador + enseñanza (20 en BR) | Brasil es el más activo: kits y robots para enseñar programación y microelectrónica (UNEB, IF Farroupilha, SENAI-SP). Nada sobre videojuegos con código real ni sobre diagnóstico con IA. |
| Global | Google Patents con filtros CPC (en lugar de Lens.org) | G09B19/0053 + juego (44; 26 concedidas desde 2015), CPC G09B + microcontrolador (26), diagnóstico de código con LLM (14–96) | Corea del Sur concentra patentes de "educación de coding con juego/robot". China tiene la patente más cercana al copiloto IA (CN121583173B). EE. UU. tiene la familia de Wonder Workshop, activa. |

!!! note "Por qué no usamos LATIPAT y Lens.org directamente"
    Las dos son buscadores interactivos. Usamos Google Patents porque permite hacer lo mismo que pide el protocolo (filtrar por país, por código CPC y por estado legal) y además muestra familias y citas. Lo dejamos escrito para que se vea qué base se usó en cada capa; la verificación directa en LATIPAT y en SIGA queda como pendiente.

### Paso 5.5 — Familias y referencias cruzadas

Para las patentes de mayor riesgo revisamos en qué países existen (familia) y quién las cita:

- **Wonder Workshop (US9370862B2 y familia):** 7 documentos en EE. UU., más EP, CN, SG y WO. **No tiene miembro en México.** Vigente en EE. UU. hasta 2035.
- **Robot virtual de Kam Ming Lau (JP2017215577A):** familia en JP, US, CN, HK y WO. La de EE. UU. está **abandonada** y la PCT **caducó**. **No tiene miembro en México.** Tiene 12 citas posteriores, entre ellas Amazon, Apple y Robotify Labs: es una patente "semilla" del tema virtual ↔ físico.
- **Hunan Huijiu (CN121583173B):** solo existe en China (concedida en abril de 2026). Cita tecnologías chinas de enseñanza con IA y realidad virtual. **No tiene miembro en México.**
- **Robotify Labs (WO2020141200A1):** la PCT caducó; siguen **pendientes** en EE. UU. (US20220076587A1) y en Europa (EP3906542A1). **No tiene miembro en México.**

### Paso 5.6 — Interpretación de reclamos: Prompt 2

**Prompt del profesor (plantilla):**

```text
Actúa como analista de PI para equipos de ingeniería sin formación legal.
Concepto: [descripción técnica]
Patente: Título / Número / Titular / Estado en MX / Año
Reclamos: [pega reivindicaciones 1–5]
Responde:
1. Qué protege (sin jerga legal)
2. Qué NO protege
3. ¿Nuestro concepto cae dentro o fuera?
   Veredicto: dentro ⚠️ / fuera ✅ / zona gris ❌
4. Recomendación: ignorar / ajustar / asesoría legal / usar como guía
```

**Nuestro prompt:** usamos la estructura del profesor casi igual porque funciona bien. Le agregamos dos cosas:

```text
[...misma estructura del profesor...]
Además:
5. ¿La patente tiene efecto en México? Revisa si existe un miembro
   de la familia en MX y si está vigente. Si no existe, dilo explícitamente.
6. Aunque no nos bloquee, ¿nos quita novedad para patentar algo
   nuestro después?
```

**Por qué lo cambiamos:** la mayoría de las patentes que encontramos **no tienen efecto en México**. Sin la pregunta 5 la IA las calificaba "zona gris" solo por la similitud técnica. La pregunta 6 separa dos riesgos distintos: que nos **demanden** (FTO) y que **no podamos patentar** lo nuestro (novedad).

**Resultados — las 5 patentes analizadas:**

??? danger "1. CN121583173B — Sistema de control de programación de robots con IA para escenarios educativos"
    **Titular:** Hunan Huijiu Intelligent Technology Co., Ltd. · **Prioridad:** 23-ene-2026 · **Estado:** concedida en China (17-abr-2026), vigente hasta 2046 · **Familia en MX:** no existe.

    **Reclamo 1 (resumen):** un sistema que (i) analiza el código del estudiante como árbol sintáctico, (ii) lo ejecuta **al mismo tiempo en una simulación virtual y en un robot físico**, (iii) compara lo esperado contra lo medido para obtener una "matriz de desviación", (iv) **regresa esa desviación a las líneas del código** para encontrar la causa raíz, y (v) genera una sugerencia de corrección para el estudiante.

    1. **Qué protege:** ese método específico de encontrar qué instrucción del estudiante causó que el robot se moviera distinto a lo esperado, calculando la "contribución causal" de cada nodo del código.
    2. **Qué NO protege:** explicar errores con un modelo de lenguaje en general; simuladores de microcontroladores; videojuegos de programación; nada fuera de China.
    3. **Veredicto:** **fuera ✅ en México** (no tiene efecto aquí). **Zona gris ❌ si algún día vendiéramos la estación física en China**, porque la idea de fondo de nuestro copiloto IA (comparar lo simulado con lo físico y señalar la línea culpable) es muy parecida.
    4. **Recomendación:** **usar como guía** para el diseño del copiloto, e implementarlo distinto (con explicación en lenguaje natural, no con gradientes sobre el árbol sintáctico).
    5. **Efecto en MX:** ninguno.
    6. **¿Nos quita novedad?** **Sí.** Es la patente más importante de esta búsqueda: demuestra que "diagnosticar el error del estudiante comparando ejecución virtual y física" ya está publicado. Patentar nuestro copiloto con esa idea sería muy difícil.

??? warning "2. US9370862B2 (familia) — Sistema y método para reforzar la educación en programación mediante retroalimentación robótica"
    **Titular:** Wonder Workshop, Inc. (antes Play-i) · **Prioridad:** 2014 · **Estado:** vigente en EE. UU. hasta 2035 · **Familia en MX:** no existe.

    **Reclamo 1 (resumen):** un método en el que un dispositivo (tablet) recibe instrucciones de programación del usuario, recibe las lecturas de los sensores del robot de juguete, genera instrucciones de control a partir de ambas, se las envía al robot y repite el ciclo.

    1. **Qué protege:** el control remoto de un robot de juguete **desde la tablet**, donde el programa corre en la tablet y el robot solo ejecuta órdenes y manda lecturas.
    2. **Qué NO protege:** que el código se compile y se **cargue en el microcontrolador** para correr ahí mismo, que es lo que haría nuestra estación con el ESP32/Pico 2.
    3. **Veredicto:** **fuera ✅**, por arquitectura (en nuestro caso el código corre en el micro) y por territorio.
    4. **Recomendación:** **ajustar/cuidar el diseño**: la estación no debe funcionar como "robot controlado en vivo desde la PC" si algún día se vende en EE. UU.
    5. **Efecto en MX:** ninguno.
    6. **¿Nos quita novedad?** En parte: la retroalimentación con sensores para enseñar programación ya es conocida.

??? warning "3. US20220076587A1 / EP3906542A1 — Producto de programa para enseñar programación"
    **Titular:** Robotify Labs Ltd. · **Prioridad:** 2019 (GB) · **Estado:** pendiente en EE. UU. y Europa; la PCT (WO2020141200A1) caducó · **Familia en MX:** no existe.

    **Reclamo 1 (resumen):** un programa con una interfaz que tiene un panel de programación, un **compilador** que compila los comandos del usuario y un panel de "video" con un **emulador de robot** sobre un fondo, para que parezca que se controla un robot real sin comprarlo.

    1. **Qué protege** (si se concede): la combinación de editor + compilador + emulador de robot mostrado como video para enseñar programación.
    2. **Qué NO protege:** simulaciones de fábricas o de microcontroladores con sus pines; nada en México.
    3. **Veredicto:** **fuera ✅ en México; zona gris ❌ en EE. UU./Europa**, porque nuestro juego también tiene editor + compilador + algo que se mueve en pantalla. La diferencia es que simulamos una fábrica y un microcontrolador, no un robot sobre un video.
    4. **Recomendación:** **vigilar**; revisar qué reclamos se conceden antes de lanzar en Steam a EE. UU. y Europa.
    5. **Efecto en MX:** ninguno.
    6. **¿Nos quita novedad?** Sí, para la parte "simulador que compila código real para enseñar".

??? success "4. JP2017215577A (familia US20170316714A1, WO2017186001A1) — Sistema educativo con robot virtual"
    **Titular:** Kam Ming Lau (particular) · **Prioridad:** 2016 (HK) · **Estado:** JP pendiente; **US abandonada; WO caducada** · **Familia en MX:** no existe.

    **Reclamo 1 (US, resumen):** un sistema educativo con un dispositivo de control, un juguete físico y un **gemelo virtual** del juguete dentro de un mundo virtual; los dos se programan desde el dispositivo.

    1. **Qué protege** (solo si se concede en Japón): la pareja robot físico ↔ robot virtual programable.
    2. **Qué NO protege:** nada en EE. UU. ni en México (abandonada/caducada).
    3. **Veredicto:** **fuera ✅**.
    4. **Recomendación:** **usar como guía**: es justo nuestro concepto de estación física + gemelo en el simulador, y como está libre fuera de Japón, se puede usar.
    5. **Efecto en MX:** ninguno.
    6. **¿Nos quita novedad?** Sí: "gemelo virtual + físico programable" ya es conocido desde 2016.

??? success "5. MX2014015820A — Dispositivo robótico para enseñanza educativa"
    **Titular:** Corporativo STR Asesoría en Desarrollo Tecnológico e Innovación, S.A. de C.V. (México) · **Prioridad:** 10-dic-2014 · **Estado:** solicitud publicada en 2016; en Google Patents no aparece concesión · **Familia:** solo MX.

    **Resumen:** un robot móvil programable que detecta objetos y los manipula con un montacargas y una pinza, para enseñar rutinas de movimiento en robótica educativa.

    1. **Qué protege** (si se concedió): ese robot con esa mecánica de montacargas + pinza.
    2. **Qué NO protege:** software, simuladores ni diagnóstico de errores.
    3. **Veredicto:** **fuera ✅**. Es la única patente mexicana cercana y es de hardware, en otro ámbito.
    4. **Recomendación:** **ignorar** para FTO; es útil para saber que en México sí hay empresas en robótica educativa.
    5. **Efecto en MX:** potencial solo si se concedió, y aun así sobre otro objeto.
    6. **¿Nos quita novedad?** No, para lo nuestro.

**Otras patentes revisadas (sin análisis de reclamos):**

| Número | Título | Titular | País / estado | Relación con GPIO Génesis |
|---|---|---|---|---|
| US20130084999A1 | Game centered on building nontrivial computer programs | Jason Churchill Costa | US, **abandonada** | Juego donde se arma un programa real. Libre para usar. |
| KR101847620B1 | System for education using integrated micro controller board | 주식회사 코더블 (antes Kim Ki-man) | KR, vigente a 2037 | Placa Arduino + sensores Scratch para educación. Solo Corea. |
| KR102547351B1 | Coding source education platform system through DIY electronic kits | 주식회사 비피 | KR, vigente a 2040 | Plataforma de coding con kits electrónicos. Solo Corea. |
| KR102107846B1 | Coding game progress system using coding robot | 주식회사 로보그램 인공지능 로봇연구소 | KR, vigente a 2039 | Juego de coding con robot. Solo Corea. |
| CN122072607A | Code error feedback method combining static and dynamic analysis | Universidad de Nanjing | CN, pendiente | Retroalimentación de errores de código para jueces en línea (sin IA generativa). |
| MX337633B | Sistema didáctico para la enseñanza de la teoría de control automático | CINVESTAV-IPN | MX, concedida | Servomotor + encoder + Simulink para enseñar control. Otro campo. |
| BR102024011267A2 | Dispositivo robótico cúbico para enseñanza de programación y microelectrónica | Universidade do Estado da Bahia | BR, solicitud | Robot con microcontrolador para enseñar. Hardware, otro formato. |
| BR102021003222A2 | Kit didáctico de enseñanza a distancia de programación por bloques | Fernando José Morse Alves | BR, solicitud | Programación por bloques, no C++. |

### Paso 5.7 — Actores tecnológicos en LATAM: Prompt 3

**Prompt del profesor (plantilla):**

```text
Actúa como analista de inteligencia tecnológica en LATAM.
Busca primero en MX y LATAM, luego global.
Concepto: [descripción + sector]
Entrega:
- Actores en México: nombre, tipo, qué hace, nivel de actividad
- Actores en LATAM (BR, CO, AR, CL, PE)
- Actores globales con presencia en LATAM
- 2–3 papers relevantes últimos 3 años
- Conclusión: densidad MX/LATAM + implicación para el equipo
```

**Nuestro prompt:**

```text
[...misma estructura del profesor...]
Concepto: GPIO Génesis — videojuego/simulador para aprender C++ real sobre
microcontroladores (ESP32, Raspberry Pi Pico 2), con estación física opcional y un
copiloto de IA que explica errores en términos del hardware. Sector: edtech + gaming +
electrónica educativa.
Usa como punto de partida a los titulares de las patentes que ya encontramos en MX y
BR, e incluye simuladores de microcontroladores en línea aunque no tengan patentes.
```

**IA usada:** el profesor sugiere Perplexity para este prompt. Nosotros usamos **Claude con búsqueda web**, y le dimos como insumo los titulares de patentes que ya teníamos. Así no empezaba desde cero y podíamos revisar cada fuente.

**Resultado:**

| Capa | Actor | Tipo | Qué hace | Actividad |
|---|---|---|---|---|
| México | **UNIT Electronics** | Empresa | Diseña y vende tarjetas ESP32 y RP2040 (p. ej. DualMCU ESP32 + RP2040, Pulsar ESP32-C6) y publica tutoriales | Alta. Posible **aliado** para la estación física. |
| México | **CINVESTAV-IPN** | Centro de investigación | Patente de sistema didáctico de control automático (MX337633B) | Media |
| México | **Corporativo STR** | Empresa | Robot educativo programable (MX2014015820A) | Baja (solicitud de 2014) |
| México | **UPAEP** | Universidad | Sistema de simulación electrónica (MX2015017704A) | Baja |
| LATAM (AR) | **Fundación Sadosky – Program.AR** | Fundación | Iniciativa nacional para llevar la enseñanza de computación a las escuelas | Alta, pero enfocada en primaria/secundaria |
| LATAM (BR) | **UNEB, IF Farroupilha, SENAI-SP** | Universidades y formación técnica | Patentes de kits y robots para enseñar programación y microelectrónica | Media. Brasil es el país más activo de la región. |
| LATAM (CO) | **Platzi** | Empresa edtech | Cursos en línea de programación, incluido C++ | Alta. Competidor indirecto (ver semana 4). |
| Global | **Wokwi** | Empresa | Simulador en línea de ESP32, Raspberry Pi Pico y Arduino | Alta. **El actor más cercano a nuestro simulador**: simula justo los mismos micros, aunque no es un juego. |
| Global | **Zachtronics** (Shenzhen I/O, TIS-100) | Estudio de videojuegos | Juegos de programación con lenguajes ficticios | Competidor directo (ver semana 4) |
| Global | **Wonder Workshop** | Empresa | Robots educativos programables; patentes vigentes en EE. UU. | Alta en EE. UU. |
| Global | **Arduino Education, Espressif, Raspberry Pi** | Fabricantes | Hardware y material educativo | Alta, con presencia en LATAM por distribuidores |

**Papers relevantes (últimos 3 años):**

1. Leinonen, J. et al. (2023). *Using Large Language Models to Enhance Programming Error Messages*. SIGCSE 2023, ACM. Muestra que un LLM puede explicar errores de programación mejor que el mensaje original del compilador, y también cuándo se equivoca. Es la base académica de nuestro copiloto.
2. *Exploring Students' Perception of Virtual Laboratory Adoption on an IoT Course*. TEM Journal, mayo 2025. Estudia a estudiantes usando un laboratorio virtual (Wokwi) en un curso de IoT.
3. *Using WOKWI Simulator to Support Engineering Student Learning in Microcontrollers and Sensors* (2024). Uso del simulador para enseñar microcontroladores y sensores en ingeniería.

**Conclusión:** la **densidad en México es baja**: no hay patentes ni empresas haciendo un juego de programación con microcontroladores reales. En LATAM la actividad es de **hardware didáctico** (sobre todo en Brasil), no de software ni de IA. Lo global está en EE. UU., Corea y China. **Implicación para nosotros:** hay espacio para ser los primeros en la región con esta combinación. El riesgo real no son las patentes sino **Wokwi**: si agrega una capa de juego, nos alcanza rápido. Nuestra ventaja tiene que venir de la narrativa de fábrica, del copiloto en español y de la velocidad de ejecución.

### Paso 5.8 — Conclusión FTO (libertad de operación)

| Parte del concepto | Nivel FTO en México | Situación | Acción |
|---|:---:|---|---|
| (a) Videojuego / simulador con C++ real | 🟢 **Alta** | No hay patentes con efecto en México. Las más cercanas están abandonadas (US20130084999A1) o solo pendientes fuera (Robotify Labs). | Continuar y documentar. Revisar los reclamos de Robotify antes de vender en EE. UU./Europa. |
| (b) Estación física ESP32 / Pico 2 | 🟢 **Alta** | Las patentes vigentes (Wonder Workshop, patentes coreanas) no tienen familia en México, y su arquitectura es distinta (control remoto desde la tablet). | Continuar. Mantener el diseño de "el código corre en el micro". |
| (c) Copiloto IA de diagnóstico | 🟡 **Media** | En México está libre, pero CN121583173B (vigente en China) protege un método muy parecido y le quita novedad a la idea. | Ajustar el concepto: diagnóstico por **explicación en lenguaje natural**, no por el método de gradientes sobre el árbol sintáctico. Protegerlo como **secreto industrial**, no como patente. |

**Conclusión general: 🟢 FTO alta en México para lanzar GPIO Génesis.** Ninguna de las patentes encontradas tiene efecto en el país. Lo que sí cambia es nuestra estrategia de PI: como el diagnóstico con IA y el gemelo virtual-físico **ya tienen antecedentes publicados**, patentar sería caro y débil. Nos protegemos con **marca + derechos de autor + secreto industrial + velocidad**, que coincide con la "estrategia pragmática" vista en clase.

!!! warning "Pendientes de esta vigilancia"
    - Repetir las búsquedas de México directamente en SIGA y las de LATAM en LATIPAT para confirmar que no falta nada.
    - Confirmar en el expediente del IMPI si MX2014015820A llegó a concederse.
    - Revisar los reclamos que finalmente se concedan a Robotify Labs en EE. UU. y Europa.

---

## Lo que cambió esta semana

- **El nombre dejó de ser una ocurrencia y ya está validado:** salió de un prompt nuestro, lo evaluamos contra otros dos finalistas con criterios de marca, verificamos su huella digital y lo buscamos en el IMPI. La conclusión es que **"GPIO" es lo que lo hace registrable** y "Génesis" solo sería un problema (por SEGA).
- **El copiloto IA ya no es "algo que nadie tiene":** en semana 2 dijimos que ningún competidor diagnosticaba el código en términos de hardware. Eso sigue siendo cierto para **productos en el mercado**, pero ya existe una **patente en China** (2026) con esa idea. Nuestro diferenciador tiene que ser la ejecución y el idioma, no la idea.
- **Apareció un actor que no teníamos en el mapa competitivo:** Wokwi, que simula exactamente los micros que queremos usar.

## Checklist de salida

- [x] ¿Qué tipo de PI proteger primero? → **Marca** (clases 9 y 41) + **derechos de autor** del juego; **secreto industrial** para el copiloto IA.
- [x] ¿Tienes 2 nombres candidatos evaluados? → **GPIO Génesis** (elegido) y **Factory++** (respaldo), más Ferrum.
- [x] ¿Sabes aplicar el protocolo de entrevista? → Sí; aplicado en las entrevistas de [Semana 4](canvas-mercado-semana4.md).
- [x] ¿Tienes 3 personas identificadas para entrevistar? → Sí; hicimos 4 entrevistas (ver Semana 4).

---

## Reflexión

Esta semana conocí un lado del desarrollo que como estudiantes de ingeniería solemos ignorar: la propiedad intelectual. De nada sirve un buen diseño si usas una librería GPL que te obliga a publicar tu código, o si alguien ya patentó tu mecanismo. Registrar la marca o firmar un acuerdo de confidencialidad no es un trámite aburrido: es lo que protege el trabajo.

La vigilancia tecnológica me gustó, pero también me generó tensión, porque buscas patentes con el miedo de encontrar que tu idea ya existe (y sí encontramos una en China muy parecida a nuestro copiloto de IA). Usar la IA para traducir los reclamos a lenguaje de ingeniería ahorró muchísimo tiempo, y cruzar los nombres que generamos con la búsqueda fonética real del IMPI hizo que se sintiera como una startup de verdad y no solo un proyecto escolar.

Del taller de entrevistas aprendí que preguntar "¿lo comprarías?" es una trampa, porque la gente dice que sí por amabilidad. Lo difícil fue no defender la idea y enfocarme solo en cómo resuelve hoy el usuario su problema.

Me queda claro que para ser un buen ingeniero de producto necesito dominar tanto lo técnico como la viabilidad legal y comercial de lo que construyo, aunque a veces el proceso sea tedioso.
