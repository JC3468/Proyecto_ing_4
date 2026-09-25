# Semana 5 — Diseño de producto: PDS y arquitectura del sistema

**Equipo:** José Carlos y Yael  
**Concepto:** GPIO Génesis — videojuego de simulación de una fábrica de drones donde se programa en C++ real, con un copiloto de IA que explica los errores en términos de lo que pasa en la fábrica  
**Tipo de producto:** 100% digital (sin hardware)  
**Fecha:** 24 de septiembre de 2026

!!! abstract "De qué trata esta semana"
    Hasta la semana 4 decidimos **qué** construir y **para quién**. Esta semana decidimos **cómo**: dónde corre la IA, qué hace cada componente, cómo se comunican y qué requisitos verificables tiene que cumplir el producto. No se trata de implementar nada todavía, sino de **justificar cada decisión** antes de escribir código o comprar componentes.

    Entregables de esta página: decisión de arquitectura de IA (sección 1), arquitectura y diagrama de bloques con el Prompt 1 (sección 2), guion de la defensa (sección 3), PDS revisado con el Prompt 2 (sección 4), BOM preliminar con los servicios críticos (sección 5), viabilidad de la primera versión y lo que dejamos fuera (sección 6) y el checklist de salida (sección 7).

## Learning outcomes

Al terminar esta semana podemos:

- Traducir la propuesta de valor en un **PDS** con requerimientos funcionales, de desempeño, de interfaz y de restricción, cada uno con su **criterio de verificación**.
- Decidir **dónde corre el modelo de IA** (edge, cloud o híbrido) y defender la decisión con requerimientos del producto, no con gustos.
- Diseñar una arquitectura por capas donde **cada flecha tiene un protocolo** y cada caja una responsabilidad clara.
- Estimar el **costo por unidad** (y, en nuestro caso, el costo de IA por jugador) y ver si el precio que el segmento paga lo aguanta.
- Distinguir un **prototipo de exploración** de un **first-iteration product**.

---

## 0. Punto de partida: un producto 100% digital

El curso plantea tres componentes: artefacto físico inteligente, aplicación y página de venta. **Decidimos que GPIO Génesis sea un producto enteramente digital** y descartamos por completo la estación de pruebas física que habíamos planteado en las semanas 2 y 3. Las razones:

1. **Tiempo y riesgo.** Somos dos personas con 8 semanas efectivas. En la [semana 2](reporte-oportunidad-semana2.md) la validación de deseabilidad ya marcaba **alto riesgo técnico** si intentábamos construir al mismo nivel el juego con compilador, una estación física, la app de diagnóstico y la web.
2. **Otro cliente, otro precio.** Al costear la estación salía en ≈ $965 MXN de materiales por unidad, así que su precio mínimo rondaría los $3,200 MXN. Eso no lo paga el estudiante individual, que está dispuesto a pagar 100–400 MXN por el juego (semana 4). Sería un producto B2B para laboratorios universitarios, un segmento que no hemos entrevistado.
3. **Lo que el segmento validó es el juego.** Lo que salió en las entrevistas fue aprender C++ real sin que se sienta como clase y con relevancia laboral. El hardware nunca apareció como necesidad.
4. **Propiedad intelectual.** La patente china CN121583173B (semana 3) protege diagnosticar el código comparando la ejecución virtual con la física. Sin estación, nuestro copiloto compara lo esperado con lo simulado y se aleja de ese reclamo.


| Componente del curso | Qué es en GPIO Génesis |
|---|---|
| Artefacto físico inteligente | **No hay artefacto físico.** El "microcontrolador" es virtual y la fábrica es simulada. Aun así, el código del jugador usa la misma API que Arduino-ESP32, así que puede llevarlo a una placa real propia si quiere: es C++ transferible, no un lenguaje de juguete |
| Aplicación | **Juego para PC** con editor, compilador, simulador de la fábrica y **copiloto IA**. Es el corazón del producto |
| Página de venta | Sitio estático con video, descarga de la demo y lista de espera |

**Prototipo de exploración vs. first-iteration product en nuestro caso:**

| | Prototipo de exploración | First-iteration product (semana 13) |
|---|---|---|
| Qué es | Un nivel de prueba que corre en nuestras computadoras con el editor de Godot abierto | Un instalador de Windows que se descarga desde la página de venta |
| Quién lo usa | Nosotros | Un estudiante de ingeniería que no nos conoce |
| Qué contiene | Lo que sea necesario para aprender si el compilador y el simulador funcionan | Tutorial + 5 niveles, compilador incluido, copiloto IA, guardado y cuenta |
| Criterio de éxito | Aprendimos algo técnico | **4 de 5 usuarios completan el tutorial y el nivel 1 sin que les expliquemos nada** (RI-01 del PDS) |

---

## 1. Decisión de arquitectura de IA

**Decisión: HÍBRIDO, con la inteligencia principal en la nube (cloud-first).**

- **En la nube** corre el copiloto: un modelo de lenguaje (Claude Haiku 4.5 vía API) que lee el código del jugador, los errores del compilador y la traza de la simulación, y explica en español qué está pasando en la fábrica y por qué.
- **En la PC del jugador (el "borde")** corre todo lo que necesita responder en menos de un segundo o funcionar sin internet: el compilador, el microcontrolador virtual, el simulador, el validador del nivel y un **diagnóstico local por reglas** (~25 patrones de error frecuentes) que da una primera pista en ≤200 ms.

**Justificación técnica (3 puntos):**

1. **La tarea exige un modelo grande.** Explicar por qué un programa C++ arbitrario no produce el comportamiento físico esperado, en español y sin dar la solución, necesita un LLM. No cabe en un ESP32 (~100 KB de parámetros con TensorFlow Lite Micro) y un modelo local de varios GB exigiría una GPU que la laptop típica del segmento no tiene garantizada. En la nube cuesta unos **US$0.005 por consulta** (ver BOM).
2. **La latencia que necesita el usuario lo permite.** Lo único que tiene que ser inmediato es compilar, simular y marcar el error, y eso corre local. La explicación del copiloto es ayuda para depurar, no un lazo de control: esperar 2–5 s es aceptable, y con *streaming* el texto empieza a aparecer en 1–2 s.
3. **La conectividad es buena pero no perfecta, y los datos no son sensibles.** El segmento estudia con internet (en el SAM filtramos 90% urbano con smartphone), pero la red del campus falla. Por eso la capa local garantiza que el juego **nunca se vuelve un ladrillo** sin señal. Lo que sale a la nube es código de ejercicios y la traza de la simulación; el nombre y el correo del jugador nunca llegan al modelo.

**Las tres preguntas guía:**

