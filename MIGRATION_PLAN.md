# DragonUI MoP 5.4.8 — Plan de migración

## Estado

En progreso. Este archivo documenta los bugs encontrados y los fixes planificados/aplicados para la migración de DragonUI de WoW 3.3.5a a Mists of Pandaria 5.4.8.

## Changelog

### 2026-08-09 — Paso 1 aplicado
- `DragonUI/modules/actionbars/extrabar.lua`: registrados `UNIT_POWER`/`UNIT_MAXPOWER` + aliases WotLK; handler usa tabla `PLAYER_POWER_EVENTS`.
- `DragonUI/modules/unitframes/player.lua`: `POWER_EVENTS` actualizada, filtro por `powerType`, fake `UNIT_MANA` reemplazado por `UnitFrameManaBar_Update`, eliminados `UNIT_HAPPINESS`.
- `DragonUI/modules/unitframes/target_style.lua`: `POWER_EVENTS` actualizada y filtro por `powerType` en el handler.
- `DragonUI/modules/nameplates/pipeline/engine.lua`: registrados `UNIT_POWER`/`UNIT_MAXPOWER` + aliases WotLK; handler usa tabla `PLATE_POWER_EVENTS`.

### 2026-08-09 — Paso 2 aplicado
- `DragonUI/modules/buff_frame.lua`: todas las referencias a `ConsolidatedBuffs` protegidas; loops de `TempEnchant` cambiados a 1..2; `GetEnchantSlack` usa `GetWeaponEnchantInfo`; anclas raíz cambiadas a `BuffFrame`; hook de `DebuffButton_UpdateAnchors` guardado.
- `DragonUI/modules/auraborders.lua`: `MAX_TEMP_ENCHANTS` cambiado a 2; hook de `BuffFrame_Update` reemplazado por `BuffFrame_UpdateAllBuffAnchors`.
- `DragonUI/database.lua`: posición por defecto de `weapon_enchants` ajustada a `(-270, -170)`.

### 2026-08-09 — Paso 3 aplicado
- `DragonUI/modules/micromenu.lua`: lista de botones actualizada a MoP (`GuildMicroButton`, `EJMicroButton`, `StoreMicroButton`, `MainMenuMicroButton`); eliminada lógica de Ascension y botón PVP; indicador de latencia movido a `MainMenuMicroButton`; atlas de `EncounterJournal` agregado como placeholder.
- `DragonUI/modules/mainbars.lua`: `MICROMENU_BUTTON_NAMES` y `MAINBAR_PROTECTED_CHILD_NAMES` actualizados.
- `DragonUI/modules/darkmode.lua`: `microNames` actualizado.
- `DragonUI/modules/chatmods.lua`: `FriendsMicroButton` reemplazado por `GuildMicroButton`.
- `DragonUI/utils/atlas.lua`: agregadas entradas placeholder en escala de grises para `guild`, `store` y `ej`.

### 2026-08-09 — Paso 4 aplicado
- `DragonUI/modules/questtracker.lua`: agregada guarda defensiva para `WatchFrame`; `GetNumAutoQuestPopUps` incluido en el conteo de contenido; descubrimiento dinámico de `WatchFrameLinkButton`; `WATCHFRAME_NUM_ITEMS` usado en lugar de hardcodear 40; `WatchFrameScenarioFrame` incluido en el hit rect; `WatchFrameItem_UpdateCooldown` reemplazado por `hooksecurefunc`; hooks críticos envueltos en `pcall`; `GetPoint(1)` hecho defensivo.

### 2026-08-09 — Fix de inspect (item level / quality)
- `DragonUI/modules/itemlevel.lua`: `INSPECT_TALENT_READY` reemplazado por `INSPECT_READY`; comparación de GUIDs; tracking de `currentInspectGUID`; hook `InspectFrame_UnitChanged` para retargeting.
- `DragonUI/modules/itemquality.lua`: mismo fix de evento de inspect; agregado `inspectDataReady` para evitar datos stale.

