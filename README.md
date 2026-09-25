# repollo

## Skills

- **relevo**: reparte trabajo entre agentes eligiendo modelo y esfuerzo por lote. Cada lote lleva un contrato explícito (entrada exacta, operación y salida esperada) para que el revisor lo trate como caja negra y diga si lo entregado es coherente con lo pedido y con el esfuerzo asignado.
  - Skill: `.claude/skills/relevo/SKILL.md`.
  - Agentes (`bulldozer`, `operario`, `insignia` y `revisor`): viven en `.claude/skills/relevo/agents/` y están enlazados desde `.claude/agents/`, así que se activan solos en las sesiones sobre este repo.
  - Para usarla en cualquier proyecto, instala el `.skill` en tu perfil o copia la carpeta a `~/.claude/skills/relevo/`, y copia `agents/*.md` a `~/.claude/agents/`. El esfuerzo de un subagente solo se fija en su definición, así que sin estos archivos la skill funciona pero no controla el esfuerzo.
