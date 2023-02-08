# Compiling Emacs30 on Ventura (macOS 13.2) with support for tree-sitter

The C-library [tree-sitter](https://tree-sitter.github.io/tree-sitter/) supports the use of the concrete syntax tree during the editing of code.
This library eases the editing of computer code.
There is even support for editing LaTeX, Markdown, Org, and reStructuredText.
Tree-sitter is being deployed by the other major text edtiors like [NeoVim](https://github.com/nvim-treesitter/nvim-treesitter) and [VSCode](https://github.com/georgewfraser/vscode-tree-sitter).
It does not seem to have been incorporated by the electronic notebooks yet.


## Prerequisites
You must installed tree-sitter C library before compiling Emacs.
The [emacs-tree-sitter.el](https://github.com/emacs-tree-sitter/elisp-tree-sitter) package is built into Emacs29 and 30 but not the C library.
I installed the tree-sitter C library with macports and with homebrew.
I am not sure which was used when execting the code below.

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
make -j8
make install -j8
# Note that the install did not go quite as planned. I resolved the problem by the next steps.
cd nextstep
cp -R Emacs.app /Applications/Emacs30.0.5tree-sitter.app
# Make a profile directory
mkdir ~/latex-tree-emacs30
# I store this alias in a .bashAppAliases file that I source fromm my .zshrc file.
alias e30ltd='/Applications/Emacs30.0.5tree-sitter.app/Contents/MacOS/Emacs --init-directory ~/latex-tree-emacs30 --debug-init'
e30ltd # Emacs should fire up.
C-h v
# enter at the : prompt
system-configuration-features RET
```

## Check for tree_sitter

The entry of RETURN will return the following list of features that were compiled with Emacs.
This list may appear is a separate buffer.
Note the presence of `TREE_SITTER`.

```bash
"ACL DBUS GIF GLIB GMP GNUTLS IMAGEMAGICK JPEG JSON LCMS2 LIBXML2 MODULES NOTIFY KQUEUE NS PDUMPER PNG RSVG SQLITE3 THREADS TIFF TOOLKIT_SCROLL_BARS TREE_SITTER WEBP XIM ZLIB"
```

## Notes

Compiling C-programs on macOS can be a nightmare if you have redundant software package managers installed and you are not an expert C programmer.
I am only a novice.
I had installed macports, homebrew, and anaconda.
Xcode and the associated commandline tools muddy the waters further. 
The `make` program often fails to find a required library, even though it may be present.
In my case I had to specify the location of one library file with an export command in order to compile.
My approach may inspire similar solutions when libraries are not found.