### 2026-08-09 — Fix de party frames
- `DragonUI/modules/unitframes/party.lua`: eventos legacy `PARTY_MEMBERS_CHANGED`/`RAID_ROSTER_UPDATE` reemplazados por `GROUP_ROSTER_UPDATE`; `PARTY_MEMBER_ENABLE/DISABLE` reemplazados por `UNIT_CONNECTION`; agregados `UNIT_HEALTH_FREQUENT`/`UNIT_MAXHEALTH`; limpiados checks de `CUF_CVar`.

### 2026-08-09 — Fix de atlas nativo
- `DragonUI/modules/unitframes/uf_layers_deps.lua`: guarda `SetAtlas` para evitar error en MoP 5.4.8.

### 2026-08-09 — Fix de Bagster variable naming
- `DragonUI/modules/bagster/bagster_classes.lua`: corregidos nombres `level`/`ilvl` a `itemLevel`/`reqLevel` para reflejar el orden real de `GetItemInfo`.

### 2026-08-09 — Fix de modo edición / menú contextual de unidades
- `DragonUI/core/api.lua`: los overlays de editor ahora se ocultan físicamente (`:Hide()`) cuando el editor está apagado; los handlers `OnDragStart`/`OnDragStop`/`OnMouseDown` están gateados por `EditorMode:IsActive()`.
- `DragonUI/core/editor_panel.lua`: `SelectEditorFrame` retorna si el editor no está activo.
- Esto debería restaurar el clic derecho en player/target/focus/party para abrir el menú de unidad (Inspect, Trade, Whisper, etc.).

### 2026-08-09 — Fix de inspect item level / quality
- `DragonUI/modules/itemquality.lua`: corregido syntax error (`end` extra) que impedía que el módulo cargara.
- `DragonUI/modules/itemlevel.lua`: `inspectDataReady` ahora se setea correctamente en el handler de `INSPECT_READY`.
- `DragonUI/modules/itemlevel.lua`: el holder del texto de promedio de item level ahora se muestra (`holder:Show()`).
- `DragonUI/modules/itemquality.lua`: reemplazados hooks WotLK (`InspectPaperDollItemSlotButton_Update`, etc.) por `InspectFrame:HookScript("OnShow/OnHide")` y `INSPECT_READY`, compatibles con MoP 5.4.8.
- `DragonUI/modules/itemlevel.lua`: ajustado `AVERAGE_Y_OFFSET.inspect` de `0` a `24` para evitar que el promedio se solape con los slots de armas.

### 2026-08-09 — Fix de errores Lua en mainbars / micromenu / pet / small_frame
- `DragonUI/modules/mainbars.lua`: protegido `BonusActionButtons` contra nil (no existe en MoP 5.4.8).
- `DragonUI/modules/micromenu.lua`: protegidas todas las referencias a `KeyRingButton` dentro del handler de `BAG_UPDATE` (el llavero fue removido en MoP).
- `DragonUI/modules/unitframes/pet.lua`: corregido `SetDrawLayer("OVERLAY", 9)` a sublevel `7` (rango válido en MoP).
- `DragonUI/modules/unitframes/pet.lua`: corregido `SetDrawLayer("OVERLAY", 10)` a sublevel `7` para `PetFrameFlash`.
- `DragonUI/modules/unitframes/small_frame.lua`: corregido `SetDrawLayer("OVERLAY", 11)` a sublevel `7` para el indicador elite.

### 2026-08-09 — Fix de hooksecurefunc en mainbars (XP/Rep bar)
- `DragonUI/modules/mainbars.lua`: protegidos los hooks a `MainMenuExpBar_Update` y `ReputationWatchBar_Update` con `type(...) == "function"`.
- `MainMenuExpBar_Update` no existe en MoP 5.4.8; el código ya actualiza la barra vía eventos (`PLAYER_XP_UPDATE`, `UPDATE_EXHAUSTION`, `UPDATE_FACTION`, etc.).

### 2026-08-09 — Fix de CLEU en nameplates y sublevel en target
- `DragonUI/modules/nameplates/features/castbar.lua`: `ParseCombatLogSpellEvent` ahora soporta el formato CLEU de MoP 5.4.8 (`sourceFlags, sourceRaidFlags, destGUID, destName, destFlags, destRaidFlags, spellId, spellName`) además de las variantes WotLK.
- `DragonUI/modules/unitframes/target.lua`: corregido `SetDrawLayer("ARTWORK", 10)` a sublevel `7` para `TargetFrameFlash`.

