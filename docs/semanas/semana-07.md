# Semana 7 — Viabilidad técnica y económica

**Equipo:** José Carlos y Yael  
**Concepto:** GPIO Génesis — videojuego de una fábrica donde se programa un microcontrolador con código C/C++ real, con un copiloto de IA  
**Tipo de producto:** 100% digital (sin hardware)  
**Fecha:** 22 de octubre de 2026

!!! abstract "De qué trata esta semana"
    Hasta la semana 6 respondimos si GPIO Génesis **se desea** (semanas 2–4) y si **se puede construir** (semanas 5–6). Esta semana respondemos la tercera pregunta: **¿el negocio se sostiene?** Para eso elegimos un modelo de ingresos, costeamos todo lo que cuesta crear y distribuir el juego, y hicimos el modelo financiero: costo unitario, precio, punto de equilibrio, flujo de caja a 12 meses y VPN.

    Entregables de esta página: benchmarking y modelo de ingresos (sección 2), inventario y clasificación de costos (sección 3), modelo financiero con fórmulas visibles (sección 4), ejercicios 1–9 (sección 5), taller D/F/S (sección 6) y checklist de salida (sección 8).

**La respuesta corta:** con el **MVP de 5 niveles** el proyecto se paga en el mes 9 si vendemos unas 400 copias el primer año (VPN de **+$9,829 MXN**). Con los **22 niveles** de los tres bloques el VPN sale negativo salvo que vendamos todo el SOM. El dinero que de verdad sale del bolsillo es poco (≈ $9,000 MXN) y se recupera con unas 50 copias; lo caro son nuestras horas.

## Learning outcomes

Al terminar esta semana podemos:

- Distinguir **relevancia** (¿vale la pena?), **factibilidad** (¿se puede construir?) y **viabilidad** (¿se sostiene en el tiempo?).
- Comparar los **5 modelos de ingreso** de IDEO y elegir uno con evidencia de benchmarking, no por costumbre.
- Construir un **inventario de costos** y clasificarlos en **D / F / S** para saber qué se puede recortar.
- Calcular el **costo de la IA** por usuario con tokens y precios oficiales.
- Calcular **costo unitario, precio cost-plus y value-based, punto de equilibrio, flujo de caja, VPN y TIR**, y leerlos para tomar una decisión.

---

## 0. Cómo adaptamos la semana a un producto digital

La semana está escrita para un artefacto con hardware y suscripción. Igual que en las semanas 5 y 6, tradujimos cada elemento antes de calcular:

| Lo que pide el curso | Qué es en GPIO Génesis |
|---|---|
| BOM con precios de LCSC, JLCPCB, DigiKey | **No hay BOM de hardware ($0).** Las "piezas" son las partes del desarrollo (motor, simulación de periféricos, niveles, arte, sonido) |
| Manufactura por unidad | **Horas de desarrollo** a $80/h (ejemplo del curso), repartidas entre las copias vendidas |
| Hardware que se vende una vez | **El juego**: pago único de $249 MXN en Steam |
| Suscripción de software (básico / premium) | **Copiloto Pro**: $49 MXN/mes, opcional |
| Costo de cloud por unidad | **Supabase Pro** ($25 USD/mes) repartido entre las copias |
| Costo de IA por usuario | Consultas al copiloto con **Claude Haiku 5.5** |
| Certificación NOM | No aplica (no hay aparato eléctrico). Aplican la **protección de datos personales** y las **reglas de Steam** |

---

## 1. Relevancia, factibilidad y viabilidad

| Pregunta | Qué responde | Dónde la contestamos | Estado |
|---|---|---|---|
| **Relevancia** | ¿Vale la pena hacerlo? | Semanas 2 y 4: problema, entrevistas, TAM/SAM/SOM | Sí, con dudas: el tema "fábrica" se validó con un concepto de granja |
| **Factibilidad** | ¿Lo podemos construir? | Semanas 5 y 6: arquitectura, PDS, boceto | Sí, pero el MVP pide ~423 h y los 3 bloques ~972 h (ver alerta 1) |
| **Viabilidad** | ¿El modelo se sostiene? | **Esta semana** | MVP: sí, por poco. 3 bloques: no en 12 meses |

Las tres dimensiones de la viabilidad, aplicadas al juego:

