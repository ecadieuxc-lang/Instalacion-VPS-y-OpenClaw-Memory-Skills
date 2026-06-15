# README / HANDOFF para ChatGPT - assistant-vps-base 0.2.13-live-ready

Este documento esta escrito para una conversacion nueva de ChatGPT con cero contexto previo. Su objetivo es que ChatGPT pueda guiar a una persona, tramo por tramo, para instalar y validar `assistant-vps-base 0.2.13-live-ready` en un VPS limpio, sin inventar estados, sin pedir secretos y sin declarar exito sin evidencia.

## 1. Que es este paquete

`assistant-vps-base 0.2.13-live-ready` es un instalador base guiado para preparar un VPS con un asistente personal nuevo.

Proposito:

- instalar una base de VPS para un asistente guiado por OpenClaw;
- avanzar por tramos numerados, del `00` al `14`;
- crear una identidad nueva para el asistente;
- configurar usuario runtime, OpenClaw, auth OpenAI/Codex, WhatsApp, skills, memoria inicial vacia, automatizaciones, healthcheck y reporte final;
- permitir que el usuario pida ayuda a ChatGPT durante la instalacion, pegando solo salidas no sensibles.

Este paquete esta disenado para una identidad nueva. No debe heredar memoria, credenciales, sesiones, numeros, llaves SSH privadas, configuracion privada ni personalidad de una instalacion anterior.

No es un instalador magico de un clic. Es una instalacion tramo por tramo. Algunos tramos requieren confirmacion humana, login OAuth, QR de WhatsApp, disponibilidad de proveedores externos o decisiones del usuario.

## 2. Que no es este paquete

Este paquete no es:

- Roy;
- el VPS vivo de Erick;
- una app movil;
- un despliegue de API publica;
- un paquete con credenciales;
- una garantia de que OpenClaw, Tailscale, OpenAI, WhatsApp, Gemini, OpenRouter u otros proveedores estaran disponibles en vivo;
- una prueba E2E ya ejecutada en un VPS limpio;
- una autorizacion para exponer SSH ampliamente;
- una autorizacion para copiar sesiones, `.env`, `creds.json`, tokens, QR o llaves privadas desde otro lugar.

## 3. Estado actual del release

Release objetivo:

- Archivo: `assistant-vps-base-0.2.13-live-ready.tar.gz`
- Version interna esperada: `0.2.13-live-ready`
- SHA256 final:

```text
f7d910ae199a6edf0392328a9fb862d6350443fe03aaff0586fe1fe62c0fbc2c
```

Estado:

- `0.2.13` es el candidato local actual.
- La auditoria local de `0.2.13` esta cerrada como PASS local.
- La siguiente fase necesaria para un PASS vivo real es una instalacion E2E en VPS limpio.
- PASS local significa candidato valido para probar, no garantia de instalacion viva final.

No usar releases anteriores:

- `0.2.11` no debe usarse: la auditoria encontro defectos de preflight. Stage 01 aceptaba un OS no soportado simulado y una instalacion OpenClaw existente simulada como PASS. Tambien habia un detector heredado literal en Stage 13.
- `0.2.12` no debe usarse como final: corrigio los defectos de `0.2.11`, pero una revision independiente encontro literales de identidad heredada en metadatos de skills y detectores activos.
- `0.2.13` existe para limpiar esos literales, actualizar version/checksums y dejar el paquete como candidato local actual.

## 4. Archivos que se deben compartir

Para que otra persona o ChatGPT pueda ayudar con la instalacion, compartir estos cuatro archivos:

```text
assistant-vps-base-0.2.13-live-ready.tar.gz
assistant-vps-base-0.2.13-live-ready.tar.gz.sha256
assistant-vps-base-0.2.13-live-ready.audit.txt
README_CHATGPT_HANDOFF_assistant-vps-base-0.2.13.md
```

No compartir credenciales, `.env`, auth stores, sesiones WhatsApp, QR, cookies, llaves privadas, tokens, screenshots privados ni dumps de configuracion sensible.

## 5. Como debe usar este README un ChatGPT nuevo

ChatGPT debe actuar como guia de instalacion, no como sistema que inventa resultados.

Reglas para ChatGPT:

