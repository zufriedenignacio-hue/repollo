---
name: relevo
description: Trabajo en cadena con agentes. Uno produce, un agente nuevo revisa lo que produjo el anterior, se corrige lo bloqueante y se cierra con un tope de dos rondas. El esfuerzo se calibra al tamaño y al riesgo de la tarea. Úsala siempre que el usuario pida agentes o subagentes, que otro agente revise el trabajo, una segunda mirada o un doble chequeo, o cuando invoque /relevo. Úsala también por defecto en tareas sustanciales aunque no mencione agentes, como cambios de código en varios archivos, features, investigaciones con varias fuentes, documentos o análisis que otros van a usar, o crear y editar skills. No la uses para preguntas puntuales, cambios de una línea ni conversación, ni cuando la tarea en sí es revisar algo ya hecho, como un PR. En inglés, agent pipeline, subagents, reviewer agent, review loop, second pass.
---

# Relevo

Trabajo en cadena con agentes. Uno produce y otro, nuevo, revisa lo que produjo el anterior. Se corrige lo que de verdad importa y se cierra. La revisión vale porque el revisor no carga los supuestos del productor. Cada ronda extra vale menos que la anterior, por eso hay tope. El esfuerzo se ajusta a la tarea.

## 1. Calibra el nivel

Decide el nivel antes de lanzar nada y dilo en una línea, por ejemplo "Nivel normal: produzco yo, revisa un agente". Así el usuario puede cambiar el rumbo temprano. Si el usuario fija el nivel ("ligero", "a fondo"), se usa ese.

| Nivel | Cuándo | Cadena | Re-revisión |
|---|---|---|---|
| Directo | Pregunta puntual, cambio de una línea o dato que se verifica al instante | Sin agentes | No |
| Ligero | Tarea acotada y de bajo riesgo, como un archivo, un borrador o una búsqueda | Producción y 1 revisor | No, se corrige y se cierra |
| Normal | Feature, documento para otros o investigación con varias fuentes | Producción, 1 revisor y corrección | Solo del delta, si hubo bloqueantes no triviales |
| Fuerte | Error caro o irreversible (producción, dinero, datos, legal) o muchas piezas | Producción (en paralelo si se puede), 2 revisores con focos distintos y corrección | Solo del delta, si hubo bloqueantes no triviales |

Si dudas entre dos niveles, elige el menor. Sube de nivel solo con evidencia. Si la revisión encuentra bloqueantes en varias partes, la tarea era más grande de lo que parecía. Aplica el nivel nuevo a lo que falta, sin reiniciar la revisión ni pasarte del tope.

Si la tarea en sí es revisar algo (un PR, un documento ajeno), esa revisión es la producción. No le pongas otro revisor encima salvo en nivel Fuerte.

En todos los niveles hay un tope de dos rondas de revisión: la inicial y una del delta.

## 2. Produce

**Quién produce.** Si ya tienes el contexto y la pieza es chica, produce tú, porque escribir un brief para un agente cuesta más que hacerlo. Delega la producción cuando:
- hay partes independientes que pueden correr en paralelo (lánzalas en un mismo mensaje);
- hay que revisar mucho código o muchas fuentes (usa `Explore` para encontrar, no para auditar);
- el trabajo es largo y llenaría tu contexto de detalles que después no necesitas.

No pongas en paralelo a dos productores que tocarían los mismos archivos. Divide por archivo o trabaja en serie.

**El brief.** Los agentes parten sin contexto y no ven esta conversación, así que una referencia a "lo de arriba" no les sirve. Cada brief lleva:
- el objetivo y para qué sirve (el porqué cambia las decisiones del agente);
- el contexto concreto: rutas, datos y decisiones que el usuario ya tomó y que no se discuten;
- las restricciones y lo que no debe hacer;
- qué significa terminado;
- un formato de retorno corto: qué hizo, dónde (rutas), qué verificó y cómo, y qué quedó dudoso.

Si el paso siguiente depende de este output, lanza el agente en primer plano (`run_in_background: false`). Usa segundo plano solo si tienes otra cosa útil que hacer mientras.

## 3. Revisa

