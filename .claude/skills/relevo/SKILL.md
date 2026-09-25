---
name: relevo
description: Reparte trabajo entre agentes eligiendo modelo y esfuerzo por lote, y revisa cada lote como caja negra contra un contrato explícito (entrada exacta, operación y salida esperada). Decide cuándo basta un script, cuándo conviene partir el trabajo en lotes chicos para modelos baratos en paralelo (bulldozers), cuándo usar el modelo insignia con esfuerzo máximo sin auditoría, qué modelo revisa y qué hacer con un lote que no cuadra (desglosarlo, subir de nivel o corregir el contrato). Úsala siempre que el usuario pida agentes o subagentes, repartir o paralelizar trabajo, elegir modelo o esfuerzo (haiku, sonnet, opus, fable), revisar el output de otro agente, o cuando invoque /relevo. Úsala también por defecto en tareas grandes y divisibles, como muchos archivos, carpetas o registros, procesamiento por lotes, migraciones o clasificaciones masivas. No la uses para preguntas puntuales, cambios chicos ni conversación.
---

# Relevo

Cada pieza de trabajo va al modelo más barato que la hace bien. El contrato de cada pieza es tan específico que quien la revisa puede tratar al agente como caja negra: ve solo la entrada y la salida, y responde una pregunta: **¿lo que entregó es coherente con lo que se le pidió y con el esfuerzo que se le asignó?** Si el contrato no permite responder eso, la falla está en la delegación, no en el modelo.

**Quién es quién.** En esta skill, "tú" eres el orquestador: el modelo de la sesión que recibió la tarea del usuario. Tus tareas son:
- partir el trabajo en lotes y escribir sus contratos;
- elegir el nivel de cada lote;
- hacer la primera vista;
- decidir qué hacer con lo que no cuadra.

Los agentes `bulldozer`, `operario` e `insignia` ejecutan contratos. El `revisor` compara cada salida con su contrato y diagnostica la causa de lo que falla. El usuario fija el objetivo y recibe el reporte.

## 1. Fija el objetivo y escribe los contratos

Primero define el objetivo global: qué quiere el usuario y qué significa terminado. Después parte el trabajo en lotes y escribe un contrato por lote:

```
Entrada: <lista exacta de ítems (rutas, IDs, filas) y cuántos son>
Operación: <qué hacer con cada ítem, como regla>
Salida: <formato exacto, una unidad por ítem de entrada>
Aceptación: <chequeos verificables: conteos, formato, invariantes>
Esfuerzo esperado: <mecánico | juicio acotado | juicio abierto> y qué implica
Si un ítem no calza con la regla: márcalo PENDIENTE con el motivo. No adivines.
```

Un contrato sirve si alguien que ve solo el contrato y la salida puede decir COHERENTE o INCOHERENTE sin preguntar nada. "Revisa todo este archivo" no sirve, porque no tiene lista, ni forma esperada, ni nada contra qué comparar.

El esfuerzo esperado va escrito porque es lo que el revisor contrasta. Una tarea mecánica entregada con interpretaciones propias es tan incoherente como una tarea de juicio entregada con respuestas genéricas.

Los tres valores posibles:
- **Mecánico:** la regla del contrato decide todo y el agente no toma decisiones propias. Dos ejecuciones correctas dicen lo mismo, aunque sea con otras palabras. El revisor lo verifica contra la fuente.
- **Juicio acotado:** el agente decide, pero dentro de un criterio que da el contrato, como una lista cerrada de categorías o un patrón a aplicar. En los casos límite, dos ejecuciones correctas pueden diferir. El revisor verifica que cada decisión quepa en el criterio, no que sea la que él habría tomado.
- **Juicio abierto:** el contrato fija el objetivo pero no el criterio, así que el agente elige el enfoque. No hay una respuesta única. El revisor juzga si la salida resuelve el objetivo y si cada decisión está argumentada.

Si el contrato mezcla tipos, declara el esfuerzo por campo. Por ejemplo, en un catálogo el resumen de cada ítem es mecánico y la categoría es juicio acotado. El esfuerzo esperado también orienta el nivel: lo mecánico va a script o `bulldozer`, el juicio acotado a `operario`, y el juicio abierto a `operario` con opus o al `insignia`.

