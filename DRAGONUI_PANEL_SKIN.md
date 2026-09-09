# DragonUI - Panel Skin Memoria

Guía de trabajo para dar a las ventanas/paneles de Blizzard el diseño DragonUI
(chrome metal + pestañas + retrato), manteniendo el contenido 100 % nativo.
Objetivo: no repetir diagnósticos; cada implementación sigue este flujo y este
checklist.

## 1. Patrón de trabajo (por cada ventana)

1. **Inventariar el frame** en juego:
   - Usar `/dragonui panel <FrameName>` (p. ej. `/dragonui panel CharacterFrame`)
     → imprime regions (layer/tex/size/pt) y **hijos** (nombre/tipo/texto)
     de forma segura y acotada. Si no se conoce el frame, `/dragonui debug on`
     + abrir la ventana para capturar el dump de apertura existente.
2. **Confirmar con el usuario** qué elementos tiene la ventana:
   - pestañas inferiores (texto) sí/no,
   - pestañas laterales (iconos) sí/no,
   - retrato sí/no,
   - botón cerrar.
3. **Aplicar por capas incremental**:
   a. `AddPanelChrome(frame, { noBg = true })` + cerrar a `TOPRIGHT(1,0)`
      → validar primero que el ring sea visible (si el contenido lo tapa, usar
      un hijo `SkinLayer` con `EnableMouse(false)`).
   b. Pestañas inferiores (texto): art `uiframetabs` con piezas
      `Left/Middle/Right`, fuentes custom (`TAB_*`), ancho
      `textW + TAB_PAD_H*2` (mín `TAB_MIN_WIDTH`), gap `TAB_GAP`.
   c. Pestañas laterales (icono): marco `sidetab` 64 px centrado en el icono
      con `SIDE_TAB_DX/DY`.
   d. Retrato: conservar el vanilla si carga bien; si es placeholder verde,
      **no usar** (pedir/incrustar asset propio grande).
4. **Hooks robustos** (nunca watchdog de redimensión):
   - `hooksecurefunc` de la **función de update nativa** de la ventana,
   - instalación perezosa tras `PLAYER_LOGIN` (el frame puede no existir aún),
     con retry breve y hook al toggle/tecla de apertura si hace falta.
   - Los anchos/gap se re-aplican en ese hook post-layout nativo.
5. **No tocar el contenido**:
   - sin `StripTextures` del área de contenido/páginas,
   - sin fondo rock propio (`noBg`),
   - `pcall` en cada operación.

## 2. Checklist de verificación en juego

> Cualquier cambio de textura se valida con **reinicio completo del cliente**.

- [ ] Marco metal visible y **sin doble marco** nativo encima.
- [ ] Cerrar pegado a `TOPRIGHT(1,0)` con arte `redbutton2x`.
- [ ] Pestañas inferiores: tamaño y separación **idénticos al libro** (`TAB_*`).
- [ ] **Sin huecos** por pestañas ocultas (p. ej. "Pet" sin mascota): al re-encadenar
      las pestañas, saltar las que no estén visibles (`IsShown()`).
- [ ] Al activar otra pestaña: ancho/separación no revierten a vanilla y no hay
      parpadeo/crece-decrece.
- [ ] Hover en pestañas: **solo el texto** reacciona (sin halo azul de borde).
- [ ] Pestañas laterales: marco alineado al icono (offsets calibrados), activo
      dorado nativo (no azul tenue).
- [ ] Retrato: no verde, no ocultar el icono.
- [ ] Reabrir la ventana y cambiar de sección/espec: el skin persiste.
- [ ] Sin errores Lua ni taint (todo bajo `pcall`; usar `hooksecurefunc`).

## 3. Paneles a implementar (orden sugerido)