- **Económica / financiera:** ¿el ingreso por copia cubre el costo y deja margen? Es casi toda esta página (sección 4).
- **Operativa:** ¿podemos mantener el juego vivo después de lanzarlo? Somos dos personas. Soporte, parches y la cuota del copiloto dependen de nosotros, así que el plan de ventas no puede suponer que trabajamos tiempo completo.
- **Legal:** no hay NOM porque no vendemos aparatos. Lo que sí aplica: (1) **datos personales**: el juego guarda el correo y el progreso en Supabase, así que necesitamos un aviso de privacidad, y el código que va al modelo de IA nunca lleva nombre ni correo (semana 5); (2) **impuestos**: vender en Steam y cobrar una suscripción genera ingresos que hay que declarar (falta consultarlo con un contador); (3) **licencias**: solo assets CC0/MIT/OFL, nada GPL (RR-02); (4) **marca**: "GPIO GÉNESIS" como marca mixta en clases 9 y 41 (semana 3).

---

## 2. Modelo de ingresos (Bloque 2)

### 2.1 Benchmarking de 3 ofertas parecidas

Buscamos ofertas que el estudiante de ingeniería podría comprar en lugar de GPIO Génesis. Precios consultados el 22 de octubre de 2026:

| Oferta | Qué es | Modelo | Precio | ≈ MXN | Fricción para nuestro segmento |
|---|---|---|---|---|---|
| **SHENZHEN I/O** | Juego de puzles: circuitos con microcontroladores y ensamblador ficticio | Pago único | US$14.99 | $262 | El lenguaje es inventado: no se transfiere a una placa real. En inglés |
| **Turing Complete** | Juego: construir una computadora desde compuertas lógicas | Pago único | US$19.99 | $350 | Enseña arquitectura, no a programar periféricos. En inglés |
| **Wokwi Hobby** | Simulador web de Arduino, ESP32 y Raspberry Pi Pico | Freemium + suscripción | €5.6/mes* | $115/mes | Es una herramienta, no un juego: no guía ni motiva. El plan gratis ya simula |

\* Wokwi muestra el precio mensual con facturación anual. El tipo de cambio de €1 = $20.5 MXN es estimado.

De la semana 4 ya teníamos otros dos: **TIS-100** (~$119 MXN, pago único) y **CodeCombat** (US$9.99/mes, freemium).

**Qué aprendimos:** los **juegos** de programación se venden por **pago único** entre ~$120 y ~$350 MXN. Las **herramientas y plataformas** cobran **suscripción** de ~$115–175 MXN/mes. Ninguno combina las dos cosas, y ninguno usa el SDK de un microcontrolador real ni está en español.

**¿Quién más podría pagar?** El jugador es quien paga, pero no es el único interesado. También podrían pagar: (1) **profesores y universidades**, con licencias por grupo para un curso como Sistemas Embebidos I; (2) **fabricantes de placas**, como patrocinio, porque el juego enseña su SDK; (3) **bootcamps** de programación de embebidos. Ninguno se ha entrevistado todavía.

### 2.2 Los 5 modelos de IDEO aplicados al juego

| Modelo | Cómo sería en GPIO Génesis | Reto de diseño | ¿Lo usamos? |
|---|---|---|---|
| Pago por evento | Comprar el juego una vez en Steam | Que la compra se sienta completa | **Sí: capa 1** |
| Suscripción | Copiloto Pro con más ayuda en la nube | Dar algo nuevo cada mes para que no se cancele | **Sí: capa 2, opcional** |
| Publicidad | Anuncios en el juego | Que no rompa la experiencia | No: arruina un juego de pago |
| Freemium | Nivel 1 gratis | Que lo gratis enganche sin regalar todo | Solo como **demo** de Steam (marketing) |
| Pago por uso | Cobrar cada consulta al copiloto | Que el usuario entienda cuánto gasta | No: castiga al que más ayuda necesita |

### 2.3 Modelo elegido: juego + Copiloto Pro

Es la versión digital del modelo de dos capas del curso: lo que se vende una vez es el juego, y lo que se cobra cada mes es la ayuda de la IA.

| Capa | Qué incluye | Precio | Dónde se cobra |
|---|---|---|---|
| **1. Juego** | Todos los niveles, copiloto básico (pistas 1–2 locales, pistas 3–4 en la nube con cuota de 10/h) y portafolio exportable | **$249 MXN**, pago único | Steam (comisión 30%) |
| **2. Copiloto Pro** | Más consultas a la nube, revisión del código completo al terminar un nivel y un reporte de avance para el portafolio | **$49 MXN/mes**, se cancela cuando quieras | Página web con Stripe (3.6% + $3 MXN + IVA) |

