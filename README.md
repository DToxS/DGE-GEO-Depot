# Analyzing 3'-DGE Random-Primed mRNA-Sequencing Data

This is a computational pipeline designed for analyzing the raw *FASTQ* data files generated from the 3'-end DGE mRNA-sequencing method and generating a set of differentially expressed genes (DEGs) for each drug treatment and each cell line.

## Software Requirements

The computational pipeline requires the following software programs:

- *Bash* shell in Unix-like operating systems (e.g. Linux, MacOS, Unix)
- *GCC* (>= v7) or *Clang* (>= v5) C++ compiler
- *CMake* (>= v3.8)
- *STAR* (>= v2.5.3a)
- *featureCounts* (from *Subread* >= v1.6.2)
- *Samtools* (>= v1.8)
- *R* (>= v3.0.0) with the following packages:
  - *cluster*
  - *edgeR*
  - *gplots*
  - *graphics*
  - *matrixStats*
  - *methods*
  - *RColorBrewer*
  - *stats*
  - *tools*

## Data Preparation

The computational pipeline requires a list of input datasets and a tree structure of data directories.

### Data List

The input datasets for the computational pipeline include:

- Multiple sets of compressed mRNA-sequencing *FASTQ* data files `RNAseq_[Set].[Well].fastq.gz`
- Multiple experiment design files `DGE-RNAseq-Experiment-Design-[Set]-[Subset].tsv.gz`
- The *UCSC* human genome reference library `Reference-Library.tar.gz`
- The data analysis programs `Program-Source-Codes.tar.gz`
- Multiple configuration files for the differential comparison analysis `Differential-Comparison-Configs-[Set]-[Subset].tar.gz`
- Multiple parameter files for the differential comparison analysis `Differential-Comparison-Params-[Set]-[Subset].tar.gz`

In above datasets, the `Set` and `Subset` tags can take the following combinations of value:

|   Set    |   Subset   |
| :------: | :--------: |
| 20150409 |    N/A     |
| 20150503 |    N/A     |
| 20151120 |    N/A     |
| 20161108 |    Cor     |
| 20161108 | Spec-Fixed |
| 20161108 | Spec-Var-1 |
| 20161108 | Spec-Var-2 |
| 20161108 | Spec-Var-3 |

And the `Well` tag in the *FASTQ* data file name uniquely identifies each individual *FASTQ* file within a set of mRNA sequencing data files marked by the `Set` tag.

### Data Preparation

Before launching the computational pipeline, a directory tree with corresponding data files needs to be created in the following steps:

1. Create a top directory `DGE-GEO-Depot` with the following tree structure:
   ```
   DGE-GEO-Depot
   ├── Dataset-20150409
   │   ├── Aligns
   │   ├── Configs
   │   ├── Counts
   │   ├── Params
   │   ├── Scripts
   │   └── Seqs
   ├── Dataset-20150503
   │   ├── Aligns
   │   ├── Configs
   │   ├── Counts
   │   ├── Params
   │   ├── Scripts
   │   └── Seqs
   ├── Dataset-20151120
   │   ├── Aligns
   │   ├── Configs
   │   ├── Counts
   │   ├── Params
   │   ├── Scripts
   │   └── Seqs
   ├── Dataset-20161108
   │   ├── Aligns
   │   ├── Configs
   │   ├── Counts
   │   ├── Params
   │   ├── Scripts
   │   └── Seqs
   ├── Programs
   └── References
   ```
2. Place all gzipped *FASTQ* data files in the `Seqs` directory. 
3. Extract the following compressed data files into corresponding directories:
   - Extract `DGE-RNAseq-Experiment-Design-[Set]-[Subset].tsv.gz` into the `Counts` directory of corresponding `[Set]` of dataset.
   - Extract `Reference-Library.tar.gz` into the `References` directory.
   - Extract `Program-Source-Codes.tar.gz` into the `Programs` directory.
   - Extract `Differential-Comparison-Configs-[Set]-[Subset].tar.gz` into the `Configs` directory of corresponding `[Set]` of dataset.
   - Extract `Differential-Comparison-Params-[Set]-[Subset].tar.gz` into the `Params` directory of corresponding `[Set]` of dataset.

## Program Compilation

After `Program-Source-Codes.tar.gz` is extracted into the `Programs` directory, the source codes of a computational program that extracts the mRNA sequence reads tagged with unique UMI barcodes will be placed in the directory `RNAseq-Pipeline-Tools-Classic`. This program is written in C++ programming language and needs to be compiled into a binary executable file using *GCC* or *Clang* compiler that supports C++17 features. Since the source codes have been configured with the *CMake* build system, it becomes quite simple to build the source codes in the following a few steps:

```bash
# Create a build directory.
mkdir $HOME/Build-RNAseq-Pipeline-Tools-Classic
cd $HOME/Build-RNAseq-Pipeline-Tools-Classic
# Create the build configuration files.
cmake -DCMAKE_BUILD_TYPE=Release [RNAseq-Pipeline-Tools-Classic Source-Code Directory]
# Build the binary excecutable.
cmake --build .
```

Once the compilation is finished, a binary executable file `SAM-Alignment-Counter` will be generated in the `SAM-Alignment-Counter` sub-directory under `$HOME/Build-RNAseq-Pipeline-Tools-Classic`. To be able to run this program, just copy it to any location that is in the search path of the system. For example, the following command copy the program to a system directory `/usr/local/bin`:

```bash
cp $HOME/Build-RNAseq-Pipeline-Tools-Classic/SAM-Alignment-Counter/SAM-Alignment-Counter /usr/local/bin/
```

Now this custom built program is ready to be used by the shell scripts in the `DGE-GEO-Depot/Programs/Feature-Counts` directory.

## Analysis Workflow

The workflow of this computational pipeline for analyzing the 3'-DGE mRNA-sequence data includes the following steps:

### Sequence Alignment

Refer to the [Sequence Alignment](https://github.com/DToxS/Sequence-Alignment) section.

### Feature Counts

Refer to the [Feature Counts](https://github.com/DToxS/Feature-Counts) section.

### Differential Comparison

Refer to the [Differential Comparison](https://github.com/DToxS/Differential-Comparison) section.

## Result Data

- Single or multiple tables of uniquely aligned sequence reads for all reference genes and all sequenced samples `DGE-RNAseq-Read-Counts-[Set]-[Subset].tsv` (compressed as `DGE-RNAseq-Read-Counts-[Set]-[Subset].tsv.gz`) in the `Counts` directory of each `[Set]` of dataset.
- Multiple sets of statistical comparison result files for the DEGs identified at all drug treatments and in all cell lines with a file name pattern of `Human-[Cell Line]-Hour-48-Plate-[Plate Number]-Calc.CTRL-[Drug Name].tsv`, located in the `Results/FDR-[Value]` directory of each `[Set]` of dataset under the `DGE-GEO-Depot` top directory.