| # | Panel | Frame(s) global | Cómo se abre |
|---|-------|-----------------|--------------|
| 1 | **Libro de hechizos** | `SpellBookFrame` | `P` — Estado: hecho/ajustado |
| 2 | **Talentos** | `PlayerTalentFrame` | `N` — Estado: en curso |
| 3 | **Personaje / Paperdoll** | `CharacterFrame` / `PaperDollFrame` | `C` |
| 4 | **Diario de misiones** | `QuestLogFrame` | `L` |
| 5 | **Mascotas / Pet Journal** | `PetJournalParent` (+ pestañas `PetJournalParentTab1..N` = "Mounts"/"Pet Journal") | tecla mascotas / `TogglePetJournal` — Estado: hecho/ajustado |
| 6 | **Monturas (Mount Journal)** | contenido `MountJournal` dentro de la pestaña "Mounts" de `PetJournalParent` | pestaña de Pet Journal — Estado: incluido con #5 |
| 7 | **Buscador de mazmorras / Dungeon Finder** | `PVEFrame` (hijos: sidebar `GroupFinderFrame`, paneles `LFDParentFrame`/`RaidFinderFrame`/`ScenarioFinderFrame`/`FlexRaidFrame`) | `i` — Estado: hecho/ajustado |
| 8 | **Player vs Player (JcJ)** | `PVPUIFrame` (contenido `PVPQueueFrame`; sidebar nativo `PVPQueueFrameCategoryButton1..3`, subpaneles Honor/Conquest/WarGames) | `H` — Estado: hecho (marco+cerrar; sin pestañas) |
| 9 | **Social / Amigos** | `FriendsFrame` (+ `FriendsFrameTab1..4`; sub-pestañas de cabecera `FriendsTabHeaderTab1..3` **nativas**) | `O` — Estado: hecho/ajustado |
| 10 | **Hermandad** | `GuildFrame` (+ `GuildFrameTab1..5`; sub-pestañas de Info `GuildInfoFrameTab1..3` **nativas**) | `G` — Estado: hecho/ajustado |
| 11 | **Monedas / divisas** | (subpanel de CharacterFrame) | pestaña de Personaje |
| 12 | **Buzón** | `MailFrame` (+ `MailFrameTab1..2` Inbox/Send Mail; retrato `Mail-Icon`) | ícono de correo — Estado: hecho/ajustado |
| 13 | **Guías (mazmorras/bandas)** | `EncounterJournal` | `J` (si lo usas) — Estado: hecho (solo marco+cerrar; selector Dungeons/Raids, sidebar de jefes y contenido nativos) |
| 14 | **Inspeccionar a otro jugador** | `InspectFrame` (+ `InspectFrameTab1..4` Character/PvP/Talents/Guild) | clic derecho → inspeccionar — Estado: hecho/ajustado |
| 15 | **Macro** | `MacroFrame` (tabs de ventana y contenido nativos; solo marco+cerrar) | `M` (si lo usas) — Estado: hecho/ajustado |
| 16 | **Profesiones** | `TradeSkillFrame` (sin pestañas de ventana; solo marco+cerrar; contenido/popup `TradeSkillGuildFrame` nativos) | abrir una profesión — Estado: hecho/ajustado |

> El panel **Achievements / Logros (`Y`) queda fuera** por decisión del usuario.
> En MoP (5.4.8) no existe `MountJournal` nativo; las monturas viven dentro del
> **Pet Journal** (pestaña Monturas de `PetJournalParent`). La fila 6 se mantiene
> por si tu cliente/repack expone `MountJournal` y hay que confirmar con un dump.

## 4. Convenciones / constantes compartidas

- **Assets** (`DragonUI/Textures/UI/`): `uiframemetal2x`,
  `uiframemetalhorizontal2x`, `uiframemetalvertical2x`, `ui-background-rock`,
  `uiframetabs`, `sidetab`, `redbutton2x`, `buttonhilight-square`.
  `PortraitRing.tga` (anillo simétrico propio, dorado #FFD100, centro
  transparente, inner≈0.70) queda **guardado** para la reimplementación futura
  del retrato — no se borra ni se usa por ahora.
- **Constantes** (en `modules/blizzardart.lua`):
  - `TAB_TEXT_SIZE`, `TAB_PAD_H`, `TAB_GAP`, `TAB_MIN_WIDTH`,
  - `SIDE_TAB_DX`, `SIDE_TAB_DY`,
  - sub-pestañas: `COMPACT_SUBTABS` (false = opción A, tamaño normal; true =
    arte+fuente reducidos), `SUB_TAB_H`, `SUB_TAB_FONT`.
  - toggle `HIDE_VANILLA_FRAME_ART` (default off).
- **Colores de texto de pestañas**: activa = blanco, inactiva = dorado #FFD100
  (según el cliente; verificar siempre, puede estar invertido).
- Cada ventana nueva se añade en `blizzardart.lua` reutilizando
  `AddPanelChrome`, `SkinTab`, `ApplyTabPaddingWidth`, `SkinSpellBookSideTabs`
  (patrón side), `TEX.*` y `GetChildFrames`.

## 5. Lecciones aprendidas (trampas)

- **Regla de validación de texturas: reinicio COMPLETO del cliente.** Cambiar o
  añadir archivos de textura (`.tga`/`.blp`) y validar con `/rl` puede mostrar
  caché viejo o estados mezclados (anillos fantasma, cuadrado verde, etc.).
  Siempre: copiar archivos → reiniciar el juego → validar.
- **El dump de regiones imprime texturas aunque estén ocultas** (`Hide()`):
  un frame "limpio" en el dump no garantiza que la textura no se vea.
- **No superponer DragonUI sobre el marco nativo sin comprobar capas** (puede
  quedar oculto bajo el contenido).
- **Nada de watchdog de redimensión** (parpadea): hookear el update nativo.
- **Encadenar solo pestañas visibles** al reposicionar (`IsShown()`): los tabs
  ocultos que existen (p. ej. "Pet" sin mascota) abren huecos en la fila.
- **Los `local` deben declararse antes de usarse** o Lua los toma como globales
  (nil) → `attempt to call global ...`.
