# L-BOT-BACKUPS

<p align="center">
  <strong>Bot modular para Discord que crea copias completas de servidores y las restaura en otro servidor.</strong>
</p>

<p align="center">
  <a href="#características">Características</a> •
  <a href="#comandos">Comandos</a> •
  <a href="#instalación">Instalación</a> •
  <a href="#configuración">Configuración</a> •
  <a href="#licencia">Licencia</a>
</p>

## Características

- **Backups completos** de roles, canales, categorías, permisos, emojis y ajustes del servidor.
- **Restauración flexible**: conserva el servidor actual o reemplaza completamente su contenido.
- **Backups automáticos** con detección de cambios y retención configurable.
- **Gestor visual** mediante paneles y botones, sin depender exclusivamente de comandos.
- **Asignación masiva de roles** y limpieza controlada del servidor.
- **Interfaz personalizable** con banner, marca, emojis personalizados y variantes monocromas.
- **Backup de sanciones**, disponible opcionalmente al reconstruir.

> La restauración no puede recuperar mensajes ni hilos debido a las limitaciones de la API de Discord.

## Comandos

| Comando | Descripción |
|---|---|
| `/hacerbackup` | Crea un backup completo del servidor. |
| `/backups` | Lista, inspecciona, restaura o elimina backups. |
| `/reconstruir codigo [modo] [sanciones]` | Reconstruye el servidor desde un código. |
| `/darrol rol` | Asigna un rol a todos los miembros con confirmación. |
| `/limpiar` | Elimina canales y roles después de pedir confirmación. |
| `/estado` | Muestra intervalos, retención y último backup. |
| `/ayuda` | Abre la guía visual de comandos. |

Todos los paneles tienen botones para las acciones principales.

## Modos de restauración

### Conservar y restaurar

Crea o actualiza roles, canales y permisos sin borrar primero el contenido existente. Es el modo recomendado.

### Reemplazar todo

Elimina contenido compatible y reconstruye el servidor desde cero, incluyendo ajustes, icono, banner y descripción cuando Discord lo permita.

## Instalación

### Requisitos

- Windows con PowerShell
- Node.js y npm
- Una aplicación y bot creados en el [Portal de Discord](https://discord.com/developers/applications)
- Permiso de **Administrador** recomendado para restauraciones completas

### 1. Crear el bot

1. Crea una aplicación en el Portal de Discord.
2. Abre **Bot** y pulsa **Add Bot**.
3. Copia el token.
4. Activa `SERVER MEMBERS INTENT`.
5. Activa `MESSAGE CONTENT INTENT` si tu configuración lo necesita.

### 2. Invitar el bot

Añade el bot con los scopes `bot` y `applications.commands`, otorgando permiso de Administrador.

El rol del bot debe estar situado por encima de los roles que necesite gestionar.

### 3. Configurar

```cmd
npm install
copy .env.example .env
```

Edita `.env`:

```env
DISCORD_TOKEN=tu_token
CLIENT_ID=tu_client_id
```

Puedes usar `GUILD_ID` durante el desarrollo para publicar los comandos en un servidor concreto:

```env
GUILD_ID=tu_guild_id
```

### 4. Ejecutar

```cmd
npm start
```

## Configuración

Variables principales:

| Variable | Uso | Predeterminado |
|---|---|---|
| `DISCORD_TOKEN` | Token del bot | Requerido |
| `CLIENT_ID` | ID de la aplicación | Requerido |
| `GUILD_ID` | Comandos solo en un servidor | Global |
| `BACKUP_INTERVAL_MINUTES` | Intervalo entre ciclos automáticos | `60` |
| `MAX_BACKUPS_PER_GUILD` | Cantidad máxima de backups automáticos por servidor | `10` |
| `BACKUP_LOG_CHANNEL_ID` | Canal para avisos de backup automático | Opcional |
| `CUSTOM_EMOJIS` | Activar emojis personalizados | `true` |

Personalización visual opcional:

```env
BRAND_NAME=L-BOT SHOP
BRAND_TAGLINE=Sistema de Backups
BANNER_URL=https://tu-imagen.com/banner.png
FOOTER_ICON_URL=https://tu-imagen.com/icono.png
CUSTOM_EMOJIS=true
BANNER_FILE=./assets/banner.jpg
```

### Emojis personalizados

Los PNG de `assets/emojis/` se sincronizan como emojis de aplicación con el prefijo `lbot_`. Sus IDs se guardan en `assets/emojis.json` para reutilizarlos.

Cuando cambia la versión interna del pack, el bot elimina los emojis antiguos y sube la versión nueva al arrancar.

Para regenerar el banner y el pack:

```powershell
powershell -ExecutionPolicy Bypass -File scripts/build-assets.ps1
```

## Qué se guarda

- Roles, color, posición, permisos y configuración.
- Categorías y canales con sus permisos.
- Texto, voz, foros, hilos y ajustes compatibles.
- Emojis personalizados y expulsiones opcionales.
- Nombre, icono, banner, descripción y ajustes generales del servidor.
- Canal AFK, canal del sistema, verificación, filtros y notificaciones.

Los permisos individuales solo pueden restaurarse para miembros que todavía formen parte del servidor destino.

## Backups automáticos

- Se ejecuta un ciclo automático según `BACKUP_INTERVAL_MINUTES`.
- Se crea un primer ciclo 15 segundos después del arranque.
- Los contenidos idénticos no generan duplicados.
- Los backups manuales no se eliminan automáticamente.
- El canal definido en `BACKUP_LOG_CHANNEL_ID` puede recibir avisos.

## Seguridad

- Nunca compartas `DISCORD_TOKEN`.
- Mantén `.env` fuera del control de versiones.
- Protege la carpeta `backups/`; contiene IDs y configuración del servidor.
- Cifra o elimina información sensible antes de subir backups a una nube.

## Solución de problemas

**No aparecen los comandos**

- Comprueba el token y el client ID.
- Espera unos minutos si los comandos fueron registrados globalmente.
- Ejecuta con `GUILD_ID` para pruebas instantáneas.
- Reinicia el cliente de Discord con `Ctrl+R` si aparecen comandos antiguos.

**Falta permisos al restaurar**

- Otorga permiso de Administrador al bot.
- Mueve el rol del bot por encima de los roles que quiera modificar.

**No se crean backups**

- Espera el primer ciclo de 15 segundos o usa `/hacerbackup`.
- Comprueba los permisos y el valor de `BACKUP_INTERVAL_MINUTES`.

## Licencia

Distribuido bajo la licencia Apache License 2.0. Consulta el archivo [LICENSE](LICENSE).