| Pregunta | Respuesta para GPIO Génesis | Qué implica |
|---|---|---|
| ¿El usuario tiene conectividad estable en el punto de uso? | Casi siempre (casa, universidad), con caídas en el campus y en el transporte | Cloud para el copiloto + capa local para no depender de la red |
| ¿La decisión necesita <1 s o acepta 2–5 s? | Compilar y simular: <1 s (local). Explicar el error: acepta 2–5 s | Separar las dos cosas es justo lo que hace el híbrido |
| ¿Los datos pueden salir del dispositivo? | Sí: es código de ejercicios del juego. Los datos personales no salen hacia el LLM | Aviso de privacidad y payload sin identificadores (RR-05) |

**Alternativas descartadas:**

- **Todo edge** (un LLM local pequeño): sin costo por consulta, pero con explicaciones pobres en español técnico, un instalador de varios GB y requisitos de hardware que dejan fuera a parte del segmento.
- **Todo cloud** (compilar y simular en servidores): el juego dejaría de funcionar sin internet, cada ejecución costaría dinero y correr código arbitrario de miles de jugadores en nuestros servidores es un problema de seguridad.

---

## 2. Arquitectura del sistema (Prompt 1)

### 2.1 Cómo adaptamos el Prompt 1

El Prompt 1 del profesor está pensado para un artefacto con sensores en campo. Como nuestra first-iteration es software, lo adaptamos sin quitarle la estructura:

| Parte del prompt | Prompt del profesor | Nuestro cambio | Por qué |
|---|---|---|---|
| Tres componentes | Artefacto físico + app + página de venta | Juego para PC + copiloto IA + página de venta; producto 100% digital | Es lo que realmente vamos a entregar en la semana 13 |
| Capacidades técnicas | Hardware, software, IA, presupuesto | Agregamos "Godot 4 (lo estamos aprendiendo)" | Que la IA no suponga que ya dominamos un motor de videojuegos |
| Paso 2 — Capa física | Microcontrolador, sensores, alimentación | "Capa cliente": motor del juego, compilador, MCU virtual y qué corre local | En un producto digital el único "hardware" es la PC del jugador |
| Paso 4 — Viabilidad | BOM de 5–10 unidades y componentes críticos | Costo de IA por jugador y **servicios críticos** (disponibilidad desde México, costo, plan B) | Sin hardware, el "costo por unidad" es lo que cuesta cada jugador en la nube y los "componentes" son servicios de terceros |
| Pregunta extra | — | "¿Qué pasa si la API sube 10×?" | Es una de las preguntas de la defensa; mejor llegar con el número |

??? note "Prompt 1 que usamos (Claude)"
    ```text
    Actúa como arquitecto de sistemas embebidos y de software con experiencia en productos
    mecatrónicos con IA para mercados latinoamericanos. Especialidad: diseñar arquitecturas
    que equilibren capacidad técnica, restricciones de manufactura y viabilidad económica para
    equipos universitarios con presupuesto limitado. No propones la más sofisticada, sino la
    más adecuada para el equipo y los requerimientos. Señalas las decisiones sin justificación.

    Somos un equipo de ingeniería en México (2 personas). El curso pide tres componentes:
    artefacto físico inteligente, aplicación y página de venta. Por tiempo, nuestra primera
    versión (8 semanas) es un producto 100% DIGITAL: un juego para PC con copiloto de IA +
    página de venta. Descartamos el artefacto físico; no habrá hardware en el producto.

    Capacidades técnicas:
    - Hardware: ESP32, Raspberry Pi, PCB 2 capas, impresión 3D, soldadura SMD
    - Software: Python, C/C++, JavaScript/React, React Native. Godot 4 (lo estamos aprendiendo)
    - IA: TensorFlow Lite, PyTorch, APIs de modelos (OpenAI, Anthropic, Google)
    - Presupuesto de materiales: máximo $3,000 MXN para el prototipo

    Nuestro producto:
    Nombre: GPIO Génesis
    Descripción: videojuego de simulación de una fábrica de drones para estudiantes de
    ingeniería de 18–25 años. El jugador escribe C++ real para automatizar la línea de
    ensamblaje (pines, PWM, sensores) y un copiloto de IA le explica por qué su código no
    produce el comportamiento físico esperado. Ataca el dolor de aprender C++ en clases
    aburridas y sin diagnóstico cuando el código falla sobre hardware.
    Propuesta de valor: "El único juego que convierte tu código C++ en portafolio real de
    automatización, para estudiantes de ingeniería que aprenden sin que se sienta como tarea."

    Requerimientos técnicos clave (PDS preliminar):
    1. Compilar el C++ del jugador y ejecutarlo en un microcontrolador virtual, sin que el
       jugador instale nada extra.
    2. Compilar y arrancar la simulación en menos de 3 s en una laptop sin GPU dedicada.
    3. El copiloto explica el error en español, en términos físicos, sin dar la solución.
    4. Sin internet el juego se puede jugar y da pistas básicas.
    5. El costo de IA por jugador no puede comerse el precio de venta (199–299 MXN, pago único).

    Nuestra decisión de arquitectura IA: Híbrido, principalmente cloud.
    Justificación:
    1. Explicar código arbitrario en lenguaje natural requiere un LLM que no cabe en el
       dispositivo del usuario.
    2. La explicación acepta 2–5 s; lo que necesita <1 s (compilar, simular) corre local.
    3. La conectividad del segmento es buena pero no perfecta; los datos no son sensibles.

    Con esta información entrega:
    PASO 1 — VALIDACIÓN DE LA DECISIÓN IA
    PASO 2 — ARQUITECTURA: capa cliente (PC del jugador, en lugar de capa física), capa de
    datos (backend/cloud) y capa de presentación (vistas del juego y página de venta).
    PASO 3 — FLUJO DE DATOS del caso de uso principal (el jugador falla un nivel y pide
    ayuda), con componente responsable, protocolo, latencia estimada y qué pasa si falla.
    PASO 4 — VIABILIDAD: costo de IA por jugador y qué pasa si la API sube 10×. En lugar de
    componentes de hardware, evalúa los 3 servicios de terceros más críticos (disponibilidad
    desde México, costo, riesgo y plan B) y el proceso de "manufactura" y distribución.

    Usa el formato de salida del curso (ARQUITECTURA DEL SISTEMA ... VIABILIDAD).
    ```

