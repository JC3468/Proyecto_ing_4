# Canvas de Mercado — Semana 4

**Equipo:** José Carlos y Yael
**Concepto:** GPIO Génesis — Industria de Drones

> Videojuego de simulación de una fábrica de drones dirigido a aspirantes a programadores y entusiastas de la tecnología, que enseña C++ al requerir la escritura de código para automatizar el ensamblaje en la línea de producción y configurar el comportamiento de vuelo de los dispositivos.

---

## 1. Segmento accionable

**CAPA 1 — DEMOGRÁFICA**

- Estudiantes de ingeniería (industrial, sistemas, mecatrónica y afines) → VERIFICADO (parcial: 1 de 4 entrevistados confirma ingeniería industrial; el resto se infiere de a quién recomiendan el producto)
- Jugadores activos de simulación/sandbox, sobre todo Minecraft → VERIFICADO (4/4 entrevistados)
- Rango de edad universitaria, 18–25 años aprox. → HIPÓTESIS

**CAPA 2 — CONDUCTUAL**

- Aprenden C++ en clase tradicional, percibida como aburrida en temas densos → VERIFICADO
- Exposición previa a juegos educativos de programación solo dentro del salón, esporádica → VERIFICADO
- Ya tienen el hábito de jugar simulación/gestión recreativamente → VERIFICADO
- Hoy no usan ninguna herramienta gamificada dedicada a C++; el workaround es la clase formal → HIPÓTESIS

**CAPA 3 — PSICOGRÁFICA**

- *"C++ se utiliza en todos los trabajos de hoy en día"* — motivación por empleabilidad → VERIFICADO
- El aprendizaje debe sentirse "como un pasatiempo... no como una clase formal" → VERIFICADO
- La credibilidad y las promesas del equipo desarrollador son clave para confiar y comprar → VERIFICADO
- El interés sube fuerte cuando hay una necesidad real de aprender a programar, no solo curiosidad → VERIFICADO
- La demo debe generar enganche sin revelar demasiado contenido → VERIFICADO

**CAPA 4 — ECONÓMICA**

- Disposición de pago: 3 de 4 entrevistados entre 100–400 MXN; 1 espera precio tipo AAA (1,000–1,500 MXN) → VERIFICADO
- Perciben que este tipo de juego está "infravalorado" en el mercado → VERIFICADO
- Decisión de compra individual, sin intermediarios → HIPÓTESIS
- El costo de no programar bien se asocia a riesgo de estancamiento profesional, sin cuantificar en dinero → HIPÓTESIS

!!! warning "Hipótesis críticas sin verificar"
    1. El tema **fábrica de drones** no se ha probado directamente — las 4 entrevistas evaluaron un concepto de granja, no de drones.
    2. El precio óptimo real: rango disperso (100–1,500 MXN), depende de si el jugador ya "necesita" aprender a programar.
    3. Si el público se extiende a "entusiastas de tecnología" en general (como dice el concepto) o está concentrado solo en estudiantes de ingeniería.

**Perfil en una oración:** Estudiantes de ingeniería de 18–25 años, jugadores activos de Minecraft y simuladores, que encuentran aburridas las clases tradicionales de programación, motivados por la relevancia laboral de C++, dispuestos a pagar 100–400 MXN por una experiencia que se sienta como pasatiempo — con el tema "fábrica de drones" aún sin validar directamente con usuarios.

---

## 2. Tamaño de mercado

| | Universo | Valor anual | Fuente / lógica |
|---|---|---|---|
| **TAM** | 380,000 personas | $95,000,000 MXN | INEGI/CONAPO (población 18–24), SEP (matrícula educación superior 2025–26), The CIU (mercado gamer México 2025), Steam pricing research 2025–26 |
| **SAM** | 219,000 personas | $54,750,000 MXN | Filtros aplicados al TAM: geografía urbana con smartphone (90%) · canal digital en español (80%) · precio compatible con ticket indie 199–299 MXN (80%) |
| **SOM (años 1–2)** | 2,200 personas | $275,000 MXN/año* | Canal: Steam + orgánico (TikTok/YouTube/Discord) · Piloto: Puebla, CDMX, Guadalajara, Monterrey · Equipo de 4 personas |

