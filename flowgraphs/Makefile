GRCC = grcc

.PHONY: all
all: flowgraph05

.PHONY: flowgraph
flowgraph: hier OreSat0.py OreSat0.cfg

.PHONY: flowgraph05
flowgraph05: OreSat05.py OreSat05.cfg

.PHONY: hier
hier: hier/*.grc
	$(GRCC) -u $^

%.py: %.grc
	$(GRCC) $^

OreSat0.py: OreSat0.grc
	$(GRCC) $^
	@echo "### The errors about '__file__' not being defined are safe to ignore"
	patch $@ patches/OreSat0-hackrf.patch

%.cfg: %.cfg-dist
	cp -n $^ $@

.PHONY: clean
clean:
	rm OreSat0.py

.PHONY: distclean
distclean: clean
	rm OreSat0.cfg