### 2026-08-09 — Fix de GUIDs hexadecimales en CLEU y UnitCastingInfo/UnitChannelInfo
- `DragonUI/modules/nameplates/features/castbar.lua`: detección de GUIDs MoP corregida a `^0x%x+$` (los GUIDs de MoP son hexadecimales, no `Player-`/`Creature-`).
- `DragonUI/modules/castbar.lua`: unpacks de `UnitCastingInfo`/`UnitChannelInfo` ajustados al formato WotLK 9 valores que devuelve este servidor (`name, rank, displayName, icon, startTime, endTime, ...`).
- `DragonUI/modules/nameplates/features/castbar.lua`: mismos ajustes de unpacks para `UnitCastingInfo`/`UnitChannelInfo`.
- `DragonUI/modules/unitframes/unitframe_layers.lua`: ajustado unpack de `UnitCastingInfo` al mismo formato WotLK 9 valores.

### 2026-08-09 — Fix de visibilidad de textos de vida/mana en unitframes
- `DragonUI/modules/unitframes/text_system.lua`: los `FontString` ahora tienen fallback a `GameFontNormal` si `TextStatusBarText` no produce fuente válida.
- `DragonUI/modules/unitframes/text_system.lua`: forzado color blanco, alpha 1 y draw layer `OVERLAY` sublevel 7 en cada actualización para evitar que queden tapados.
- `DragonUI/modules/unitframes/text_system.lua`: `SetupFrameTextSystem` llama `updateCallback()` inmediatamente después de crear los elementos.

### 2026-08-09 — Fix de SendAddonMessage en battleground
- `DragonUI/modules/versioncheck.lua`: eliminado envío a canal `BATTLEGROUND` (no válido en MoP 5.4.8); los jugadores en BG ya están cubiertos por el envío `RAID`.
- `DragonUI/modules/versioncheck.lua`: agregada validación de canales permitidos en `SendVersion` para evitar errores futuros.

### 2026-08-09 — Fix de UI de opciones, micromenu, minimapa y menú del juego
- `DragonUI_Options/libs/AceGUI-3.0/widgets/AceGUIWidget-Button.lua`, `AceGUIWidget-Keybinding.lua`, `AceGUIWidget-MultiLineEditBox.lua`: reemplazado `UIPanelButtonTemplate2` (no existe en MoP) por `UIPanelButtonTemplate`.
- `DragonUI/modules/micromenu.lua`: agregados pre-hooks a `SetNormalTexture`/`SetPushedTexture`/`SetHighlightTexture`/`SetDisabledTexture` de cada botón no-Character para interceptar restauraciones nativas en colored mode.
- `DragonUI/modules/micromenu.lua`: hook a `UpdateMicroButtons` reforzado para re-aplicar texturas colored.
- `DragonUI/modules/minimap.lua`: agregado `QueueStatusMinimapButton` a `BLIZZARD_MINIMAP_BUTTONS` para evitar que el collector lo trate como botón de addon.
- `DragonUI/modules/minimap.lua`: creada `StyleQueueStatusButton()` para el botón unificado de cola BG/LFG de MoP 5.4.8; asignado `OnClick` para abrir la tabla de estadísticas de BG (`ToggleWorldStateScoreFrame`) cuando el jugador está en una battleground.
- `DragonUI/modules/minimap.lua`: corregido `StylePVPBattlefieldFrame` para crear texturas si no existen tras `SetNormalTexture('')`.
- `DragonUI/modules/gamemenu.lua`: botón "DragonUI" del menú del juego ahora usa colores rojos en lugar de azules.

### 2026-08-09 — Fix de iconos del minimapa (quest / correo / tracking)
- `DragonUI/modules/minimap.lua`: default de `blip_skin` cambiado a `false` para usar la textura nativa `Interface\Minimap\ObjectIcons` en MoP 5.4.8.
- El atlas custom de DragonUI no incluye los iconos de quest POI (interrogaciones/admiraciones) que MoP renderiza desde `ObjectIcons`, por lo que con la textura custom aparecían figuras geométricas.

