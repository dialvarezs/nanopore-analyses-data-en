---
title: 1. Working environment and file types
---
> [!tip] Topics in this chapter
> - Command line (CLI) usage
> - Conda working environment
> - File types
## How to use the command line

Command line or CLI is a text-based interface that allows you to execute commands. Executing programs in command line has multiples advantages, primarily the easy integration with other tools and the ability to execute in enviroment that lack a graphical interface (cloud/HPC). Most bioinformatics tools works only through command line.

A command is a program that when executed in the command line performs a specific action. Upon executing a command, the operating system run a process where, in most cases, it reads an input,  processes the data, and then generates a result (output).

In general, a command have the next sintaxis:
```bash
command [subcommand] [options] <arguments>
```

Usually the `<arguments>` are the input and output files to the command, and are required. On the other hand, options are used to modify the behavior of the command and can be optional (or not). Generally, they are indicated by the format `--option (long way)` or `-o` (short way), and may or may not have an argument associated with the option. Depending on the tools `[subcommands]` may exist.

For example, in the next command:
```bash
samtools view seq.sam --output seq.bam --bam --threads 4
```
- `samtools`: Command (tools). 
- `view`: Subcommand, this tool have several subcommands that have differents functions.
- `seq.sam`: Argument.
- `--output seq.bam`: Option `--output` which has the argument `seq.bam`. This tools supports short form of option, so you can indicate `-o` instead of `--output`.
- `--bam`: Option without arguments.
- `--threads 4`: Options with the argument `4`.


Usually, the order of the arguments and options  specified doesn't matter, in the previous example we could give to the command the options and arguments (the 4 elements after to the subcommand), in any order and the result would be the same. However, there are tools that require the options to come before the arguments for example, so for this, it's important to see the manual or the help of every tool that uou use.

Most commands provides a help in which the options and arguments are detailed. Although it's not required, the convention is to used `-h` or `--help` to see the help.
#### Command line shortcuts?
We can use shortcut upon indicate directories in command line:
- `.` (dot): actual directory.
- `..`: parent directory.
- `~`: "home" (base directory from the user).
- `*`: Indicate that you want to perform an operation over all the files. It can be combinated with other patterns.

#### Remote access to a server (`ssh`)
Secure Shell (`SSH`)  is a protocol for the remote administration that allows users to connect to and use remote servers over the internet. `ssh`  conections are encrypted, which means that sending password, storing data and editing files are secure.

To connect to a remote server we used `ssh` command. `ssh` requires the username and the desire server to connect to. 

```bash
ssh <user>@<host>

ssh jaldridge@10.34.1.15
ssh dalvarez@10.9.0.163
```

To login through SSH in Windows, the Windows console must be open (`cmd`) and run `shh` command with the username and server.

Upon to login the password will be requiered for the server. On many servers, when the user is typing the password, the typical `*` symbols won't show because the password is entered in hidden way.

Once the account is authenticated on the server, the user can access thire session in their `home` directory.

![[ssh.webm]]

#### Move between directories
The `cd` command allows you to move between directories. After the command, you need to specify the destination directory.

```bash
cd ..                 # Mueve la ubicación de la sesion al directorio padre 
cd                    # Mueve al home
cd secuencias         # Ingresa al directorio secuncias
cd secuencias/fasta   # Ingrsa al directorio fasta (que se encuentra dentro del directorio secuencias)
```

![[cd_1.webm]]

Also, the shortcut `..` can be used  to move to parent directory. If the `cd` command is issued without a destination directory, it will move to the user's `home` directory.

![[cd_2.webm]]

#### List content of a directory 
The `ls` command list all the files and directorys in a directory. It has several parameters that can be added when executing the command. These parameters modify the way the output (the file list) will be displayed. The `-l` parameter allows you to show the detail information about the files (permissions, modification date, size, etc). On the other hand the `-h` parameter enables you to see the size in a human-readable format (Kb, Mb, Gb, Tb). The `-a`/`-all` allow you to visualize hidden files and directories.