??? note "Salida del Prompt 1 (resumida al formato del curso)"
    ```text
    ════════════════════════════════════════════════════════
    ARQUITECTURA DEL SISTEMA
    Producto: GPIO Génesis · Decisión IA: Híbrido (cloud-first)
    ════════════════════════════════════════════════════════

    VALIDACIÓN DECISIÓN IA:
    Confirmada ✅ (con una condición)
    La explicación en lenguaje natural de código arbitrario sí justifica un LLM en la nube,
    y separar lo inmediato (compilar/simular, local) de lo que tolera segundos (explicar) es
    correcto. Condición: el requerimiento 5 no está garantizado. Si cada jugador hace cientos
    de consultas, la IA se come una parte relevante del precio; hace falta una cuota y que la
    capa local resuelva los errores frecuentes. Sin eso la decisión es frágil ante un alza de
    precio de la API.

    ────────────────────────────────────────────────────────
    CAPA CLIENTE — PC del jugador (en un producto digital sustituye a la capa física)

    Plataforma: Windows 10/11 64 bits, laptop sin GPU dedicada, 8 GB RAM
    Motor: Godot 4 — licencia MIT, sin regalías, integra C++ vía GDExtension, y su nodo
      CodeEdit ya trae resaltado de sintaxis. Unity no aporta nada extra para un juego 2D/2.5D
      y Unreal es demasiado pesado para una laptop del segmento y para 8 semanas.
    Compilador: clang de wasi-sdk (Apache 2.0 + excepción LLVM) empaquetado en el instalador;
      compila el C++ del jugador a WebAssembly. No usar GCC enlazado (GPL, ver semana 3).
    MCU virtual: runtime WAMR (Apache 2.0) que ejecuta el .wasm en sandbox. El programa del
      jugador solo ve un HAL «gpio.h» con la misma API que Arduino-ESP32 (pinMode,
      digitalWrite, analogWrite, analogRead, delay, millis, Serial). Así el código que
      escribe el jugador también compila en un ESP32 real: es C++ transferible a un empleo.
    Simulador: física simplificada (motor DC de primer orden, banda, brazo, sensores) con tick
      lógico de 1 kHz y render a 60 fps.
    Parte "edge" de la IA: diagnóstico por reglas (errores de clang + patrones de la traza:
      pin sin configurar, PWM fuera de rango, delay que bloquea, loop sin salida) ≤200 ms.
      No es un modelo entrenado en la v1; los eventos guardados servirán para entrenar un
      clasificador después.

    ────────────────────────────────────────────────────────
    CAPA DATOS — Backend/Cloud

    Servicio de datos: Supabase (Auth + Postgres + Edge Functions)
    Por qué este: el plan Free cubre el piloto (500 MB, 50,000 usuarios activos al mes,
      500,000 invocaciones), da login con GitHub (útil para el portafolio) y evita montar
      servidores. Ojo: el proyecto Free se pausa tras 1 semana sin actividad.

    Modelo IA:
    · Qué hace: explica por qué el código no logra el objetivo del nivel, en términos físicos
    · Dónde corre: Cloud API, llamado SOLO desde una Edge Function (la API key nunca va en
      el juego)
    · Input: código (~800 tokens), errores de clang, resumen de la traza (esperado vs.
      obtenido), objetivo del nivel, pistas ya dadas
    · Output: JSON {lineas, causa_fisica, pista, concepto_cpp}; nunca la solución completa
    · Modelo específico: Claude Haiku 4.5 (US$1 / US$5 por millón de tokens de entrada /
      salida) por costo y latencia; proveedor intercambiable detrás de la Edge Function

    Protocolo cliente → backend: HTTPS REST (JSON) + SSE para la respuesta en streaming
      Latencia estimada: 150–300 ms de red + 0.5–1.5 s al primer token

    ────────────────────────────────────────────────────────
    CAPA PRESENTACIÓN

    Tipo: juego nativo de escritorio (Godot) — Por qué: necesita compilador y simulación
      locales; una PWA no puede empaquetar clang de forma práctica.

    Vistas principales:
    1. Fábrica — la línea de drones animada, estado de cada pin y "osciloscopio" simple
    2. Editor + consola serial — escribir, compilar, ejecutar, ver Serial.print
    3. Copiloto — explicación, línea marcada en el editor y botón "otra pista"

    Actualización de datos: no hay polling; el cliente sincroniza el progreso al terminar
      cada nivel y el copiloto responde por SSE
    Notificaciones: no aplican en la v1 (no hay eventos que ocurran sin el jugador presente)
    Página de venta: sitio estático en GitHub Pages; lista de espera → Supabase (HTTPS REST);
      descarga de la demo en itch.io; Steam cuando haya tracción (US$100 por juego)

    ────────────────────────────────────────────────────────
    FLUJO DATOS — Caso de uso principal: "falla el nivel y pide ayuda"

    [Paso 1] Editor → compilador local: código .cpp, proceso local — ~1–3 s
    [Paso 2] Compilador → MCU virtual: módulo .wasm en memoria — ~50 ms
    [Paso 3] MCU virtual ↔ simulador: llamadas HAL / estado de pines — tick 1 ms, render 16 ms
    [Paso 4] Simulador → validador: traza de pines y eventos — ~20–50 ms
    [Paso 5] Validador → diagnóstico local → panel: desviación → pista local — ≤200 ms
    [Paso 6] Panel → Edge Function: HTTPS POST, JSON ~5 KB — ~150–300 ms (+JWT, cuota, caché)
    [Paso 7] Edge Function → Claude API: HTTPS Messages API — primer token ~0.5–1.5 s
    [Paso 8] Claude → Edge Function → panel: SSE — respuesta completa ~3–6 s

    Latencia total: pista local <0.5 s después de la corrida; explicación del copiloto:
      primer texto ~1–2 s, completa ~4–7 s después de pedirla (estimado, hay que medirlo)
    Funcionamiento sin conexión: SÍ — compilar, simular, validar, pista local y guardado.
      Solo se pierde la explicación del copiloto.
    Si algo falla: timeout a los 10 s → pista local + "reintentar"; cuota agotada → pista
      local; Supabase caído o pausado → se guarda local y se sincroniza después; bucle
      infinito del jugador → el runtime lo corta (como el watchdog de un ESP32) y lo reporta.

    ────────────────────────────────────────────────────────
    VIABILIDAD — Software (v1)

    Costo por consulta: 3,000 tokens de entrada + 400 de salida ≈ US$0.005 (≈ $0.09 MXN)
    Con 300 consultas por jugador: US$1.50 ≈ $26 MXN = 15% del ingreso neto de una copia
      de $249 (después del 30% de la tienda) ⚠️
    Si la API sube 10×: ≈ 150% del ingreso neto → cada venta pierde dinero ❌
    Mitigación: cuota de consultas en la nube (≤200 por jugador mantiene la IA ≤10%),
      caché de explicaciones repetidas, capa local para los errores frecuentes y proveedor
      intercambiable. Con eso un alza de precio degrada el copiloto, no el juego.

    VIABILIDAD — Servicios críticos (los "componentes" de un producto digital)

    Volumen objetivo de prueba de mercado: 20 jugadores en el piloto (semana 13)

    Servicio crítico 1: API del LLM (Claude Haiku 4.5)
    · Disponible desde México: ✅ — se paga con tarjeta, sin restricción de país
    · Costo: US$1 / US$5 por millón de tokens → ≈ US$30 para el piloto
    · Riesgo: alza de precio o caída del servicio
    · Plan B: cuota + caché + reglas locales; proveedor intercambiable en la Edge Function

    Servicio crítico 2: backend (Supabase)
    · Disponible desde México: ✅
    · Costo: Free en el piloto; Pro US$25/mes al lanzar
    · Riesgo: el plan Free pausa el proyecto tras 1 semana sin actividad
    · Plan B: guardado local primero y sincronización después; pasar a Pro al lanzar

    Servicio crítico 3: firma de código de Windows
    · Disponible desde México: ⚠️ — Azure Artifact Signing (US$9.99/mes) sería lo más
      barato, pero hay que verificar si acepta cuentas de México
    · Costo alternativo: certificado OV de una autoridad certificadora (~US$99/año)
    · Riesgo: sin firma, Windows SmartScreen muestra una advertencia al instalar y el
      jugador puede abandonar (choca con "usarlo sin instrucciones del equipo")
    · Plan B: en el piloto, guía visual en la página de descarga; al lanzar, distribuir
      por Steam

    Proceso de "manufactura": exportar el build de Godot + empaquetar clang/wasi-sdk en el
      instalador. Minutos por versión y costo cero; lo que hay que cuidar es el tamaño
      (≤500 MB) y probarlo en una Windows limpia.
    Distribución: itch.io para el piloto (sin cuota) → Steam al lanzar (US$100, recuperable).

    Costo por unidad (una copia vendida): IA ≈ $26 MXN (300 consultas) + 30% de la tienda.
    Precio $249 → ingreso neto $174.30 → margen después de IA ≈ $148 MXN por copia.
    ¿Segmento pagaría ese precio? ✅ — 3 de 4 entrevistados en 100–400 MXN (semana 4).
    ════════════════════════════════════════════════════════
    ```

