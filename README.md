# nvim-config-linux

Configuracion completa del entorno de desarrollo en Linux Ubuntu 26.04 (WSL2). Basado en **Neovim/LazyVim**, **Fish**, **Tmux**, **Zellij** y herramientas CLI modernas.

## Stack del ecosistema

| Area | Herramienta | Version | Rol |
|------|-------------|---------|-----|
| OS | Ubuntu 26.04 (WSL2) | — | Sistema base |
| Package Manager | Homebrew (linuxbrew) | — | `/home/linuxbrew/.linuxbrew/` |
| Shell | Fish | 4.7.0 | Shell principal |
| Prompt | Starship | 1.25.1 | Prompt rapido y contextual |
| Historial | Atuin | 18.16.0 | Busqueda de historial sincronizada |
| Completions | Carapace | — | Autocompletados avanzados multi-shell |
| Navegacion | zoxide | — | Saltos inteligentes (`z`) |
| Selector | fzf | 0.72.0 | Filtrado difuso (archivos, texto, historial) |
| Busqueda texto | ripgrep (rg) | 15.1.0 | Busqueda rapida en codigo |
| Busqueda archivos | fd | 10.4.2 | Find moderno |
| Visor archivos | bat | 0.26.1 | Cat con resaltado y numeros de linea |
| Listado | eza | 0.23.4 | ls moderno con git e iconos |
| Diffs | delta | 0.19.2 | Diffs de Git mejorados |
| Markdown | glow | 2.1.2 | Visor de Markdown en terminal |
| Git TUI | lazygit | 0.61.1 | Operaciones Git interactivas |
| Docker TUI | lazydocker | 0.25.2 | Gestion de contenedores Docker |
| Git | git | 2.54.0 | Control de versiones |
| Terminal | Ghostty | 1.3.1 | Terminal GPU-accelerated |
| Multiplexor | Tmux | 3.6 | Sesiones, paneles, persistencia |
| Multiplexor | Zellij | 0.44.2 | Alternativa moderna a Tmux |
| Editor | Neovim | 0.12.2 | Editor modal |
| Distribucion | LazyVim | v8 | LSP, Treesitter, pickers |

## Shell: Fish

### Plugins (Fisher)

| Plugin | Rol |
|--------|-----|
| `jorgebucaran/fisher` | Plugin manager |
| `jorgebucaran/nvm.fish` | Node version manager |
| `patrickf1/fzf.fish` | Integracion fzf |
| `oh-my-fish/plugin-pj` | Project jumper |

### Configuracion

- **Vi mode**: `fish_vi_key_bindings` activado
- **Editor por defecto**: `nvim`
- **Greeting**: desactivado
- **Tema de colores**: personalizado (Tokyo Night)

### Funciones personalizadas

| Funcion | Descripcion |
|---------|-------------|
| `ff` | Busca archivo con `fd + fzf` y abre en Neovim |
| `ffg <patron>` | Busca texto con `rg + fzf` y abre en Neovim en la linea |
| `ll` | Listado largo con `eza --long --git --icons` |
| `la` | Listado largo con ocultos `eza --long --all --git --icons` |
| `lt` | Arbol de directorio `eza --tree --level=2 --icons` |
| `mdv <archivo>` | Ver Markdown con `glow` |
| `z <proyecto>` | Saltar a carpeta frecuente con `zoxide` |

### Buscadores vs Exploradores

| Tipo | Herramienta | Como se usa |
|------|-------------|-------------|
| **Finder** | `ff` (shell) | `fd + fzf` → busca archivo y abre en Neovim |
| **Finder** | `ffg <patron>` (shell) | `rg + fzf` → busca texto y abre en Neovim en la linea |
| **Finder** | `<leader>tf` (nvim) | Buscar archivos dentro de Neovim |
| **Finder** | `<leader>tg` (nvim) | Buscar texto dentro de Neovim |
| **Explorador** | `oil.nvim` (nvim) | `-` en modo normal, edita el sistema de archivos como buffer |
| **Explorador** | `mini-files` (nvim) | Via LazyVim extra, explorador de archivos |

### TUIs de gestion

| Herramienta | Comando | Uso |
|-------------|---------|-----|
| **lazygit** | `lazygit` o `lg` | Operaciones Git: stage, commit, push, branches, diffs |
| **lazydocker** | `lazydocker` | Contenedores, imagenes, logs, stats |

## Terminal: Ghostty

Terminal moderna con aceleracion GPU, soporte nativo para ligatures, Nerd Fonts y temas.

- **Fuente**: CaskaydiaCove Nerd Font (u otra Nerd Font)
- **Tema**: configurado via `~/.config/ghostty/config`

## Shell: Fish + Zellij — Fixes

### Fix 1: Loop infinito al abrir terminales nuevos