**Reto de diseño de la suscripción:** el juego tiene final. Cuando el jugador termina los niveles, o el semestre, ya no tiene razón para seguir pagando. Por eso en el modelo supusimos que **35% de los suscriptores cancela cada mes**, y por eso el juego tiene que funcionar completo **sin** Pro.

!!! warning "Esto va contra lo que dijeron los entrevistados"
    En la semana 4, 3 de 4 entrevistados hablaron de "comprar un juego", no de pagar un servicio, y ahí recomendamos venta única. Elegimos agregar Pro porque es opcional y protege el negocio si la IA sube de precio. Pero en el modelo **Pro solo aporta ~5% del ingreso**: el negocio depende del juego. Hay que validarlo en el piloto (¿cuántos de 20 jugadores pagarían Pro?).

---

## 3. Costos (Bloque 3)

El Bloque 3 está en dos Excel con fórmulas visibles:

- [costos-gpio-genesis-semana7.xlsx](../recursos/archivos/semana7/costos-gpio-genesis-semana7.xlsx): inventario de costos, costo de IA, clasificación D/F/S y desarrollo por ciclos de aprendizaje.
- [alcance-niveles-costos-gpio-genesis.xlsx](../recursos/archivos/semana7/alcance-niveles-costos-gpio-genesis.xlsx): los niveles del juego, los 22 temas de los 3 bloques del curso, y cuánto cuesta cada parte del proyecto.

### 3.1 Inventario: qué se queda y qué no

| Actividad | ¿Indispensable? | Por qué |
|---|---|---|
| CREAR: componentes, PCB, carcasa | No | El producto es 100% digital (semana 5) |
| CREAR: programación, arte y diseño de niveles en Godot 4 | **Sí** | Es el costo dominante |
| CREAR: copiloto de IA (API de Claude) y backend (Supabase) | **Sí** | Es lo que nos diferencia |
| CREAR: firma de código de Windows | Sí, para lanzar | Sin ella Windows avisa al instalar |
| DISTRIBUIR: comisión y cuota de Steam | **Sí** | Es el canal de venta |
| DISTRIBUIR: dominio, página de venta, soporte, parches | Sí | Casi todo es tiempo nuestro |
| DISTRIBUIR: marketing pagado, empaque, envío | No | Orgánico en GitHub/YouTube (semana 4); descarga digital |

### 3.2 El costo que nadie calcula: la IA

Fórmula del curso: **costo mensual = llamadas/día × tokens × precio por token × 30 días.**

Precios oficiales de la API de Claude (consultados el 9 de octubre de 2026, en USD por millón de tokens): Haiku 4.5 $1 / $5; **Haiku 5.5 $0.10 / $0.50**; Sonnet 5.5 $2 / $10.

| Concepto | Valor |
|---|---|
| Tokens por consulta (instrucciones + JSON del nivel + código + error) | 3,000 de entrada + 400 de salida (**estimados**) |
| Costo por consulta con Haiku 5.5 | US$0.0005 (≈ $0.009 MXN) |
| **Usuario activo, plan base** (5 consultas/día × 30 días) | **$1.31 MXN/mes** |
| **Suscriptor Pro** (10 consultas/día × 30 días) | **$2.62 MXN/mes** |
| IA de un jugador en todo el juego (MVP / 3 bloques) | $0.86 / $6.28 MXN |
| Mismo cálculo con Haiku 4.5, el modelo de la semana 5 | $8.57 / $62.83 MXN |

Cambiar de Haiku 4.5 a Haiku 5.5 bajó el costo de IA 10 veces. En la semana 5 la IA era 15% del ingreso neto; ahora es menos del 4%.

!!! note "Falta medir los tokens"
    Los 3,000 / 400 tokens son una estimación. El curso pide medirlos. Lo haremos con `count_tokens` y el campo `usage` de la API cuando el prompt del copiloto esté escrito. En la sección 4.6 probamos qué pasa si la API costara 10 veces más.

### 3.3 Costos de desarrollo

Fórmula del curso: directos fijos + directos variables + indirectos (15%) + contingencia (30%; el curso da 25–40% para innovación).

