# SemiBin

SemiBin is a command line tool for metagenomic binning with semi-supervised siamese neural network using additional information from reference genomes and contigs themselves. It will output the reconstructed bins in single sample/co-assembly/multi-samples binning mode.

### Single sample binning

Single sample binning means that each sample is assembled and binned
independently.

This mode allows for parallel binning of samples and avoid cross-sample
chimeras, but it does not use co-abundance information across samples.

### Co-assembly binning

Co-assembly binning means samples are co-assembled first (as if the pool of
samples were a single sample) and binned later.

This mode can generate better contigs (especially from species that are at a
low abundance in any individual sample) and use co-abundance information, but
co-assembly can lead to intersample chimeric contigs and binning based on
co-assembly dows not retain sample specific variation. It is appropriate when
the samples are very similar.

### Multi-sample binning

With multi-sample binning, multiple samples are assembled and binned
individually, but _information from multiple samples is used together_.
This mode can use co-abundance information and retain sample-specific
variation at the same time. However, it has increased computational costs.

This mode is implemented by concatenating the contigs assembled from the
individual samples together and then mapping reads from each sample to this
concatenated database.

## Commands

#### `single_easy_bin`

Reconstruct bins with single or co-assembly binning using one line command.

* `-i/--input-fasta` : Path to the input contig fasta file (gzip and bzip2 compression are accepted).
* `-b/--input-bam`: Path to the input BAM files.
* `-o/--output`: Output directory (will be created if non-existent).
* `--cannot-name:` Name for the cannot-link file(Default: cannot).
* `-r/--reference-db`: GTDB reference file.(Default: $HOME/.cache/SemiBin/mmseqs2-GTDB/GTDB) If not set `--reference-db` and can not find GTDB in the default path, we will download GTDB to the default path.
* `-p/--processes/-t/--threads`: Number of CPUs used(0: use whole).
* `--minfasta-kbs`: minimum bin size in kilo-basepairs (Default: 200).
* `--recluster` : Recluster bins(which will take more time and better results).
* `--epoches`: Number of epoches used in the training process(Default: 20).
* `--batch-size`: Batch size used in the training process(Default: 2048).
* `--max-node`: Percentage of contigs that considered to be binned(Default: 1).
* `--max-edges`: The maximum number of edges that can be connected to one contig(Default: 200).
* `--random-seed`: Random seed to reproduce results.
* `--environment`: Environment for the built-in model(human_gut/dog_gut/ocean).

#### multi_easy_bin

Reconstruct bins with multi-samples binning using one line command.


* `-b/--input-bam`: Path to the input BAM files. Unlike in `single_easy_bin`, you can pass multiple BAM files, one per sample
* `-s/--separator`: Used when multiple samples binning to separate sample name and contig name(Default is `:`).

The following options (including synonyms) are the same as for
`single_easy_bin`: `--input-fasta`, `--output`, `--reference-db`,
`--processes`, `--minfasta-kbs`, `--recluster`,`--epoches`, `--batch-size`, `--max-node`, and
`--max-edges`, `--random-seed`.

#### predict_taxonomy

Run the contig annotations using mmseqs with GTDB and generate cannot-link file used in the semi-supervised deep learning model training.

* `-i/--input-fasta` : Path to the input contig fasta file (gzip and bzip2 compression are accepted).
* `-o/--output`: Output directory (will be created if non-existent).
* `--cannot-name:` Name for the cannot-link file(Default: cannot).
* `-r/--reference-db`: GTDB reference file.(Default: $HOME/.cache/S3N2Bin/mmseqs2-GTDB/GTDB) If not set `--reference-db` and can not find GTDB in the default path, we will download GTDB to the default path.

#### generate_data_single

Generate training data(data.csv;data_split.csv) for single and co-assembly binning.

* `-i/--input-fasta` : Path to the input contig fasta file (gzip and bzip2 compression are accepted).
* `-b/--input-bam`: Path to the input bam files. If there are several samples, you can input several bam files.
* `-o/--output`: Output directory (will be created if non-existent).
* `-p/--processes/-t/--threads`: Number of CPUs used(0: use whole).

#### generate_data_multi

Generate training data(data.csv;data_split.csv) for multi-samples binning.

* `-i/--input-fasta` : Path to the input contig fasta file (gzip and bzip2 compression are accepted).
* `-b/--input-bam`: Path to the input bam files. If there are several samples, you can input several bam files.
* `-o/--output`: Output directory (will be created if non-existent).
* `-s/--separator`: Used when multiple samples binning to separate sample name and contig name(Default is `:`).
* `-p/--processes/-t/--threads`: Number of CPUs used(0: use whole).

#### train ####

* `-i/--input-fasta` : Path to the input contig fasta file (gzip and bzip2 compression are accepted).
* `-b/--input-bam`: Path to the input bam files. If there are several samples, you can input several bam files.
* `-o/--output`: Output directory (will be created if non-existent).
* `--data`: Path to the input data.csv file.
* `--data_split`: Path to the input data_split.csv file.
* `-c/--cannot-link` : Path to the input cannot link file generated from other additional biological information, one row for each cannot link constraint. The file format: contig_1,contig_2.
* `--epoches`: Number of epoches used in the training process(Default: 20).
* `--batch-size`: Batch size used in the training process(Default: 2048).
* `-p/--processes/-t/--threads`: Number of CPUs used(0: use whole).
* `--random-seed`: Random seed to reproduce results.
* `--mode`:  [single/several]Train models from one sample or several samples(train model across several samples can get better pre-trained model for single-sample binning.) In several mode, must input data, data_split, cannot, fasta files for corresponding sample with same order.

#### bin

Training the model and clustering contigs into bins.

* `-i/--input-fasta` : Path to the input contig fasta file (gzip and bzip2 compression are accepted).
* `-b/--input-bam`: Path to the input bam files. If there are several samples, you can input several bam files.
* `-o/--output`: Output directory (will be created if non-existent).
* `--data`: Path to the input data.csv file.
* `--minfasta-kbs`: minimum bin size in kilo-basepairs (Default: 200).
* `--recluster` : Recluster bins(which will take more time and better results).
* `--max-node`: Percentage of contigs that considered to be binned(Default: 1).
* `--max-edges`: The maximum number of edges that can be connected to one contig(Default: 200).
* `-p/--processes/-t/--threads`: Number of CPUs used(0: use whole).
* `--random-seed`: Random seed to reproduce results.
* `--model`: Path to the trained model.
* `--environment`: Environment for the built-in model(human_gut/dog_gut/ocean).

#### download_GTDB

Download reference genomes(GTDB).

* `-r/--reference-db`: GTDB reference file path to download(~/path/GTDB).(Default: $HOME/.cache/SemiBin/mmseqs2-GTDB/GTDB) If not set `--reference-db` , we will download GTDB to the default path.