- En este cliente el blend `ADD` ignora el alpha del vertex: premultiplicar RGB
  (`r*a, g*a, b*a`).
- `noBg`: nunca añadir fondo rock propio ni `StripTextures` del contenido.
- Los frames de algunas ventanas se crean tarde: hooks perezosos + retry corto
  tras login y hook al toggle de apertura.
- Antes de skinar pestañas/retrato de una ventana nueva, mapear con el dump
  (nombres reales: p. ej. `PlayerTalentFrameTab1..4`,
  `PlayerTalentFrame_UpdateTabs`).
- **Política (2026-09): las pestañas/sub-tabs INTERNAS de contenido NO se
  skinan** (p. ej. `GuildInfoFrameTab1..3`, `FriendsTabHeaderTab1..3`,
  `MacroFrameTab1..2`, selector del EJ). Solo las pestañas de **ventana**
  (bottom/top del frame) reciben el diseño. Se revirtió el skin de
  GuildInfoFrameTab y FriendsTabHeaderTab; la variante compacta
  (`COMPACT_SUBTABS`) queda sin uso activo.
- **La "ventana visible" no siempre es el frame obvio.** En LFG/PvE la ventana
  real es `PVEFrame` (título, `Portrait2`, `UI-Frame` y `CloseButton`); el
  nombre sugerente `LFDParentFrame` resultó ser un **contenedor interno
  centrado** bajo el título (chrome ahí = retrato en el centro, no en la esquina).
  Elegir el host del chrome con el dump, no por el nombre.
- **Las pestañas de texto de una ventana suelen ser hijos directos del frame
  raíz** (p. ej. `PVEFrameTab1..N` = "Dungeon Finder"/"Challenges"), fuera del
  contenedor de contenido; si el scan desde un contenedor interno no las
  encuentra, quedan vanilla.
- **No todo "tab" usa piezas `Left/Middle/Right`**: el selector del Encounter
  Journal (Raids/Dungeons) usa arte propio segmentado de
  `UI-EncounterJournalTextures` → `SkinTab` no lo toca; decidimos dejarlo
  nativo. Confirmar con dump antes de asumir que SkinTab aplicará.
- **Boss-tiles del EJ** (`...InfoBossTab<N>`, iconos verticales): se intentó
  aplicar el marco `sidetab` del libro pero **se descartó** (difícil de lograr
  limpio por su creación dinámica/estructura). EncounterJournal queda solo con
  marco+cerrar; no re-intentar sin un plan de re-skin bien verificado.
- **Header UX del EJ (título a la izquierda + Select a la derecha) se probó y
  REVIRTIÓ (2026-09)** por decisión del usuario (no se veía robusto).
- **Pestañas de ventana "Dungeons/Raids" del EJ (clonar los internos ocultos +
  `CreateDragonUITabButton`) también REVIRTIERON (2026-09)**: estados
  activo/inactivo nunca quedaron fiables (problemas persistentes con
  `SetActive`, arte y detección nativa). EncounterJournal vuelve a su barra
  segmentada ORIGINAL nativa (solo marco+cerrar DragonUI). No re-intentar
  construir tabs custom para EJ sin un plan mucho más simple; si se quiere
  reposicionar, preferir mover los botones nativos (estados nativos), no clonar.
- **Retrato: reset del experimento (2026-09).** El anillo propio encima del
  retrato se intentó con overlay `host` (frame level alto, `EnableMouse(false)`)
  y dio problemas: un "mini anillo" desplazado (adorno horneado de la esquina
  metal `uiframemetal2x`, que NO tiene esquina TL sin anillo) y luego un anillo
  gigante difuminado que parecía **adherirse y rotar entre ventanas** según el
  orden de apertura. Se hizo reset completo del retrato:
  - **Se conserva** (validado y funcionando): `AddPanelChrome` completo (marco
    metal + esquina TL con su anillo original + bordes), botón cerrar
    `TOPRIGHT(1,0)` con `redbutton2x`, pestañas inferiores (`uiframetabs`),
    pestañas laterales (`sidetab`), fuentes y espaciado `TAB_*`/`SIDE_TAB_*`,
    contenido 100 % nativo, hooks perezosos.
  - **Se eliminó**: `SkinPortraitRing`/`FindPortraitTexture`/`IsDragonUITex`,
    toggles `USE_PORTRAIT_RING`/`PORTRAIT_RING_SIZE`, opción `noTopLeft` de
    `AddPanelChrome`, ocultación de la corona vanilla 70–90 `UI-Frame`.
  - `PortraitRing.tga` se conserva en repo para retomarlo.
  - **Para reimplementar el retrato limpio** (cuando se retome): un panel a la
    vez; anillo de ancla/posición por constantes explícitas por ventana; validar
    SIEMPRE con reinicio completo; verificar que el overlay no pueda quedar
    huérfano (parentearlo SIEMPRE al frame objetivo, nunca suelto) y que no se
    cree más de una vez por ventana.