| Parte del proyecto | MVP (5 niveles) | 3 bloques (22 niveles) |
|---|---|---|
| Motor del juego (interfaz, C++ → WASM, marco de niveles, menú) | $20,571 | $20,571 |
| Simulación de periféricos (GPIO, timers, interrupciones, UART, PWM, ADC, I2C) | $2,392 | $21,528 |
| Niveles (programación, diseño y pruebas) | $5,741 | $43,056 |
| Arte base y arte de máquinas | $9,927 | $17,103 |
| Copiloto IA, sonido, venta, piloto y pruebas de IA | $11,966 | $14,023 |
| **Total de desarrollo** | **$50,597** (423 h) | **$116,281** (972 h) |
| Lanzamiento, 12 meses (Steam, dominio, firma, Supabase Pro) | $8,943 | $8,943 |

Casi todo es costo de oportunidad: nuestras horas. El dinero que de verdad sale del bolsillo es ≈ **$8,950 MXN**.

---

## 4. Modelo financiero (Bloque 4)

Todo está en [modelo-financiero-gpio-genesis-semana7.xlsx](../recursos/archivos/semana7/modelo-financiero-gpio-genesis-semana7.xlsx), con fórmulas visibles. La hoja **Supuestos** tiene los datos (en azul) y los supuestos por confirmar (en amarillo). Si cambian uno, todo se recalcula.

Hicimos el modelo para los **dos alcances** (MVP y 3 bloques) y con **tres escenarios de ventas** el primer año: pesimista (150 copias), base (400) y optimista (1,100, que es el SOM de la semana 4).

??? note "Prompt del modelo económico que usamos (adaptado del curso)"
    ```text
    Actúa como analista financiero de startups de videojuegos indie en México.
    Nuestro producto es 100% digital: no hay hardware, BOM ni manufactura.

    Producto: GPIO Génesis, juego de PC (Godot 4) donde se programa un
    microcontrolador simulado en C/C++, con un copiloto de IA (Claude Haiku 5.5).
    Modelo de ingresos: juego $249 MXN pago único en Steam (comisión 30%) +
    Copiloto Pro $49 MXN/mes opcional, cobrado con Stripe (3.6% + $3 + IVA).
    Costos: desarrollo MVP $50,597 / 3 bloques $116,281 MXN (horas a $80/h,
    ya con indirectos 15% y contingencia 30%); lanzamiento $8,943 MXN/año;
    IA por jugador $0.86 / $6.28 MXN; IA por suscriptor Pro $2.62 MXN/mes.
    Mercado: SOM 1,100 copias/año. Equipo: 2 estudiantes.

    Paso 1: costo unitario real a 10 y a 100 copias, por alcance.
    Paso 2: precio cost-plus (40% de margen), precio value-based con base en
    SHENZHEN I/O (US$14.99), Turing Complete (US$19.99) y Wokwi (€5.6/mes),
    y precio recomendado. ¿Nuestro segmento lo paga (100–400 MXN)?
    Paso 3: punto de equilibrio del año 1 y si es alcanzable.
    Paso 4: flujo de caja de 12 meses (mes 0 = inversión) y en qué mes se
    vuelve positivo.
    Paso 5: VPN a 12 meses con 15% anual. ¿Crea valor?

    Formato: costo unitario, precio, equilibrio, flujo, VPN, alertas y
    palancas. Marca qué números son supuestos. No inventes datos de mercado.
    ```

    Las respuestas de Claude se revisaron y se pasaron al Excel con fórmulas. Los números de abajo salen del Excel, no del chat.

### 4.1 Paso 1 — Costo unitario real

| Concepto (MXN por copia) | MVP · 10 | MVP · 100 | 3 bloques · 10 | 3 bloques · 100 |
|---|---|---|---|---|
| BOM / hardware | $0 | $0 | $0 | $0 |
| Desarrollo repartido ("manufactura") | $5,060 | $506 | $11,628 | $1,163 |
| Lanzamiento repartido (Steam, dominio, firma) | $370 | $37 | $372 | $37 |
| Nube repartida (Supabase Pro, 12 meses) | $525 | $53 | $525 | $53 |
| IA del copiloto | $0.86 | $0.86 | $6.28 | $6.28 |
| Comisión de Steam (30%) | $74.70 | $74.70 | $74.70 | $74.70 |
| **Costo unitario total** | **$6,030** | **$671** | **$12,606** | **$1,334** |
| de eso, variable (IA + comisión) | $75.56 | $75.56 | $80.98 | $80.98 |

