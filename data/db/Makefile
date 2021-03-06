BIN:=../../src/crawler
PAP:=./papers
OALISTURL:=ftp://ftp.ncbi.nlm.nih.gov/pub/pmc/oa_file_list.txt

all:
	make init
	make phenotypes
	make gwas-catalog
	make dl-papers
	make dl-non-gwas-papers
	make gwas-central
	# make gwas-catalog snpedia

clean:
	-rm /tmp/gwas.sql

init:
	python $(BIN)/parse_gwas_catalog.py --init

snpedia:
	make -C ../associations/snpedia/

phenotypes:
	make -C ../phenotypes/efo load-ontology
	make -C ../phenotypes/snorkel load-ontology

gwas-catalog: ../associations/gwas_catalog
	make -C $<
	python $(BIN)/parse_gwas_catalog.py \
	  --phenotypes $</trait_mappings.tsv \
	  --crawl $</gwas_catalog.tsv

gwas-central: all-paper-ids.txt
	python $(BIN)/parse_gwas_central.py \
		--ids $< \
		--min-neg-log-pval 4

all-paper-ids.txt:
	  ls $(PAP) | grep xml | cut -f 1 -d '.' > $@

first-100-paper-ids.txt:
	  ls $(PAP) | grep xml | cut -f 1 -d '.' | head -n 100 > $@

# TODO: add parsing intructions
snpedia-associations:
	make -C ../associations/snpedia/

dl-papers: open-access.txt
	mkdir -p $(PAP)
	python $(BIN)/dl_papers.py \
	  --mark-open-access $< \
	  --download-oa $(PAP)

dl-non-gwas-papers:
	mkdir -p ./non-gwas
	python $(BIN)/dl_non_gwas_papers.py \
	  --download-folder ./non-gwas \
	  --num-files 100

dl-predicted-papers:
	mkdir -p ./predicted-papers
	python $(BIN)/dl_predicted_papers.py \
	  --download-folder ./predicted-papers \
	  --filter-type weak

open-access.txt:
	wget $(OALISTURL) -O $@.tmp
	mv $@.tmp $@