- Primero leer este README completo y entender el contexto.
- Guiar al usuario paso a paso, tramo por tramo.
- Pedir la salida del comando actual antes de avanzar.
- No pedir ni recibir secretos.
- No inventar credenciales.
- No inventar que un tramo paso.
- No declarar `PASS` sin evidencia objetiva del reporte del tramo.
- Interpretar `PASS`, `PASS_WITH_WARNINGS`, `PARTIAL_PASS_*`, `PARTIAL_*`, `BLOCKED_*`, `FAILED` o `FAIL` segun los archivos de evidencia.
- Mantener las compuertas de seguridad: no saltar `AVB_LIVE=1`, root requerido, OAuth, QR, Tailscale, UFW ni healthcheck.
- Si un tramo bloquea, detener el avance, pedir el reporte no sensible del tramo y explicar la causa.
- Si el usuario pega algo sensible por error, indicarle que lo revoque o regenere y no repetirlo.

Plantilla de conducta para ChatGPT:

```text
No voy a declarar exito hasta ver evidencia.
Ejecuta solo el comando del tramo actual.
Pega solo salidas no sensibles: final_status, checks PASS/FAIL, errores sin tokens, y rutas sin contenido secreto.
No pegues API keys, tokens, QR, .env, creds.json, secrets.json, cookies ni llaves privadas.
```

## 6. Verificacion antes de instalar

Estos comandos se ejecutan en la maquina donde se recibieron los archivos, antes de tocar el VPS o antes de ejecutar tramos mutantes.

### 6.1 Verificar SHA256 externo

Ubicarse en el directorio donde estan el tarball y el `.sha256`:

```bash
sha256sum -c assistant-vps-base-0.2.13-live-ready.tar.gz.sha256
```

Resultado esperado:

```text
assistant-vps-base-0.2.13-live-ready.tar.gz: OK
```

Si hay mismatch, detenerse. No extraer ni instalar. Pedir que se vuelva a transferir el archivo.

### 6.2 Extraer el tarball

```bash
tar -xzf assistant-vps-base-0.2.13-live-ready.tar.gz
cd assistant-vps-base-0.2.13-live-ready
```

Debe existir una sola carpeta raiz llamada:

```text
assistant-vps-base-0.2.13-live-ready
```

### 6.3 Verificar VERSION

```bash
cat VERSION
```

Resultado esperado:

```text
0.2.13-live-ready
```

### 6.4 Verificar checksums internos

Dentro de la carpeta extraida:

```bash
sha256sum -c CHECKSUMS.sha256
```

Resultado esperado: todas las entradas deben terminar en `OK`.

Si falla cualquier archivo, detenerse. No ejecutar el instalador.

### 6.5 Revisar ejecutables inesperados

El paquete fue validado localmente sin bits ejecutables inesperados. Se puede revisar asi:

```bash
find . -type f -perm /111 -print
```

Resultado esperado: salida vacia.

### 6.6 Escaneo de identidad heredada si se desea

Si el usuario conoce nombres, dominios, IPs, rutas o identificadores de una instalacion anterior, puede buscarlos sin exponerlos en ChatGPT:

```bash
grep -RInE 'PATRON_HEREDADO_1|PATRON_HEREDADO_2|PATRON_HEREDADO_3' .
```

Resultado esperado: salida vacia. Si aparece algo real de una instalacion anterior, detenerse y pedir revision sin pegar secretos.

## 7. Estructura del paquete

Archivos y carpetas principales:

- `install.sh`: dispatcher del instalador. Soporta `--list`, `--status`, `--run NN` y `--resume`.
- `VERSION`: version del paquete. Debe decir `0.2.13-live-ready`.
- `MANIFEST.md`: resumen del alcance, politica de skills y elementos incluidos/no incluidos.
- `CHECKSUMS.sha256`: checksums internos de archivos estaticos del paquete.
- `docs/`: documentacion de stages, seguridad, estado/resume, validacion, ChatGPT help context y politicas.
- `lib/`: helpers de shell para estado, evidencia, contexto de ayuda y validacion.
- `scripts/`: scripts de los tramos `00` a `14`.
- `skills/`: seis skills bundled, usadas como fuente local limpia.
- `templates/`: plantillas para personalidad, memoria, automatizaciones y catalogo de skills.
- `evidence/`: contexto inicial y luego evidencia generada por el instalador.
- `state/`: estado actual del instalador. El archivo principal es `state/current.env`.
- `logs/`: carpeta mutable para logs durante ejecucion.

