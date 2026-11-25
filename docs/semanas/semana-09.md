# Semana 9 — Integración, go-to-market y pitch

**Equipo:** José Carlos y Yael  
**Concepto:** GPIO Génesis — videojuego de una fábrica de drones donde se programa una Raspberry Pi Pico 2 virtual con C/C++ real, con un copiloto de IA  
**Tipo de producto:** 100% digital (sin hardware)  
**Fecha:** 24 de noviembre de 2026

!!! abstract "De qué trata esta semana"
    Es la semana de juntar todo y salir a probarlo: que el juego, el copiloto y la landing funcionen como un solo sistema, que lo prueben personas del segmento, que los números del negocio se sostengan y que podamos contarlo en 3 minutos y mostrarlo en 7.

    Entregables de esta página: integración y pruebas T1–T6 (sección 1), pruebas con usuarios (sección 2, **PENDIENTE**), go-to-market, economía unitaria y landing (sección 3), checklist regulatorio (sección 4), pitch (sección 5) y guion de la demo (sección 6).

!!! warning "Estado al 24 de noviembre de 2026"
    - **Pruebas con usuarios: PENDIENTES.** No hemos hecho ninguna sesión, así que no hay tiempos, tasas de éxito ni citas de usuarios del juego.
    - **Landing: publicada**, con el formulario y las métricas conectados. Todavía no hay visitas ni pre-registros que reportar.
    - **Economía unitaria: SUPUESTOS.** Los números salen del Excel; ninguno está medido todavía.

!!! info "Dólares y pesos"
    Lo que se cobra en dólares (cuota de Steam, dominio, firma de código, Supabase y la API de Claude) se deja en **US$**, porque el tipo de cambio varía. Para sumarlo con lo que está en pesos usamos la aproximación **US$1 ≈ $17.50 MXN** (supuesto del proyecto desde la semana 5, el mismo de los Excel; no es el tipo de cambio del día). Las cifras en pesos que dependen de esa conversión llevan **≈**.

## 0. Cómo adaptamos la semana a un producto digital

| Lo que pide el curso | Qué es en GPIO Génesis |
|---|---|
| Frontera sensor → microcontrolador | **F1:** código del jugador → MCU virtual (el intérprete del juego) |
| Frontera dispositivo → red | **F2:** juego → internet (petición HTTPS a la API de Anthropic) |
| Frontera backend → IA | **F3:** Claude → respuesta que se puede mostrar (español, sin código, sin Markdown) |
| Frontera backend → app / landing | **F4:** juego y landing → lo que ve el usuario (pantalla, Google Forms) |
| Instalación del dispositivo | Descargar el `.exe` y llegar al nivel 1 |
| Autonomía de batería (T4) | No aplica; equivalente: rendimiento en la PC mínima |
| Producto físico (30% de la rúbrica de la demo) | No hay: el producto es 100% digital. Opción para la demo: cargar en una Pico 2 real un `main.c` exportado por el juego |

## 1. Integración del sistema (Bloque 1)

### 1.1 Las cuatro fronteras y dónde ponemos el registro

| Frontera | Protocolo | Registro para la bisección | Estado |
|---|---|---|---|
| **F1** · código → MCU virtual | Llamada interna del juego (GDScript) | Pestaña Consola del juego (errores de compilación y de ejecución) y comparación contra gcc en las pruebas de lenguaje | ✅ 41 programas de C dan la misma salida que gcc y 14 de C++ la misma que g++. Los tiempos no se han comparado con una Pico real |
| **F2** · juego → internet | HTTPS, API de mensajes de Anthropic (JSON) | Código HTTP y tokens de entrada y salida de cada petición | ⚠️ La petición se arma y se revisa en las pruebas, y el 25-nov ya se mandó con una clave real y Claude respondió. **Falta medir** tokens y tiempo |
| **F3** · Claude → respuesta utilizable | Texto | Respuesta cruda antes del filtro de código y del convertidor de Markdown | ⚠️ Filtro y convertidor hechos; falta evaluar las respuestas reales |
| **F4** · juego y landing → usuario | Pantalla; landing → Google Forms por HTTPS (`formResponse`) | Capturas de pantalla; fila nueva en la hoja; visitas en GoatCounter | ⚠️ Juego probado en la prueba de niveles; landing publicada, falta medir |