### 2.2 Auditoría de lo que dijo la IA

| Lo que dijo la IA | ¿Lo comprobamos? | Resultado |
|---|---|---|
| Precio de Claude Haiku 4.5: US$1 / US$5 por millón de tokens | Sí, en la página oficial de precios | Correcto al 24-sep-2026 |
| Supabase Free: 500 MB, 50,000 MAU, 500,000 invocaciones, pausa tras 1 semana | Sí, en supabase.com/pricing | Correcto. La pausa es un riesgo real para el piloto |
| Steam cobra US$100 por juego | Sí, en la documentación de Steamworks | Correcto, y es recuperable después de US$1,000 en ventas |
| Firma de código: "verificar si Azure Artifact Signing acepta México" | Sí, en la documentación de Microsoft | **No lo acepta**: para individuos solo EE. UU. y Canadá, y para organizaciones una lista de países sin México. Es nuestro "componente difícil de conseguir" |
| Latencias (1–3 s compilar, 1–2 s primer texto) | **No todavía** | Son estimaciones. Hay que medirlas con el primer prototipo (semanas 6–7) |
| "300 consultas por jugador" | **No** | Es un supuesto nuestro (15 por hora × 20 horas). Es la cifra que más mueve el costo y la vamos a medir en el piloto |

### 2.3 Diagrama de bloques

[![Arquitectura del sistema GPIO Génesis](../recursos/imgs/semana5/arquitectura-gpio-genesis.svg)](../recursos/imgs/semana5/arquitectura-gpio-genesis.svg)

<small>Clic en la imagen para verla en tamaño completo.</small>

[📐 Diagrama editable en draw.io](../recursos/archivos/semana5/arquitectura-gpio-genesis.drawio) (se abre en [diagrams.net](https://app.diagrams.net) con *Archivo → Abrir desde → Dispositivo*).

Cómo leerlo: cada caja tiene una sola responsabilidad, cada flecha lleva su protocolo, la **capa de IA** está marcada en amarillo (borde) y morado (nube), las dependencias externas tienen borde punteado y los números (1)–(10) siguen el caso de uso principal.

### 2.4 Protocolos: por qué este y no otro

| Conexión | Protocolo | Por qué este | Por qué no la alternativa |
|---|---|---|---|
| Juego → copiloto (Edge Function) | **HTTPS REST** (POST JSON) | El jugador inicia la consulta y espera una respuesta; son pocas por hora (~5–15) | **MQTT** sirve para muchos dispositivos que publican telemetría pequeña y frecuente; aquí no hay flota ni broker que justificar |
| Copiloto → juego | **SSE** (Server-Sent Events) sobre HTTPS | El texto aparece mientras el modelo lo genera, así se siente rápido | **WebSocket** es bidireccional y mantiene la conexión abierta; no necesitamos que el servidor le hable al juego fuera de una consulta |
| Edge Function → Claude API | **HTTPS** (Messages API con streaming) | Es la interfaz del proveedor; queda en el servidor junto con la API key | Llamar desde el juego expondría la key en el ejecutable |
| Juego ↔ Supabase | **HTTPS REST** (PostgREST + JWT), login OAuth | Sincroniza al terminar cada nivel, no en cada tick | Sincronizar en tiempo real no aporta nada al jugador y gasta cuota |
| Página de venta → Supabase | **HTTPS REST** | Un formulario de lista de espera | — |
| Exportador → GitHub | **HTTPS REST** (GitHub API, OAuth) | Crea el repo de portafolio del jugador | — |

---

## 3. Defensa de la arquitectura (5 minutos)

**La decisión que defendemos:** dónde corre la IA.

**Minuto 1–2 · Decisión**

> "Elegimos una arquitectura **híbrida con la IA principal en la nube** porque nuestro requerimiento central (RF-04) es explicar en español por qué un código C++ cualquiera no mueve la fábrica como debería, y eso necesita un modelo de lenguaje grande. La alternativa era un modelo local en la PC del jugador, pero la descartamos porque exige varios GB y una GPU que la laptop típica de un estudiante no tiene, y las explicaciones salen mucho peores. Lo que sí tiene que ser inmediato (compilar, simular y marcar el error) corre local."

**Minuto 3–4 · Consecuencia**

> "Esta decisión implica que en el cliente empaquetamos el compilador y el microcontrolador virtual (el instalador pesa más, pero el jugador no instala nada), que la comunicación con la nube es **HTTPS + SSE** a través de una Edge Function que guarda la API key y aplica cuotas, y que la app tiene un panel de copiloto con respuesta en streaming y un modo sin conexión. **El riesgo principal es el costo y la dependencia de la API**: con 300 consultas por jugador la IA ya se lleva el 15% del ingreso neto, y si el precio sube 10× cada venta pierde dinero. Lo mitigamos con una cuota de consultas en la nube, una caché de explicaciones repetidas, reglas locales para los errores frecuentes y un proveedor intercambiable detrás de la Edge Function. Si la API falla o sube de precio, el copiloto se degrada, pero el juego sigue funcionando."

**Respuestas preparadas para las preguntas del profesor:**

| Pregunta | Nuestra respuesta |
|---|---|
| ¿Cuántos KB ocupa el modelo en el ESP32? ¿Cuánta RAM queda? | No hay ESP32 físico: el producto es 100% digital. El microcontrolador es virtual y corre en la PC del jugador, y el modelo de IA está en la nube, así que no hay límite de RAM de un microcontrolador. Lo que sí medimos es la RAM de la PC mínima (8 GB). |
| Si la API sube 10× el próximo año, ¿qué falla? | El margen: la IA pasaría de ~15% a ~150% del ingreso neto por copia. Con la cuota (≤200 consultas por jugador) y la caché baja a ≤10%, y con 10× la bajamos más o pasamos a un modelo más barato. El juego no se rompe: la pista local sigue. |
| ¿Qué pasa en una zona sin señal? | Se puede jugar completo: compilar, simular, validar, pistas locales y guardado. Solo se pierde la explicación del copiloto y el progreso se sincroniza después. No es un ladrillo. |
| ¿Por qué no MQTT en lugar de HTTP? ¿Cuántos mensajes por hora? | ~5–15 consultas por hora por jugador, iniciadas por él y esperando respuesta. Es request/response puro; MQTT agregaría un broker sin beneficio. |
| ¿Broker propio o de terceros? ¿Costo con 1,000 usuarios? | No usamos broker. Con 1,000 jugadores: Supabase Pro US$25/mes y la IA ≈ US$1,500 en toda la campaña (≈ $26,250 MXN) frente a ≈ $174,300 MXN de ingreso neto; con la cuota baja a ≈ US$1,000. |
| ¿Ese sensor está en stock? ¿Días de entrega? | No hay sensores. Nuestros "componentes" son servicios, y el más difícil de conseguir desde México es la **firma de código de Windows**: Azure Artifact Signing no acepta México. Plan B: certificado OV (~US$99/año) o distribuir por Steam; en el piloto, una guía en la página de descarga. |
| ¿La PCB la fabrican en el laboratorio o la mandan hacer? | No hay PCB. Nuestra "manufactura" es exportar el build de Godot y armar el instalador: minutos por versión y costo cero. El cuello de botella es probarlo en una Windows limpia. |

---

## 4. PDS — Product Design Specification (Prompt 2)

### 4.1 PDS preliminar (primer borrador)

Antes de pasar el PDS por el Prompt 2, este era nuestro primer borrador. Lo dejamos aquí para que se vea qué corrigió la revisión:

- **Funcionales:** 1) El juego debe compilar C++. 2) El copiloto debe explicar los errores. 3) El juego debe guardar el progreso.
- **Desempeño:** 1) El juego debe correr fluido. 2) El copiloto debe responder rápido.
- **Interfaz:** 1) La interfaz debe ser fácil de usar. 2) Debe estar en español.
- **Restricción:** 1) Debe estar listo para la semana 13. 2) Debe ser barato de operar.