## 8. Modelo de ejecucion del instalador

El instalador usa un modelo de tramos.

Comandos principales:

```bash
bash install.sh --list
bash install.sh --status
bash install.sh --run 00
bash install.sh --run 01
sudo AVB_LIVE=1 bash install.sh --run 02
sudo AVB_LIVE=1 bash install.sh --resume
```

Reglas importantes:

- Tramos `00` y `01`: validaciones previas de solo lectura.
- Tramos `02` a `14`: tramos live/mutantes o dependientes del sistema; normalmente requieren root y `AVB_LIVE=1`.
- El estado se guarda en `state/current.env`.
- Cada tramo escribe evidencia bajo `evidence/<numero-nombre>/`.
- Cada tramo actualiza `evidence/chatgpt-help-context.md` con contexto seguro para pedir ayuda.
- `--resume` avanza solo desde estados seguros como `PASS`, `PASS_*` o `PARTIAL_PASS_*`.
- Si el ultimo estado es `BLOCKED*`, `FAILED*` o `PARTIAL*` no controlado, `--resume` repite el tramo bloqueado/incompleto.
- No editar `state/current.env` manualmente para forzar avance.

Cuando un stage bloquea:

1. Detenerse.
2. Ejecutar `bash install.sh --status` o `sudo AVB_LIVE=1 bash install.sh --status`.
3. Leer el reporte del tramo en `evidence/<stage>/`.
4. Pegar en ChatGPT solo lineas no sensibles: `final_status`, checks PASS/FAIL y mensajes sin tokens.
5. Resolver la causa antes de reintentar.

## 9. Tramos 00 a 14

### 00 - audit-package

Proposito: validar el paquete antes de instalar nada.

El usuario puede necesitar: nada externo.

Evidencia esperada:

- `evidence/00-audit-package/audit-package-report.md`
- `evidence/00-audit-package/checksums.log`
- `final_status=PASS` si el paquete esta integro.

Bloqueos comunes:

- `checksums_sha256=FAIL`: paquete alterado o transferencia corrupta.
- `live_state_contamination=FAIL`: posible contaminacion heredada.
- `unexpected_executables=FAIL`: archivos ejecutables no esperados.

### 01 - preflight

Proposito: revisar que el VPS parezca compatible y limpio antes de mutar.

El usuario puede necesitar: acceso shell al VPS y permisos para leer estado del sistema.

Evidencia esperada:

- `evidence/01-preflight/preflight-report.md`
- checks de Linux, systemd, apt, sudo, arquitectura, Ubuntu, memoria y disco.

Bloqueos comunes:

- `BLOCKED_UNSUPPORTED_OS`: no se detecto Ubuntu.
- `BLOCKED_REBOOT_REQUIRED`: el VPS requiere reboot.
- `BLOCKED_EXISTING_OPENCLAW_INSTALLATION`: ya existe OpenClaw; no continuar sin decision humana.
- `BLOCKED_PREFLIGHT`: falla de prerequisito basico.

### 02 - system-base

Proposito: crear usuario runtime, instalar paquetes base y preparar carpetas operativas.

El usuario puede necesitar:

- elegir nombre de usuario runtime;
- opcionalmente entregar una SSH public key nueva;
- confirmar uso de `sudo AVB_LIVE=1`.

Evidencia esperada:

- `evidence/02-system-base/system-base-report.md`
- `evidence/02-system-base/apt.log`
- `state/install-config.env` con el runtime user, sin secretos.

Bloqueos comunes:

- `BLOCKED_LIVE_GATE`: falto `AVB_LIVE=1`.
- `BLOCKED_ROOT_REQUIRED`: no se ejecuto como root.
- `BLOCKED_INVALID_RUNTIME_USER`: nombre de usuario invalido.
- `BLOCKED_RESERVED_RUNTIME_USER`: nombre reservado.
- `BLOCKED_RUNTIME_HAS_SUDO`: el runtime user quedo con sudo, lo cual no debe pasar.

