# Reporte de Oportunidad — Semana 2


**Equipo:**  José Carlos y Yael
**Concepto elegido:** GPIO Génesis *(confirmado y afinado con datos de mercado — ver sección 8, "Lo que cambió")*

## 1. El problema

**Segmento específico:** estudiantes de ingeniería (mecatrónica, sistemas, electrónica) de 18–25 años en México y LATAM, jugadores activos de Minecraft y simuladores, que cursan materias que exigen integrar código C/C++ con hardware real (microcontroladores, sensores, actuadores) para proyectos y prácticas de laboratorio.

**Dolor ⭐:** en clase, aprender C/C++ se siente aburrido y desconectado de aplicaciones reales, pese a que el estudiante sabe que es clave para conseguir empleo. El código se escribe y depura en un entorno abstracto de texto plano, sin retroalimentación clara de por qué falla al ejecutarse sobre hardware real. Cuando el estudiante enfrenta un error de sintaxis o de lógica sin un entorno que le indique dónde está la falla, se generan bloqueos que en los casos más graves terminan en reprobar la materia o el proyecto — no por falta de esfuerzo, sino por falta de diagnóstico.

**Workaround actual (con costo observable):** los estudiantes ya gastan de su bolsillo en kits de Arduino/Elegoo ($600–8,270 MXN según el kit) y componentes adicionales, y pasan horas — a veces madrugadas — quemando placas y probando por ensayo y error. Cuando se atoran, recurren a foros (Stack Overflow, comunidades de robótica) y repositorios de GitHub con guías de aprendizaje autoconstruidas, ninguno de los cuales les dice *específicamente* por qué su código no logra el comportamiento que el hardware necesita.

## 2. Evidencia de deseabilidad

*(Verificación cruzada con datos de fuentes primarias — Pasos 1, 2 y 5)*

- **Pago por soluciones imperfectas — ✅ confirmada.** Los kits educativos (Arduino Education Starter Kit ~$8,270 MXN, kits básicos $600–900 MXN, presupuesto de proyecto promedio $1,320–6,120 MXN) y las plataformas de cursos (Platzi ~$4,227 MXN/año, Codecademy Pro ~$370 MXN/mes) tienen mercado activo y pagado en México.
- **Tamaño del segmento — ✅ confirmada con fuente oficial.** Según matrícula ANUIES 2024–2025, solo en 6 estados (CDMX, Edomex, Jalisco, Nuevo León, Puebla, Veracruz) hay ~459,000 estudiantes de Ingeniería, Manufactura y Construcción; a nivel nacional, ingeniería + TIC supera los 900,000 estudiantes.
- **Costo observable — ✅ confirmada.** La deserción en carreras de tecnología en México ronda 8–20%, y estudios de cursos introductorios de programación (CS1) documentan tasas de reprobación/abandono de 28–50%, atribuidas explícitamente a la brecha entre la lógica abstracta del código y su aplicación práctica.
- **Comunidades activas — ✅ confirmada.** Foros de hardware y programación (Stack Overflow, r/Devmexico, r/taquerosprogramadores, MexicoDev) y repositorios de GitHub de aprendizaje autodidacta (33,000+ estrellas en repos tipo *TheAlgorithms/C-Plus-Plus*) muestran volumen constante de estudiantes buscando ayuda con este problema.
- **Competencia mapeada con brecha clara — ✅ confirmada.** Arduino Education, Elegoo y National Instruments venden kits o interfaces de bloques, pero **ninguno ofrece una capa de IA que diagnostique en tiempo real por qué el código C/C++ no produce el comportamiento esperado en el hardware** — ese es el espacio sin cubrir.

## 3. Pain-Gain Map (versión final, auditada)

**Usuario / segmento:** estudiantes de ingeniería (mecatrónica, sistemas, electrónica) en LATAM que necesitan integrar C/C++ con hardware real.

### Dolores (de mayor a menor intensidad)

| # | Dolor |
|---|-------|
| ⭐ D2 | Enfrentarse a errores de sintaxis o de lógica sin un entorno interactivo que señale dónde está la falla genera bloqueos que, en el peor caso, terminan en reprobar la materia o el proyecto. |
| D1 | El código C++ tradicionalmente se compila y depura en una consola de texto plano, lo que dificulta visualizar cómo las variables y estructuras de datos se traducen en el comportamiento real del hardware. |
| D3 | Los ejercicios teóricos estándar (calculadoras, bases de datos simples) se sienten desconectados de la motivación real: hacer que un microcontrolador o un mecanismo físico funcione. |