### 2026-08-10 — Botón PVP custom, fix de colored icons y placeholder de Collections
- `DragonUI/modules/micromenu.lua`: creado `DragonUIPVPMicroButton` como botón custom para MoP (el botón nativo `PVPMicroButton` fue removido en 5.x).
- `DragonUI/modules/micromenu.lua`: el botón PVP custom se inserta antes de `StoreMicroButton`, usa la textura de facción `Micromenu/micropvp.blp` y abre el panel **Player vs. Player** (`TogglePVPFrame()`).
- `DragonUI/modules/micromenu.lua`: arreglado colored mode para `GuildMicroButton` y `LFDMicroButton` recreando las texturas de estado después de `texture_strip()`.
- `DragonUI/modules/micromenu.lua`: `CollectionsMicroButton` ahora usa un placeholder `INV_Misc_QuestionMark` hasta que se diseñe un icono propio.
- `DragonUI/modules/micromenu.lua`: el hook de `UpdateMicroButtons` ahora re-aplica `SetupPVPButton` al botón PVP custom y salta `Collections` para no sobrescribir el placeholder.
- `DragonUI/modules/minimap.lua`: `StyleQueueStatusButton()` ahora fuerza visibilidad del botón, establece alpha 1 y re-ancla el botón cerca del minimapa (`BOTTOMLEFT`, 0, 18) para evitar que quede oculto tras el reposicionamiento de `MinimapCluster`.

### 2026-08-10 — Fix del ojo de mazmorra/cola (QueueStatusMinimapButton)
- `DragonUI/modules/micromenu.lua`: eliminado el `SetScript('OnClick', ...)` sobre `QueueStatusMinimapButton` para evitar que el módulo de micromenú sobrescriba el handler del minimapa.
- `DragonUI/modules/minimap.lua`: revertido el re-anchore forzado a `Minimap`; ahora solo se fuerza visibilidad y alpha del botón y del ojo, preservando el anclaje nativo de Blizzard.
- `DragonUI/modules/minimap.lua`: el handler `OnClick` ahora está envuelto en `pcall` con fallback a dropdown (right-click) y a `PVEFrame`/`PVPFrame` (left-click).
- `DragonUI/modules/minimap.lua`: añadido un event frame que escucha `LFG_QUEUE_STATUS_UPDATE`, `UPDATE_BATTLEFIELD_STATUS` y `PLAYER_ENTERING_WORLD` para estilizar el botón dinámico en el momento en que aparece.

### 2026-08-10 — Fix de visibilidad/posición del ojo de cola
- `DragonUI/modules/minimap.lua`: en `RegisterLFGEditorFrame()` se añadió `lfgWrapper:Show()` después de posicionarlo; el ojo (`QueueStatusMinimapButton`) es hijo del wrapper, y al wrapper estar oculto por `CreateUIFrame()` el ojo nunca se veía aunque Blizzard lo mostrara.
- `DragonUI/modules/minimap.lua`: posición por defecto del wrapper cambiada de `(-20, -220)` a `(-20, -20)` para que el ojo aparezca cerca del minimapa (esquina superior derecha) en lugar de caer cerca del micromenú.

### 2026-08-10 — Restaurar OnClick nativo del ojo de cola
- `DragonUI/modules/minimap.lua`: eliminado el `SetScript('OnClick', ...)` custom de `StyleQueueStatusButton()` para preservar el handler nativo de MoP.
- El ojo vuelve a mostrar el menú contextual nativo según el contexto: tiempo estimado/cancelar cola cuando estás en cola, opciones de mazmorra/BG cuando estás dentro, etc.

### 2026-08-10 — Anclar ojo de cola fijo al minimapa y respetar visibilidad nativa
- `DragonUI/modules/minimap.lua`: `RegisterLFGEditorFrame()` reescrito para reparentar `QueueStatusMinimapButton` directamente a `Minimap` y anclarlo en `BOTTOMLEFT` del minimapa.
- `DragonUI/modules/minimap.lua`: eliminado el wrapper editable `lfgWrapper`; el ojo ya no es movible independientemente porque debe permanecer unido al minimapa.
- `DragonUI/modules/minimap.lua`: `StyleQueueStatusButton()` ya no fuerza `button:Show()`; respeta el estado nativo de visibilidad (invisible cuando no hay cola).