### 03 - tailscale-ufw

Proposito: instalar/configurar Tailscale y endurecer UFW sin cortar acceso.

El usuario puede necesitar:

- login Tailscale;
- abrir una segunda terminal;
- confirmar acceso por Tailscale escribiendo exactamente `SI` antes de activar UFW restrictivo.

Evidencia esperada:

- `evidence/03-tailscale-ufw/tailscale-ufw-report.md`
- `evidence/03-tailscale-ufw/ufw-status.txt`
- Tailscale IP enmascarada, no completa.

Bloqueos o parciales comunes:

- `PARTIAL_TAILSCALE_LOGIN_REQUIRED`: falta completar login.
- `PARTIAL_UFW_SKIPPED_PENDING_TAILSCALE_CONFIRMATION`: el usuario no confirmo segunda via.
- `BLOCKED_LIVE_GATE` o `BLOCKED_ROOT_REQUIRED`.

Seguridad: no dejar SSH publico abierto como regla amplia. El paquete busca SSH solo por `tailscale0` y UDP de Tailscale.

### 04 - openclaw

Proposito: instalar OpenClaw como usuario runtime, actualizarlo si se puede y configurar workspace/modelo/gateway local.

El usuario puede necesitar:

- conectividad a internet;
- aceptar que el instalador oficial de OpenClaw se ejecute para el runtime user.

Evidencia esperada:

- `evidence/04-openclaw/openclaw-report.md`
- `openclaw-version.txt`
- logs de instalacion/update/daemon.

Bloqueos o parciales comunes:

- `BLOCKED_MISSING_INSTALL_CONFIG_RUN_STAGE_02_FIRST`: falta Stage 02.
- `BLOCKED_RUNTIME_USER_NOT_FOUND`: runtime user no existe.
- `PARTIAL_OPENCLAW_CONFIG_PENDING`: falta `openclaw.json`.
- `PARTIAL_OPENCLAW_DAEMON_RESTART_FAILED`: daemon no reinicio.
- `BLOCKED_ROOT_OWNED_OPENCLAW_PATHS`: paths de OpenClaw quedaron como root.

### 05 - openai-auth

Proposito: autenticar OpenAI/Codex para el usuario nuevo mediante flujo interactivo.

El usuario puede necesitar:

- cuenta ChatGPT/OpenAI del nuevo usuario;
- completar OAuth o login interactivo;
- confirmar con `SI`.

Evidencia esperada:

- `evidence/05-openai-auth/openai-auth-report.md`
- estados como `oauth_login=COMPLETED_INTERACTIVE`, `openai_auth_list=PASS`, `openai_probe=PASS`.

Bloqueos o parciales comunes:

- `BLOCKED_OPENAI_API_KEY_ENV_PRESENT`: hay `OPENAI_API_KEY` en el entorno; este paquete no usa ese fallback silencioso.
- `PARTIAL_OPENAI_AUTH_SKIPPED`: el usuario salto auth.
- `BLOCKED_OPENAI_AUTH_LIST_FAILED`: auth no quedo verificable.
- `PARTIAL_OPENAI_AUTH_PROBE_FAILED`: el probe no paso.

No pegar tokens, URLs privadas de OAuth, device codes, emails ni screenshots privados en ChatGPT.

### 06 - personality

Proposito: crear identidad y personalidad nuevas en el workspace de OpenClaw.

El usuario puede necesitar responder:

- nombre de la IA;
- nombre del usuario principal;
- trato preferido;
- estilo general;
- nivel de critica;
- estilo WhatsApp;
- frases o cosas que debe evitar.

Evidencia esperada:

- `evidence/06-personality/personality-report.md`
- `identity_written=PASS`
- `soul_written=PASS`
- `memory_written=PASS`
- `inherited_identity_scan=PASS`

Bloqueos comunes:

- `BLOCKED_UNSAFE_PERSONALITY_INPUT`: se ingresaron caracteres inseguros.
- `BLOCKED_INHERITED_IDENTITY_SCAN`: se detecto identidad heredada.
- `BLOCKED_WORKSPACE` o runtime faltante.

No usar datos de un asistente anterior.

### 07 - whatsapp