```bash
ls 
ls secuencias
ls -lh
```

If the `ls` command is executed without any parameters, the output will show the files in the directory without any extra information. 

![[ls.webm]]

#### Copy files `cp`

The `cp` command allows copying files between directories. The `-r` option can be used to copy directories recursively. If a file with the same name exists in the destination folder, it will be overwritten.

```bash
cp sequences.fasta sequences # Copiamos el archivo sequences.fasta al directorio secuences
cp -r sequences data # Copiamos el directorio secuences y todo su contenido en la carpeta data
cp sequences.fasta . # Copia el archivo sequences.fasta al direcotrio actyal
cp *.fasta sequences # Copia todos los archivos que finalicen en .fasta al directorio sequences
```

![[cp_1.webm]]

You can copy one file at a time, or several files at the same time using a shortcut or a common pattern between the file names. `*` is a useful symbol to use en these cases, as it allows to copy all the files from the directory. In the next example, the expression `*.fastq.xz` allows to copy all the files that end with `.fastq.xz`, without caring about the previous characters.

![[cp_2.webm]]

#### Move files (`mv`)
The `mv` command allows moving files between directories. It works the same way that `cp` command.

```bash
mv sequences.fasta sequences # Mueve el archivo sequences.fasta al directorio secuences
mv sequences data # Mueve el directorio secuences y todo su contenido en la carpeta data
mv sequences.fasta . # Mueve el archivo sequences.fasta al direcotrio actyal
mv *.fasta sequences # Mueve todas los archivos que finalicen en .fasta al directorio sequences
```

Like `cp` command, the `mv`command allows moving files one at a time, or several files or directories using a common pattern.

![[mv_1.webm]]

#### Rename files (`mv`)
Also, the `mv` command can be used to rename files or directories. For this, first the original name and then the new name need to be specified.

```bash
mv secuences.fasta sequences.fasta 
```

![[mv_2.webm]]
#### Create directories `mkdir`
The `mkdir` command allows creating new directories,

```bash
mkdir analyses # Crear el directorio analyses en el directorio actual
mkdir data/analyses # Crea el directorio analyses dentro del directorio data
```

![[mkdir.webm]]


#### Current directory `pwd`
The `pwd` command print in the console the full path name of the working directory.

```bash
pwd 
```

![[pwd.webm]]
#### Delete files `rm`
The `rm` command is used to remove files and directories. It must be used  with care,  as once you delete the files, then you will not able to recover the contents of files and directories.

![[rm.webm]]

The `rm -r` command is used to remove directories recursively. Be careful as all the information in the directory will be remove.
#### View content file (`less`, `cat`, `head`, `tail`)
There are differents commands that you can use to visualice files.

- `cat`:  Prints the entire file to the standard output (terminal).
- `head`: Prints the first 10 lines from the file to the standard output (terminal).
- `tail`: Prints the last 10 lines from the file to the standard output (terminal).

For `head` and `tail` command, the `-n` option can be used to specify the number of lines to print.
	- `less`: Allows you to view one page at a time. To navigate within the file, you can used the following shortcuts:
	- `N`: Move to the previous page.
	- `n`: Move to the next page.
	- `q`: Exit the display
	- `/[string]`: Search for a word forward in the file.
	- `?[string]`: Search for a word backward in the file.

![[less.webm]]

#### Filter lines (`grep`)
In some cases, it is useful to filter lines in a file in order to visualize information of interest or count the number of times that a word appears in a file.

In the following example, a filter is applied to display only the lines that contain `chrx`.
En el siguiente ejemplo podemos realizar un filtro y obtener solo aquellas filas que contienen la palabra `chrx`.
```bash
grep "chrX" multisample_anno_filtered.anno.vcf.csv
```

#### Edit files (`micro`)
There are diferents options for editing files through the terminal such as `nano`, `vim`, `micro`, etc. In these lessons we will use `micro`, which is quite easy and intuitive to use because it has common shortcuts and allows you to use the cursor to position yourself in the file.