## 2. Asigna el nivel y el tamaño del lote

| Nivel | Cómo se lanza | Sirve para | Lote |
|---|---|---|---|
| Script | Bash, sin modelo | Reglas 100% deterministas: mover, renombrar, contar, validar formato | Todo de una vez |
| Bulldozer | `bulldozer` (haiku) | Trabajo mecánico que exige leer: extraer, clasificar con una regla explícita, resumir un archivo | Chico |
| Operario | `operario` (sonnet, esfuerzo medio) | Juicio acotado: aplicar un patrón con variaciones, criterios difusos | Mediano |
| Insignia | `insignia` (fable, esfuerzo máximo) | Problemas ambiguos o globales que no se pueden partir sin perder contexto | Entero |

Cada definición fija el esfuerzo y trae un modelo por defecto. El parámetro `model` de la llamada cambia el modelo sin tocar el esfuerzo. Por ejemplo, `operario` con `model: opus` es opus con esfuerzo medio, y `bulldozer` con `model: sonnet` es sonnet con esfuerzo bajo. Haiku no tiene niveles de esfuerzo, así que su única palanca es el tamaño del lote.

Costo relativo por token (precios de API de 2026, con haiku = 1): sonnet ≈ 2, opus ≈ 4 a 5, fable ≈ 10.

Cómo elegir:
- **Parte por el nivel más barato cuyos errores el contrato permite detectar.** Si la revisión no ve el error, el ahorro es ilusorio.
- **Usa el insignia con esfuerzo máximo** cuando revisar costaría tanto como hacer, o cuando la tarea necesita ver todo junto. En ese caso omite la revisión con modelo y deja solo los chequeos mecánicos, que casi no cuestan.
- **Cuenta el costo por tarea terminada, no por llamada.** Un lote barato que hay que rehacer dos veces no es barato. Antes de armar una cadena de modelos, evalúa si un solo modelo fuerte con menos esfuerzo lo resuelve de forma más simple.
- **Si una pieza es chica y tú, el orquestador, ya la tienes en contexto, hazla sin delegarla.** Escribir el contrato cuesta más que hacerla.

Tamaño del lote:
- **Fija el tamaño del lote y deriva la cantidad de agentes.** Con lotes de 4 carpetas, un nivel de 20 carpetas son 5 bulldozers, uno de 40 son 10 y uno de 120 son 30. La carga por agente se mantiene; lo que crece es la cantidad de agentes.
- **El techo lo pone la revisión.** Un lote no puede ser más grande de lo que un revisor alcanza a comparar, entrada contra salida, de un vistazo. Lo que el modelo "aguanta" no es el límite.
- **El piso lo pone el arranque.** Cada agente parte de cero y carga decenas de miles de tokens de contexto propio antes de trabajar. Arrancar un agente, sin trabajo, cuesta del orden de US$0,07 con sonnet y US$0,30 con fable (medido en Claude Code 2.1.282, a precios de API). Si el lote es tan chico que el arranque pesa más que el trabajo, junta lotes o usa un script.
- **Lanza un lote piloto.** Antes de lanzar 30 lotes, lanza 1 y revísalo. Si sale bien, lanza el resto con el mismo contrato. Si sale mal, corrige el contrato o el nivel antes de multiplicar el error por 30.
- **Trabaja por oleadas cuando hay dependencias.** Si el paso siguiente necesita el resultado del anterior (abrir un nivel para llegar al siguiente), termina y revisa cada oleada antes de lanzar la otra. Dentro de una oleada, los lotes van en paralelo en un mismo mensaje. Si lo que sigue depende de la oleada, lánzala en primer plano (`run_in_background: false`).
- **Respeta el tope de simultaneidad.** Claude Code corre como máximo 20 subagentes a la vez, contando los que ya estén corriendo. El tope se cambia con `CLAUDE_CODE_MAX_CONCURRENT_SUBAGENTS`. Los que pasan del tope se rechazan y no se reintentan solos. Lanza en tandas que no pasen el tope y lanza la tanda siguiente cuando termine la anterior. En primer plano no hay rechazo, pero corren de a 10.
- **Dos agentes en paralelo no pueden tocar los mismos archivos.** Reparte los lotes por archivo o carpeta.

