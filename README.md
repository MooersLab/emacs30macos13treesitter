![Version](https://img.shields.io/static/v1?label=emacs30macos13treesitter&message=0.1&color=brightcolor)
[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](https://opensource.org/licenses/MIT)

# Compiling Emacs30 on Ventura (macOS 13.2) with support for tree-sitter

The C-library [tree-sitter](https://tree-sitter.github.io/tree-sitter/) supports the use of the concrete syntax tree during the editing of code.
This library eases the editing of computer code.
There is even support for editing LaTeX, Markdown, Org, and reStructuredText.
Tree-sitter is being deployed by other major text editors, such as [NeoVim](https://github.com/nvim-treesitter/nvim-treesitter) and [VSCode](https://github.com/georgewfraser/vscode-tree-sitter).

## Prerequisites
You must install the tree-sitter C library before compiling Emacs.
The [emacs-tree-sitter.el](https://github.com/emacs-tree-sitter/elisp-tree-sitter) package is built into Emacs 29 and 30 but not the C library.
I installed the tree-sitter C library, gnutils, and imagemagick with macports.
The export of giflib could probably have been mapped to the giflib in macports.
This protocol worked on a 2017 Mac workstation and a 2018 MacBook Pro with Mac OS 13.2.

## Compile protocol

```bash
cd ~/software
git clone git://git.sv.gnu.org/emacs emacs30
cd emacs30
git checkout -b emacs29
export LDFLAGS=-L/usr/local/Cellar/giflib/5.2.1/lib
./autogen.sh
mkdir build
cd build
../configure --with-gnutls=ifavailable --with-cairo --with-tree-sitter --with-imagemagick --program-suffix=30 --prefix=/Users/blaine/bin
# Use 8 processors to speed up make.
make -j8
make install -j8
# Note that the install did not go quite as planned: Nothing appeared in ~/bin. I resolved the problem by the next steps.
cd nextstep
cp -R Emacs.app /Applications/Emacs30.0.5tree-sitter.app
# Make a profile directory
mkdir ~/latex-tree-emacs30
# I stored this alias in a .bashAppAliases file that I source fromm my .zshrc file.
alias e30ltd='/Applications/Emacs30.0.5tree-sitter.app/Contents/MacOS/Emacs --init-directory ~/latex-tree-emacs30 --debug-init'
e30ltd # Emacs should fire up.
C-h v
# enter at the : prompt
system-configuration-features RET
```

## Check for tree_sitter

The entry of RETURN on the last line of the code listing above will return the following list of features that were compiled with Emacs.
This list may appear is a separate buffer.
Note the presence of `TREE_SITTER`.

```bash
"ACL DBUS GIF GLIB GMP GNUTLS IMAGEMAGICK JPEG JSON LCMS2 LIBXML2 MODULES NOTIFY KQUEUE NS PDUMPER PNG RSVG SQLITE3 THREADS TIFF TOOLKIT_SCROLL_BARS TREE_SITTER WEBP XIM ZLIB"
```
The list of features differed somewhat between my two computers because the laptop has more software in macports.

## Notes

Compiling C-programs on macOS can be a nightmare if you have redundant software package managers installed, and you are not an expert C programmer.
I am only a novice.
I had installed macports, homebrew, and anaconda.
Xcode and the associated command line tools can sometimes muddy the waters further via the misplacement of some of the libraries. 
The `make` program often fails to find a required library, even though it may be present.
In my case, I had to specify the location of one library file with an export command for the make to finish successfully.