### 2026-08-10 — Estilizar ojo de cola como botón de DragonUI
- `DragonUI/modules/minimap.lua`: creado un `DragonUI_LFGHolder` fijo (no editable) anclado al minimapa que contiene el `QueueStatusMinimapButton`.
- `DragonUI/modules/minimap.lua`: el botón nativo se escala a 21×21 (igual que `DragonUI_MinimapSettingsButton`) y se le añade el anillo dorado `border_buttons.tga`.
- `DragonUI/modules/minimap.lua`: `StyleQueueStatusButton()` escala el ojo a `1.5` para que llene el anillo dorado.
- `DragonUI/modules/minimap.lua`: `StyleQueueStatusButton()` sincroniza la visibilidad del holder con el botón nativo, y `RegisterLFGEditorFrame()` engancha `OnShow`/`OnHide` como respaldo.
- `DragonUI/modules/minimap.lua`: `RegisterLFGEditorFrame()` y `ResetMinimapSystem()` ahora guardan y restauran parent, points, scale y hooks del botón nativo.
- `DragonUI/modules/micromenu.lua`: `ApplyLFGFrameStyle()` ya no escala el ojo a `1.5` ni cambia su textura en MoP; solo oculta el borde nativo.
- `DragonUI/modules/micromenu.lua`: `StoreOriginalMicroButtonStates()` y `RestoreMicromenuSystem()` ahora guardan/restauran el parent del LFG frame.

### 2026-08-10 — Fix micromenú: nombres de botones y resolución dinámica
- `DragonUI/modules/micromenu.lua`: reemplazada la tabla estática `MICRO_BUTTONS` por `MICRO_BUTTON_NAMES` (strings) y función `GetMicroButtons()` que resuelve los botones en tiempo de ejecución.
- `DragonUI/modules/micromenu.lua`: añadidos nombres alternativos usados por el cliente MoP 5.4.8: `CompanionsMicroButton` y `MainMenuMicroButton`.
- `DragonUI/modules/micromenu.lua`: añadida `RESTORE_ONLY_BUTTON_NAMES` para guardar/restaurar botones que no se estilizan (`FriendsMicroButton`, `HelpMicroButton`) y devolverlos a su posición nativa.
- `DragonUI/modules/micromenu.lua`: mapeado `companions` al atlas de `Collections`.
- `DragonUI/modules/micromenu.lua`: hook a `MiniMapLFG_UpdateIsShown` protegido: solo se engancha si la función existe (WotLK); en MoP no existe y causaba error de login.
- `DragonUI/modules/micromenu.lua`: añadido hook a `UpdateMicroButtons` para reparentar los botones del micromenú a `pUiMicroMenu` y re-ejecutar `LayoutMicroButtons()` cuando Blizzard reorganiza los botones (por ejemplo, al abrir/cerrar el mapa del mundo).

### 2026-08-10 — Fix ojo de mazmorras: visibilidad independiente del minimap
- `DragonUI/modules/minimap.lua`: parent del holder cambiado a `UIParent` para que el ojo permanezca visible cuando el minimap nativo se oculta al abrir el mapa del mundo.
- `DragonUI/modules/minimap.lua`: anclaje calculado respecto a `Minimap` para colocar el holder en la esquina decorativa del `MinimapFrame`.

### 2026-08-10 — Fix botón PVP: ocultar nativo y reutilizar su OnClick
- `DragonUI/modules/micromenu.lua`: el botón PVP nativo `PVPMicroButton` se oculta mientras el micromenú de DragonUI está activo para evitar que se superponga a `LFDMicroButton`.
- `DragonUI/modules/micromenu.lua`: `DragonUIPVPMicroButton` usa una función `OpenPVPFrame()` robusta que carga `Blizzard_PVPUI` bajo demanda e intenta `PVPUIFrame_Toggle`, `TogglePVPFrame`, `PVEFrame_ToggleFrame("PVPQueueFrame")`, `ShowUIPanel(PVPQueueFrame)` y `PVPUIFrame` en orden.
- `DragonUI/modules/micromenu.lua`: el hook de `UpdateMicroButtons` mantiene oculto `PVPMicroButton` nativo tras cada reorganización de Blizzard.
- `DragonUI/modules/micromenu.lua`: `RestoreMicromenuSystem()` vuelve a mostrar `PVPMicroButton` nativo si se desactiva el módulo.

