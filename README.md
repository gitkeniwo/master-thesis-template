# Master Thesis LaTeX 

## Prerequisites

### MacTeX Installation

1. Install MacTeX using Homebrew
   ```bash
   brew install --cask mactex
   ```

   This will install the full MacTeX distribution (~4GB), which includes:
   - LaTeX distribution (TeX Live)
   - TeX Live Manager (tlmgr)
   - GUI applications (TeXShop, BibDesk, LaTeXiT, etc.)

2. Configure PATH for your shell

   **For Bash** (add to `~/.bash_profile` or `~/.bashrc`):
   ```bash
   # Add MacTeX to PATH
   export PATH="/Library/TeX/texbin:$PATH"
   ```

   Then reload your configuration:
   ```bash
   source ~/.bash_profile
   ```

   **For Fish** (add to `~/.config/fish/config.fish`):
   ```fish
   # Add MacTeX to PATH
   fish_add_path /Library/TeX/texbin
   ```

   Then reload your configuration:
   ```fish
   source ~/.config/fish/config.fish
   ```

3. Verify installation
   ```bash
   which latex
   # Should output: /Library/TeX/texbin/latex

   latex --version
   # Should show TeX version information
   ```

### Using TeXLive Package Manager `tlmgr`

`tlmgr` is the package manager for **TeXLive** distributions.

**TeXLive** is a distro of LaTeX packages and programs with `tlmgr` being its package manager.

`tlmgr` pulls packages from the TeXLive repository and installs them into your local TeXLive installation.

**CTAN** (Comprehensive TeX Archive Network) is the main repository for LaTeX packages, but `tlmgr` uses its own repository to manage packages.

If you are hosting Overleaf community edition + local TeXLive environment, you can also use `tlmgr` to manage LaTeX packages.

1. Update tlmgr itself
   ```bash
   sudo tlmgr update --self
   ```

2. Update all installed packages
   ```bash
   sudo tlmgr update --all
   ```

3. Search for packages
   ```bash
   tlmgr search <package-name>
   ```

4. Install a new package
   ```bash
   sudo tlmgr install <package-name>
   ```

5. List installed packages
   ```bash
   tlmgr list --only-installed
   ```

6. Get package information
   ```bash
   tlmgr info <package-name>
   ```

## VSCode Setup

### Install LaTeX Workshop Extension

1. Search for "LaTeX Workshop"
2. Install the extension by James Yu

### Configure VSCode Settings

The project includes a pre-configured [.vscode/settings.json](.vscode/settings.json) file with the following setup:

```json
{
    "latex-workshop.latex.outDir": "./build",
    "latex-workshop.latex.tools": [
        {
            "name": "latexmk",
            "command": "latexmk",
            "args": [
                "-synctex=1",
                "-interaction=nonstopmode",
                "-file-line-error",
                "-pdf",
                "-outdir=./build",
                "%DOC%"
            ]
        },
        {
            "name": "copy-pdf",
            "command": "cp",
            "args": [
                "./build/%DOCFILE%.pdf",
                "./"
            ]
        }
    ],
    "latex-workshop.latex.recipes": [
        {
            "name": "latexmk + copy",
            "tools": [
                "latexmk",
                "copy-pdf"
            ]
        }
    ]
}
```

- **Build output directory**: All auxiliary files are generated in `./build/` to keep the root directory clean
- **Build tool**: Uses `latexmk` with `-pdf` flag for automatic compilation
- **Post-build step**: Automatically copies the final PDF from `./build/` to the root directory
- **Recipe**: Combines `latexmk` compilation with PDF copying in one command

## Project Structure

```
.
├── appendices
│   ├── appendix_a.tex
│   ├── appendix_b.tex
│   ├── appendix_c.tex
│   ├── appendix_d.tex
│   └── main.tex
├── build
│   ├── appendices
│   ├── chapters
│   ├── thesis.aux
│   ├── thesis.bbl
│   ├── thesis.blg
│   ├── thesis.brf
│   ├── thesis.fdb_latexmk
│   ├── thesis.fls
│   ├── thesis.lof
│   ├── thesis.log
│   ├── thesis.lol
│   ├── thesis.lot
│   ├── thesis.out
│   ├── thesis.pdf
│   ├── thesis.synctex.gz
│   ├── thesis.toc
│   └── titlepage.aux
├── chapters
│   ├── 0-abstract.tex
│   ├── 0-preface.tex
│   ├── 1-introduction.tex
│   ├── 2-background.tex
│   ├── 3-related-work.tex
│   ├── 4-methodology.tex
│   ├── 5-results-evaluation.tex
│   ├── 6-conclusions.tex
│   └── 7-discussion.tex
├── figures
│   └── tue-logo-high.PNG
├── output.pdf
├── README.md
├── references.bib
├── thesis.pdf
├── thesis.sty
├── thesis.tex
└── titlepage.tex

7 directories, 37 files
```

## Building the Thesis

### Using VSCode (Recommended)

1. Open [thesis.tex](thesis.tex) in VSCode
2. Click the "Build LaTeX project" button (or press Cmd+Option+B)
3. The extension will compile the document and copy the PDF to the root directory
4. View the PDF by clicking "View LaTeX PDF" (or press Cmd+Option+V)

### Using Command Line

```bash
# Build using latexmk (recommended)
latexmk -pdf -outdir=./build thesis.tex

# Copy the PDF to root directory
cp ./build/thesis.pdf ./

# Clean auxiliary files
latexmk -c -outdir=./build
```

### Using Traditional LaTeX Commands

```bash
cd build/
pdflatex ../thesis.tex
bibtex thesis
pdflatex ../thesis.tex
pdflatex ../thesis.tex
cp thesis.pdf ../
```

## Git Configuration

The [.gitignore](.gitignore) file is configured to exclude:

- **Build directory**: `build/` (all auxiliary compilation files)
- **Auxiliary files**: `*.aux`, `*.log`, `*.out`, `*.toc`, etc.
- **Bibliography files**: `*.bbl`, `*.blg`, `*.bcf`, etc.
- **Build tool files**: `*.fdb_latexmk`, `*.synctex.gz`, etc.
- **Index and glossary files**: `*.idx`, `*.ind`, `*.glo`, `*.gls`, etc.

**Note**: The final `thesis.pdf` is currently tracked in the repository. If you prefer not to version control the PDF, add `*.pdf` to [.gitignore](.gitignore).

## Troubleshooting

### LaTeX command not found

If you get a "command not found" error after installing MacTeX:
1. Verify the installation path exists: `ls -la /Library/TeX/texbin`
2. Check if the PATH is correctly set: `echo $PATH | grep texbin`
3. Restart your terminal or reload the shell configuration

### Missing LaTeX packages

If compilation fails due to missing packages:
```bash
# Update all packages first
sudo tlmgr update --all

# Install specific missing package
sudo tlmgr install <package-name>
```

### Build directory issues

If you encounter permission issues with the build directory:
```bash
# Remove and recreate the build directory
rm -rf build/
mkdir build/
```

### VSCode LaTeX Workshop not working

1. Ensure `latexmk` is installed and in PATH: `which latexmk`
2. Reload VSCode window: Cmd+Shift+P "Developer: Reload Window"
3. Check the LaTeX Workshop output panel for detailed error messages

## License

This thesis template is based on the TU/e Style Master Thesis template for LaTeX (Public version 1.0, 2010-2013 by Thijs Nugteren and Joos Buijs).

## Contributing

This is a personal thesis project. If you're using this template for your own thesis, feel free to fork and modify as needed.