<small>\* Corrección de cálculo: 2,200 clientes × $250 MXN = $550,000 MXN **a lo largo de 2 años** → $275,000 MXN/año en promedio.</small>

!!! warning "Punto a revisar antes de defender el número"
    El TAM se calculó primero de forma directa en 229,000 personas (1.27M estudiantes STEM × 60% jugadores × 30% preferencia sandbox), y luego se redondeó hacia arriba a 760,000 citando la penetración de Minecraft/Roblox — sin una fórmula explícita que sustente ese salto de 3.3x. Esto es justo lo que el curso llama "la trampa del TAM aspiracional". Si se pregunta por la lógica de esa reducción específica, la respuesta honesta es que falta una fuente más precisa del % de estudiantes de ingeniería que juegan sandbox.

**Señal de viabilidad:** SOM × precio anual ≈ $275,000 MXN/año. **Marginal / insuficiente** — no cubre la nómina básica de un equipo de 4 personas (~$720,000–960,000 MXN/año). Requiere financiamiento externo, preventas, o ampliar el modelo de ingresos (ver precio y modelo, abajo).

---

## 3. Mapa competitivo

**Directos**

| Competidor | Descripción | Opera en | Precio | Debilidad específica |
|---|---|---|---|---|
| Shenzhen I/O | Simulación de circuitos con lenguaje assembly ficticio | Global (Steam) | ~255 MXN | No enseña C++ real; curva brutal para principiantes; sin narrativa de empleabilidad |
| TIS-100 | Puzzle de programación assembly en arquitectura ficticia | Global (Steam) | ~119 MXN | Conceptos de bajo nivel no transferibles a C++; sin mecánica de fábrica/ensamblaje |
| EXAPUNKS | Automatización/hacking con lenguaje ficticio | Global (Steam) | ~340 MXN | Lenguaje ficticio; contexto ciberpunk sin conexión a manufactura/drones |

**Indirectos**

| Competidor | Descripción | Opera en | Precio | Debilidad específica |
|---|---|---|---|---|
| CodeCombat | RPG gamificado con código real (incluye C++) | Global, tracción en LATAM | Gratis + $9.99 USD/mes | Experiencia abstracta, sin simulación tangible; se percibe como "otra plataforma de cursos" |
| Platzi (curso C++) | Curso online con certificación | México/LATAM | ~$4,227 MXN/año | Se siente como clase grabada — justo lo que el usuario quiere evitar; 14–21x más caro |
| Udemy (cursos C++) | Cursos individuales con proyectos | Global | ~$170–1,530 MXN | Proyectos genéricos, sin gamificación; alta tasa de abandono |

**Sustitutos**

| Comportamiento | Costo para el usuario |
|---|---|
| Proyectos personales en GitHub + tutoriales de YouTube | 20–40 horas de tiempo autodirigido, debugging sin guía, riesgo de abandono |
| Curso universitario tradicional de C++ | Incluido en colegiatura, pero percibido como aburrido y desconectado de aplicaciones reales |

!!! danger "El competidor más peligroso: GitHub + YouTube"
    Costo marginal cero, flexibilidad total de proyecto, y produce una señal de empleabilidad directa (repositorio real) más creíble ante reclutadores que "terminar un videojuego". Para desplazarlo, GPIO Génesis necesita: portafolio exportable a GitHub, alguna forma de validación externa (alianzas con empresas de manufactura/drones), y comunidad/leaderboards que el aprendizaje solitario no ofrece.

---

## 4. Lienzo Blue Ocean

**Marco de las cuatro acciones**

