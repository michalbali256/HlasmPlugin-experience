ifeq ($(OS),Windows_NT)
    DEL_COMMAND := del
else
    DEL_COMMAND := rm
endif

all: documentation.pdf

documentation.pdf: documentation.tex $(wildcard *.tex) $(wildcard img/*)
	pdflatex documentation
	bibtex documentation
	texindy -M indexConfig.xdy -C utf8 documentation.idx
	pdflatex documentation
	pdflatex documentation

clean:
	$(DEL_COMMAND) -f pdfa.xmpi *.synctex* *.nlo *.nls *.ilg *.log *.dvi *.aux *.toc *.lof *.lot *.out *.bbl *.blg *.ind *.dix *.idx
	$(DEL_COMMAND) -f documentation.pdf
