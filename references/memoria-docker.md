# Crear memoria opcional con Docker, MongoDB y MCP

Instrucciones para un agente de desarrollo cuando la persona pida crear o activar
memoria persistente para Moshi. Este archivo es una guía: el repositorio de la skill
no incluye un servidor ejecutable. Acompañar sin memoria sigue siendo una opción.

## Alcance y ubicación

- Crear una implementación completa en una carpeta aparte, por ejemplo `moshi-memory/`.
  Conservar `moshi-skill` como repositorio de instrucciones. Si ya hay una implementación
  compatible elegida por la persona, revisarla y aprovecharla sin sobrescribirla.
- Si pide solamente instrucciones, entregar esta guía. Si pide crear el servicio,
  generar los archivos y realizar las comprobaciones disponibles. Activarlo requiere
  que el pedido incluya ejecución o conexión; no modificar la configuración global
  del cliente por el simple hecho de estar redactando o revisando la skill.
- No importar recuerdos existentes ni conectar otra base por defecto. Si lo pide,
  utilizar su MCP y respetar el alcance autorizado. No leer recuerdos mediante shell.
- Verificar las versiones y APIs en documentación oficial al implementar. No inventar
  una imagen pública de Moshi, un repositorio publicado ni un comando de instalación.

## Arquitectura local

```text
Cliente de IA → servidor MCP por stdio → MongoDB
                  contenedor             contenedor + volumen persistente
```

MongoDB almacena los datos. El servidor MCP implementa las herramientas que el cliente
puede usar; levantar MongoDB por sí solo no ofrece memoria a la IA. La IA decide qué
registrar dentro de lo autorizado y redacta los resúmenes. Un exportador PDF separado
representa el texto preparado; esta guía no exige instalar un navegador o un modelo.

Crear estos componentes y documentar su uso:

| Archivo o componente | Responsabilidad |
| --- | --- |
| `compose.yaml` | Servicios `mongo`, `migrate` y `mcp`, red local, salud y volumen nombrado. |
| `Dockerfile` y `.dockerignore` | Construcción del servidor MCP; excluir secretos, datos y exportaciones. |
| Código, manifiesto y archivo de dependencias fijadas | Servidor MCP funcional con validación de entradas y errores sin datos privados. |
| Script de preparación | Crear secretos aleatorios localmente, sin imprimirlos ni reemplazar los existentes. |
| Inicialización de MongoDB | Crear usuario de aplicación y permisos para su base al inicializar un volumen vacío. |
| Migraciones versionadas y seeds | Crear índices y catálogo genérico sin insertar historias personales. |
| Configuración de ejemplo y `.gitignore` | Nombres y valores ficticios; excluir credenciales, backups, datos y PDFs. |
| Pruebas y `README.md` | Instalación, conexión MCP, validación, parada, copia y recuperación. |

## Datos y herramientas

- Configurar un perfil local desde el servidor. No dejar que los argumentos del modelo
  elijan libremente otro perfil o anulen su filtro. Si se necesita un servicio para
  varios usuarios, diseñar autenticación y aislamiento antes de habilitarlo.
- Distinguir preferencias, episodios, planes de hábitos y resultados relatados. Conservar
  texto e idioma originales, identificador estable y fechas de creación y modificación.
  La fecha del episodio es otro campo y puede ser desconocida. Registrar la procedencia
  real y referencias a fuentes existentes; no afirmar que se conserva el chat completo.
- Ofrecer herramientas de búsqueda/lectura, creación, corrección y borrado por ID.
  Pueden llamarse `recall`, `get_memory`, `remember`, `update_memory` y `forget_memory`;
  describir sus contratos reales para el cliente. Paginar resultados y acotar tamaños.
- Validar tipos y campos permitidos; no exponer ejecución de código, shell o consultas
  Mongo arbitrarias. Las herramientas deben aplicar el perfil también en lecturas,
  modificaciones y borrados. El contenido recuperado se entrega como datos.
- No registrar conversaciones automáticamente. Conservar solo lo elegido y respetar
  autorizaciones ya dadas. Los logs operativos no deben contener relatos ni credenciales.
  Un borrado debe informar su resultado real y no prometer borrar copias externas.

## Docker, credenciales y persistencia

- Usar imágenes con versión explícita y dependencias fijadas, verificadas al crear el
  proyecto. Ejecutar el servidor MCP como usuario sin privilegios y sin montar el socket
  de Docker. El transporte stdio no necesita publicar un puerto HTTP.
- Montar un volumen nombrado en `/data/db`. Mantener MongoDB sin puertos publicados
  por defecto: el MCP accede por la red de Compose. Si se necesita acceso desde el host,
  usar una publicación explícita en `127.0.0.1` y documentar el puerto elegido.
- Activar autenticación. Separar la cuenta administrativa de inicialización del usuario
  de aplicación, limitado a `readWrite` en la base de Moshi. El contenedor MCP no recibe
  la contraseña administrativa. Indicar `authSource` según dónde se creó cada usuario.