Con Zellij 0.44.2 y Fish, la configuracion tipica `exec zellij attach --create` puede causar un **loop infinito** (Zellij no exporta `ZELLIJ_SESSION_NAME` correctamente en algunos paneles).

**Solucion:** Verificar el arbol de procesos:

```fish
set -l parent_name (ps -o comm= -p $parent_pid 2>/dev/null | string trim)
if test "$parent_name" != "zellij"
    exec zellij attach --create
end
```

### Fix 2: Hang al hacer `exit` de fish en terminales como Warp

**Contexto:** Si abris un terminal que usa **bash** como shell por defecto (como Warp, o muchos emuladores por defecto) y ejecutas `fish` manualmente, fish lee `config.fish` y hace `exec zellij attach --create`. El `exec` **reemplaza** fish con zellij, lo cual **rompe la integracion** de terminales modernas como Warp que necesitan trackear el proceso shell activo.

**Resultado:** Al hacer `exit` dentro de zellij, la terminal se queda colgada porque Warp pierde el control del shell session.

**Solucion:** Detectar si fish fue invocado manualmente desde otro shell (bash, sh, zsh, dash) y **omitir** zellij en ese caso:

```fish
set -l shell_parents bash sh zsh dash
set -l is_nested_shell 0
for shell in $shell_parents
    if test "$parent_name" = "$shell"
        set is_nested_shell 1
        break
    end
end

if test "$parent_name" != "zellij"; and test $is_nested_shell -eq 0
    exec zellij attach --create
end
```

**Comportamiento esperado:**

| Escenario | ¿Lanza zellij? | Razon |
|-----------|---------------|-------|
| Terminal nuevo con Fish como shell por defecto | ✅ Si | Fish es la shell principal del emulador |
| `fish` ejecutado manualmente desde bash/zsh | ❌ No | Evita `exec` que rompe integraciones de la terminal |
| Dentro de paneles de zellij | ❌ No | Ya detectado por `ZELLIJ_SESSION_NAME` o parent = zellij |

---

## Multiplexores

### Tmux

Plugins via TPM (`~/.tmux/plugins/`):

| Plugin | Rol |
|--------|-----|
| `tpm` | Plugin manager |
| `tmux-sensible` | Opciones sensatas por defecto |
| `tmux-yank` | Manejo de clipboard |
| `vim-tmux-navigator` | Navegacion seamless Neovim <-> Tmux |
| `tmux-resurrect` | Persistencia de sesiones |
| `tmux-which-key` | Menu de atajos |
| `tmux-kanagawa` | Tema Kanagawa Dragon |

Shell por defecto: Fish

### Zellij

Alternativa moderna con layouts predefinidos:

| Layout | Descripcion |
|--------|-------------|
| `work` | Layout base |
| `work_kanagawa` | Tema Kanagawa |
| `work_everforest` | Tema Everforest |
| `work_sakura` | Tema Sakura |
| `work_oldWorld` | Tema Old World |

Plugins: `zjstatus` (barra de estado), `zellij_forgot` (recordatorios)

## Editor: Neovim + LazyVim

### Plugins activos

| Plugin | Rol |
|--------|-----|
| `blink.cmp` | Autocompletado |
| `copilot.lua` | GitHub Copilot |
| `claude-code.nvim` | Claude Code integrado |
| `code-companion.nvim` | Asistente de codigo via LLM |
| `fzf-lua` | Picker con fzf |
| `which-key.nvim` | Menu de atajos |
| `nvim-tmux-navigation` | Navegacion Neovim <-> Tmux |
| `oil.nvim` | Explorador de archivos como buffer |
| `obsidian.nvim` | Integracion con Obsidian |
| `nvim-dap` | Debug Adapter Protocol |
| `markdown-preview` | Preview de Markdown |
| `twilight.nvim` | Dim zonas inactivas |
| `venn.nvim` | Dibujo ASCII |
| `screenkey.nvim` | Muestra teclas presionadas |
| `rip.nvim` | Animacion de borrado |
| `smear-cursor` | Animacion de cursor (desactivado) |
| `precognition` | Hint de movimiento (desactivado) |
| `avante.nvim` | Asistente IA (desactivado) |
| `copilot-chat` | Chat con Copilot (desactivado) |
| `gemini-cli.nvim` | Gemini CLI (desactivado) |

### LazyVim extras

| Extra | Area |
|-------|------|
| `editor.harpoon2` | Navegacion rapida entre archivos |
| `editor.mini-files` | Explorador de archivos |
| `editor.snacks_picker` | Picker rapido |
| `editor.mini-diff` | Diffs mejorados |
| `dap.core` | Debugging |
| `lang.typescript` | Soporte TypeScript |
| `lang.json` | Soporte JSON |
| `lang.markdown` | Soporte Markdown |
| `formatting.biome` | Formateo con Biome |
| `formatting.prettier` | Formateo con Prettier |
| `linting.eslint` | Linting ESLint |
| `coding.mini-surround` | Surround actions |
| `coding.blink` | Autocompletado blink |
| `util.mini-hipatterns` | Highlight patterns |
| `ai.copilot` | GitHub Copilot |