## 3. Revisa como caja negra

El revisor recibe el contrato y la salida (o dónde encontrarla), nada más. No recibe el razonamiento del agente ni su autoevaluación ("listo, procesé todo"), porque lo sesgan.

El contrato le llega textual. Guarda cada contrato en un archivo y pásales la misma ruta al agente y al revisor. Un resumen puede borrar justo la frase que causó la falla, y entonces el revisor juzga contra un contrato que el agente nunca recibió.

Diagnosticar la causa de una falla le toca al revisor, porque es quien tiene el contrato y la salida frente a frente. Tú solo confirmas lo que diagnostica.

Revisa en dos capas, primero la barata:
1. **Primera vista (mecánica).** Correspondencia uno a uno entre entrada y salida, conteos, formato y PENDIENTES. Hazla tú o con un script. Casi no cuesta y atrapa lo grueso: ítems omitidos, salidas truncadas o ítems inventados.
2. **Juicio.** Solo sobre lo que marcó la primera vista, más una muestra del resto siempre que el contenido no se pueda validar con un script. Eso pasa casi siempre que la salida la produjo un modelo, porque una respuesta genérica tiene el formato correcto y pasa cualquier conteo. Aquí entra el agente `revisor`.

**Cuánto muestrear.** Elige la muestra al azar (por ejemplo con `shuf`) entre los lotes que la primera vista no marcó. Toma lotes completos e incluye al menos uno de cada nivel que trabajó. Para decidir el tamaño hay una cuenta simple: si revisas n ítems y no encuentras errores, lo único que puedes afirmar, con 95% de confianza, es que la tasa de error de los lotes no marcados está bajo 3/n.
- Por defecto, revisa al menos 30 ítems, o todos si son menos. Con 0 errores en 30, la tasa está bajo 10%.
- Para afirmar menos de 5%, revisa 60 ítems. Para menos de 1%, 300.
- Si no puedes aceptar ningún error, una muestra no sirve y hay que revisar todo. Si revisar todo cuesta lo mismo que hacer el trabajo, usa el `insignia`.

Si la muestra encuentra errores, aplica la tabla del paso 4. Si el mismo error aparece en varios lotes, la causa es el contrato o el nivel, y afecta también a los lotes que no revisaste. Si el error es aislado, corrígelo y reporta cuántos más estimas que quedan en lo no revisado.

El modelo del revisor depende de lo que tiene que juzgar, no del nivel de quien produjo:
- Los conteos y el formato los revisa un script.
- Para ver si una regla mecánica se aplicó bien, basta `revisor` con su modelo por defecto (Sonnet 4.6). Si le pasas `model: sonnet`, corre en Sonnet 5.
- Para juzgar decisiones, usa `revisor` con `model` de al menos el nivel que la tarea necesitaba. Un revisor más débil que el juicio que evalúa da falsos positivos y deja pasar lo sutil.

Señales de esfuerzo incoherente:
- Por debajo de lo pedido: ítems omitidos, respuestas genéricas que no dependen del ítem, PENDIENTES sin motivo, salida truncada o calidad que cae hacia el final del lote.
- Fuera del contrato: cambios no pedidos, interpretaciones propias de una regla explícita o ítems inventados.

Brief para el revisor:

```
Revisa estos lotes como caja negra. Solo tienes el contrato y la salida.

Contrato: <ruta del archivo con el contrato, el mismo que recibió el agente>
Salida: <dónde está o el texto>
Qué revisar: <todos los lotes | los marcados en la primera vista: ... | una muestra de N>

Para cada lote revisado responde:
- COHERENTE o INCOHERENTE respecto del contrato y del esfuerzo esperado.
- Si es INCOHERENTE: el ítem, qué pedía el contrato, qué llegó y la causa probable
  (contrato ambiguo, lote demasiado grande, nivel insuficiente o ruido).
- Antes de atribuir una falla a nivel insuficiente, revisa si el contrato dejaba
  margen para ese error. Si lo dejaba, la causa es contrato ambiguo: cita la frase.

Verifica contra los archivos o datos reales en vez de opinar. No edites nada.
No incluyas comentarios de estilo ni sugerencias sin un ítem que falle.
```