??? note "Prompt 2 que usamos (Claude)"
    ```text
    Actúa como ingeniero de producto senior con experiencia en redactar Product Design
    Specifications de hardware + software en etapa de prototipo avanzado. Especialidad:
    identificar requerimientos mal redactados, demasiado vagos para verificarse, demasiado
    restrictivos para alcanzarse, o que faltan y harán falta en el desarrollo. No eres
    condescendiente: señalas el problema y propones una corrección específica.

    Somos un equipo de ingeniería en México desarrollando:
    GPIO Génesis — videojuego para PC donde el jugador programa en C++ real una fábrica de
    drones; un copiloto de IA (híbrido: reglas locales + LLM en la nube) le explica los
    errores en términos físicos. Incluye página de venta. Es un producto 100% digital (no
    hay hardware).
    Usuario final: estudiantes de ingeniería de 18–25 años, jugadores de Minecraft y
    simuladores, aburridos de las clases tradicionales de programación, motivados por la
    relevancia laboral de C++, dispuestos a pagar 100–400 MXN.
    First-iteration product: instalador de Windows descargable desde la página de venta, con
    tutorial y 5 niveles, compilador incluido, copiloto IA, guardado y cuenta; un estudiante
    que no nos conoce lo usa sin instrucciones del equipo.

    Este es nuestro PDS preliminar:

    REQUERIMIENTOS FUNCIONALES:
    1. El juego debe compilar C++.
    2. El copiloto debe explicar los errores.
    3. El juego debe guardar el progreso.

    REQUERIMIENTOS DE DESEMPEÑO:
    1. El juego debe correr fluido.
    2. El copiloto debe responder rápido.

    REQUERIMIENTOS DE INTERFAZ:
    1. La interfaz debe ser fácil de usar.
    2. Debe estar en español.

    REQUERIMIENTOS DE RESTRICCIÓN:
    1. Debe estar listo para la semana 13.
    2. Debe ser barato de operar.

    Revisa el PDS completo y entrega: 1) diagnóstico por categoría, 2) correcciones con
    criterio de verificación explícito, 3) requerimientos faltantes (mínimo 2 por
    categoría). Usa el formato REVISIÓN DE PDS del curso.
    ```