**Frontera donde es más probable que falle:** F2/F3, porque es la única que nunca se ha probado de verdad. El registro que vamos a poner ahí: código HTTP, tokens y la respuesta cruda de las primeras 10 consultas con la clave del equipo.

### 1.2 Pruebas del sistema (T1–T6)

| # | Prueba del curso | Versión GPIO Génesis | Criterio | Resultado |
|---|---|---|---|---|
| T1 | Instalación sin ayuda | Descargar el `.exe` y llegar al nivel 1 | < 5 min sin intervención | ⏳ **PENDIENTE** (el Release ya está; falta una persona) |
| T2 | Latencia de punta a punta | Compilar → la planta reacciona; pedir ayuda a la IA → respuesta | ≤ 3 s y ≤ 8 s (RD-01, RD-03) | ⏳ **PENDIENTE** |
| T3 | Desconexión y recuperación | Cortar el internet 10 min mientras se juega | Sigue con pistas locales, avisa «IA sin conexión» y no pierde progreso | ⚠️ Probado con red simulada en las pruebas automáticas; falta en vivo |
| T4 | Autonomía de batería | Rendimiento en la PC mínima | ≥ 30 fps (RD-02) | ⏳ **PENDIENTE** |
| T5 | Falla de la API de IA | Clave inválida o sin respuesta | Mensaje claro, se queda la pista 4 y el juego no se rompe | ✅ En pruebas automáticas (clave falsa y sin conexión); falta con la API real |
| T6 | Operación continua | Sesión de 2 h seguida; cerrar y volver a abrir | Sin cierres; el progreso sigue | ⏳ **PENDIENTE** |

### 1.3 Definición de "listo"

- [ ] El juego corre en una PC que no es nuestra, sin Godot (hoy solo se ha abierto en nuestras PCs).
- [x] La demo (`GPIO_Genesis_demo.exe`) se descarga de un Release público (25-nov); usa Claude Haiku 5.5.
- [x] La landing está publicada en internet, con el Google Form y GoatCounter.
- [ ] El flujo descargar del Release → nivel 1 → pedir ayuda → exportar, y aparte landing → apartar una copia, sale sin que intervengamos.