To edit a file, you have to use `micro` command and specify the file to edit. If file doesn't exist, a new empty file will be created.

```bash
micro file.txt
```

![[micro.webm]]
`micro` interface is based on the use of the mouses and differents shortcuts to perform operations in the file: Following there are the most relevant shortcuts:
- `Ctrl`+ `q`: Exit the editor.
- `Ctrl`+ `c`: Copy a selected text. 
- `Ctrl`+ `p`: Paste a selected text.
- `Ctrl`+ `z`: Undo last edit.
- `Ctrl`+ `y`: Reundo last edit .
- `Ctrl`+ `s`: Save the changes. 

### Other elements

#### Pipe (`|`)
The `|` operator allows you to send the output of one command to another, enabling you to  sequentially process multiple command at a time without saving the intermediate output.

In the following example, the `grep` command will be used to filter the lines, and then, the `|` operator will be used to redirect the output and count the number of lines that contains `chrX` with `wc -l` command.

```bash
grep "chrX" multisample_anno_filtered.anno.vcf.csv | wc -l
```

#### Output redirection (`>`)
The symbol `>` can be used to save the output of a command. This is very useful when programs that displays logs in the standard output are running.

In the following example, only the lines that contain the word `chrX`.  are stored in the new file (`chrX_variants.vcf`). 

```bash
grep "chrX" multisample_anno_filtered.anno.vcf.csv > chrX_variants.vcf
```

If the file exists using `>` will overwrite it. if you want to add new information to an existing file, you can used `>>`.

#### Multiline commands (`\`)
If you have a very long command, the `\` operator  can be used to split the command into multiple lines. After `\` cannot be any space or empty character.

Following  are two examples:
```bash
grep "chrX" multisample_anno_filtered.anno.vcf.csv \
| wc -l
```

```bash
guppy_basecaller \
    --input_path <directorio_fast5> \
    --save_path <directorio_output> \
    --config dna_r9.4.1_450bps_sup \
    --recursive \
    --device 'auto' \
    --num_callers 16