??? note "Salida del Prompt 2 (diagnóstico)"
    ```text
    ════════════════════════════════════════════════════════
    REVISIÓN DE PDS
    Producto: GPIO Génesis
    ════════════════════════════════════════════════════════

    REQUERIMIENTOS FUNCIONALES
    ✅ Bien redactados: ninguno todavía; los tres apuntan a lo correcto pero no se pueden
       verificar.
    ⚠️ Con problema:
      1 "Compilar C++" — ¿qué C++? ¿dónde corre?
        → Corrección: compilar un subconjunto definido de C++17 y ejecutarlo en el MCU
          virtual sin instalar herramientas; verificar con 30 programas de prueba.
      2 "Explicar los errores" — no dice en qué términos ni qué NO debe hacer.
        → Corrección: explicar en español la causa física, marcar líneas y dar una pista
          sin la solución completa; verificar con 40 errores etiquetados.
      3 "Guardar el progreso" — ¿local, en la nube, sin conexión?
        → Corrección: guardado local + sincronización con la cuenta; prueba offline/online.
    ❌ Faltantes: la simulación debe reflejar cada pin en la fábrica; el validador
       automático de niveles (sin él no hay "juego"); el modo sin conexión; la exportación
       a portafolio (es su propuesta de valor); la página de venta.

    REQUERIMIENTOS DE DESEMPEÑO
    ✅ Bien redactados: ninguno.
    ⚠️ Con problema:
      1 "Correr fluido" — sin número ni hardware de referencia.
        → Corrección: ≥30 fps promedio en una PC mínima definida.
      2 "Responder rápido" — mezcla la pista local con la nube.
        → Corrección: pista local ≤200 ms; nube: primer texto ≤2 s y completa ≤8 s (p90).
    ❌ Faltantes: tiempo de compilación; calidad mínima del copiloto (% de aciertos);
       costo de IA por consulta y por jugador (es el riesgo número uno de la arquitectura);
       robustez ante código defectuoso del jugador (bucles infinitos, divisiones entre 0).

    REQUERIMIENTOS DE INTERFAZ
    ✅ Bien redactados: "en español" va bien encaminado; falta decir qué NO se traduce.
    ⚠️ Con problema:
      1 "Fácil de usar" — no es verificable.
        → Corrección: 4 de 5 usuarios del segmento completan tutorial + nivel 1 sin ayuda
          en ≤20 min. Es literalmente la definición de first-iteration del curso.
    ❌ Faltantes: interfaz del editor (resaltado, marca de línea); la API del HAL
       compatible con Arduino-ESP32 (es lo que hace transferible su código); la API del backend;
       la instalación sin dependencias.

    REQUERIMIENTOS DE RESTRICCIÓN
    ✅ Bien redactados: "semana 13" es verificable; agreguen el tamaño del equipo.
    ⚠️ Con problema:
      1 "Barato de operar" — ¿cuánto es barato?
        → Corrección: tope en pesos para el piloto y límite de gasto en la API.
    ❌ Faltantes: licencias (en semana 3 detectaron GPL en GCC y QEMU); plataforma
       objetivo; privacidad (el código sale a un tercero); la API key fuera del cliente.

    ────────────────────────────────────────────────────────
    PDS COMPLETO CORREGIDO: ver sección 4.2
    ════════════════════════════════════════════════════════
    ```

**Qué aceptamos y qué cambiamos de la revisión:** aceptamos casi todo. Cambiamos dos cosas: 1) el umbral de calidad del copiloto lo dejamos en **80%** y no en el 85% del ejemplo de clase, porque es una tarea más abierta que clasificar humedad y preferimos un número que podamos cumplir y después subir; 2) el tope de costo de IA lo expresamos como **% del ingreso neto por copia** y no como un monto fijo, porque si cambiamos el precio del juego el tope se ajusta solo.

### 4.2 PDS v1.0 (entregable)

!!! info "PRODUCT DESIGN SPECIFICATION"
    **Producto:** GPIO Génesis (juego para PC + copiloto IA + página de venta) · **Versión:** 1.0  
    **Equipo:** José Carlos y Yael · **Fecha:** 24-sep-2026  
    **PC mínima de referencia:** Core i3 / Ryzen 3, 8 GB RAM, gráficos integrados, Windows 10 64 bits

**REQUERIMIENTOS FUNCIONALES**

| ID | Requerimiento | Verificación |
|---|---|---|
| **RF-01** | El sistema debe compilar el C++ que escribe el jugador (subconjunto de C++17: tipos básicos, funciones, if/switch, ciclos, arreglos, struct/class simples, punteros) y ejecutarlo en el MCU virtual sin instalar herramientas adicionales. | 30 programas de prueba (15 válidos, 15 con error): los 15 válidos corren y los 15 con error muestran mensaje con número de línea. |
| **RF-02** | El sistema debe reflejar cada escritura/lectura de pin del MCU virtual (digital, PWM 0–255, analógico 0–4095) en el actuador o sensor correspondiente de la fábrica dentro del mismo ciclo de simulación. | Programa que conmuta un pin a 1 Hz y barre un PWM durante 60 s; la traza y la animación coinciden en todo el registro. |
| **RF-03** | El sistema debe validar cada nivel comparando la traza con las condiciones del objetivo e indicar cuál condición falló. | Por cada uno de los 5 niveles, 1 solución de referencia (pasa) y 3 soluciones con fallas conocidas (fallan señalando la condición): 20/20. |
| **RF-04** | Cuando el jugador pida ayuda, el copiloto debe explicar en español la causa del error en términos del comportamiento físico, marcar las líneas probables y dar una pista sin escribir la solución completa. | Set de 40 errores reales etiquetados, revisado con rúbrica (línea, causa física, no entrega solución); umbral en RD-04. |
| **RF-05** | Sin conexión a internet, el sistema debe dar una pista local para los errores de su catálogo (mínimo 25 patrones) y permitir jugar y guardar. | Con la red desactivada, 25/25 patrones generan pista; se completa un nivel, se cierra el juego y el progreso sigue al abrirlo. |
| **RF-06** | El sistema debe sincronizar progreso y soluciones con la cuenta del jugador cuando haya conexión, y exportar las soluciones de los niveles superados como proyecto (.cpp + README por nivel). | 2 niveles jugados offline aparecen en otra PC tras reconectar; el proyecto exportado compila con g++ usando el HAL de escritorio incluido. |
| **RF-07** | La página de venta debe permitir descargar la demo y registrarse en la lista de espera. | 10 registros de prueba aparecen en la base en <5 s; la descarga funciona en Chrome, Edge y Firefox. |

**REQUERIMIENTOS DE DESEMPEÑO**

| ID | Requerimiento | Criterio |
|---|---|---|
| **RD-01** | El sistema debe compilar y arrancar la simulación rápido. | P95 ≤ 3 s para programas de hasta 300 líneas en la PC mínima (50 corridas). |
| **RD-02** | El simulador debe mantenerse fluido y en tiempo real. | ≥ 30 fps promedio y ≥ 24 fps mínimo en el nivel más grande en la PC mínima; MCU virtual a 1,000 ticks/s con desfase < 1% en 5 min. |
| **RD-03** | El copiloto debe responder dentro de la paciencia del jugador. | Pista local ≤ 200 ms; nube: primer texto ≤ 2 s y respuesta completa ≤ 8 s en el p90 con conexión ≥ 5 Mbps; a los 10 s cae a la pista local. |
| **RD-04** | El copiloto debe acertar. | ≥ 80% de respuestas correctas (línea + causa física) en el set de 40 errores y 0 respuestas que entreguen la solución completa. |
| **RD-05** | El costo de IA debe ser sostenible. | ≤ US$0.006 por consulta y costo de IA por jugador ≤ 10% del ingreso neto por copia (≈ $17 MXN con precio de $249), medido con los logs de tokens del piloto. |
| **RD-06** | El sistema debe resistir código defectuoso del jugador. | 0 cierres del juego con 20 programas defectuosos (bucle infinito, división entre cero, acceso fuera de arreglo, recursión sin fin); el runtime los detiene en ≤ 2 s y los reporta. |

**REQUERIMIENTOS DE INTERFAZ**

