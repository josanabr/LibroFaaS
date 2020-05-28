.PHONY: phony

FIGURES = $(shell find . -name '*.svg')
DIRBIB = "/Users/johnsanabria/Documents/BibTeX/FaaS.bib"

PANDOCFLAGS =                        \
  --table-of-contents                \
  --pdf-engine=pdflatex               \
  --from=markdown                    \
  --number-sections                  \
  --listings                  \
  --bibliography $(DIRBIB) \
  --indented-code-classes=javascript \
  --filter pandoc-citeproc \
  --highlight-style=monochrome       \
  -V mainfont="Palatino"             \
  -V documentclass=report            \
  -V papersize=letter                    \
  -H TEX-disable_float.tex                    \
  -H TEX-make-code-scriptsize.tex                    \
  -H TEX-make-box.tex                    \
  -V geometry:margin=1in

all: phony output/book.pdf

output/%.pdf: %.md $(FIGURES) Makefile | output
	pandoc $< metadatos.yaml -o $@ $(PANDOCFLAGS)

output/%.epub: %.md $(FIGURES) Makefile | output
	pandoc $< -o $@ $(PANDOCFLAGS)

output:
	mkdir ./output

clean: phony
	rm -rf ./output

open: phony output/book.pdf
	open output/book.pdf