- **ELIMINAR:** lenguaje de programación ficticio — el segmento quiere C++ real y transferible a empleo.
- **REDUCIR:** precio frente a plataformas de curso — 199–299 MXN vs. 1,530–4,227 MXN de la competencia indirecta.
- **INCREMENTAR:** narrativa de empleabilidad/conexión industrial, y feedback visual/tangible inmediato (ver el dron volar por tu código).
- **CREAR:** portafolio exportable a GitHub, y profundidad de simulación de sistemas embebidos reales (ventaja de dominio del equipo en mecatrónica).

**Tabla de puntuación** (1 = muy bajo · 5 = muy alto)

| Atributo | Shenzhen I/O | CodeCombat | GitHub+YouTube | **GPIO Génesis** |
|---|:---:|:---:|:---:|:---:|
| Precio accesible | 3 | 3 | 5 | 4 |
| Lenguaje ficticio (eliminado) | 5 | 1 | 0 | **0** |
| Curva accesible para principiantes | 1 | 4 | 2 | 4 |
| Fidelidad a C++ real | 1 | 4 | 5 | 5 |
| Narrativa de empleabilidad/industria | 1 | 2 | 3 | **5** |
| Feedback visual/tangible inmediato | 2 | 3 | 2 | **5** |
| Portafolio exportable (creado) | 0 | 1 | 5 | **5** |
| Profundidad sistemas embebidos (creado) | 2 | 0 | 3 | **5** |

**El hueco azul en una oración:** GPIO Génesis ocupa el espacio donde el código es tan real y transferible a empleo como el que ofrece el sustituto gratuito, pero con feedback visual inmediato, narrativa industrial y profundidad de sistemas embebidos que ningún competidor ofrece hoy.

!!! warning "Advertencia de océano rojo"
    En "Fidelidad a C++ real" el concepto empata con GitHub+YouTube (5 vs. 5). Si el jugador percibe que aprender ahí es equivalente a lo gratuito, el precio se vuelve el único criterio de decisión. El portafolio exportable y el feedback visual son lo que tiene que sostener esa comparación.

---

## 5. Propuesta de valor

**Oferta (qué vendemos):**
Un videojuego de simulación donde escribes código en C++ para automatizar una línea de ensamblaje de drones y configurar su comportamiento de vuelo.

**Propuesta de valor (≤25 palabras):**
"El único juego que convierte tu código C++ en portafolio real de automatización, para estudiantes de ingeniería que aprenden sin que se sienta como tarea."

**Nivel en la pirámide de Bain:** Funcional, con oportunidad real de subir a Emocional apoyándose en la ansiedad de empleabilidad verificada en las 4 entrevistas.

**Evaluación IDEO**

- Insightful ✅ — combina código real + feedback tangible + narrativa de empleo, algo que ningún competidor da junto.
- Unique ✅ — el conocimiento del equipo en mecatrónica/sistemas embebidos es difícil de copiar por un estudio genérico.
- Targeted ⚠️ — el comportamiento e intereses del segmento están verificados; la carrera/edad exacta del usuario sigue siendo mayoría hipótesis.

**Error que cometía la primera versión:**
La descripción inicial del concepto ("un videojuego que enseña C++ al requerir la escritura de código para automatizar...") es una **oferta técnica**, no una propuesta de valor — describe el mecanismo del producto en lugar del resultado para el usuario (Error 1: confundir feature con valor).

---

## Precio y modelo de ingresos

**Recomendación:** Venta única, **199–299 MXN**, con posible expansión futura tipo DLC (módulos avanzados de C++, temas de sistemas embebidos). 3 de 4 entrevistados se ubicaron en el rango 100–400 MXN y hablaron del producto como "comprar un juego", no como un servicio recurrente — una suscripción mensual añade fricción sin sustento en la evidencia recabada. El análisis de viabilidad del Bloque 4 sugiere que este modelo por sí solo es marginal para sostener al equipo — el DLC y/o preventas son la vía más consistente con lo que dicen los usuarios.