## 4. Decide qué hacer con lo que no cuadra

El revisor también se equivoca, así que confirma cada hallazgo mirando el ítem antes de actuar. Después actúa según el patrón:

| Señal | Causa probable | Acción |
|---|---|---|
| Varios lotes fallan igual | Contrato ambiguo | Corrige el contrato y relanza esos lotes. No subas de nivel. |
| Ítems omitidos, salida truncada o calidad que cae al final | Lote demasiado grande | Desglosa ese lote en lotes más chicos, con el mismo nivel. |
| Decisiones equivocadas con un contrato claro | Nivel insuficiente | Relanza el lote un nivel más arriba o con más esfuerzo. |
| Falla un lote aislado y el resto sale bien | Ruido | Rehazlo una vez, igual. |

**Corrige solo lo que levantó sospecha.** La tabla es para fallas de fondo que tocan varios ítems. Hay dos casos en que no se relanza el lote:
- La falla es de forma y se arregla sin modelo, por ejemplo con un comando que borra una fila inventada o cambia un separador. Arréglala así.
- La falla toca un solo ítem. Corrígelo tú.

Lo que pasó la revisión no se vuelve a recorrer: rehacerlo no agrega evidencia y abre la puerta a errores nuevos.

Cada lote tiene como máximo un reintento: rehacerlo, desglosarlo o subirlo de nivel. Si vuelve a fallar, lo resuelves tú o el insignia, o queda abierto en el reporte. No iteres en círculo.

## 5. Reporta

Escribe corto y en el idioma del usuario:
- El plan ejecutado: cuántos lotes, qué nivel tuvo cada grupo y quién revisó, en una línea o una tabla chica.
- Lo que no cuadró y qué hiciste: desglose, subida de nivel o contrato corregido.
- Lo que quedó abierto, incluida la parte que solo pasó la primera vista y que ningún modelo revisó. Eso también es parte del resultado. Indica cuántos ítems se revisaron y qué tasa de error permite afirmar la muestra.

No narres agente por agente ni pegues sus reportes.

## Agentes

Los niveles son definiciones de agente porque, en Claude Code, el esfuerzo de un subagente solo se fija en su definición. El parámetro `model` de la llamada cambia el modelo, no el esfuerzo. Las definiciones (`bulldozer`, `operario`, `insignia` y `revisor`) están en la carpeta `agents/` de esta skill. Para que la herramienta Agent las liste, tienen que estar en `~/.claude/agents/` o en `.claude/agents/` del proyecto. Una sesión nueva las carga siempre. Una sesión que ya está corriendo puede tardar en detectarlas. Si no aparecen, ofrécele al usuario copiarlas desde la carpeta `agents/` de esta skill a `~/.claude/agents/`; quedan activas en la sesión siguiente. Mientras tanto, usa `general-purpose` con el parámetro `model` (el esfuerzo queda en el valor por defecto) y agrega al brief del revisor las reglas de `agents/revisor.md`.

## Ejemplo

Hay un árbol de carpetas por niveles: hay que procesar un nivel para llegar al siguiente, y cada nivel tiene más carpetas que el anterior.
- Si procesar una carpeta es una regla fija (mover o renombrar), el nivel es Script y no hacen falta agentes.
- Si hay que leer y decidir con una regla explícita, van bulldozers por oleadas, una por nivel, con lotes de 4 carpetas: 5 bulldozers para el nivel de 20 carpetas y 30, en dos tandas, para el de 120. La primera oleada parte con un lote piloto.
- Contrato de un lote: "Entrada: estas 4 rutas. Operación: … Salida: una fila por carpeta con ruta|decisión|motivo. Aceptación: 4 filas con rutas idénticas a la entrada."
- La primera vista (conteos y rutas) la hace un script sobre todos los lotes. El `revisor` mira solo los lotes marcados y una muestra del resto.
- La alternativa es un insignia con esfuerzo máximo que recorre todo el árbol. Es más lento y más caro por token, pero no necesita revisión con modelo. Conviene cuando las decisiones dependen de ver varias carpetas a la vez, que es justo lo que un bulldozer no ve.