### Ganancias (de mayor a menor deseo)

| # | Ganancia |
|---|----------|
| ⭐ G3 | Adquirir fundamentos de desarrollo de software y control de hardware directamente transferibles al mundo profesional — no ejercicios de juguete, sino habilidad certificable. |
| G1 | La satisfacción inmediata de ver cómo una instrucción de código se traduce en el movimiento de un motor, la lectura de un sensor o la sincronización de un proceso físico real. |
| G2 | Asimilar conceptos complejos (bucles, condicionales, punteros, funciones) de forma orgánica, como herramientas necesarias para resolver un problema físico tangible, no como temario aislado. |

### El cruce más poderoso

**Dolor ⭐ D2 × Ganancia ⭐ G3** — el estudiante no abandona por falta de interés en programar; abandona porque no tiene forma de saber *por qué* su código no logra el resultado físico que busca, y eso le cuesta la certeza de estar construyendo una habilidad real y transferible a su carrera. Un sistema que traduzca cada error en una explicación concreta sobre el hardware — no solo un mensaje de compilador — ataca directamente ese cruce.

Este cruce es la explicación técnica detrás de una idea sencilla: la sensación de *jugar* depende de que cada error tenga una respuesta inmediata y clara, y la confianza real en la habilidad depende de que sea genuinamente transferible — exactamente G1 y G3.

## 4. Concepto recomendado

**Nombre:** GPIO Génesis — el núcleo del concepto es un videojuego de simulación de fábrica de drones donde el jugador escribe C++ real (no un lenguaje ficticio, a diferencia de Shenzhen I/O o TIS-100) para automatizar la línea de ensamblaje. La investigación de esta semana confirma ese núcleo y agrega una capa adicional, respaldada por evidencia de mercado.

**Descripción:** el simulador de fábrica de drones sigue siendo el producto central — la capa de juego que enseña sintaxis y lógica de forma práctica y entretenida, compitiendo directamente contra juegos de programación con lenguaje ficticio (Shenzhen I/O, TIS-100, EXAPUNKS) e indirectamente contra plataformas abstractas sin narrativa (CodeCombat, Platzi, Udemy). A eso se suma, como expansión validada por la investigación de esta semana, una **estación de pruebas física** — un módulo con motores, sensores y actuadores conectado a un ESP32 — que recibe el código que el estudiante ya practicó en el simulador y lo ejecuta sobre hardware real. Una **app con IA actúa como copiloto de depuración**: en vez de un mensaje de error genérico, explica la falla en términos del hardware ("tu motor recibe señales erráticas porque tu bucle no está gestionando bien el delay, ajusta aquí").

**De dónde sale (trazabilidad):**
- El simulador (núcleo del producto) resuelve bien D1 y D3 del Pain-Gain Map — la parte de "aburrido y desconectado" — pero por sí solo no resuelve el dolor más intenso (D2, la falta de diagnóstico).
- La estación física + app de diagnóstico vienen de la arista identificada en el Paso 2 (Insight B, verificado con datos ANUIES y de gasto real en kits) y confirmada en la auditoría del Pain-Gain Map del Paso 3. Refuerzan, no reemplazan, la ventaja "Unique" del equipo: su formación en mecatrónica y sistemas embebidos.

**Riesgo principal a vigilar (heredado del Paso 5):** la validación de deseabilidad marcó **alto riesgo de inviabilidad técnica** si se intenta construir, en seis meses, un compilador de C++ funcional embebido en el videojuego completo, más la estación física, más la app de diagnóstico, más la web — las cuatro piezas al mismo nivel de fidelidad. La lectura correcta no es descartar la estación física — es que el MVP prioriza el simulador (el componente de menor riesgo técnico) y trata la estación física como expansión de la segunda mitad del semestre, no como parte del lanzamiento inicial.

**Modelo de negocio:** venta única del videojuego/simulador en un rango objetivo de $199–299 MXN — muy por debajo de plataformas de curso como Platzi o Udemy, pero por encima de las alternativas gratuitas, siguiendo la lógica de reducción de precio del análisis Blue Ocean. Este rango queda como hipótesis a confirmar en las entrevistas de semana 3 (ver sección 7). La estación de pruebas física se vende por separado, a precio de referencia tipo Arduino Education (~$1,300–8,270 MXN), y abre además un canal institucional (B2B con laboratorios universitarios): licencias comparables del sector (CodeCombat y análogos) cotizan entre $7 y $25 USD por estudiante al año, un techo de referencia razonable para una propuesta con analítica y soporte incluidos.