Proposito: configurar WhatsApp desde cero con allowlist y grupos deshabilitados.

El usuario puede necesitar:

- decidir si configura WhatsApp ahora;
- entregar numero propio en formato E.164;
- escanear QR desde WhatsApp - Dispositivos vinculados;
- no pegar QR ni pantallazos.

Evidencia esperada:

- `evidence/07-whatsapp/whatsapp-report.md`
- `owner_phone_valid=PASS`
- `whatsapp_config_written=PASS`
- `dm_policy_allowlist=PASS`
- `group_policy_disabled=PASS`
- `qr_logged_to_evidence=NO`

Bloqueos o parciales comunes:

- `PARTIAL_PASS_WHATSAPP_SKIPPED_BY_USER`: el usuario decidio saltar.
- `BLOCKED_INVALID_OWNER_PHONE_E164`: numero invalido.
- `BLOCKED_WHATSAPP_CHANNEL_ADD_FAILED`: no se pudo agregar canal.
- `PARTIAL_PASS_WHATSAPP_LOGIN_PENDING`: QR/login pendiente.

No copiar `creds.json`, sesiones viejas ni QR viejo.

### 08 - whatsapp-patches

Proposito: aplicar patches controlados de WhatsApp solo si existe un patch pack compatible.

El usuario puede necesitar: nada si no hay patch pack. Si hay patch pack, debe estar auditado y compatible.

Evidencia esperada:

- `evidence/08-whatsapp-patches/whatsapp-patches-report.md`
- Si no hay patch pack: `final_status=PARTIAL_PASS_WHATSAPP_PATCHES_SKIPPED_NO_PATCH_PACK`.

Bloqueos comunes:

- `BLOCKED_WHATSAPP_PATCH_MANIFEST_MISSING`
- `BLOCKED_WHATSAPP_PATCH_MANIFEST_INVALID`
- `BLOCKED_WHATSAPP_PATCH_TARGET_MISSING`
- `BLOCKED_WHATSAPP_PATCH_INCOMPATIBLE`
- `BLOCKED_WHATSAPP_PATCH_DRY_RUN_FAILED`

Limitacion intencional: si no hay patch pack compatible, el tramo queda en `PARTIAL_PASS_WHATSAPP_PATCHES_SKIPPED_NO_PATCH_PACK`. Esto es honesto y esperado; no prometer typing/presence/read receipts avanzados.

### 09 - credentials

Proposito: capturar credenciales opcionales para skills sin escribir valores en evidencia.

El usuario puede necesitar:

- Gemini/Google API key nueva, opcional;
- OpenRouter API key nueva, opcional;
- decidir saltar credenciales para configurarlas despues.

Evidencia esperada:

- `evidence/09-credentials/credentials-report.md`
- `secret_values_written_to_evidence=NO`
- `secrets_file_written=PASS`
- `workspace_env_written=PASS`

Bloqueos o parciales comunes:

- `PARTIAL_PASS_CREDENTIALS_SKIPPED_BY_USER`: no se configuro ninguna.
- `PARTIAL_PASS_CREDENTIALS_PENDING`: input vacio o invalido.
- `BLOCKED_MISSING_INSTALL_CONFIG_RUN_STAGE_02_FIRST`.

No pegar API keys en ChatGPT. Se ingresan solo en la terminal del VPS.

### 10 - skills

Proposito: instalar exactamente seis skills desde fuente local bundled.

Skills esperadas:

- `ontology`
- `total-recall`
- `2nd-brain`
- `mindmap-generator`
- `memory-maintenance`
- `self-improving-agent`

El usuario puede necesitar: confirmar con `SI` la instalacion local de las seis skills.

Evidencia esperada:

- `evidence/10-skills/skills-install-report.md`
- `skills_expected_count=6`
- cada `skill_<nombre>_local_source=PASS`
- `skills_installed_count=6`
- `skills_failed_count=0`

Bloqueos o parciales comunes:

- `BLOCKED_MISSING_BUNDLED_SKILL_<skill>`: falta una skill del bundle.
- `PARTIAL_SKILLS_SKIPPED_BY_USER`: el usuario salto un tramo central; `--resume` debe repetirlo.
- `PARTIAL_SKILLS_INSTALL_INCOMPLETE`: no instalaron todas.

