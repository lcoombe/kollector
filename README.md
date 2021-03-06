# Kollector

**_de novo_ Targeted Gene Assembly**

Kollector is a targeted assembly tool using [BioBloom Tools](http://www.bcgsc.ca/platform/bioinfo/software/biobloomtools) and [ABySS](http://www.bcgsc.ca/platform/bioinfo/software/abyss).


### Installation

Kollector is implemented as a bash script and can be run directly from the downloaded github repo.

### Dependencies 

Kollector requires the following tools:

* [ABySS](http://www.bcgsc.ca/platform/bioinfo/software/abyss)(min v1.5.x, tested on 
  2.0.3 )
* [BioBloomTools](http://www.bcgsc.ca/platform/bioinfo/software/biobloomtools) (min 
  v2.1.x)
* [GMAP/GSNAP](http://research-pub.gene.com/gmap)
* [BWA](http://bio-bwa.sourceforge.net)
* [Samtools](http://www.htslib.org/)

The binaries for the above tools are needed to be added to your path. The easiest way to install these tools is to install [Linuxbrew](http://linuxbrew.sh/) and add linuxbrew binary to your `PATH`:

```{bash}
export PATH="$HOME/.linuxbrew/bin:$PATH"
```

Installing any package will be as easy as:

```{bash}
brew install (package name)
```
For example to install ABySS run:

```{bash}
brew install abyss
```

To see an simple example for running Kollector please see the `Example` section below.

## Running Kollector

Kollector consists of three bash scripts:

* `kollector-extract.sh`
* `kollector-recruit.sh`
* `kollector.sh`

`kollector.sh` is the main script.It calls `kollector-recruit.sh` to recruit PET reads,invokes ABySS to perform contig assembly and calls `kollector-extract.sh` to extract assembled targets and finally aligns input transcripts to the assembly with GMAP.

To run Kollector simply run :

`./kollector.sh <params> <seed.fa> <read1.fa> <read2.fa>`


The parameters options are as following:

```{r} 
    -h        show this help message
    -j N      threads [1]
    -r N      min match length for tagging reads. Decimal value are
              the proportion of the valid k-mers and integer values
              will require that minimum number of bases to match [0.7]
    -s N      min match length for recruiting reads [0.50]
    -k N      k-mer size for ABySS contig assembly [32]
    -K N      k-mer size for read overlap detection [25]
    -n N      max k-mers to recruit in total [10000]
    -o FILE   output file prefix ['kollector']
    -p FILE   Bloom filter containing repeat k-mers for
              exclusion from scoring calculations; must match
              k-mer size selected with -K opt [disabled]
    -B N      pass bloom filter size to abyss 2.0.2 
              (B option, to be written: ex - 100M, optional)
```


 `<seed.fa>` is the input transcript sequence in a form of FASTA file to recruit reads.
 
 `<read1.fa>` and `<read2.fa>` are the PET sequencing reads and could be in a form of FASTA/FASTQ files.
All the input files can be gzipped.

The `-r` and `-s` parameters may be integer values if exact match lengths of the reads are desired rather than k-mer proportions during read tagging or recruitment.

### Running Kollector Iteratively 
`kollector-multiple.sh` is a wrapper script for running Kollector iteratively with a large number of targets. After each iteration targets that are successfully assembled are removed from the input, while the failed ones are re-tried in then next iteration with a lower r value. `kollector-multiple.sh` is run with same arguments as `kollector.sh`, with two additional parameters:
```{r} 
    -max_iterations N        number of iterations to be performed [5]
    -decrement N             decrement of the r parameter in each iteration [0.1]
  
```
By using the `output file prefix`_assembledtargets.fa is the primary output and the `output file prefix`_hitlist.txt can tell you which successfully assembled targets assocate to each bait sequence.

### Example : Testing Kollector with C.elegans dataset

The `test` folder contains a `Makefile` that runs kollector on C.elegans data set.
The test target  C. elegans transcript C17E4.10 FASTA file (Acession NM_060106.6, RefSeq mRNA sequences longer than 1 kb) is provided in `data` folder.
To run Kollector on C.elegans data set simply run `make` in the test folder. Make sure `linuxbrew` 
is on your `PATH` as mentioned in the in `Dependancies` section.

The `Makefile` installs `samtools`,  `biobloomtools`,`abyss`,`gmap-gsnap` and `bwa` via `linuxbrew`.
Then,it downloads WGS read pairs FASTQ.gz files (SRA Accession: DRR008444,read length:110pb, total number of base pairs:7.5G and 75x raw coverage) to the data folder.
Finally, it runs kollector pipeline with the default parameters mentioned above. The output of kollector is test-kollector_assembledtargets.fa file. 

### Troubleshooting

Kollector.sh is expected to be run with its relative path to the other scripts and it 
should autodetect your path and add it during runtime. If you get errors related to 
missing scripts you can add the kollector bin directory directly to be added to your `PATH`:
```{bash}
export PATH="$HOME/kollector/bin:$PATH"
```

### Citing Kollector
If you find Kollector useful in your work please cite:
Erdi Kucuk, Justin Chu, Benjamin P. Vandervalk, S. Austin Hammond, René L. Warren, Inanc Birol; Kollector: transcript-informed, targeted de novo assembly of gene loci. Bioinformatics 2017 btx078. doi: 10.1093/bioinformatics/btx078