## 5. La oportunidad en una oración

"Existe una oportunidad para estudiantes de ingeniería en LATAM, motivados por la relevancia laboral de C++ pero aburridos de las clases tradicionales, que necesitan aprender programando algo real y, eventualmente, validar ese código en hardware real — porque hoy la oferta educativa es teórica y desconectada, los juegos de programación existentes usan lenguajes ficticios, y los kits de hardware (Arduino, Elegoo) no diagnostican por qué el código falla al ejecutarse."

## 6. Por qué este equipo

**Elemento técnico concreto (Criterio 2):** el equipo cursa actualmente sistemas embebidos y viene de formación en mecatrónica, lo que le permite diseñar la lógica de la estación de pruebas (ESP32, sensores, actuadores) y el diagnóstico de la app con conocimiento real de control de hardware — no como un estudio de videojuegos genérico que tendría que aprenderlo desde cero.

## 7. Hipótesis para Semana 3

**Hipótesis 1 — sobre el dolor:** "Creemos que los estudiantes de primeros semestres de ingeniería experimentan bloqueos paralizantes al integrar código con hardware con frecuencia de 2–3 veces por semana, y les cuesta horas de frustración y riesgo de reprobar, porque las herramientas actuales no explican la falla en términos del comportamiento físico. Si esto es falso, el concepto necesita volver a la programación pura, sin componente de hardware."
*Cómo probarla:* pedir que cuenten, paso a paso, la última vez que se atoraron más de una hora integrando código con un componente físico — qué intentaban hacer, qué mensaje de error recibieron, y qué hicieron para salir de ahí.

**Hipótesis 2 — sobre la solución:** "Creemos que estos estudiantes preferirían un diagnóstico de IA que traduzca el error a comportamiento físico ('tu motor no gira porque...') sobre leer un mensaje de compilador tradicional, porque tangibiliza la causa del problema. Si esto es falso, el concepto necesita enfocarse en mejorar el simulador visual en vez del diagnóstico de hardware."
*Cómo probarla:* mostrar dos versiones de un mismo error — el mensaje crudo de consola vs. la explicación traducida por la IA — y preguntar cuál usarían las próximas dos horas de trabajo y por qué.

**Hipótesis 3 — sobre el pago:** "Creemos que estos estudiantes pagarían entre $199 y $299 MXN por el software/simulador, y que instituciones pagarían entre $7 y $25 USD por estudiante al año por acceso con analítica, porque lo perciben como herramienta de supervivencia académica, no como entretenimiento. Si esto es falso, el modelo necesita moverse a venta directa de hardware con margen, sin depender de licencias recurrentes."
*Cómo probarla:* pedir el último recibo o gasto real que hicieron en un componente, kit o curso para resolver un proyecto de la carrera, y preguntar cuánto pagarían hoy por evitar repetir esa frustración.

## 8. Lo que cambió

Al arrancar esta investigación probamos el concepto original con una definición amplia de usuario ("gamers interesados en la tecnología"), sin distinguir todavía el segmento específico que resultaría más sólido.

Hoy, con evidencia de tres análisis independientes (auditoría de ecosistema, comparación de insights con datos de ANUIES y del mercado, y validación de deseabilidad), descubrimos que el dolor real y verificable no está en gamers genéricos que *quieren* aprender C++ por curiosidad — está específicamente en estudiantes de ingeniería que *ya tienen* que integrarlo con hardware real para no reprobar, que además resultan ser jugadores activos de simuladores, y que ya gastan dinero y tiempo real resolviendo ese problema a ciegas.

Eso no cambia el producto central — el videojuego de fábrica de drones con C++ real sigue siendo el corazón del concepto — lo hace más preciso: acota el segmento a estudiantes de ingeniería, identifica la causa exacta del aburrimiento (falta de diagnóstico cuando el código falla en hardware), y agrega una expansión concreta que el concepto original no tenía: una estación de pruebas física (ESP32 + sensores + actuadores) con una app que traduce errores de código a comportamiento físico — el diferenciador que ningún competidor mapeado, ni de juegos (Shenzhen I/O, TIS-100) ni de hardware (Arduino Education, Elegoo), ofrece hoy.
