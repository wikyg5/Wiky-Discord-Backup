# 🛡️L-BOT-BACKUPS — Backups completos para Discord

Bot de Discord con estética minimalista (negro, gris y blanco) que crea copias de seguridad **completas** de tu servidor y lo reconstruye exactamente igual.

## ✨ Qué se guarda

Cada backup (formato v2) guarda absolutamente toda la configuración de permisos y ajustes:

- **Roles**: nombre, color, colores degradados, permisos, jerarquía (posición), hoist, mencionable, emoji/icono, roles gestionados.
- **@everyone**: sus permisos globales del servidor.
- **Categorías**: nombre, posición y **todos sus permisos** (overwrites).
- **Canales** de todo tipo:
  - Texto y anuncios: tema, NSFW, slowmode, archivado automático.
  - Voz y escenarios: bitrate, límite de usuarios, región, calidad de vídeo.
  - Foros y media: etiquetas, reacción por defecto, orden, layout, slowmode de hilos.
  - **Permisos por rol y por miembro** en cada canal y categoría.
- **Orden** de canales y categorías.
- **Emojis** personalizados.
- **Expulsiones (bans)** con su motivo.
- **Ajustes del servidor**: nombre, icono, banner, descripción, nivel de verificación, notificaciones por defecto, filtro de contenido, canal AFK y su tiempo, canal del sistema y sus flags, canal de reglas, canal de anuncios, idioma, MFA, nivel de boost y features.

> Los backups antiguos (v1) siguen siendo legibles, aunque solo contienen roles y canales básicos.

## 💎 Estética

Los embeds usan:

- Estética monocroma: barra negra, texto blanco y notas en gris claro. Sin colores de acento.
- Banner propio adjunto en los paneles (`assets/banner.jpg`), sin depender de hosting externo.
- Jerarquía tipográfica: títulos grandes, etiquetas pequeñas en gris (`-#`), valores en monoespaciado y campos organizados en columnas.
- **Emojis personalizados** monocromos estilo *tile* negro con icono blanco (pack en `assets/emojis/`).
- Botones grises con emoji; el rojo se reserva para acciones destructivas.
- Pie de página con la marca y fecha dinámica.

Se puede personalizar desde `.env` sin tocar código:

```env
BRAND_NAME=L-BOT SHOP
BRAND_TAGLINE=Sistema de Backups
BANNER_URL=https://tu-imagen.com/banner.png
FOOTER_ICON_URL=https://tu-imagen.com/icono.png
CUSTOM_EMOJIS=true
BANNER_FILE=./assets/banner.jpg
```

### Emojis personalizados

Al arrancar, el bot sube los PNG de `assets/emojis/` como **emojis de la aplicación** (con el prefijo `lbot_`) y guarda sus IDs en `assets/emojis.json` para no volver a subirlos.

- El pack tiene una versión interna: al cambiar el diseño, el bot elimina los emojis antiguos y sube los nuevos automáticamente en el siguiente arranque.
- Si `CUSTOM_EMOJIS=false`, o la subida falla (por ejemplo, sin conexión), se usan emojis unicode de reserva.
- Para regenerar el pack de emojis y el banner optimizado: `powershell -ExecutionPolicy Bypass -File scripts/build-assets.ps1` (usa `assets/Banner.png` como origen y `assets/emojis-preview.png` como previsualización).
- Discord permite un máximo de 2000 emojis de aplicación; el pack actual usa 32.


## 🚀 Instalación

### 1. Crear el bot

1. Ve a <https://discord.com/developers/applications> → **New Application**.
2. En **Bot**, pulsa **Add Bot** y copia el **TOKEN**.
3. Activa los **Privileged Gateway Intents**:
   - ✅ SERVER MEMBERS INTENT
   - ✅ MESSAGE CONTENT INTENT *(opcional)*
4. Copia el **CLIENT ID** desde **OAuth2 → General**.

### 2. Invitar el bot

En **OAuth2 → URL Generator** selecciona los scopes `bot` y `applications.commands`, y el permiso **Administrator** (recomendado; necesario para restaurar todo).

> El rol del bot debe estar **por encima** de los roles que quiera crear o editar.

### 3. Configurar

```cmd
npm install
copy .env.example .env
```

Edita `.env` con tu token y tu client ID:

```env
DISCORD_TOKEN=tu_token
CLIENT_ID=tu_client_id
```

### 4. Arrancar

```cmd
npm start
```

## 🧩 Comandos

| Comando | Función |
| --- | --- |
| `/hacerbackup` | Crea una copia completa al instante. |
| `/backups` | Gestor visual: listar, ver detalle, restaurar o eliminar. |
| `/reconstruir codigo [modo] [sanciones]` | Restaura un backup (autocompletado de códigos). |
| `/darrol rol` | Da un rol a todos los miembros con confirmación. |
| `/limpiar` | Vacía canales y roles, pidiendo confirmación. |
| `/estado` | Estado del bot, intervalo, retención y último backup. |
| `/ayuda` | Guía visual de comandos. |

Todos los paneles incluyen botones para gestionar sin escribir más comandos.

## ♻️ Modos de restauración

- **Conservar y restaurar** (recomendado): crea o actualiza roles, canales y permisos sin borrar nada.
- **Reemplazar todo**: elimina los canales y roles actuales y reconstruye el servidor desde cero (incluye nombre, icono, banner y descripción).

Al restaurar se aplican también los ajustes del servidor, emojis y, opcionalmente, las expulsiones.

## 🔄 Backups automáticos

- Se ejecuta un ciclo cada `BACKUP_INTERVAL_MINUTES` (60 por defecto) y uno 15 s después de arrancar.
- Si nada ha cambiado desde el último backup, **no se duplica** el archivo (huella de contenido).
- Se conservan los últimos `MAX_BACKUPS_PER_GUILD` backups automáticos por servidor. Los manuales nunca se borran solos.
- Si defines `BACKUP_LOG_CHANNEL_ID`, se publica un embed de aviso en ese canal.

## ⚠️ Importante

- Guarda tu carpeta `backups/` en un lugar seguro: contiene los IDs y la configuración del servidor.
- Los permisos individuales de **miembros** solo se restauran si el usuario sigue en el servidor destino (Discord no permite asignar permisos a alguien que no está).
- Los iconos de rol y el banner del servidor requieren las mejoras/condiciones de Discord correspondientes; si no se pueden aplicar, el bot continúa y lo indica en los avisos.
- No se pueden restaurar mensajes ni hilos (limitación de la API de Discord).
- Los comandos solo están disponibles para **Administradores**.

## 🐛 Solución de problemas

**El bot no responde o ves comandos duplicados**
- Revisa que `DISCORD_TOKEN` y `CLIENT_ID` sean correctos.
- Al arrancar, el bot sincroniza los comandos y elimina registros antiguos (incluidos los de servidor que quedaron de versiones anteriores).
- Si definiste `GUILD_ID`, los comandos aparecen al instante en ese servidor; si no, se registran globalmente y Discord puede tardar unos minutos.
- Tras reiniciar, actualiza el cliente de Discord con `Ctrl+R` si aún ves las entradas antiguas.

**`Missing Permissions` al restaurar**
- El bot necesita **Administrador**.
- Comprueba que el rol del bot esté por encima de los roles que intenta crear.

**No hay backups**
- Espera 15 s al arrancar o usa `/hacerbackup`.

## 🔒 Seguridad

- Nunca compartas tu token.
- `.env` y `backups/` están excluidos de git.
- Cifra o mueve los backups si los subes a la nube.

## 📄 Licencia

ISC
