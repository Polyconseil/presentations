default: all

DOTS = $(shell find . -type f -name '*.dot' -printf '%P\n')
RST = $(shell find . -type f -name '*.rst' -printf '%P\n')
OUTDIR ?= build

PNGS = $(patsubst %.dot,$(OUTDIR)/%.png,$(DOTS))
HTML = $(patsubst %.rst,$(OUTDIR)/%.html,$(RST))

.PHONY: all clean prepare

$(OUTDIR):
	mkdir -p $(OUTDIR)

all: $(OUTDIR) $(HTML) $(PNGS)

$(OUTDIR)/%.html: %.rst
	landslide $< -d $@ -t polyconseil -i

$(OUTDIR)/%.png: %.dot
	dot -Tpng -o $@ $<
	mogrify -resize '80%' $@

clean:
	rm -rf $(OUTDIR)
