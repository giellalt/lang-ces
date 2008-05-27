# This is a makefile that builds the Czech morphological parser
# *****************************************************************

# This is a file that builds a Czech parser based upon
# xfst and not twolc.

# Here we build the final generator , an inverted transducer of the analyzer.
# It is dependent upon ces.save

XFST = xfst -utf8
LEXC = lexc -utf8


ifeq (victorio.uit.no, $(shell hostname))
XFST = /opt/sami/xerox/c-fsm/ix86-linux2.6-gcc3.4/bin/fst -utf8
LEXC = /opt/sami/xerox/c-fsm/ix86-linux2.6-gcc3.4/bin/lexc -utf8
endif



SRCS = ces-lex.txt noun-ces-morph.txt noun-ces-lex.txt \
        adj-ces-lex.txt adj-ces-morph.txt verb-ces-lex.txt verb-ces-morph.txt \
	adv-ces-lex.txt pron-ces-lex.txt punct-ces-lex.txt \
	pp-ces-lex.txt cc-ces-lex.txt cs-ces-lex.txt

# This is the ultimate goal, the inverse transducer ices.fst
# It depends on all the files.

ices.fst: ../bin/ices.fst
../bin/ices.fst: ../bin/ces.fst
	@echo
	@echo "*** Building the inverse ices.fst ***"
	@echo
	@printf "load ../bin/ces.save \n\
	invert net \n\
	save stack ../bin/ices.fst \n\
	quit \n" > ../../tmp/ices-fst-script
	$(XFST) < ../../tmp/ices-fst-script
	@rm -f ../../tmp/ices-fst-script


# This goal is to build the ... almost ... final analyser, ces.fst. 

ces.fst: ../bin/ces.fst
../bin/ces.fst: ../bin/ces.save ../bin/caseconv.fst
	@echo
	@echo "*** Building ces.fst ***" ;
	@echo
	@printf "read regex [[@\"../bin/ces.save\"] .o. \
	[@\"../bin/caseconv.fst\"]] ; \n\
	save stack ../bin/ces.fst \n\
	quit \n" > ../../tmp/ces-fst-script
	$(XFST) < ../../tmp/ces-fst-script
	@rm -f ../../tmp/ces-fst-script


# The second goal is to build the caseconv.fst file
# This goal depends on case.regex

caseconv.fst: ../bin/caseconv.fst
../bin/caseconv.fst: case.regex
	@echo
	@echo "*** Building caseconv.fst ***" ;
	@echo
	@printf "read regex < case.regex \n\
	save stack ../bin/caseconv.fst \n\
	quit \n" > ../../tmp/caseconv-script
	$(XFST) < ../../tmp/caseconv-script
	@rm -f ../../tmp/caseconv-script


# Here we build ces.save, without capital normalisation, 
# and working as input to the inverse ices.fst.

ces.save: ../bin/ces.save
../bin/ces.save: ../bin/xfst-ces.bin ../bin/ces-lex.save
	@echo
	@echo "*** Building the parser ces.save ***"
	@echo
	@printf "read regex [[@\"../bin/ces-lex.save\"] .o. \
	[@\"../bin/xfst-ces.bin\"]] ; \n\
	save stack ../bin/ces.save \n\
	quit \n" > ../../tmp/ces-save-script
	$(XFST) < ../../tmp/ces-save-script
	@rm -f ../../tmp/ces-save-script

# The first goal is to build ces-lex.save
# This goal depends on a bunch of lexicon files

ces-lex.save: ../bin/ces-lex.save
../bin/ces-lex.save: $(SRCS)
	@echo
	@echo "*** Building ces-lex.save ***" ;
	@echo
	printf "compile-source $(SRCS) \n\
	save-source ../bin/ces-lex.save \n\
	quit \n" > ../../tmp/ces-lex-save-script
	$(LEXC) < ../../tmp/ces-lex-save-script
	rm -f ../../tmp/ces-lex-save-script

# The second goal is to build xfst-ces.bin
# This goal depends on xfst-ces.txt

xfst-ces.bin: ../bin/xfst-ces.bin
../bin/xfst-ces.bin: xfst-ces.txt
	@echo
	@echo "*** Building xfst-ces.bin ***" ;
	@echo
	@printf "source xfst-ces.txt \n\
	save stack ../bin/xfst-ces.bin \n\
	quit \n" > ../../tmp/xfst-ces-script
	$(XFST) < ../../tmp/xfst-ces-script
	@rm -f ../../tmp/xfst-ces-script

clean:
	@rm -f ../bin/*.bin ../bin/*.fst ../bin/*.save


