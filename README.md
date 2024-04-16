# Ipe figure manager.

A script used to manage figures for my LaTeX documents.
Based on inskcape-figures.

## Requirements

You need Python >= 3.7, as well as a picker. Current supported pickers are:

* [rofi](https://github.com/davatorium/rofi) on Linux systems
* [choose](https://github.com/chipsenkbeil/choose) on MacOS

## Setup

Add the following code to the preamble of your LateX document.

```tex
\usepackage{import}
\usepackage{pdfpages}
\usepackage{transparent}
\usepackage{xcolor}

% ipe support
\usepackage{tikz}
\usetikzlibrary{arrows.meta,patterns}
\usetikzlibrary{ipe} % ipe compatibility library

\pdfsuppresswarningpagegroup=1
```
The settings above assume the following directory structure:

```
master.tex
figures/
    figure1.pdf_tex
    figure1.svg
    figure1.pdf
    figure2.pdf_tex
    figure2.svg
    figure2.pdf
```

## Usage

* Watch for figures: `ipe-figures watch`.
* Creating a figure: `ipe-figures create 'title'`. 
* Creating a figure in a specific directory: `ipe-figures create 'title' path/to/figures/`.
* Select figure and edit it: `ipe-figures edit`.
* Select figure in a specific directory and edit it: `ipe-figures edit path/to/figures/`.

## Vim mappings

This assumes that you use [VimTeX](https://github.com/lervag/vimtex).

```vim
inoremap <C-f> <Esc>: silent exec '.!ipe-figures create "'.getline('.').'" "'.b:vimtex.root.'/figures/"'<CR><CR>:w<CR>
nnoremap <C-f> : silent exec '!ipe-figures edit "'.b:vimtex.root.'/figures/" > /dev/null 2>&1 &'<CR><CR>:redraw!<CR>
```

First, run `ipe-figures watch` in a terminal to setup the file watcher.
Now, to add a figure, type the title on a new line, and press <kbd>Ctrl+F</kbd> in insert mode.
This does the following:

1. Find the directory where figures should be saved depending on which file you're editing and where the main LaTeX file is located, using `b:vimtex.root`.
1. Check if there exists a figure with the same name. If there exists one, do nothing; if not, go on.
1. Copy the figure template to the directory containing the figures.
1. In Vim: replace the current line – the line containing figure title – with the LaTeX code for including the figure.
1. Open the newly created figure in Ipe.
1. Set up a file watcher such that whenever the figure is saved as an ipe file by pressing <kbd>Ctrl + S</kbd>, it also gets saved as pdf+LaTeX.

To edit figures, press <kbd>Ctrl+F</kbd> in command mode, and a fuzzy search selection dialog will popup allowing you to select the figure you want to edit.


## Configuration

You can change the default LaTeX template by creating `~/.config/ipe-figures/config.py` and adding something along the lines of the following:

```python
def latex_template(name, title):
    return '\n'.join((r"\begin{figure}[ht]",
                      r"    This is a custom LaTeX template!",
                      r"    \centering",
                      rf"    \incfig[1]{{{name}}}",
                      rf"    \caption{{{title}}}",
                      rf"    \label{{fig:{name}}}",
                      r"\end{figure}"))
```