El revisor es siempre un agente nuevo. Nunca revisa el productor su propio trabajo, ni tú si fuiste el productor, porque la idea es que no comparta tus supuestos. No le bajes el modelo al revisor (deja el que hereda). Un revisor más débil que el productor da falsos positivos y deja pasar lo sutil. Los modelos livianos sirven para búsquedas mecánicas, no para revisar.

Pásale el objetivo original (en lo posible con las palabras del usuario), el output o dónde encontrarlo (rutas, diff, rama) y los criterios de terminado. No le pases la autoevaluación del productor ("todo funciona", "está completo"), porque lo sesga.

En nivel Fuerte, lanza dos revisores en paralelo con focos distintos según la tarea. Por ejemplo, correctitud y seguridad, datos y conclusiones, o fondo y forma en un documento externo.

Plantilla de brief para el revisor:

```
Revisa el siguiente trabajo. No lo hiciste tú, así que no asumas que está bien.

Objetivo original: <lo que pidió el usuario, textual si se puede>
Qué se produjo y dónde: <rutas / diff / rama / texto>
Terminado significa: <criterios>

Verifica en vez de opinar: corre los tests o el código, abre los archivos y
contrasta cada afirmación con su fuente. Si algo no se puede verificar, dilo.
No edites nada, solo reporta.

Para cada hallazgo indica:
- si es BLOQUEANTE o MENOR. Bloqueante: incumple el objetivo, es falso, rompe
  algo o falla en un caso realista. Menor: mejora real que no impide usarlo.
- qué falla, en qué caso concreto y la evidencia (línea, salida o cita).

No incluyas gustos de estilo, sugerencias de mejora sin un caso que falle,
reescrituras completas ni recomendaciones que nadie va a implementar.

Termina con APROBADO o CORREGIR y una línea sobre lo que verificaste de verdad.
```

## 4. Corrige y cierra

1. **Filtra.** El revisor también se equivoca. Confirma cada bloqueante antes de actuar (lee la línea o reproduce el caso). Descarta los que no se sostienen y anota por qué.
2. **Corrige los bloqueantes.** Si el cambio es chico, hazlo tú. Si es grande y lo produjo un agente, retómalo con `SendMessage`, que conserva su contexto, en vez de lanzar uno nuevo que parta de cero.
3. **Menores.** Aplica de paso los que sean triviales y deja el resto en el reporte. Nunca provocan otra ronda.
4. **Re-revisión, si el nivel la permite.** Hazla solo si hubo bloqueantes y la corrección no fue trivial. Retoma con `SendMessage` al revisor que levantó los bloqueantes (en Fuerte, a cada uno que haya levantado alguno) y pídele que revise solo el delta: si se resolvieron los bloqueantes y si la corrección rompió algo. No le pidas revisar todo el trabajo de nuevo. Un revisor nuevo sobre el trabajo completo siempre encuentra algo más y el ciclo no termina.
5. **Para.** Después de la segunda ronda no hay más revisiones. Si la última confirmó un error con un arreglo puntual, aplícalo y verifícalo tú (corre el test o reproduce el caso). Lo que siga abierto se reporta tal cual junto con lo que se intentó, porque seguir iterando casi nunca converge. Para también apenas una ronda traiga solo hallazgos menores.

## 5. Reporta

Escríbele corto al usuario y en su idioma. El usuario no ve los reportes de los agentes, así que transmite solo lo que importa:
- qué se hizo y dónde (rutas, rama o link);
- qué encontró la revisión y qué se corrigió, con una línea por hallazgo relevante;
- qué quedó abierto y por qué.

No narres cada paso, no pegues los reportes completos y no cierres con ofertas genéricas.

## Errores frecuentes

- Usar agentes para lo trivial. Si se resuelve en un paso, es nivel Directo.
- Aceptar el veredicto de un revisor que aprueba sin mostrar qué verificó. Pídele la evidencia.
- Dejar que el revisor reescriba por gusto. Solo cuentan los hallazgos con un caso que falle.
- Revisar todo en cada ronda en vez de solo el delta.
- Iterar por hallazgos menores.
- Tratar los hallazgos del revisor como órdenes sin confirmarlos.