### 2026-08-10 — Fix botón PVP: usar botón nativo en el layout
- `DragonUI/modules/micromenu.lua`: eliminado `DragonUIPVPMicroButton` custom; ahora se usa directamente el botón nativo `PVPMicroButton` en el layout del micromenú.
- `DragonUI/modules/micromenu.lua`: `PVPMicroButton` añadido a `MICRO_BUTTON_NAMES` entre `EJMicroButton` y `StoreMicroButton`.
- `DragonUI/modules/micromenu.lua`: el loop de setup detecta PVP por nombre (`buttonName == "PVP"`), limpia sus texturas nativas con `texture_strip()` y aplica `SetupPVPButton()`.
- `DragonUI/modules/micromenu.lua`: el hook de `UpdateMicroButtons` para colored mode salta PVP (`bKey ~= "pvp"`) para no sobreescribir el estilo de `SetupPVPButton`.
- `DragonUI/modules/micromenu.lua`: eliminada toda la lógica que ocultaba `PVPMicroButton`; ahora el botón nativo permanece visible y su `OnClick` original abre el panel PVP.

### 2026-08-10 — Fix GuildMicroButton: reemplazo por botón custom
- `DragonUI/modules/micromenu.lua`: creado `DragonUIGuildMicroButton` como reemplazo del nativo `GuildMicroButton`.
- `DragonUI/modules/micromenu.lua`: `DragonUIGuildMicroButton` usa `ToggleGuildFrame()` para abrir el panel de guild y tiene tooltip "Guild" en `OnEnter`/`OnLeave`.
- `DragonUI/modules/micromenu.lua`: `GuildMicroButton` nativo se eliminó de `MICRO_BUTTON_NAMES`; `DragonUIGuildMicroButton` se inserta entre `LFDMicroButton` y `CompanionsMicroButton`.
- `DragonUI/modules/micromenu.lua`: añadidas `HideNativeGuildButton()` y `StartGuildHideScheduler()` para ocultar el botón Guild nativo de forma agresiva: ocultación inmediata, hook `OnShow`, scheduler de 0.2s durante 2s, y event frame escuchando `PLAYER_ENTERING_WORLD`, `GUILD_ROSTER_UPDATE` y `PLAYER_GUILD_UPDATE`.
- `DragonUI/modules/micromenu.lua`: el botón Guild nativo se restaura al desactivar el módulo.
- `DragonUI/modules/micromenu.lua`: añadidos mapeos de atlas para `dragonuiguild` y `GetGrayscaleAtlasName()` para que el botón custom use las texturas de guild existentes.

### 2026-08-10 — Fix ojo de cola: no desaparecer al abrir el mapa
- `DragonUI/modules/minimap.lua`: añadida `GetOrCreateLFGHolder()` para crear el holder de forma robusta, incluso cuando `QueueStatusMinimapButton` no existe al inicio.
- `DragonUI/modules/minimap.lua`: eliminado el hook `OnHide` de `QueueStatusMinimapButton` que ocultaba `DragonUI_LFGHolder` cuando el mapa se abría.
- `DragonUI/modules/minimap.lua`: `StyleQueueStatusButton()` vuelve a sincronizar el holder con `QueueStatusMinimapButton:IsShown()`; al no haber hook `OnHide`, el holder permanece visible cuando el mapa oculta el minimapa nativo.
- `DragonUI/modules/minimap.lua`: añadido evento `WORLD_MAP_UPDATE` al frame de eventos de cola para refrescar la visibilidad del holder al abrir/cerrar el mapa.

## Hallazgos clave que cambian el scope

