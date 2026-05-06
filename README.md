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

## Terminal: Ghostty

Terminal moderna con aceleracion GPU, soporte nativo para ligatures, Nerd Fonts y temas.

- **Fuente**: CaskaydiaCove Nerd Font (u otra Nerd Font)
- **Tema**: configurado via `~/.config/ghostty/config`

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

| Herramienta | Ubicacion | Estado |
|-------------|-----------|--------|
| **opencode** | `~/.config/opencode/` | CLI principal |
| **engram** | Brew | Memoria persistente |
| **copilot.vim** | Plugin Neovim | Completado inline |
| **claude-code.nvim** | Plugin Neovim | Claude Code |
| **code-companion.nvim** | Plugin Neovim | LLM multi-modelo |
| **github-copilot** | `~/.config/github-copilot/` | CLI |
| **gentle-ai** | Brew | Configurado |

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

# Git
lg                        # lazygit
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
brew install fish starship atuin fzf fd ripgrep bat eza git-delta glow lazygit zellij

# Iniciar Neovim (LazyVim se instala automaticamente)
nvim +Lazy sync +qa
```

## Mantenimiento

- `nvim +Lazy sync` — sincronizar plugins
- `nvim +Lazy update` — actualizar plugins
- `nvim +checkhealth` — diagnosticos
- `brew update && brew upgrade` — actualizar herramientas CLI
- `fisher update` — actualizar plugins de Fish