```


#### Reuse of previous command
- You can used the arrow up ↑ in command line to navigate between previously used commands.
- You can used the `history` command, which displays the last 500 used commands.
- Using `Ctrl+r`, you can search for a command that contains a specific word.

## Working environment

Work enviroment is the management system used for the management and execution of tools. This aspect is extremely relevant, as there are generally many ways in which user can  install and used the tools and the requirements, making management can be quite tedious. The most notable alternative is `conda` and containers, such as `docker` or `apptainer`. During these lesson we will use conda for the installation and use of bioniformatics tools.
### Conda
Conda is a open source and multiplatform package manager that allows the installation of tools and their requirements. For this, use the  [Anaconda](https://anaconda.org) repositories, in which one are a lot of tools and libraries for Python and R.

Another relevant feature of Conda is the creation of virtual envirments, in which the user can have installed differents tools and version of these tools. Each virtual enviroment is independent from the others, and the user only needs the activate the enviroment when need to use it.

![[conda.png]]

#### Conda usage
##### Create virtual enviroment
To create virtual enviroment the subcommand `create`is used. The name of the enviroment can be specify with the option `--name (-n)`.
```bash
conda create --name <name_env>
conda create --name bioinformatic-analyses
conda create -n bioinformatic-analyses
```

The enviroment can be created with specific packages from their creation.
```bash
conda create -n python38 python=3.8
```

##### List virutal enviroments
To display the name of the existing virutal enviroment, the following command is used:
```bash
conda env list
conda info --envs
```

##### Activate enviroment
When a virtual enviroment is activated, all the tools installed in this enviroment can be used. The enviroment will remain activate as long as the terminal is active. Only one enviroment per terminal can be used, but if the user needs to change the enviroment, they can deactivate the current envioroment and activate the new one.
```bash
conda activate <name_env>
conda activate bioinformatic-analyses
```

##### Deactivate enviroment
When an enviroment is deactivated, the tools installed in this enviroment cannot be used.
```bash
conda deactivate 
```

##### Delete enviroments 
The subcommand `remove` is used to remove an enviroment. With the option `--name` or `-n` the name of the enviroment to be deleted must be specified.
```bash
conda env remove -n <name_env>
conda env remove -n bioinformatic-analyses
conda env remove --name bioinformatic-analyses
```

##### Install packages 
To install a package the subcommand `install` is used, followed by the name of the enviroment to be created. The Anaconda page has a secction where users can search available packages and the command to used for installation  ([Anaconda Search](https://anaconda.org/search)). Another option is to use the subcommand `search`to locate packages.

There are differents repositories called "channels". In some cases it is necessary to use additional channels to install specific tools or newer versions of tools. In particular, the [bioconda](https://bioconda.github.io/) channel is a  specialized channel in bioinformatics tools. Therefore it is relevant to have added this channel permanently,

When installing a package, the channel can be specified using the  `-c` the option.
```bash
conda install -c bioconda flye
conda install -c bioconda seqkit
```

Additionally, a package version can be specified.
```bash
conda install -c bioconda flye=2.9.1
```

##### List installed packages
To list the installed packages in a enviroment, the subcommand `list` can be used (the enviroment have to be activated).

```bash
conda list
```

Also, the  `|` and `grep` commands can be used to filter the package list and see only the relevant packages.
```bash
conda list | grep samtools
```

##### Update packages
The subcommand `update` is used to update packages, and the name of the package to update must be specified.
```bash
conda update multiqc
```

##### Delete packages 
To delete packages the subcommand `remove` is used and the name of the package to delete must be specified.
```bash
conda remove samtools
```

#### Recommendations
* There is an alternative implementation of conda called [mamba](https://github.com/mamba-org/mamba) which is much faster when installing packages. It can be installed with conda, and then used in the same way that conda is used.
* When installing packages, it is highly recommended to check the version of the package on the official site or repository, because sometimes conda installs older versiones when there are unsolved dependencies.
* On the official conda page, you can be find all the installation instructions for all operating systems([installation instructions](https://conda.io/projects/conda/en/latest/user-guide/install/)).

## File formats

### FAST5 / POD5
The `pod5`  and `fast5` files store the unprocessed raw signal from Nanopore sequencing. They are stored in  `HDF5` (hierarchical data format).

![[MinION_GIF_01.webm]]

These files store the signal read by the pores, which is later processed and transformed into a nucleotide sequence (`fastq`).

![[fast5_view.png]]

### FASTQ
The `fastq` format stores sequences, and has the particularity of storing the quality of the sequences encoded in `Phred` format. Each sequence is stored in 4 lines:
1. Sequence identifier, always beginning with a `@` symbol.
2. Sequence
3.  `+` (separator)
4. Phred quality, represented with ASCII characters. Each nucleotide has an associated ASCII symbol, and each ASCII symbol has an associated phred quality.
5. 
The following image shows the equivalence between quality and ASCII characters
![[qscores.png]]

  
To calculate the probability that the base is incorrect, the formula `10^(-Q/10)` is used.

| Phread Quality Score | Probabildad de una base incorrecta | Exactitud |
| -------------------- | ---------------------------------- | --------- |
| 10                   | 1 en 10                            | 90%       |
| 20                   | 1 en 100                           | 99%       |
| 30                   | 1 en 1000                          | 99,9%     |
| 40                   | 1 en 10000                         | 99,99%    |

Following is an example of how a sequences looks in a `fastq` file:
```
@ee15a423-b008-44be-a4b2-5441d11b0b94 runid=fa1d76e661ac2bbb sampleid=1 read=5087 ch=53 start_time=2019-10-18T22:14:05Z
GTTGTACTTCGTTCAATCGGTAGGTGTTTAACCGGATGGTCACGCCTACCGTGACAAAGAGATTGTCGGTGTCTTTGTGTTTC
+
:F$&$&/035881()'$0&*('-.=;685()$.%($$%%&&&)+..0,&+&%.-/+,%&()$3:0&@09BF=>CC8(78029F
```

### FASTA
The `fasta` format stores sequences in two lines. The first line begins with `>` symbol, followed by the identifier of the sequence and the description. The identifier is the first word after `>` until the first space. The second line contains the sequence. Optionally, the sequence can be splitted in multiple lines with a fixed width, which is quite common in large sequences.

Unlike `fastq` files, `fasta` files do not contain quality information, so they are usually used to store processed data.

```
>tr|A0A0S4KA35|A0A0S4KA35_9BURK Nitrous-oxide reductase OS=Janthinobacterium sp. CG23_2 GN=BN2497_12073 PE=4 SV=1
MKKNSPAPSSPAIVGSRRDFLGKSALAGLATAGATIGLTGCKEEAAPGKAAVPAKAADNSDHIKPGQLDTYYAFISAGHGGEVRVLGLPSGRTLKRIPVFNMDCMVGWGITNESKAIIGTKADGSLQYNTGDTHHVHGSYKDGTYDSRWLFVNDKINARLARIRMDTMECDKITNLPNVMGFHGIFPDKRDPVDAAVNYTTRVFCGAEFHTPLPNDGRDLDDPTKWGCLFTCVDAASMEVRWQCRIDGNMELVATSYDGKLAAATQYNTEGGVDLAGMMSAERDACIFFNVARIEQAIKDGKATTIGNSKVPVVDGRKAANADPATAVTCYVPVGKNPHGVNASPDGKYFVCAGKLSPTATVIELTKVLAWFGGEVKLPRDCVVAEPELGLGPLHTAFDGKGNAF
>barcode01 MN908947.3
NNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNAGATCTGTTCTCTAAACGAACTTTAAAATCTGTGTGGCTGTCACTCGGCTGCATGCTTAGTGCACTCACGCAGTATAATTAATAACTAATTACTGTCGTTGACAGGACACGAGTAACTCGTCTATCTTCTGCAGGCTGCTTACGGTTTCGTCCGTGTTGCAGCCGATCATCAGCACATCTAGGTTTTGTCCGGGTGTGACCGAAAGGTAAGATGGAGAGCCTTGTCCCTGGTTTCAACGAGAAAACACACGTCCAACTCAGTTTGCCTGTTTTACAGGTTCGCGACGTGCTCGTACGTGGCTTTGGAGACTCCGTGGAGGAGGTCTTATCAGAGGCACGTCAACATCTTAAAGATGGCACTTGTGGCTTAGTAGAAGTTGAAAAAGGCGTTTTGCCTCAACTTGAACAGCCCTATGTGTTCATCAAACGTTCGGATGCTCGAACTGCACCTCATGGTCATGTTATGGTTGAGCTGGTAGCAGAACTCGAAGGCATTCAGTACGGTCGTAGTGGTGAGACACTTGGTGTCCTTGTCCCTCATGTGGGCGAAATACCAGTGGCTTACCGCAAGGTTCTTCTTCGTAAGAACGGTAATAAAGGAGCTGGTGGCCATAGTTACGGCGCCGATCTAAAGTCATTTGACTTAGGCGACGAGCTTGGCACTGATCCTTATGAAGATTTTCAAGAAAACTGGAACACTAAACATAGCAGTGGTGTTACCCGTGAACTCATGCGTGAGCTTAACGGAGGGGCATACACTCGCTATGTCGATAACAACTTCTGTGGCCCTGATGGCTACCCTCTTGAGTGCATTAAAGACCTTCTAGCACGTGCTGGTAAAGCTTCATGCACTTTGTCCGAACAACTGGACTTTATTGACACTAAGAGGGGTGTATACTGCTGCCGTGAACATGAGCATGAAATTGCTTGGTACACGGAACGTTCTGAAAAGAGCTATGAATTGCA
```