- **Quest tracker:** este cliente 5.4.8 todavía usa `WatchFrame`. No es necesario migrar a `ObjectiveTrackerFrame`. El trabajo es adaptar el código de DragonUI a la versión MoP de `WatchFrame`.
- **Resize APIs:** `SetResizeBounds` no existe en MoP 5.4.8. Se mantiene `SetMinResize`/`SetMaxResize`.
- **Power events:** MoP usa `UNIT_POWER` / `UNIT_MAXPOWER` (con `powerType`), pero los eventos por escuela (`UNIT_MANA`, `UNIT_RAGE`, etc.) todavía pueden emitirse en servidores privados. Se mantienen como fallback.

## Paso 1 — Eventos de poder

### Objetivo
Que player/target/focus/nameplates/actionbars actualicen mana, rage, focus, energy y runic power correctamente.

### Archivos afectados
- `DragonUI/modules/actionbars/extrabar.lua`
- `DragonUI/modules/unitframes/player.lua`
- `DragonUI/modules/unitframes/target_style.lua`
- `DragonUI/modules/nameplates/pipeline/engine.lua`

### Cambios planificados
- Agregar `UNIT_POWER` y `UNIT_MAXPOWER` como eventos principales.
- Mantener `UNIT_MANA`, `UNIT_RAGE`, etc. como fallback para servidores privados.
- Filtrar `UNIT_POWER`/`UNIT_MAXPOWER` por `powerType` (MANA/RAGE/FOCUS/ENERGY/RUNIC_POWER).
- En `player.lua`, reemplazar el fake `UNIT_MANA` por `UnitFrameManaBar_Update`.
- Eliminar `UNIT_HAPPINESS`/`UNIT_MAXHAPPINESS` (se removió en Cata/MoP).

### Verificación
- Cambiar de forma de poder en personaje (ej. druido).
- Targetear enemigos con diferentes recursos.

## Paso 2 — Buffs / Auras

### Objetivo
Que el módulo de buffs cargue sin errores y muestre buffs, debuffs y weapon enchants correctamente.

### Archivos afectados
- `DragonUI/modules/buff_frame.lua`
- `DragonUI/modules/auraborders.lua`
- `DragonUI_Options/panel/tab_auras.lua`
- `DragonUI/database.lua` (posición por defecto de `weapon_enchants`)

### Cambios planificados
- Proteger **todas** las referencias a `ConsolidatedBuffs` con `if ConsolidatedBuffs then ... end`.
- Cambiar loops de `TempEnchant1..3` a `TempEnchant1..2`.
- Reemplazar hooks de `BuffFrame_Update` por `BuffFrame_UpdateAllBuffAnchors`.
- Guardar el hook de `DebuffButton_UpdateAnchors` con `if type(...) == "function"`.
- Usar `GetWeaponEnchantInfo()` para calcular cuántos weapon enchants hay.
- Cambiar el ancla raíz de `ConsolidatedBuffs` a `BuffFrame`/`dragonUIBuffFrame`.
- Ajustar la posición por defecto de `weapon_enchants` para que no se solape.

### Verificación
- Loguear con buffs activos.
- Verificar weapon enchants (rogue poisons, shaman imbues, sharpening stones).
- Probar separación de weapon enchants desde opciones.

## Paso 3 — Micro menú

### Objetivo
Que los botones del micro menú sean los correctos de MoP y no aparezcan nils ni texturas rotas.

### Archivos afectados
- `DragonUI/modules/micromenu.lua`
- `DragonUI/modules/mainbars.lua`
- `DragonUI/modules/darkmode.lua`
- `DragonUI/modules/chatmods.lua`
- `DragonUI/utils/atlas.lua`

### Cambios planificados
- Reemplazar la lista de botones por la de MoP:
  - `CharacterMicroButton`
  - `SpellbookMicroButton`
  - `TalentMicroButton`
  - `AchievementMicroButton`
  - `QuestLogMicroButton`
  - `GuildMicroButton` (reemplaza `SocialsMicroButton`)
  - `LFDMicroButton`
  - `CollectionsMicroButton`
  - `EJMicroButton`
  - `StoreMicroButton`
  - `MainMenuMicroButton` (reemplaza `HelpMicroButton`)
