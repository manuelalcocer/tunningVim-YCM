# youcompleme

Instalar You-Complete-me para vim en Debian Jessie

![alt_tag](vim.png?raw_true "vim")

## Instalación de Vim y sus dependencias

Para que funcione el plugin es necesario que Vim tenga soporte parra python con lo que tenemos dos opciones, o bien compilamos vim, o bien insdalamos gvim que ya trae soprte. Por otra parte YCM debe ser compilado así que necesitaremos unas cuantas dependencias.

### Dependencias necesarias para todo el proceso

- build-essential
- vim-gtk
- cmake
- python-dev
- curl
- git

```
root@berry:~# apt-get install build-essential vim-gtk cmake python-dev curl git
```

## Instalación de vim-plug

[vim-plug](https://github.com/junegunn/vim-plug) es un gestor minimalista de plugins, su instalación es muy sencilla.

```
debian@berry:~$ curl -fLo ~/.vim/autoload/plug.vim --create-dirs \
    https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim
```

## Configuración de vim-plug

### Editar ~/.vimrc y añadir estas líneas:

```
call plug#begin('~/.vim/plugged')
Plug 'jiangmiao/auto-pairs'
Plug 'Yggdroot/indentLine'
Plug 'vim-airline/vim-airline'
Plug 'vim-airline/vim-airline-themes'
call plug#end()
```

### Instalar los nuevos plugins añadidos

Entrar en vim y ejecutar __:PlugInstall__

## Instalación de valloric/you-complete-me

[YouCompleteMe](https://valloric.github.io/YouCompleteMe/) es un plugin para vim que despliega y autocompleta los comandos que se van introduciendo. De todos los que he probado es el que más me ha gustado.

La instalación con vim-plug no es complicada pero hay que insertar un par de líneas adicionales.
Antes de la zona de plugins añadir esta pequeña función (sacada de la wiki de vim-plug):

```
function! BuildYCM(info)
  " info is a dictionary with 3 fields
  " - name:   name of the plugin
  " - status: 'installed', 'updated', or 'unchanged'
  " - force:  set on PlugInstall! or PlugUpdate!
  if a:info.status == 'installed' || a:info.force
    !./install.py
  endif
endfunction
```

Luego añadir la línea correspondiente para su instalación en vim:

```
Plug 'Valloric/YouCompleteMe', { 'do': function('BuildYCM') }
```

Por tanto el fichero .vimrc quedaría así:

```
debian@berry:~$ cat .vimrc
function! BuildYCM(info)
  " info is a dictionary with 3 fields
  " - name:   name of the plugin
  " - status: 'installed', 'updated', or 'unchanged'
  " - force:  set on PlugInstall! or PlugUpdate!
  if a:info.status == 'installed' || a:info.force
    !./install.py
  endif
endfunction

call plug#begin('~/.vim/plugged')
Plug 'jiangmiao/auto-pairs'
Plug 'Yggdroot/indentLine'
Plug 'vim-airline/vim-airline'
Plug 'vim-airline/vim-airline-themes'
Plug 'Valloric/YouCompleteMe', { 'do': function('BuildYCM') }
call plug#end()
```

Entramos en vim y ejecutamos __:PlugInstall__

... Con esto ha quedado instalado YCM y un par de plugins más

## Tips & Tricks

### .vimrc

Voy a poner aquí mi .vimrc completo, contiene algunos mapeos y funciones que llevo arrastrando algún tiempo. Seguramente no te venga bien pero tiene algunas utilidades interesantes, sobre todo __<leader>+r__.

```
set mouse=a
set t_Co=256
set timeoutlen=1000 ttimeoutlen=0

" Map the leader key to SPACE
let mapleader="\<SPACE>"

"set showcmd             " Show (partial) command in status line.
set showmatch           " Show matching brackets.
set showmode            " Show current mode.
set ruler               " Show the line and column numbers of the cursor.
set number              " Show the line numbers on the left side.
set formatoptions+=o    " Continue comment marker in new lines.
set textwidth=0         " Hard-wrap long lines as you type them.
set expandtab           " Insert spaces when TAB is pressed.
set tabstop=4           " Render TABs using this many spaces.
set shiftwidth=4        " Indentation amount for < and > commands.

set noerrorbells        " No beeps.
set modeline            " Enable modeline.
set esckeys             " Cursor keys in insert mode.
set linespace=0         " Set line-spacing to minimum.
set nojoinspaces        " Prevents inserting two spaces after punctuation on a join (J)

" More natural splits
set splitbelow          " Horizontal split below current.
set splitright          " Vertical split to right of current.

if !&scrolloff
  set scrolloff=3       " Show next 3 lines while scrolling.
endif
if !&sidescrolloff
  set sidescrolloff=5   " Show next 5 columns while side-scrolling.
endif
set display+=lastline
set nostartofline       " Do not jump to first character with page commands.

" Tell Vim which characters to show for expanded TABs,
" trailing whitespace, and end-of-lines. VERY useful!
if &listchars ==# 'eol:$'
  set listchars=tab:>\ ,trail:-,extends:>,precedes:<,nbsp:+
endif
set list                " Show problematic characters.

" Also highlight all tabs and trailing whitespace characters.
highlight ExtraWhitespace ctermbg=darkgreen guibg=darkgreen
match ExtraWhitespace /\s\+$\|\t/

set hlsearch            " Highlight search results.
set ignorecase          " Make searching case insensitive
set smartcase           " ... unless the query has capital letters.
set incsearch           " Incremental search.
set gdefault            " Use 'g' flag by default with :s/foo/bar/.
set magic               " Use 'magic' patterns (extended regular expressions).

" Use <C-L> to clear the highlighting of :set hlsearch.
if maparg('<C-L>', 'n') ==# ''
  nnoremap <silent> <C-L> :nohlsearch<CR><C-L>
endif

" Relative numbering
function! NumberToggle()
  if(&relativenumber == 1)
    set nornu
    set number
  else
    set rnu
  endif
endfunc

" Toggle between normal and relative numbering.
nnoremap <leader>r :call NumberToggle()<cr>
nnoremap <leader>c :!scp -v %:p oracentos7:trabajo<cr>
nnoremap <leader>t :Term<cr>
nnoremap <leader>v :VTerm<cr>

" Insertar una línea en blanco abajo o arriba
nnoremap <silent><A-j> :set paste<CR>m`o<Esc>``:set nopaste<CR>
nnoremap <silent><A-k> :set paste<CR>m`O<Esc>``:set nopaste<CR>


function! BuildYCM(info)
  " info is a dictionary with 3 fields
  " - name:   name of the plugin
  " - status: 'installed', 'updated', or 'unchanged'
  " - force:  set on PlugInstall! or PlugUpdate!
  if a:info.status == 'installed' || a:info.force
    !./install.py
  endif
endfunction

call plug#begin('~/.vim/plugged')
Plug 'jiangmiao/auto-pairs'
Plug 'Yggdroot/indentLine'
Plug 'vim-airline/vim-airline'
Plug 'vim-airline/vim-airline-themes'
Plug 'Valloric/YouCompleteMe', { 'do': function('BuildYCM') }
call plug#end()

set laststatus=2
let g:airline_powerline_fonts = 1
let g:airline#extensions#tabline#enabled = 1

" YCM Options
let g:ycm_complete_in_comments = 1
let g:ycm_complete_in_strings = 1
let g:ycm_collect_identifiers_from_comments_and_strings = 1
let g:ycm_autoclose_preview_window_after_completion = 1
let g:ycm_key_list_select_completion = ['<Enter>']
```

### neovim

Ya se encuentra en los repositorios de Debian stretch __neovim__ (y por supuesto desde hace décadas en archlinux  :-D ).

Neovim es idéntico a vim en su apariencia externa e internamente se supone que gestiona mejor la inserción de plugins. Es compatible con vim al 99'9% (ese .vimrc está extraído de mi init.vim de neovim en archlinux) pero yo le he encontrado 2 diferencias muy atractivas:

- Soporte para 'True Colors'

- Emulador nativo de terminal sin bugs (no como otros plugins emuladores de terminal para vim que acaban crasheando), con posibilidad de hacer split tanto horizontal como vertical a la hora de lanzarlo.

