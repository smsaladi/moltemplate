To create the file "moltemplate_manual.pdf", install pdflatex and run
these commands:

pdflatex moltemplate_manual
pdflatex moltemplate_manual
bibtex moltemplate_manual
bibtex moltemplate_manual
pdflatex moltemplate_manual
pdflatex moltemplate_manual

To build sphnix html docs, make sure deps are installed:

    pip install -r requirements.txt

then run 

    make html

Site will build into `build`