| ID | Requerimiento | Verificación |
|---|---|---|
| **RI-01** | La interfaz debe permitir que un estudiante del segmento que nunca vio el juego complete el tutorial y el nivel 1 sin ayuda del equipo. | Prueba con 5 usuarios; ≥ 4 de 5 lo logran en ≤ 20 min. |
| **RI-02** | La interfaz del editor debe resaltar sintaxis C++, numerar líneas y marcar la línea del error de compilación o la señalada por el copiloto. | Con los 15 programas con error de RF-01, la marca cae en la línea correcta en 15/15. |
| **RI-03** | La interfaz y las explicaciones del copiloto deben estar en español de México, sin traducir las palabras del lenguaje (pinMode, int, for). | Revisión de todas las cadenas y de las 40 respuestas del set: 0 en inglés. |
| **RI-04** | La interfaz de programación (HAL virtual) debe exponer la misma API que el core Arduino-ESP32: pinMode, digitalWrite, digitalRead, analogWrite, analogRead, delay, millis, Serial. | Las soluciones de referencia de los niveles 1–3 compilan sin cambios con arduino-cli para ESP32-S3. |
| **RI-05** | La interfaz del backend debe ser HTTPS/JSON autenticada con JWT, y el copiloto debe responder por streaming (SSE). | Con curl, sin token → 401; con token → respuesta en stream; 20 payloads revisados sin nombre ni correo. |
| **RI-06** | La instalación debe completarse sin instalar compilador, SDK ni crear cuenta para jugar los niveles 1–2. | VM limpia de Windows 10: instalar desde la página y jugar; instalador ≤ 500 MB. |

**REQUERIMIENTOS DE RESTRICCIÓN**

| ID | Requerimiento | Verificación |
|---|---|---|
| **RR-01** | El sistema debe estar listo como first-iteration en la semana 13 (8 semanas) con un equipo de 2 personas. | Build publicado en itch.io antes de la semana 13. |
| **RR-02** | El sistema no debe enlazar dependencias con licencia GPL; solo MIT, Apache 2.0, BSD o zlib (ver semana 3). | Inventario de licencias (SBOM) del build. |
| **RR-03** | El costo operativo del piloto no debe superar $600 MXN en total. | Facturación de Anthropic (con límite de gasto) y panel de Supabase. |
| **RR-04** | El sistema debe correr en Windows 10/11 de 64 bits en la PC mínima; macOS, Linux y móvil quedan fuera de la versión 1. | Pruebas en 2 PCs reales de estudiantes del segmento. |
| **RR-05** | El sistema debe cumplir la Ley Federal de Protección de Datos Personales en Posesión de los Particulares (aviso de privacidad) y no enviar al LLM nombre, correo ni identificadores. | Revisión de 20 payloads en los logs de la Edge Function. |
| **RR-06** | El sistema no debe incluir la API key del LLM en el cliente; toda llamada pasa por la Edge Function con cuota por usuario (10 consultas por hora). | 0 coincidencias de la key en el ejecutable; la consulta 11 en una hora recibe la pista local. |

**DIAGRAMA DE ARQUITECTURA:** [arquitectura-gpio-genesis.drawio](../recursos/archivos/semana5/arquitectura-gpio-genesis.drawio) · **BOM PRELIMINAR:** [bom-preliminar-gpio-genesis.xlsx](../recursos/archivos/semana5/bom-preliminar-gpio-genesis.xlsx)

---

## 5. BOM preliminar: servicios y costos

El BOM completo, con fórmulas y fuentes, está en la [hoja de cálculo](../recursos/archivos/semana5/bom-preliminar-gpio-genesis.xlsx). Tiene tres pestañas: supuestos, costos del software y servicios críticos. Como el producto es 100% digital, no hay componentes de hardware: el BOM son los servicios de los que dependemos y el costo de la IA por jugador. Tipo de cambio: **$17.50 MXN por dólar** (FIX del 24-sep-2026).

### 5.1 First-iteration (software)

| Concepto | Proveedor | Costo | Cuándo |
|---|---|---|---|
| Motor del juego, compilador y runtime | Godot 4 (MIT), wasi-sdk (Apache 2.0), WAMR (Apache 2.0) | $0 | — |
| Backend (Auth + Postgres + Edge Functions) | Supabase Free | $0 | Piloto |
| Copiloto IA para 20 jugadores | Claude Haiku 4.5 | ≈ US$30 (≈ $525 MXN) | Piloto |
| Página de venta y demo | GitHub Pages + itch.io | $0 | Piloto |
| Dominio | Por cotizar | ≈ US$15/año (estimado) | Al lanzar |
| Firma de código para Windows (opcional) | Certificado OV de una autoridad certificadora | ≈ US$99/año (estimado) | Opcional |
| Backend en producción | Supabase Pro | US$25/mes | Al lanzar |
| Alta en Steam | Steam Direct | US$100 (recuperable) | Al lanzar |
| **Total para llegar al piloto** | | **≈ $525 MXN** | |

**Costo de la IA por jugador (el "costo por unidad" de un producto de software):**

| | Valor |
|---|---|
| Costo por consulta (3,000 tokens de entrada + 400 de salida) | US$0.005 ≈ $0.09 MXN |
| Costo por jugador con 300 consultas | US$1.50 ≈ $26 MXN |
| Ingreso neto por copia ($249 − 30% de la tienda) | $174.30 MXN |
| IA como % del ingreso neto | **15%** ⚠️ |
| Si la API sube 10× | **151%**: cada venta pierde dinero ❌ |
| Consultas máximas para que la IA sea ≤10% | ≈ 199 por jugador → cuota de 10 por hora |

### 5.2 Servicios críticos (los "componentes" de un producto digital)