**Lectura:** a 10 copias cada una cuesta miles de pesos porque el desarrollo se reparte entre muy pocas. En un producto digital el costo variable es muy bajo (~$76–81 por copia, casi todo la comisión de Steam). El precio no se decide con el costo a 10 copias: lo que importa es el volumen.

### 4.2 Paso 2 — Precio: piso y techo

| Capa 1 — juego (con 400 copias, escenario base) | MVP | 3 bloques |
|---|---|---|
| Costo por copia sin comisión | $150 | $319 |
| **Piso cost-plus** (× 1.4 ÷ 0.7 por la comisión) | **$299** | **$639** |
| **Techo value-based** (Turing Complete, el más caro del benchmark) | **$350** | **$350** |
| **Precio recomendado** | **$249** | **$249** |
| ¿Cubre el piso? | Cubre costos, pero no el 40% de margen | No cubre costos |

Nos quedamos en **$249** aunque esté debajo del piso cost-plus del MVP: entra en el rango de $199–299 de la semana 4 y queda entre SHENZHEN I/O ($262) y Turing Complete ($350). Con los 3 bloques el piso ($639) **pasa el techo** ($350): ningún precio que el mercado acepte paga ese desarrollo con 400 copias. Esa es la primera señal de que los 22 niveles no son viables para el lanzamiento.

| Capa 2 — Copiloto Pro | MXN/mes |
|---|---|
| Costo por suscriptor (IA + pasarela con IVA) | $8.15 |
| Piso cost-plus (× 1.4) | $11.41 |
| Techo value-based (Wokwi Hobby) | $114.80 |
| **Precio recomendado** | **$49** |
| Margen por suscriptor | $40.85 |

En Pro el precio no lo decide el costo, porque la IA cuesta muy poco. Lo decide cuánto vale para el jugador: $49 queda debajo de Wokwi y de CodeCombat. El riesgo es que alguien quiera pagarlo cada mes.

### 4.3 Paso 3 — Punto de equilibrio

**Equilibrio (copias) = costos fijos ÷ margen de contribución por copia.** El margen por copia incluye el margen esperado de Pro: 10% se suscribe × 2.9 meses de vida media × $40.85 = $11.67.

| Concepto | MVP | 3 bloques |
|---|---|---|
| Costos fijos del año 1, contando horas | $59,539 | $125,224 |
| Margen de contribución por copia (juego + Pro) | $185.11 | $179.69 |
| **Equilibrio contando horas** | **322 copias** | **697 copias** |
| Como % del SOM (1,100/año) | 29% | 63% |
| **Equilibrio solo con el dinero que sale del bolsillo** | **49 copias** | **50 copias** |
| ¿Alcanzable con el escenario base (400)? | **Sí** | **No**: faltan 297 copias |

### 4.4 Paso 4 — Flujo de caja a 12 meses (MVP, escenario base)

Supusimos que las ventas tienen un pico en el lanzamiento, bajan y repuntan en el mes 6 (inicio de semestre). El mes 0 es la inversión: desarrollo contando horas + cuota de Steam, dominio y firma.

| Mes | Copias | Ventas juego | Comisión Steam | Suscriptores Pro | Ventas Pro (neto) | Supabase | Flujo neto | Acumulado |
|---|---|---|---|---|---|---|---|---|
| 0 | — | — | — | — | — | — | −$54,289 | −$54,289 |
| 1 | 80 | $19,920 | −$5,976 | 8 | $327 | −$438 | $13,764 | −$40,525 |
| 2 | 48 | $11,952 | −$3,586 | 10 | $409 | −$438 | $8,296 | −$32,228 |
| 3 | 32 | $7,968 | −$2,390 | 10 | $396 | −$438 | $5,509 | −$26,720 |
| 4 | 24 | $5,976 | −$1,793 | 9 | $356 | −$438 | $4,081 | −$22,639 |
| 5 | 24 | $5,976 | −$1,793 | 8 | $329 | −$438 | $4,054 | −$18,585 |
| 6 | 40 | $9,960 | −$2,988 | 9 | $378 | −$438 | $6,877 | −$11,707 |
| 7 | 32 | $7,968 | −$2,390 | 9 | $376 | −$438 | $5,489 | −$6,219 |
| 8 | 24 | $5,976 | −$1,793 | 8 | $343 | −$438 | $4,067 | −$2,151 |
| **9** | 20 | $4,980 | −$1,494 | 7 | $304 | −$438 | $3,336 | **+$1,184** |
| 10 | 20 | $4,980 | −$1,494 | 7 | $279 | −$438 | $3,311 | +$4,495 |
| 11 | 24 | $5,976 | −$1,793 | 7 | $280 | −$438 | $4,005 | +$8,500 |
| 12 | 32 | $7,968 | −$2,390 | 8 | $313 | −$438 | $5,425 | +$13,925 |