- Eliminar lógica de Ascension (`PathToAscensionMicroButton`, `ChallengesMicroButton`).
- Eliminar lógica del botón PVP (`PVPMicroButton` no existe).
- Mover el indicador de latencia de `HelpMicroButton` a `MainMenuMicroButton`.
- Actualizar `MICROMENU_BUTTON_NAMES` y `MAINBAR_PROTECTED_CHILD_NAMES` en `mainbars.lua`.
- Actualizar `microNames` en `darkmode.lua`.
- Reemplazar `FriendsMicroButton` por `GuildMicroButton` (o remover integración) en `chatmods.lua`.
- Actualizar/limpiar entradas de atlas en `utils/atlas.lua`.

### Verificación
- `/rl` y abrir cada botón del micro menú.
- Probar modo gris y modo oscuro.
- Verificar indicador de latencia.

## Paso 4 — Quest Tracker

### Objetivo
Hacer que el quest tracker de DragonUI funcione con la versión MoP de `WatchFrame`.

### Archivos afectados
- `DragonUI/modules/questtracker.lua`
- `DragonUI/modules/minimap.lua` (línea de exclusión, opcional)

### Cambios planificados
- Agregar guarda defensiva: si `WatchFrame` no existe, desactivar el módulo.
- Descubrir `WatchFrameLinkButton` dinámicamente (no son globals nombrados en MoP).
- Incluir `GetNumAutoQuestPopUps()` en el cálculo de contenido.
- Incluir `WatchFrameScenarioFrame` en el cálculo del hit rect.
- Usar `WATCHFRAME_NUM_ITEMS` en lugar de hardcodear 40.
- Reemplazar la reasignación global de `WatchFrameItem_UpdateCooldown` por `hooksecurefunc` o remover si el bug ya no existe.
- Envolver hooks en `pcall` para evitar que un error rompa todo el tracker.
- Hacer defensivo el guardado del punto original de `WatchFrame`.

### Verificación
- Trackear misiones, logros, scenarios.
- Probar colapsar/expandir.
- Probar fade en combate / hover.
- Probar `/dragonui edit` para mover el tracker.

## Paso 5 — Verificación in-game general

Antes de pasar a otras áreas, probar estos 4 fixes juntos:

1. Login sin errores de Lua.
2. Barras de poder actualizándose.
3. Buffs/debuffs/weapon enchants visibles.
4. Micro menú completo y funcional.
5. Quest tracker visible y draggable.

## Áreas pendientes de investigación

- Item level / cambios en `GetItemInfo`.
- Party frames / `CompactPartyFrame`.
- Atlas faltantes en texturas personalizadas.
- `INSPECT_TALENT_READY` en inspect.
- Bags / Bagster / `GetAuctionItemClasses`.
- Nameplates (módulo grande, dejar para después de estabilizar lo básico).
- **Mejora futura — Minimap `blip_skin`:** actualmente `blip_skin` está desactivado por defecto porque el atlas custom `objecticons.tga` no incluye los iconos de quest POI de MoP (interrogaciones/admiraciones), que se renderizan desde `Interface\Minimap\ObjectIcons`. Para reactivar el estilo custom de blips sin romper los iconos de quest, habría que regenerar `objecticons.tga` incluyendo todas las coordenadas que usa MoP 5.4.8, o encontrar una textura alternativa compatible.

## Archivos afectados en total

| Archivo | Paso |
|---|---|
| `DragonUI/modules/actionbars/extrabar.lua` | 1 |
| `DragonUI/modules/unitframes/player.lua` | 1 |
| `DragonUI/modules/unitframes/target_style.lua` | 1 |
| `DragonUI/modules/nameplates/pipeline/engine.lua` | 1 |
| `DragonUI/modules/buff_frame.lua` | 2 |
| `DragonUI/modules/auraborders.lua` | 2 |
| `DragonUI_Options/panel/tab_auras.lua` | 2 |
| `DragonUI/database.lua` | 2 |
| `DragonUI/modules/micromenu.lua` | 3 |
| `DragonUI/modules/mainbars.lua` | 3 |
| `DragonUI/modules/darkmode.lua` | 3 |
| `DragonUI/modules/chatmods.lua` | 3 |
| `DragonUI/utils/atlas.lua` | 3 |
| `DragonUI/modules/questtracker.lua` | 4 |
| `DragonUI/modules/minimap.lua` | 4 (opcional) |