### Keymaps principales de Neovim

| Atajo | Accion |
|-------|--------|
| `<leader>tf` | Buscar archivos (Telescope/FzfLua) |
| `<leader>tg` | Buscar texto (live grep) |
| `<leader>tb` | Cambiar buffer |
| `<leader>ha` | Harpoon: agregar archivo |
| `<leader>hm` | Harpoon: menu rapido |
| `<leader>h1..h4` | Harpoon: saltar a archivo 1-4 |
| `<leader>og` | Abrir Gemini CLI (desactivado) |
| `<C-h/j/k/l>` | Navegar entre paneles Tmux + Neovim |
| `<leader>oc` | Obsidian: toggle checkbox |
| `<leader>ot` | Obsidian: insertar template |
| `<leader>oo` | Obsidian: abrir en app |
| `<leader>os` | Obsidian: buscar notas |
| `-` | Oil: abrir directorio padre |
| `<leader>uk` | Screenkey: mostrar teclas |
| `<leader>md` | Borrar todas las marcas |
| `<leader>bq` | Cerrar otros buffers |
| `<C-s>` | Guardar archivo |
| `<C-c>` | Escape a modo normal |
| `<leader>sg` | Grep texto seleccionado |
| `<leader>sG` | Grep texto seleccionado (root dir) |

## AI / LLM

| Herramienta | Ubicacion | Estado | Version |
|-------------|-----------|--------|---------|
| **opencode** | `~/.config/opencode/` | CLI principal (via gentle-ai) | — |
| **engram** | Brew | Memoria persistente | **1.15.10** |
| **copilot.vim** | Plugin Neovim | Completado inline | — |
| **claude-code.nvim** | Plugin Neovim | Claude Code | — |
| **code-companion.nvim** | Plugin Neovim | LLM multi-modelo | — |
| **github-copilot** | `~/.config/github-copilot/` | CLI | — |
| **gentle-ai** | Brew | Configurado | **1.26.6** |

### Agentes configurados via gentle-ai

| Agente | Estado | Notas |
|--------|--------|-------|
| **OpenCode** | ✅ Activo | Plugin + MCP server instalados. Perfil SDD `gentle-orchestrator` listo. |
| **Codex** | ✅ Activo | Plugin + MCP server instalados en `~/.codex`. |
| **Claude Code** | ✅ Activo | Preexistente, sincronizado con gentle-ai. |
| **Kilo Code** | ❌ N/A | No soportado aun en la version MVP de gentle-ai. |

### Comandos post-instalacion

```bash
# Iniciar servidor de memoria persistente (engram)
engram serve &

# Dentro de cualquier proyecto con OpenCode:
/sdd-init          # Detecta stack, testing, activa Strict TDD si aplica
skill-registry     # Genera el catalogo de skills para el proyecto
```

En OpenCode, presionar **Tab** cambia entre el perfil SDD default (`gentle-orchestrator`) y perfiles personalizados.

## Git

Configurado con **delta** para diffs mejorados:

```bash
git config --global core.pager delta
git config --global interactive.diffFilter "delta --color-only"
git config --global delta.navigate true
git config --global delta.side-by-side false
git config --global merge.conflictstyle zdiff3
git config --global diff.colorMoved default
```

## Flujo de trabajo diario

```
# Navegar
z mi-proyecto

# Explorar
ll                        # listado largo con git
la                        # incluyendo ocultos
lt                        # arbol de directorio

# Buscar y abrir
ff                        # buscar archivo -> abrir en Neovim
ffg patron                # buscar texto -> abrir en linea

# Abrir proyecto
nvim .                    # Neovim en raiz del proyecto

# Git / Docker
lazygit                   # TUI para Git
lazydocker                # TUI para Docker
git diff                  # diff con delta

# Markdown
mdv README.md             # ver con glow

# Multiplexor
tmux                      # arranca automatico al abrir fish
```

## Instalacion

```bash
# Clonar config de Neovim
git clone https://github.com/QuantumEdu/nvim-config-linux.git ~/.config/nvim

# Instalar herramientas CLI (requiere Homebrew)
brew install fish starship atuin fzf fd ripgrep bat eza git-delta glow lazygit lazydocker zellij

# Iniciar Neovim (LazyVim se instala automaticamente)
nvim +Lazy sync +qa
```

## Mantenimiento

- `nvim +Lazy sync` — sincronizar plugins
- `nvim +Lazy update` — actualizar plugins
- `nvim +checkhealth` — diagnosticos
- `brew update && brew upgrade` — actualizar herramientas CLI
- `fisher update` — actualizar plugins de Fish