"Ventas Pro (neto)" ya resta la comisión de la pasarela y la IA de Pro. El flujo neto también resta la IA de los jugadores (≈ $17–69 al mes). El detalle completo, con los 6 casos, está en la hoja **Flujo 12 meses**.

**El acumulado se vuelve positivo en el mes 9.** Si solo contamos el dinero que sale del bolsillo, sin las horas, la inversión inicial es de $3,698 y se recupera en el **mes 1**.

### 4.5 Paso 5 — VPN a 12 meses (15% anual → 1.17% mensual)

| VPN contando horas | MVP | 3 bloques |
|---|---|---|
| Pesimista (150 copias) | −$33,289 | −$99,738 |
| **Base (400 copias)** | **+$9,829** | **−$57,892** |
| Optimista (1,100 copias) | +$130,561 | +$59,275 |
| Mes en que el acumulado se vuelve positivo (base) | 9 | no en 12 meses |

| VPN solo con el dinero que sale del bolsillo | MVP | 3 bloques |
|---|---|---|
| Pesimista / base / optimista | +$17,302 / +$60,420 / +$181,152 | +$16,514 / +$58,359 / +$175,526 |

**¿Crea valor en 12 meses?**

- **MVP:** sí en el escenario base, por poco: +$9,829 sobre una inversión de $54,289, con una TIR mensual de 4.6%. Si vendemos 150 copias, no.
- **3 bloques:** solo si vendemos el SOM completo el primer año, que es muy optimista.
- **En efectivo**, los dos alcances son positivos hasta en el escenario pesimista: el riesgo de perder dinero real es bajo. Lo que arriesgamos es **tiempo**.

### 4.6 Sensibilidad: ¿y si la API costara 10 veces más?

Pusimos el multiplicador del precio de la API en 10 (celda azul en Supuestos):

| Concepto | API × 1 | API × 10 |
|---|---|---|
| IA por usuario activo al mes (plan base) | $1.31 | $13.12 |
| Margen de Pro por suscriptor al mes | $40.85 | $17.22 |
| Equilibrio MVP / 3 bloques | 322 / 697 | 349 / 1,076 |
| VPN base MVP / 3 bloques | +$9,829 / −$57,892 | +$4,719 / −$81,335 |
| VPN optimista 3 bloques | +$59,275 | −$5,193 |

El MVP aguanta aunque la IA suba 10 veces, sobre todo porque las pistas 1–2 son locales. Con los 3 bloques ni el escenario optimista sale positivo.

---

## 5. Ejercicios de matemáticas financieras

Resueltos con fórmulas en la hoja **Ejercicios** del Excel.

| # | Datos | Resultado | Decisión |
|---|---|---|---|
| 1 | VF de US$45,000 al 7.5% capitalizable trimestralmente, 5 años 6 meses (22 trimestres) | **VF = US$67,717.39** | — |
| 2 | VP de $125,000 MXN en 48 meses al 12% anual compuesto mensual | **VP = $77,532.55** | — |
| 3 | VP al 9% de $10,000 (año 1), $15,000 (año 3), $8,000 (año 5) | **VP = $25,956.52** | — |
| 4 | Inversión US$250,000; US$60,000 por 6 años; 11% | VP = $253,832 → **VPN = +US$3,832**; TIR 11.5% | Aceptar, por poco |
| 5 | Inversión US$50,000; 7.5K / 12K / 20K / 25K; 4% | **VPN = +US$7,456**; TIR 9.1% | Aceptar |
| 6 | Inversión US$1M; 300K / 350K / 375K / 300K + 150K de salvamento; 10% | **VPN = +US$151,083**; TIR 16.4% | Aceptar. El salvamento aporta $102,452 del VPN |
| 7 | X: 25K × 3 años; Y: 10K / 20K / 45K; inversión $50,000; 8% | **VPN X = +$14,427** (TIR 23.4%); **VPN Y = +$12,128** (TIR 18.2%) | Los dos son viables; elegimos **X**: más VPN y recupera antes |
| 8 | Sensibilidad del proyecto X | 8%: +$14,427 · 15%: +$7,081 · 20%: +$2,662 · 25%: **−$1,200** | Aceptar hasta ~23%; rechazar al 25% |
| 9 | Inversión US$500,000; 180K / 200K / 190K / 170K; 14% (resuelto en clase) | VPN 14% = +$40,686; VPN 22% = −$36,714; **TIR interpolada ≈ 18.2%** (exacta 17.9%) | Viable: TIR > 14% |

