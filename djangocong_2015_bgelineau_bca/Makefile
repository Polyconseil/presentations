default: all

DOTS = $(shell find . -name '*.dot')
RST = $(shell find . -name '*.rst')

PNGS = $(patsubst %.dot,%.png,$(DOTS))
HTML = $(patsubst %.rst,%.html,$(RST))

.PHONY: all clean

all: $(HTML) $(PNGS)

%.html: %.rst
	landslide $< -d $@ -t polyconseil -i

%.png: %.dot
	dot -Tpng -o $@ $<
	mogrify -resize '80%' $@

clean:
	@rm -f $(HTML) $(PNGS)
