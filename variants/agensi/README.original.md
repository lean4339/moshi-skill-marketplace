# moshi-skill

Moshi es una skill experimental de **acompañamiento emocional no profesional** para
clientes de IA. Ofrece instrucciones inspiradas en TCC para escuchar y ayudar a elegir
el próximo paso. **No es un tratamiento, un acompañante terapéutico profesional ni un
servicio de emergencias.** Su eficacia y seguridad no están validadas; requiere evaluación
profesional y pruebas de comportamiento.

[Leer SKILL.md](SKILL.md) · [Alcance en 32 idiomas](ALCANCE.md) · [Casos ficticios de evaluación](references/casos-evaluacion.md)

Este repositorio contiene instrucciones, referencias y documentación. La skill tiene
un núcleo breve y módulos que el agente consulta según la situación. Para instalarla
o compartirla completa, conservar `SKILL.md` junto con la carpeta `references/` y los
documentos enlazados. La lectura del proyecto no inicia acompañamiento emocional.

## Usos previstos

- **Emociones e impulsos:** escuchar, ofrecer una acción breve, surfear impulsos y
  reflexionar cuando la persona quiera, sin exigir que desaparezca el malestar.
- **Cuidados y vínculos:** frenar respuestas dañinas hacia personas o animales,
  acompañar la sobrecarga de cuidados, reconocer posible abuso y preparar mensajes asertivos.
- **Hábitos y adicciones:** elegir acciones pequeñas, preparar apoyos, trabajar sobre
  disparadores y acompañar recaídas sin juicios ni planes de desintoxicación.
- **Consultas y documentos:** organizar hechos, pensamientos, emociones y acciones en
  un resumen revisable; generar un PDF cuando haya un exportador disponible.

Ante situaciones que exceden este alcance, la skill orienta a ayuda humana y mantiene
escucha y apoyo práctico. Las instrucciones priorizan urgencias y protección antes de
ejercicios o registros. La persona conserva el derecho a parar y decidir qué compartir.

## Uso

Con la skill instalada, escribí dentro del chat de Claude Code o Codex:

```text
activate moshi
```

También podés contar qué necesitás en el mismo mensaje:

```text
activate moshi Estoy sobrepasado y necesito acompañamiento.
```

Moshi continúa en el idioma de la conversación. Si el cliente no selecciona la skill,
usá la invocación explícita indicada en la sección de instalación.

**Contá lo que te pasa con claridad y libertad.** Podés abrirte y expresarte con tus propias
palabras: qué pasó, qué sentís, qué impulso aparece y qué ayuda necesitás. Si querés que
te acompañe a no fumar, por ejemplo, decilo explícitamente. Esto ayuda a orientar la
conversación; no hace falta explicarlo perfecto ni completar una lista. Vos elegís el
ritmo y cuánto contar, y podés reservarte detalles. Evitá nombres, direcciones u otros
datos identificatorios que no sean necesarios. La privacidad del cliente se explica abajo.

La skill no requiere servicios adicionales para ofrecer acompañamiento conversacional,
siempre que se cargue en un cliente de IA compatible. Ejemplos:

> Necesito apoyo emocional para atravesar este impulso.

> Estoy sobrepasado cuidando a alguien y no quiero descargar mi enojo.

> Tengo ganas de volver a apostar. Ayudame con el próximo paso.

> Quiero pasar estas ganas sin fumar. Acompañame a surfear el impulso, paso a paso.

> Quiero retomar un hábito sin castigarme cuando me cuesta.

> Me hablaron mal y quiero responder sin agredir ni aceptar los insultos.

> Ayudame a preparar un resumen de esta semana para mi consulta.

Responde en el idioma de la persona. Las [32 versiones del alcance](ALCANCE.md) son
traducciones generadas con IA y pendientes de revisión; no son pruebas de equivalencia
entre idiomas ni copias traducidas de toda la skill.

## Instalación local en Claude Code y Codex

En Linux, se puede enlazar este repositorio a las carpetas personales de skills.
Reemplazar `/ruta/absoluta/moshi-skill` por su ubicación real. Los enlaces conservan
una única copia: las ediciones del repositorio quedan disponibles en ambos clientes.
Estos comandos no reemplazan una instalación existente.

**Claude Code:**

```sh
mkdir -p ~/.claude/skills
ln -sT /ruta/absoluta/moshi-skill ~/.claude/skills/moshi-skill
```

Escribir `activate moshi` en el chat o invocar explícitamente `/moshi-skill`.
Si Claude ya estaba abierto antes de crear la carpeta de skills,
usar `/reload-skills` o iniciar otra sesión. Esta ubicación corresponde a Claude Code
local; no instala la skill en Claude web o Cowork.
[Documentación de Claude Code](https://code.claude.com/docs/en/skills).

**Codex:**

```sh
mkdir -p ~/.agents/skills
ln -sT /ruta/absoluta/moshi-skill ~/.agents/skills/moshi-skill
```

Escribir `activate moshi` en el chat o invocar explícitamente `$moshi-skill`.
Codex detecta los cambios de skills;
si no aparece en el siguiente turno, reiniciar el cliente.
[Documentación oficial de OpenAI](https://learn.chatgpt.com/docs/build-skills).

La presencia de archivos y un encabezado válido verifica la instalación estructural;
no demuestra que cada modelo siga correctamente las instrucciones. Los
[casos de evaluación](references/casos-evaluacion.md) se prueban por separado.

## Memoria opcional y privacidad

Un diario persistente requiere un MCP de memoria configurado y autorización para
guardar. La [guía de Docker, MongoDB y MCP](references/memoria-docker.md) permite pedir
a un agente que cree el servicio en otra carpeta, con volumen persistente, credenciales,
migraciones y seeds genéricos. Este repositorio contiene la guía, no el servidor.

En esa configuración, MongoDB guarda los registros en el equipo que ejecuta Docker.
Pueden acceder quienes tengan permisos suficientes sobre el equipo, la base o sus
copias, además de las herramientas autorizadas. **Memoria local no significa procesamiento
local:** el cliente de IA puede enviar los recuerdos utilizados al proveedor del modelo,
según su configuración y sus políticas. No se promete confidencialidad absoluta.

La persona puede pedir consultar, corregir o borrar registros mediante el MCP. El borrado
de la base no elimina chats, PDFs descargados ni backups: esas copias se gestionan por
separado. La skill no registra conversaciones ni envía mensajes o informes automáticamente.
Si la memoria o el exportador no están disponibles, informa la limitación.

## Estado y revisión

Proyecto experimental significa que **las instrucciones están pendientes de evaluación**;
no que la skill evalúe clínicamente a quien la usa. Las fuentes y los límites se documentan
en [referencias](references/fuentes.md). Gran parte de las fuentes especializadas procede
de EE. UU. y Reino Unido; también se incluye material de OPS/OMS para las Américas.
Los recursos de ayuda se verifican según el lugar y las necesidades de la persona.

Los casos ficticios distinguen situaciones cotidianas, emociones intensas sin peligro
inmediato y situaciones que necesitan ayuda urgente. Redactar casos o validar Markdown
no equivale a haberlos superado. La revisión clínica, de cuidado infantil, cultural y
lingüística sigue pendiente.

Los ejemplos son genéricos: este repositorio no contiene historias personales,
credenciales ni bases de datos. Licencia MIT para las instrucciones originales;
las fuentes externas conservan sus propias licencias.