**Ejercicio 9:** el curso da +$40,732 y −$36,560; al recalcularlo nos dio +$40,686 y −$36,714. La diferencia es de redondeo y no cambia la conclusión. La interpolación lineal da 18.2% y la TIR exacta 17.9%: la interpolación se pasa un poco porque el VPN no es una recta.

**Fórmulas que usamos:** VF = VP(1 + i/m)^(n·m) · VP = VF/(1 + i)^n · VPN = −I + Σ Fₜ/(1 + r)ᵗ · TIR = la tasa donde VPN = 0 · TIR interpolada = r₁ + (r₂ − r₁) · VPN₁/(VPN₁ − VPN₂).

---

## 6. Taller (Bloque 5): las 10 partidas más caras y D / F / S

No tenemos BOM de hardware, así que las "piezas" de un juego son sus partes de desarrollo y lanzamiento (MXN, con indirectos y contingencia).

| # | Partida | MVP | 3 bloques | Tipo | Si el modelo no cierra… |
|---|---|---|---|---|---|
| 1 | Simulación de periféricos | $2,392 | $21,528 | **D** | Se recortan periféricos (alcance), no calidad |
| 2 | Motor del juego | $20,571 | $20,571 | **D** | Ya es lo mínimo para que corra el código del jugador |
| 3 | Niveles Bloque 2 · Tiempo y serie | $0 | $17,222 | F | Se lanzan después como actualización o DLC |
| 4 | Niveles Bloque 1 · GPIO | $5,741 | $14,352 | **D** | Los 5 del MVP son el núcleo; los otros 4 se posponen |
| 5 | Niveles Bloque 3 · Analógico y buses | $0 | $11,482 | F | Igual que el Bloque 2 |
| 6 | Arte de máquinas nuevas | $2,153 | $9,329 | F | Reutilizar sprites; tope de 30 h de arte (semana 6) |
| 7 | Arte y animación base (pixel art) | $7,774 | $7,774 | F | Acepta assets CC0 |
| 8 | Supabase Pro (12 meses) | $5,250 | $5,250 | F | Quedarse en el plan gratuito mientras alcance |
| 9 | Sonido | $2,392 | $4,425 | F | Efectos CC0, música al final |
| 10 | Copiloto IA (desarrollo) | $3,588 | $3,588 | **D** | Es lo que nos diferencia de SHENZHEN I/O y Turing Complete |
| + | Comisión de Steam (escenario base) | $29,880 | $29,880 | S | No la controlamos; vender también en itch.io |

**Conteo:** 4 D, 6 F, 1 S. **Modelo de ingresos elegido:** juego + Copiloto Pro (sección 2.3).

La regla del curso dice que si el modelo no cierra, primero se recortan los F y se aceptan los S, y que tocar un D obliga a replantear la propuesta de valor. Aplicada al juego: lo que no cierra son los niveles de los bloques 2 y 3, y esos son **F**. Se pueden mover a actualizaciones sin tocar la propuesta de valor.

---

## 7. Alertas y palancas

**Alertas:**

1. **El tiempo es el cuello de botella, no el dinero.** El MVP pide ~423 h. Con 12 h/semana por persona son ~18 semanas, y quedan pocas semanas de curso. El modelo financiero supone que el juego se termina; el calendario dice que no a este ritmo.
2. **El equilibrio contando horas está cerca del escenario base.** Necesitamos 322 copias y suponemos 400. Si vendemos menos de ~320, el MVP no recupera nuestras horas en el primer año.
3. **Pro no está validado.** Supusimos que 10% se suscribe y 35% cancela cada mes; la semana 4 dice que el segmento piensa en "comprar un juego".
4. **Los tokens no están medidos** (3,000 / 400 son estimados), y faltan cotizar la firma de código y el dominio y consultar impuestos.