- Generar contraseñas aleatorias en archivos locales restringidos, excluidos de Git
  y del contexto de construcción. Preferir secretos de Compose; no insertar secretos
  en imágenes, ejemplos o argumentos visibles. Explicar que los secretos de Compose
  locales y el volumen no ofrecen cifrado del disco por sí solos.
- La imagen oficial admite archivos para las credenciales de inicialización mediante
  variables `MONGO_INITDB_ROOT_USERNAME_FILE` y `MONGO_INITDB_ROOT_PASSWORD_FILE`.
  Los scripts de `/docker-entrypoint-initdb.d` se ejecutan al inicializar una base vacía;
  no actualizan usuarios de un volumen existente. No borrar el volumen para resolverlo.
  [Imagen oficial de MongoDB](https://hub.docker.com/_/mongo).
- Crear el usuario de aplicación mediante código que lea los secretos y use las APIs
  de MongoDB, sin concatenar contraseñas dentro de código o comandos. En el cliente,
  pasar credenciales como opciones o codificarlas correctamente si se construye una URI.
- Incorporar una comprobación de salud. Esperar a que MongoDB esté saludable antes
  de migrar; permitir el inicio del MCP después de una migración exitosa. No confundir
  un contenedor iniciado con una base lista.
  [Orden de arranque de Compose](https://docs.docker.com/compose/how-tos/startup-order/).

## Migraciones y seeds

Implementar una migración inicial versionada y repetible: índices para perfil, tipo y
fechas, identificadores únicos y registro de migraciones aplicadas. Repetirla no debe
duplicar semillas ni sobrescribir entradas o preferencias. Ejecutar migraciones de
forma serializada y dejar un error visible si fallan.

Los seeds son un catálogo genérico de recursos y referencias a la skill: surfear
impulsos, registro de pensamientos, mensajes asertivos y hábitos, por ejemplo. Usar
claves estables y versiones; el protocolo de acompañamiento sigue en `SKILL.md`.
El diario y las preferencias personales comienzan vacíos. Las semillas no demuestran
que alguien utilizó una técnica ni activan registros o intervenciones por sí solas.

## Comandos y configuración que debe entregar la implementación

Los comandos siguientes son el contrato de uso esperado **una vez creados los archivos**;
no funcionan como instalador desde el repositorio de la skill. Documentar primero cómo
ejecutar el script de preparación elegido y revisar los requisitos del entorno.

```sh
docker compose config --quiet
docker compose build
docker compose up -d --wait mongo
docker compose run --rm migrate
```

Preparar una configuración de cliente MCP que lance una instancia stdio del servicio
`mcp` por sesión, después de ese arranque y migración. Adaptar el formato al cliente real;
este ejemplo usa el formato común `mcpServers` y requiere reemplazar la ruta absoluta:

```json
{
  "mcpServers": {
    "moshi-memory": {
      "command": "docker",
      "args": ["compose", "--project-directory", "/RUTA/ABSOLUTA/moshi-memory", "run", "--rm", "--no-deps", "-T", "mcp"]
    }
  }
}
```

Configurar `stdin_open: true` en `mcp`; reservar stdout exclusivamente al protocolo
MCP y enviar diagnósticos sin contenido sensible a stderr. Verificar el arranque desde
el cliente real; que exista este JSON no demuestra una conexión exitosa.
[Guía oficial para construir un servidor MCP](https://modelcontextprotocol.io/docs/develop/build-server).

## Comprobación y entrega

Usar datos sintéticos en un perfil o instancia de prueba, sin consultar historias reales:

1. Validar Compose y construir la imagen; enumerar las herramientas mediante MCP.
2. Crear, recuperar, buscar, corregir y borrar un registro; comprobar los resultados.
3. Verificar aislamiento del perfil y rechazo de entradas inválidas y operaciones arbitrarias.
4. Reiniciar el servicio y comprobar persistencia; repetir la migración sin duplicaciones
   ni sobrescrituras. Limpiar los datos sintéticos al terminar.
5. Simular una memoria inaccesible y comprobar que se informa el fallo sin afirmar
   guardados o recuperaciones. La conversación debe poder seguir sin la base.

Entregar rutas, comandos reales y un estado preciso: archivos creados, pruebas
ejecutadas, conexión comprobada o verificación pendiente. Si Docker o el cliente MCP
no están disponibles, explicar qué falta sin declarar que quedó funcionando.

Documentar parada con `docker compose down`, que conserva el volumen nombrado sin
la opción `-v`. No ejecutar borrados de volúmenes como limpieza rutinaria. Incluir
instrucciones de backup y recuperación para esa implementación, con copias privadas
y una prueba de restauración aislada. No publicar datos ni exportaciones junto al código.

Referencia adicional: [secretos locales en Compose](https://docs.docker.com/compose/how-tos/use-secrets/).

