**MetaCherchant** is a tool for analysing genomic environment of a nucleotide sequence
within a metagenome. The implementation is based on [MetaFast](https://github.com/ctlab/metafast/wiki) source code.

Starting from [version 0.1.0](https://github.com/ctlab/metacherchant/releases/tag/v0.1.0) it supports Hi-C reads input to facilitate genomic context extraction for extrachromosomal DNA. For detailed instructions, please refer to [wiki page](https://github.com/ctlab/metacherchant/wiki).

It also provides user with tools for comparing two metagenomes. For more details, please consult the 
[reads classifier description](ReadsClassifier.md).

## Content

* [Installation](#installation)
* [Running MetaCherchant](#running-metacherchant)
* [Usage example](#usage-example)
* [Output description](#output-description)
* [Citation](#citation)

========

### Installation

To run MetaCherchant you need to have JRE version 1.8 or higher installed and either of
these three files: `metacherchant.sh` for Linux/MacOS, `metacherchant.bat` for Windows or `metacherchant.jar` for any OS.

### Running ***MetaCherchant***

To run MetaCherchant use the following syntax:
* `metacherchant.sh [<Launch options>]`
* `metacherchant.bat [<Launch options>]`
* `java -jar metacherchant.jar [<Launch options>]`

### Usage example

#### Single-metagenome mode

Here is a bash script showing a typical usage of MetaCherchant:

~~~
./metacherchant.sh --tool environment-finder \
	--k 31 \
	--coverage=5 \
	--reads $READS_DIR/*.fasta \
	--seq $GENE_FILE.fasta \
	--output $OUTPUT_DIR/output/ \
	--work-dir $OUTPUT_DIR/workDir \
	--maxkmers=100000 \
	--bothdirs=False \
	--chunklength=100
~~~

* `--k` --- the size of k-mer used in de Bruijn graph.
* `--coverage` the minimum coverage threshold for a k-mer to be included in the graph.
* `--reads` list of all input files with metagenomic reads separated by space. FASTA and FASTQ formats are supported.
* `--seq` a FASTA file with the target nucleotide sequences, for each of which a genomic environment will be built.
* `--output` output folder.
* `--work-dir` working directory with intermediate files and logs.
* `--maxkmers` maximum allowed number of distinct k-mers present in the resulting genomic environment.
* `--bothdirs` flag setting the BFS (breadth-first search) algorithm to make 1 bidirectional pass from the target sequence. If this flag is not set, BFS makes two one-directional passes.
* `--chunklength` minimum length of a contracted graph node to be included in output FASTA file for further analysis.

After the end of analysis, found metagenomic environment can be visualised using de Bruijn graph, as on the figure below. For more information see [output description](#output-description) section.

<img src="https://i.imgur.com/T9cxItu.png" alt="Single-metagenome environment" width="450">

***adeC** gene in genome context of E.faecium. Target AR gene is shown in red.*


#### Differential (multiple-metagenome) mode

In this mode, it is possible to join two or more graphs constructed as described above and join them into a single graph. The example command is:

~~~
./metacherchant.sh \
	--tool environment-finder-multi \
	--seq OXA-347.fasta \
	--work-dir "k31/TUE-S2_3_4/workDir" \
	--output "k31/TUE-S2_3_4" \
	--env "k31/TUE-S2_3/output/env.txt" "k31/TUE-S2_4/output/env.txt"
~~~

* All parameters except the last one are described in the single-mode section.
* `--env` ordered list of `env.txt` files (results of single mode analysis) to be joined into a single graph.

After the end of analysis, found metagenomic environment can be visualised using de Bruijn graph, as on the figure below. For more information see [output description](#output-description) section.

<img src="https://i.imgur.com/F5dxCb8.png" alt="Multiple-metagenome environment" width="450">

*Combined graph of AR gene context produced from two metagenomes
of the same subject. Red color denotes the part of the
graph present only at the time point 2, blue color — only at the point 3, black — at
both points; green color denotes the graph nodes corresponding to the target AR gene*

### Output description

After the end of the analysis, the results can be found in the folder specified in `--output` parameter (if there were multiple sequences in file in `--seq`, there will be separate folder for each one).

* `graph.gfa` - de Bruijn graph in GFA format. Is is recommended to be viewed using [Bandage](http://rrwick.github.io/Bandage/). Version 0.8.0 or later is recommended. Names(s) of the node(s) corresponding to the target sequence ends with a suffix `_start` (you can use 'Find Nodes' feature of Bandage to select them). For a single graph, there is no special coloring. For a graph constructed with a differential mode for exactly 2 metagenomes, green color corresponds to the starting sequence, red - nodes that are only present in the first graph, blue - only in the second graph, black nodes - in both. For 3 or more graphs, node color are greyscale, and darker shade of the node corresponds to more graphs in which that node is contained. 

* `env.txt` contains de Bruijn graph in a simple text format for later use: each line contains a k-mer and its coverage.

* `seqs.fasta` - a FASTA file containing all sufficiently long unitigs (non-branching sequences in de Bruijn graph) for later analysis.

* `tsvs/*` - graph descriptions in .tsv format for use in [Cytoscape](http://www.cytoscape.org/) tool.

### Citation

If you use MetaCherchant in your research, please cite the following publication:

Olekhnovich, E. I., Vasilyev, A. T., Ulyantsev, V. I., Kostryukova, E. S., & Tyakht, A. V. (2018). MetaCherchant: analyzing genomic context of antibiotic resistance genes in gut microbiota. Bioinformatics, 34(3), 434-444.
[https://doi.org/10.1093/bioinformatics/btx681](https://doi.org/10.1093/bioinformatics/btx681)