**Palancas para que sea más viable:**

1. **Lanzar el MVP y vender los bloques 2 y 3 como actualizaciones o DLC** de pago. Eso convierte costos F en ingresos nuevos.
2. **Licencias para universidades** (B2B): un grupo de 30 alumnos de Sistemas Embebidos equivale a 30 copias en una sola venta.
3. **Demo gratis en Steam** (nivel 1) para bajar la fricción de compra sin regalar el juego.
4. **No pagar Supabase Pro hasta necesitarlo**: el plan gratuito aguanta 50,000 usuarios activos al mes. Eso baja el costo fijo $5,250.
5. **Mantener las pistas 1–2 locales**: es lo que hace que el negocio aguante aunque la IA suba de precio.

---

## 8. Checklist de salida

| Pregunta | Respuesta |
|---|---|
| Costo unitario a 10 unidades | **MVP $6,030 · 3 bloques $12,606 MXN** por copia (casi todo es desarrollo repartido; el costo variable es ~$76–81) |
| Modelo de ingresos y su reto de diseño | **Juego $249 (pago único) + Copiloto Pro $49/mes.** Reto: que Pro dé algo nuevo cada mes, en un juego que tiene final |
| Costo mensual de IA por usuario activo | **$1.31 MXN** (plan base) · **$2.62 MXN** (Pro), con Claude Haiku 5.5 |
| ¿VPN a 12 meses positivo con números reales? | **MVP: sí, +$9,829** (base, 400 copias). **3 bloques: no, −$57,892.** En efectivo, los dos son positivos |

**Archivos de la semana:**

- [modelo-financiero-gpio-genesis-semana7.xlsx](../recursos/archivos/semana7/modelo-financiero-gpio-genesis-semana7.xlsx): Resumen, Supuestos, Benchmark, Costo unitario, Precio, Equilibrio, Flujo 12 meses, Taller D-F-S, Ejercicios.
- [costos-gpio-genesis-semana7.xlsx](../recursos/archivos/semana7/costos-gpio-genesis-semana7.xlsx): inventario, costo de IA, D/F/S y desarrollo por ciclos.
- [alcance-niveles-costos-gpio-genesis.xlsx](../recursos/archivos/semana7/alcance-niveles-costos-gpio-genesis.xlsx): niveles y costo por parte del proyecto.

**Fuentes de precios** (consultadas el 22 de octubre de 2026, salvo donde se indica): [SHENZHEN I/O en itch.io](https://zachtronics.itch.io/shenzhen-io) · [Turing Complete (Steambase)](https://steambase.io/games/turing-complete/info) · [Wokwi Pricing](https://wokwi.com/pricing) · [Stripe México](https://stripe.com/mx/pricing) · [Reparto de ingresos de Steam (TechCrunch, 2018)](https://techcrunch.com/?p=1753729) · Precios de la API de Claude y de Supabase: consultados el 9 de octubre de 2026 (Bloque 3).

---

## Reflexión

Lo que más me sorprendió esta semana es que el desarrollo de un proyecto sale mucho más caro de lo que parece. Al principio uno piensa que lo caro son los materiales, pero en GPIO Génesis no hay materiales: el BOM es de $0 y aun así el MVP cuesta unos $50,000 MXN. Casi todo son las horas que invertimos. El dinero que de verdad sale del bolsillo son unos $9,000 MXN; lo demás es nuestro tiempo, y ese tiempo también cuesta aunque nadie nos lo pague.

También aprendí que hay que pensar en un colchón para cualquier imprevisto. Al presupuesto le sumamos 15% de indirectos y 30% de contingencia, y entendí por qué el curso lo pide: en un proyecto de innovación casi nada sale a la primera, y si no se aparta ese margen desde el inicio el proyecto se queda sin presupuesto a la mitad.

Por último, los números nos obligaron a ser realistas con el alcance. Con los 22 niveles el VPN sale negativo, y aun el MVP de 5 niveles pide más horas de las que tenemos en lo que queda del curso. Por cuestiones de tiempo, nuestra meta mínima es lograr una pequeña demo y después continuar con el resto. Es lo mismo que dice el taller D/F/S: los niveles que faltan son costos flexibles que se pueden ir agregando como actualizaciones, sin tocar lo que hace valioso al juego.