No hay fallback remoto a ClawHub/latest/original.

### 11 - memory-init

Proposito: inicializar memoria vacia en el workspace nativo de OpenClaw.

El usuario puede necesitar: haber completado OpenClaw/workspace.

Evidencia esperada:

- `evidence/11-memory-init/memory-init-report.md`
- `ontology_graph_created_empty=PASS`
- `ontology_graph_empty=PASS`
- `inherited_memory_scan=PASS`

Bloqueos comunes:

- `BLOCKED_WORKSPACE_NOT_FOUND_RUN_STAGE_04_FIRST`
- `BLOCKED_MEMORY_GRAPH_NOT_EMPTY`
- `BLOCKED_INHERITED_MEMORY_SCAN`

No crea recuerdos demo ni importa memoria anterior.

### 12 - automations

Proposito: configurar automatizaciones de skills con marcadores idempotentes en crontab.

El usuario puede necesitar:

- haber instalado skills;
- tener credenciales opcionales si quiere activar automatizaciones dependientes de Gemini/OpenRouter.

Evidencia esperada:

- `evidence/12-automations/skills-automation-report.md`
- `automation_policy=CRONTAB_MARKED_IDEMPOTENT`
- marcadores `ASSISTANT_INSTALLER_TOTAL_RECALL_*` y `ASSISTANT_INSTALLER_MEMORY_MAINTENANCE_*`.

Parciales comunes:

- `PARTIAL_PASS_AUTOMATIONS_MISSING_SCRIPTS`: faltan scripts de skills.
- `PARTIAL_PASS_AUTOMATIONS_PENDING_CREDENTIALS`: faltan credenciales opcionales para automatizaciones.

Un parcial aqui puede ser aceptable si el usuario decidio no configurar ciertas API keys, pero debe quedar declarado.

### 13 - healthcheck

Proposito: validar estado final tecnico del sistema antes del reporte.

Valida, entre otros:

- bash syntax;
- ausencia de ejecutables inesperados;
- ausencia de identificadores heredados;
- usuario runtime;
- OpenClaw;
- config de modelo y auth;
- probe OpenAI;
- WhatsApp allowlist;
- Tailscale/UFW;
- workspace, memoria y skills;
- cron markers.

Evidencia esperada:

- `evidence/13-healthcheck/healthcheck-report.md`
- `evidence/13-healthcheck/bash-n-results.txt`
- `evidence/13-healthcheck/skills-dirs.txt`
- `evidence/13-healthcheck/runtime-crontab.txt`

Estados comunes:

- `PASS`: sin fallas criticas ni warnings.
- `PASS_WITH_WARNINGS`: sin fallas criticas, pero con advertencias.
- `FAIL`: hay fallas criticas.
- `BLOCKED_LIVE_GATE` o `BLOCKED_ROOT_REQUIRED`.

No declarar instalacion final completa si Stage 13 no se ejecuto o no paso.

### 14 - final-report

Proposito: generar reporte final y documentos de estado en el assistant home.

El usuario puede necesitar: haber corrido Stage 13.

Evidencia esperada:

- `evidence/14-final-report/final-report-stage.md`
- `/home/<runtime_user>/assistant/INSTALLATION_REPORT.md`
- `/home/<runtime_user>/assistant/docs/ASSISTANT_VPS_STATE.md`
- `/home/<runtime_user>/assistant/docs/SKILLS_STATE.md`

Estados comunes:

- `PASS`: healthcheck paso y no hay parciales/bloqueos previos.
- `PARTIAL_PASS_FINAL_REPORT_HEALTHCHECK_NOT_RUN`: Stage 13 no corrio.
- `PARTIAL_PASS_FINAL_REPORT_HEALTHCHECK_NOT_PASS`: Stage 13 no paso.
- `PARTIAL_PASS_FINAL_REPORT_PREVIOUS_STAGE_BLOCKED_OR_FAILED`: hay bloqueos/fallos previos.
- `PARTIAL_PASS_FINAL_REPORT_PREVIOUS_STAGE_PARTIALS`: hay parciales previos.

El reporte final no debe ocultar parciales anteriores.

## 10. Credenciales e inputs del usuario