| Servicio | Proveedor | Costo | ¿Disponible desde México? | Riesgo | Plan B |
|---|---|---|---|---|---|
| LLM del copiloto ★ | [Claude Haiku 4.5](https://platform.claude.com/docs/en/about-claude/pricing) | US$1 / US$5 por millón de tokens | ✅ Sí | Alza de precio o caída del servicio; es la dependencia más cara | Cuota + caché + reglas locales; proveedor intercambiable detrás de la Edge Function |
| Backend ★ | [Supabase](https://supabase.com/pricing) | Free: $0 · Pro: US$25/mes | ✅ Sí | El plan Free se pausa tras 1 semana sin actividad | Guardado local primero; Pro al lanzar |
| Firma de código de Windows ★ | Autoridad certificadora (OV) o [Azure Artifact Signing](https://learn.microsoft.com/en-us/azure/artifact-signing/quickstart) | ≈ US$99/año (OV, precio de revendedor) | ❌ **Artifact Signing no**: individuos solo de EE. UU. y Canadá; organizaciones de una lista de países sin México | Sin firma, Windows muestra "Windows protegió su PC" y el jugador puede abandonar la instalación | Piloto: guía visual en la página de descarga. Lanzamiento: distribuir por Steam |
| Tienda | itch.io → [Steam](https://partner.steamgames.com/doc/gettingstarted/appfee) | $0 → US$100 (recuperable después de US$1,000 en ventas) | ✅ Sí | Revisión y tiempos de espera de Steam | Vender la demo en itch.io mientras tanto |
| Compilador y runtime | wasi-sdk (clang) + WAMR | $0 (Apache 2.0) | ✅ Código abierto | Tamaño del instalador; C++ limitado en WebAssembly | Acotar el subconjunto de C++ que enseña el juego |

<small>★ = servicio crítico.</small>

**Lectura:** sin hardware, el costo por unidad de GPIO Génesis es lo que cuesta cada jugador en la nube (≈ $26 MXN con 300 consultas) más la comisión de la tienda. Con un precio de $249 quedan ≈ $148 MXN por copia después de la IA, y la cuota de consultas protege ese margen. El único "componente" que no se consigue fácil desde México es la firma de código, y tiene salida por Steam.

---

## 6. Viabilidad de la primera versión y lo que dejamos fuera

**Las cuatro preguntas de viabilidad, traducidas a un producto digital:**

| Pregunta del curso | Versión digital | Respuesta |
|---|---|---|
| ¿Cuántas unidades para una prueba de mercado real? | ¿Cuántos jugadores reales? | **20 jugadores** del segmento en el piloto de la semana 13, con la meta de que 4 de cada 5 terminen el tutorial y el nivel 1 solos |
| ¿Componentes disponibles en México en <2 semanas? | ¿Servicios disponibles desde México? | Sí, todos menos Azure Artifact Signing (sección 5.2); la firma tiene plan B |
| ¿PCB fabricable con el proceso elegido? | ¿El build se puede generar y distribuir? | Sí: export de Godot + instalador ≤500 MB, distribuido en itch.io; hay que probarlo en una Windows limpia |
| ¿El costo por unidad permite un precio viable? | ¿La IA por jugador cabe en el precio? | Sí, con cuota: ≈ $26 MXN de IA por jugador frente a $174.30 de ingreso neto; con la cuota de 10 consultas por hora baja a ≤10% |

**Qué dejamos fuera al volverlo 100% digital, y qué implica:**

| Lo que se va | Implicación |
|---|---|
| Estación de pruebas (ESP32-S3, motor, servo, sensores) | No hay BOM de hardware, ni manufactura, ni proveedores de componentes.
| Comparar lo simulado contra lo medido en hardware real | El copiloto compara lo **esperado** por el nivel contra lo **simulado**. Pierde el "momento wow" del hardware real, pero nos aleja de la patente CN121583173B |
| Modelo de utilidad y diseño industrial (semana 3) | Ya no aplican. La PI se queda en marca, derechos de autor y secreto industrial (el diagnóstico y sus datos) |
| Canal B2B con laboratorios vendiendo estaciones | Se puede recuperar vendiendo **licencias del juego** a universidades (semana 2: US$7–25 por estudiante al año), sin hardware |
| "Ver tu código mover algo real" | Lo cubre la animación de la fábrica y la API compatible con Arduino-ESP32: quien tenga una placa puede llevar su código a ella por su cuenta |

---

## 7. Lo que debemos poder responder al salir

- [x] **¿Dónde corre el modelo de IA y por qué?** Híbrido cloud-first: el LLM que explica los errores corre en la nube (Claude Haiku 4.5 detrás de una Edge Function) porque la tarea necesita un modelo grande y tolera 2–5 s; las reglas de diagnóstico, el compilador y el simulador corren en la PC del jugador para responder en <1 s y funcionar sin internet.
- [x] **¿Qué protocolo hay entre el artefacto y el backend, y por qué ese?** HTTPS REST para enviar la consulta y SSE para recibir la respuesta en streaming: son pocas consultas por hora iniciadas por el jugador (request/response), así que MQTT o WebSocket no aportan nada. Como el producto es 100% digital, no hay protocolo con hardware.
- [x] **¿Cuál componente es el más difícil de conseguir en México y cuál es el plan B?** No tenemos hardware. El equivalente en un producto digital es la **firma de código de Windows**: el servicio barato de Microsoft (Azure Artifact Signing) no acepta México. Plan B: un certificado OV (~US$99/año) o distribuir por Steam; en el piloto, una guía en la página de descarga. La dependencia más frágil es la API del LLM: plan B = cuota, caché, reglas locales y proveedor intercambiable.
- [x] **¿Qué diferencia hay entre lo que entregaremos en la semana 13 y un prototipo de exploración?** El prototipo de exploración corre en nuestras máquinas para que aprendamos. La first-iteration es un instalador que un estudiante que no nos conoce descarga de la página de venta y usa solo: 4 de 5 deben terminar el tutorial y el nivel 1 sin ayuda.

---

## Reflexión

Esta semana tomamos la decisión más grande desde que elegimos la idea: GPIO Génesis va a ser un producto 100% digital. Al principio dejamos la estación física "para después", pero al costearla vimos que su precio mínimo pasaba de $3,000 MXN por unidad, algo que nuestro estudiante nunca pagaría. No era una fase 2 del mismo producto, sino otro producto para otro cliente. Me costó soltarla, porque es la parte más "mecatrónica" del proyecto, pero quedarnos con ella solo por gusto habría sido diseñar para nuestra imaginación y no para el usuario, que es justo lo que el PDS intenta evitar.

Lo que más me sorprendió fue hacer las cuentas de la IA. En el chat se siente gratis, pero al multiplicar tokens por consultas y por jugadores, el copiloto se lleva el 15% de lo que nos queda de cada venta, y si la API subiera diez veces perderíamos dinero con cada copia. Eso cambió cómo veo la capa local y la cuota: no son detalles técnicos, son lo que mantiene vivo el negocio. También entendí que un producto de software también tiene "costo por unidad", solo que no aparece en un BOM de componentes.

Escribir el PDS me enseñó a desconfiar de mis propios requerimientos. Nuestro primer borrador decía cosas como "correr fluido" y "fácil de usar", que suenan bien pero no se pueden comprobar. Tener que escribir cómo se verifica cada uno lo convierte casi en una prueba, y eso obliga a definir cosas que no habíamos pensado, como cuál es la computadora mínima o qué pasa si el jugador escribe un bucle infinito.

Por último, justificar cada flecha del diagrama me hizo ver que elegir el protocolo más simple también es una decisión. Al preguntarnos "¿por qué no MQTT?" nos dimos cuenta de que no lo necesitamos. Y aunque ya no tenemos componentes que comprar, sí tenemos dependencias: descubrimos que la forma barata de firmar el instalador para Windows no está disponible en México. Un producto digital también tiene su "componente difícil de conseguir".