**Qué no funciona todavía y por qué:** cuentas y progreso en la nube, cuota de consultas con nuestra clave y respuestas en streaming (no hay backend); compilación con el toolchain real de ARM (se usa el intérprete); niveles de los bloques 2 y 3 (tiempo). Detalle y errores encontrados por frontera en la [página del proyecto final, §17](../proyecto-final.md#s17).

## 2. Pruebas con usuarios (Bloque 2) — PENDIENTE

!!! danger "PENDIENTE: todavía no hemos hecho ninguna sesión"
    Vamos a probar con **5 personas: 3 del segmento** (estudiantes de Mecatrónica o Electrónica que llevan o llevaron Sistemas Embebidos) **y 2 de otro equipo** (prueba cruzada). Esta sección se llena con lo que pase en esas sesiones; no hay resultados todavía.

**Protocolo (30 min por persona):**

| Min | Qué pasa | Qué se observa |
|---|---|---|
| 0–3 | Guion fijo: «Es un juego para PC. Te voy a pedir tres cosas; piensa en voz alta. Yo no puedo ayudarte.» | Nada del producto se explica |
| 3–10 | **Tarea 1:** descargar la demo del Release de GitHub y abrir el nivel 1 | Si lo logra solo, tiempo y dudas (incluida la advertencia de Windows) |
| 10–20 | **Tarea 2:** completar el turno 1 del nivel 1 y decir qué le pide el copiloto cuando algo falla | Si entiende la alerta y la pista, y qué hace después |
| 20–25 | **Tarea 3:** encontrar el precio y apartar una copia | Si encuentra el botón y completa el pre-registro |
| 25–30 | Cierre: ¿cuándo fue la última vez que te atoraste con un programa y qué hiciste? ¿Cuánto pagaste por tu último juego o curso? ¿Te apunto para el piloto? | Compromisos, no opiniones |

Reglas del observador: no ayudar, no explicar, no defender el diseño; si se atora, anotar el minuto y esperar 30 s; ante «¿esto qué hace?», responder «¿qué esperabas que hiciera?». Grabar la pantalla solo con permiso.

**Registro por sesión (para llenar):**

| Sesión | Perfil | Tarea | ¿Sin ayuda? | Tiempo | Errores / dudas | SEQ (1–7) | Cita textual |
|---|---|---|---|---|---|---|---|
| S1 | Segmento | T1 / T2 / T3 | | | | | |
| S2 | Segmento | T1 / T2 / T3 | | | | | |
| S3 | Segmento | T1 / T2 / T3 | | | | | |
| S4 | Otro equipo | T1 / T2 / T3 | | | | | |
| S5 | Otro equipo | T1 / T2 / T3 | | | | | |

**Reporte de síntesis (se escribe después de las 5 sesiones):**

| Parte del reporte | Resultado |
|---|---|
| Tabla por tarea: éxito sin ayuda, tiempo mediano, SEQ promedio | **PENDIENTE** |
| Hallazgos por severidad (**crítico:** impide terminar la tarea · **mayor:** la termina con ayuda o con un error recuperable · **menor:** molesta, sin consecuencias), con cuántos lo vivieron y una cita | **PENDIENTE** |
| Tres cambios de alto impacto y bajo esfuerzo, cada uno con la hipótesis que prueba | **PENDIENTE** |
| Lo que no se puede concluir con 5 personas | **PENDIENTE** |
| Señal de negocio: compromisos reales (dejó sus datos, se apuntó al piloto) separados de opiniones («sí lo compraría») | **PENDIENTE** |

??? note "Ideas de mejora que vamos a observar (sin evidencia todavía)"
    Salieron de revisar el juego nosotros mismos, no de usuarios. Las sesiones dirán si hacen falta:

    | Idea | Qué vamos a observar |
    |---|---|
    | Al pasar el cursor sobre una constante del código (p. ej. `MOTOR_BANDA`), iluminar su máquina en la fábrica | Si tardan en ubicar qué máquina corresponde a cada pin |
    | Una ventana corta la primera vez que se abre el osciloscopio: 1 = 3.3 V, 0 = 0 V | Si leen el osciloscopio sin explicación |
    | Explicar los errores de compilación en lenguaje claro | **Ya existe:** cada nivel trae explicaciones de los errores de compilación (falta `;`, paréntesis sin pareja, C++ no soportado). Observaremos si alcanzan |

## 3. Go-to-market y economía unitaria (Bloque 3)

### 3.1 Primeros 20 clientes y cliente número 1

**Cliente número 1:** estudiante de Mecatrónica o Electrónica de 19–23 años que cursa Sistemas Embebidos con la Raspberry Pi Pico 2, juega Minecraft o simuladores en una laptop con Windows y ha comprado juegos independientes de $100–400 MXN. Carrera y edad: HIPÓTESIS; que juega simuladores y lo que pagaría: VERIFICADO en 4 entrevistas (semana 4). **Nombre y apellido: PENDIENTE**, se lo pondremos con la primera persona real del piloto.

| Rasgo de un early adopter | En nuestro caso | Estado |
|---|---|---|
| Dolor agudo y reciente | Una práctica de Sistemas Embebidos que compila pero no funciona, esta semana | HIPÓTESIS |
| Ya intentó resolverlo | YouTube, foros, compañeros o IA | HIPÓTESIS |
| Acceso y poder de decisión | Tiene su laptop con Windows y paga sus propios juegos (3 de 4 pagarían $100–400) | VERIFICADO en entrevistas |
| Habla con otros como él | Comparte grupo y chat con 20–30 compañeros de la misma materia | HIPÓTESIS |

**Los primeros 20:** un grupo de Sistemas Embebidos, con el permiso del profesor, para el piloto gratis.

### 3.2 Canales

| Canal | Cuándo conviene | Qué aprendemos | Riesgo |
|---|---|---|---|
| **Venta directa:** demo de 10 min en un salón + QR a la landing; después, licencia escolar | Ahora: el segmento está concentrado en salones y el profesor da credibilidad | Conversión visita → pre-registro por grupo (`?ref=salon`) | Depende de que un profesor nos preste la clase; la licencia escolar tiene ciclos lentos |
| **Distribuidor:** Steam (y un Release de GitHub para el piloto) | Lanzamiento, con la demo del nivel 1 gratis | Ventas reales y reseñas | 30% de comisión; revisión de Valve y declaración del uso de IA |
| **Landing con pre-registro** | Ya: está hecha y no cuesta | Qué mensaje convierte y cuántos dejan sus datos | Un pre-registro sin pago es una señal débil |
| **Crowdfunding** | No por ahora: no tenemos comunidad ni video real del juego | Si pagan por adelantado | Una campaña que no llega a la meta se ve en público |

**Elección:** landing con pre-registro + demos en salones ahora (cuestan horas, no dinero, y se pueden medir), Steam para el lanzamiento y la licencia escolar como palanca.

**Embudo:** publicaciones y demos en salones → landing → «Apartar mi copia» (nombre, correo y carrera, sin pago) → invitación al piloto con el `.exe` → compra en Steam cuando exista la página. Las wishlists de Steam llegan hasta que tengamos página en la tienda, que requiere pagar la cuota de US$100.

### 3.3 Economía unitaria (Prompt 3)

Con las fórmulas del curso y los datos de la semana 7. Como el juego es pago único, el LTV suma la contribución del juego y lo que se espera de Copiloto Pro. Excel con fórmulas: [economía unitaria](../recursos/archivos/proyecto-final/economia-unitaria-gpio-genesis.xlsx).

| Métrica | Fórmula | Valor | Estado |
|---|---|---|---|
| Contribución del juego por copia | $249 − 30% de Steam − IA (US$0.049 ≈ $0.86) | **≈ $173.44** | SUPUESTO (tokens sin medir) |
| Contribución mensual de Pro | $49 − (IA de US$0.15 al mes ≈ $2.63 + pasarela) | ≈ $40.85 | SUPUESTO |
| Churn mensual de Pro | cancelan en el mes ÷ suscriptores al inicio | 35% | SUPUESTO |
| Vida media | 1 ÷ churn | 2.9 meses | SUPUESTO |
| LTV por comprador | $173.44 + 10% × ($40.85 × 2.9) | **≈ $185.11** | SUPUESTO |
| CAC | gasto en conseguir clientes ÷ clientes nuevos = (16 h × $80 + $200 de traslados) ÷ 33.3 copias al mes | **$44.40** | SUPUESTO |
| Inversión por cliente | CAC + hardware subsidiado ($0) | $44.40 | — |
| LTV ÷ inversión | | **4.2×** (regla: ≥ 3) | SUPUESTO |
| Payback | inversión ÷ contribución | **0 meses**: el pago único cubre el CAC el día de la compra | SUPUESTO |
| CAC máximo para llegar a 3× | | $61.70 | Derivado |

**Lo que más mueve el resultado (±25% por insumo):** el CAC y el precio (de −24% a +33%). Churn y conversión a Pro casi no pesan (±2%) porque Pro es ~6% del LTV.

| Palanca | Efecto en LTV ÷ inversión |
|---|---|
| Subir el precio a $299 | 4.2× → 4.96× |
| Demos en clase y referidos (CAC a la mitad) | 4.2× → 8.3× |
| Licencia escolar de 30 alumnos a $150 | 9.3× por grupo |
| Riesgo: la API sube 10 veces | 4.2× → 3.84× (sigue cumpliendo) |

!!! warning "Alerta"
    Con el mismo esfuerzo de difusión pero solo 150 copias al año (escenario pesimista), el CAC sube a $118 y la razón cae a **1.6×**: ahí no se cumple la regla. Por eso la primera métrica que hay que medir es el CAC real con la landing.

**Recuperar lo invertido:**

| Concepto | Valor | Fuente |
|---|---|---|
| Precio | $249 MXN, pago único | Semana 7 |
| Cuota de Steam Direct | US$100 (≈ $1,750 MXN) | Semana 7 (verificada) |
| Lanzamiento, 12 meses: Steam US$100, dominio US$12, firma US$99, Supabase Pro US$300 | **US$511** (≈ $8,942.50 MXN) | Excel de alcance |
| Copias para recuperar el dinero que sale del bolsillo | **≈ 52** solo con el juego; **≈ 49** contando Copiloto Pro | Excel de alcance / modelo financiero |
| Copias para recuperar también nuestras horas | ≈ 343 solo con el juego; ≈ 322 con Pro | Ídem |

Conversiones con US$1 ≈ $17.50 MXN. Por cada peso que suba el dólar, el lanzamiento cuesta unos $511 MXN más: alrededor de 3 copias más para recuperarlo.

### 3.4 Landing page

**URL:** <https://jc3468.github.io/Proyecto_ing_4/gpio-genesis/> (funciona después del push). Para las demos, el QR lleva `?ref=qr`.

| # | Elemento | Qué tiene |
|---|---|---|
| 1 | Headline | «Aprende C/C++ para microcontroladores arrancando una fábrica de drones» |
| 2 | Visual | Captura real del juego en una laptop, con el globo del copiloto |
| 3 | Tres beneficios | Proyectos que compilan para tu portafolio · ves por qué falla tu código · un copiloto que guía sin hacerte la tarea |
| 4 | Demo de ~30 s | Video armado con capturas reales (la página lo dice); **PENDIENTE** cambiarlo por una grabación |
| 5 | Evidencia | Cita y cifras de las 4 entrevistas de la semana 4 |
| 6 | Precio visible | $249 MXN, pago único, PC con Windows; Pro como opcional |
| 7 | Un solo CTA | «Apartar mi copia» → nombre, correo y carrera → hoja de Google Forms |

**Metadatos:** `title`, `description` y Open Graph (imagen de 1200×630).

| Métrica | Valor |
|---|---|
| Visitas | **PENDIENTE** (desde la publicación) |
| Clics en «Apartar mi copia» | **PENDIENTE** |
| Pre-registros | **PENDIENTE** |

### 3.5 Tres publicaciones de lanzamiento (borradores, PENDIENTES de publicar)

1. **El problema:** «Tu código compila, pero la práctica no funciona y nadie te dice por qué.» Con la cita de uno de los estudiantes que entrevistamos, que quiere aprender «como un pasatiempo… no como una clase formal».
2. **El producto en uso:** video de 30 s grabado de verdad: descargar, abrir, escribir una línea en el nivel 1 y ver arrancar la banda. **PENDIENTE** grabarlo.
3. **Cómo se construye:** de los renders de la semana 6 al juego de 9 niveles; las 834 pruebas automáticas; y un error que encontramos: el juego usaba un modelo de IA 10 veces más caro que el de nuestro costeo.

## 4. Regulación mínima (Bloque 4)

No es asesoría legal. Las fuentes oficiales y el borrador del aviso de privacidad están en la [página del proyecto final, §16](../proyecto-final.md#s16).

| Tema | ¿Aplica? | Estatus |
|---|---|---|
| Radio y homologación (IFT) | No: no hay hardware | VERIFICADO |
| Datos personales ([LFPDPPP](https://www.diputados.gob.mx/LeyesBiblio/ref/lfpdppp.htm), DOF 20-03-2025) | Sí: la landing pide nombre, correo y carrera | POR VERIFICAR: el aviso actual es simplificado y le falta el correo de contacto |
| Clasificación de videojuegos ([lineamientos de SEGOB](https://dof.gob.mx/nota_detalle_popup.php?codigo=5606047)) | Probablemente | POR VERIFICAR si aplica a una tienda digital |
| Baterías | No | VERIFICADO |
| Licencias de software y de IA | Sí: Godot (MIT), fuentes (OFL), términos de Anthropic | POR VERIFICAR |
| Venta en Steam con IA | Sí: declarar el contenido generado por IA en vivo | POR VERIFICAR |
| Términos de venta ([LFPC, art. 32](https://www.diputados.gob.mx/LeyesBiblio/pdf/LFPC.pdf): publicidad veraz y comprobable) | Sí | En curso: la landing ya no promete lo que el juego no hace |
| Economía circular | No aplica a un producto digital | VERIFICADO |
| Impuestos (SAT) | Sí: Steam vende fuera de México | POR VERIFICAR con un contador |
| Marca (IMPI, marca mixta en clases 9 y 41) | Sí | POR VERIFICAR |

**En una prueba de 5–10 personas sí podemos:** dar el `.exe` gratis, sin cobrar; usar el aviso simplificado y pedir permiso para grabar la pantalla; que jueguen sin IA o con nuestra clave en nuestra laptop.

**Antes de vender a alguien de fuera hay que resolver:** aviso de privacidad integral con correo y domicilio; un backend con nuestra clave, límite de gasto y cuota (no pedirle su clave al jugador); la declaración de IA en Steam; términos de venta y de cancelación de Pro; la solicitud de marca; y el alta en el SAT.

**Tema que puede impedirnos vender:** la IA. Sin backend, venderlo obligaría a pedirle al jugador su propia clave de Anthropic o a esconder la nuestra en el `.exe`; y Steam pide declarar el contenido generado por IA.

## 5. Pitch de 3 minutos (Bloque 5)

| Parte | Tiempo | Palabras (tope) | Texto | De dónde sale |
|---|---|---|---|---|
| Impacto y problema | 0:00–0:35 | 65 (76) | Son las dos de la mañana y Diego, estudiante de mecatrónica, lleva tres horas con una práctica que no funciona. Su código compila, pero la banda no arranca y nadie le dice por qué. No es el único: entre 28 y 50% de los alumnos reprueba o abandona su primer curso de programación. Muchos, además, ya pagaron un kit de hasta 8,000 pesos para practicar. | Diego es una persona ilustrativa; cifras en el [proyecto final, §2](../proyecto-final.md#s2) |
| Solución y por qué nosotros | 0:35–1:10 | 68 (76) | GPIO Génesis es un juego para PC: arrancas una fábrica de drones escribiendo C/C++ de verdad para una Raspberry Pi Pico 2 virtual. Un tutorial de YouTube te da código para copiar; aquí, si tu código falla, la banda se detiene y el copiloto te pregunta qué ves. Somos dos estudiantes de mecatrónica, y uno de nosotros lleva Sistemas Embebidos: el juego enseña lo mismo que esa clase. | [Semana 8](semana-08.md), sección 2 |
| Mercado y modelo | 1:10–1:45 | 66 (76) | Hay 219 mil estudiantes de ingeniería en México que juegan simuladores. Queremos llegar a 1,100 cada año. Cobramos 249 pesos, pago único, como un juego independiente, no como un curso; el copiloto Pro es opcional. Después de la tienda y de la inteligencia artificial, cada copia nos deja 173 pesos. Con 49 copias recuperamos el dinero que sale de nuestro bolsillo. Con 322, también nuestras horas. | SAM y SOM: [proyecto final, §4](../proyecto-final.md#s4); números: sección 3.3 |
| Tracción | 1:45–2:20 | 72 (76) | Hoy funcionan los nueve niveles del primer bloque del curso, con interrupciones y prioridades. Los cuidan 834 pruebas automáticas, y cada plantilla pasa la revisión del compilador con las cabeceras reales de la Pico. De cuatro estudiantes entrevistados, tres pagarían entre 100 y 400 pesos por un juego así. Lo que todavía no tenemos, y es lo siguiente: pruebas con usuarios del juego. La página de preventa va a contar cada registro. | Semana 8 y semana 4. **La confesión honesta es que faltan las pruebas con usuarios** |
| Equipo | 2:20–2:35 | 25 (32) | Somos José Carlos y Yael, de Ingeniería Mecatrónica. Usamos inteligencia artificial para construir, pero cada nivel lo revisamos contra el SDK real de la Pico. | [Proyecto final, §1](../proyecto-final.md#s1) |
| Ask y cierre | 2:35–3:00 | 47 (54) | Buscamos veinte estudiantes para el piloto y un profesor que lo use con su grupo. En la demo van a ver la fábrica fallar y al copiloto negarse a darnos la solución. GPIO Génesis: si tu código falla, ves por qué; si funciona, va a tu portafolio. | Sección 3 |

**Total: 343 palabras** (rango del curso: 330–360). Cumple: abre con una persona y un costo, contrasta con YouTube, una cifra por frase con origen, una sola confesión, anuncia la demo sin hacerla y cierra con la promesa. Sin «innovador», «plataforma», «solución integral» ni «revolucionario».

**Video del pitch:** **PENDIENTE** (grabarlo, verlo y reescribir lo que no funcione). Las respuestas a preguntas de inversionista (costo por copia, competencia, costo de la IA, punto de equilibrio, requisitos legales, retención) están en la [página del proyecto final, §20](../proyecto-final.md#s20).

## 6. Guion de la demo de 7 minutos (Bloque 6)

| Tiempo | Quién opera | Qué se hace y se dice | Plan B |
|---|---|---|---|
| 0:00–0:45 | Narrador | Qué van a ver: un nivel normal, el copiloto, una falla controlada y el pre-registro | Diapositiva con la captura de cada tramo |
| 0:45–3:15 | Operador | El `.exe` ya abierto. Nivel 1, turno 1: escribir la línea que falta → **Compilar y ejecutar** → la banda arranca. Exportar el turno y abrir el `main.c` | Video de respaldo; partida guardada en ese punto |
| 3:15–4:45 | Operador | Demo del copiloto: el programa repinta el mismo chasis (el evento en vivo). Preguntas ▾ → «Dame la solución»: se niega y da la pista 2. Pregunta libre → la contesta Claude sin escribir código | Sin red: lo contesta el copiloto local; captura de una respuesta de Claude |
| 4:45–5:45 | Operador | Fallo manejado: apagar el Wi-Fi en vivo → «IA sin conexión» y la pista local sigue; volver a prenderlo | Clave inválida a propósito: mensaje claro |
| 5:45–6:30 | Narrador | QR → landing en el teléfono del jurado → $249 → «Apartar mi copia» → la fila nueva en la hoja | Landing abierta en la laptop; captura de la hoja |
| 6:30–7:00 | Narrador | Qué funciona, qué falta (usuarios, backend, firma) y el siguiente paso (piloto de 20). Atajos declarados: el video de la landing está armado con capturas | — |

**Roles (propuesta):** narrador, Yael; operador, José Carlos. **15 minutos antes:** laptop cargada · `.exe` congelado · progreso limpio (Versiones → Restaurar original) · hotspot del teléfono probado · clave guardada y «Probar conexión» en verde · landing abierta y QR impreso · videos de respaldo en el teléfono y la laptop. **Prohibido:** depender del Wi-Fi del salón o cambiar el `.exe` ese día.

**Ensayos con cronómetro (3) y video de respaldo:** **PENDIENTES.** Claude ya respondió con una clave real (25-nov); antes de la demo hay que medir cuánto tarda, porque el tramo 3:15–4:45 depende de eso.

## 7. Preguntas de cierre

1. **¿En qué frontera es más probable que falle el sistema y qué registro pondrán ahí?** F2/F3 (juego → Claude → respuesta): con una clave real solo probamos que responde; no hemos medido tiempos ni qué pasa con red lenta. Registro: código HTTP, tokens y respuesta cruda de cada petición.
2. **¿Cuáles son su CAC, LTV y LTV ÷ inversión, y qué palanca mueve más el resultado?** CAC $44.40, LTV ≈ $185.11, 4.2× (todo SUPUESTO). Lo que más mueve es el CAC; la mejor palanca, demos en clase y referidos (8.3×).
3. **¿Qué tema regulatorio puede impedirles vender y qué fuente lo confirma?** Los datos personales de la landing ([LFPDPPP](https://www.diputados.gob.mx/LeyesBiblio/ref/lfpdppp.htm)) y vender un juego con IA sin backend; además Steam pide declarar el contenido generado por IA (POR VERIFICAR).
4. **¿Qué evidencia respalda cada parte del pitch y qué está pendiente?** Ver la columna "De dónde sale" de la sección 5. Pendiente: pruebas con usuarios y pre-registros.
5. **¿Cuál es el camino principal de la demo, qué evento provocan en vivo y cuál es su plan B?** Nivel 1 → compilar → la banda arranca → exportar. Evento: el chasis que se repinta y el copiloto que se niega. Plan B: video de respaldo y copiloto local si no hay red.

## Reflexión

Esta semana juntamos lo que habíamos hecho por separado: el juego, el copiloto, la landing y los números. Al ponerlo todo en una sola lista de pruebas y en una definición de "listo", vi que el juego funciona en nuestras computadoras pero todavía no está listo para alguien de fuera: falta el Release con el `.exe`, publicar la landing con su formulario y, sobre todo, las pruebas con usuarios.

Lo que más me hizo pensar fue la economía unitaria. Con los supuestos sale bien, 4.2 veces la inversión y payback inmediato porque es un pago único, pero al revisar de dónde sale cada número me di cuenta de que casi todo es supuesto: el CAC, la conversión a Pro y hasta el costo de la IA, que todavía no medimos. En el escenario pesimista la razón baja a 1.6 y ya no cumple la regla. Un número bonito en un Excel no es tracción; la tracción la van a dar los pre-registros.

También aprendí que contar el proyecto es otro trabajo. El pitch tiene topes de palabras y una regla que me gustó: una sola confesión honesta. La nuestra es la que más pesa: todavía no probamos el juego con usuarios. Prefiero decirlo así que presentar resultados que no tenemos, y cerrar esa brecha es lo primero de las próximas dos semanas.