Inputs generales que pueden pedirse durante la instalacion:

- nombre del usuario runtime;
- SSH public key nueva para el runtime user, opcional;
- nombre nuevo de la IA;
- nombre del usuario principal;
- preferencias de trato y estilo;
- OpenAI/Codex auth mediante login/OAuth interactivo;
- numero WhatsApp propio en formato E.164;
- QR de WhatsApp escaneado localmente, nunca compartido con ChatGPT;
- allowlist WhatsApp para el numero del usuario;
- Gemini/Google API key opcional;
- OpenRouter API key opcional.

Reglas:

- no usar credenciales heredadas;
- no usar sesiones heredadas;
- no usar `OPENAI_API_KEY` como fallback silencioso para OpenAI/Codex;
- no pegar valores de API keys en ChatGPT;
- no guardar valores secretos en evidence;
- si una credencial se expone por error, rotarla.

## 11. Skills bundled

El paquete incluye exactamente estas seis skills:

- `ontology`
- `total-recall`
- `2nd-brain`
- `mindmap-generator`
- `memory-maintenance`
- `self-improving-agent`

Estas skills estan bundled como fuente local generica. No son especificas de Roy, no deben arrastrar memoria viva y no deben instalarse desde latest remoto como fallback.

Politica:

- fuente local bundled;
- instalacion oficial via `openclaw skills install <local_path> --as <slug>`;
- memoria inicial vacia;
- sin credenciales empaquetadas;
- sin fallback remoto.

## 12. Reglas de seguridad

Reglas duras:

- No compartir secretos.
- No compartir API keys.
- No compartir tokens OAuth o refresh tokens.
- No compartir cookies.
- No compartir QR.
- No compartir `.env`, `creds.json`, `secrets.json` ni auth stores.
- No compartir llaves SSH privadas.
- No heredar identidad de otra instalacion.
- No copiar sesiones WhatsApp.
- No abrir SSH ampliamente.
- No exponer OpenClaw como API publica si el paquete no lo configura asi.
- No hacer cambios reales en VPS sin confirmacion del usuario y sin entender el tramo.
- No declarar `PASS` falso.
- Logs y evidence no deben filtrar secretos.

Si hay duda entre avanzar o bloquear, bloquear y pedir evidencia no sensible.

## 13. Limitaciones conocidas e intencionales

- Stage 08 puede quedar en `PARTIAL_PASS_WHATSAPP_PATCHES_SKIPPED_NO_PATCH_PACK` si no existe un patch pack compatible. Esto es esperado.
- Validaciones locales/mock no equivalen a una instalacion E2E real en VPS limpio.
- OAuth real requiere accion manual del usuario.
- QR de WhatsApp requiere accion manual y disponibilidad del canal.
- Tailscale depende de disponibilidad del proveedor y login del usuario.
- OpenClaw depende de instalador/proveedor externo y red.
- Gemini/OpenRouter son opcionales y dependen de credenciales propias del usuario.
- Un `PASS_WITH_WARNINGS` no es lo mismo que un `PASS` limpio.

## 14. Flujo E2E recomendado en un VPS limpio

Secuencia sugerida:

1. Subir al VPS los cuatro archivos compartibles.
2. Verificar checksum externo:

```bash
sha256sum -c assistant-vps-base-0.2.13-live-ready.tar.gz.sha256
```

3. Extraer:

```bash
tar -xzf assistant-vps-base-0.2.13-live-ready.tar.gz
cd assistant-vps-base-0.2.13-live-ready
```

4. Leer este README y el `README.md` del paquete.
5. Validar `VERSION` y `CHECKSUMS.sha256`.
6. Listar tramos:

```bash
bash install.sh --list
```

7. Ejecutar Stage 00:

```bash
bash install.sh --run 00
```

8. Entregar a ChatGPT el `final_status` y checks no sensibles.
9. Ejecutar Stage 01:

```bash
bash install.sh --run 01
```

10. Si Stage 01 es `PASS`, continuar con tramos mutantes, uno a uno:

```bash
sudo AVB_LIVE=1 bash install.sh --run 02
sudo AVB_LIVE=1 bash install.sh --run 03
sudo AVB_LIVE=1 bash install.sh --run 04
sudo AVB_LIVE=1 bash install.sh --run 05
sudo AVB_LIVE=1 bash install.sh --run 06
sudo AVB_LIVE=1 bash install.sh --run 07
sudo AVB_LIVE=1 bash install.sh --run 08
sudo AVB_LIVE=1 bash install.sh --run 09
sudo AVB_LIVE=1 bash install.sh --run 10
sudo AVB_LIVE=1 bash install.sh --run 11
sudo AVB_LIVE=1 bash install.sh --run 12
sudo AVB_LIVE=1 bash install.sh --run 13
sudo AVB_LIVE=1 bash install.sh --run 14
```

11. Tambien se puede usar resume despues de cada PASS:

```bash
sudo AVB_LIVE=1 bash install.sh --resume
```

12. Detenerse ante cualquier `BLOCKED*`, `FAILED`, `FAIL` o parcial no entendido.
13. No emitir reporte final como full PASS hasta revisar Stage 14 y Stage 13.

## 15. Troubleshooting

### Checksum externo no coincide

No instalar. Volver a transferir el tarball y `.sha256`. Verificar que el `.sha256` corresponde a `0.2.13`.

### `CHECKSUMS.sha256` interno falla

No ejecutar `install.sh`. El paquete extraido no coincide con el manifest interno.

### OS no soportado

Si Stage 01 devuelve `BLOCKED_UNSUPPORTED_OS`, no forzar. Este release espera Ubuntu con apt/systemd.

### Ya existe OpenClaw

Si Stage 01 devuelve `BLOCKED_EXISTING_OPENCLAW_INSTALLATION`, detenerse. El paquete esta pensado para VPS limpio. No mezclar con una instalacion existente sin un task de migracion especifico.

### Faltan skills

Si Stage 10 o 13 reporta skills faltantes, revisar que el tarball fue extraido completo y que `CHECKSUMS.sha256` paso. No instalar desde remoto como parche rapido.

### OpenAI/Codex auth no confirmado

Si Stage 05 queda parcial o bloqueado, repetir Stage 05 cuando el usuario pueda completar OAuth. No usar `OPENAI_API_KEY` como fallback silencioso.

### WhatsApp QR o sesion no vinculada

Si Stage 07 queda en `PARTIAL_PASS_WHATSAPP_LOGIN_PENDING`, repetir Stage 07 y escanear un QR nuevo. No pegar QR en ChatGPT. No copiar sesiones viejas.

### Healthcheck no pasa

Leer:

```text
evidence/13-healthcheck/healthcheck-report.md
```

Separar fallas criticas de warnings. No correr Stage 14 como full PASS si Stage 13 no paso.

### Final report parcial

Si Stage 14 devuelve `PARTIAL_PASS_FINAL_REPORT_*`, el instalador esta diciendo que falta algo. Leer:

```text
evidence/14-final-report/final-report-stage.md
evidence/14-final-report/previous-stage-statuses.txt
```

Resolver el tramo anterior correspondiente antes de declarar cierre completo.

## 16. Politica de decision final

Reglas para declarar estado:

- `PASS` local de auditoria significa candidato local, no PASS vivo final.
- El audit local de `0.2.13` esta cerrado.
- La siguiente fase es una instalacion E2E en VPS limpio.
- Solo se puede declarar PASS vivo final despues de Stage 14, siempre que Stage 13 haya pasado y no existan parciales/bloqueos/fallos previos.
- Un `PARTIAL_PASS_*` puede ser honesto y aceptable, pero debe quedar declarado como parcial.
- Un `BLOCKED_*` o `FAILED` detiene el avance.
- Si no hay evidencia, no hay PASS.

Cuando detenerse y pedir ayuda:

- checksum mismatch;
- OS no soportado;
- OpenClaw ya instalado;
- se detecta identidad/credencial heredada;
- Tailscale no confirma segunda via;
- OAuth no puede completarse;
- QR WhatsApp no vincula;
- skills no instalan 6/6;
- healthcheck tiene fallas criticas;
- el usuario no entiende un parcial o bloqueo.

La forma segura de pedir ayuda es copiar `evidence/chatgpt-help-context.md` y el `final_status` del tramo, revisando antes que no contenga secretos.
