---
title: 1. Working environment and file formats
---
> [!summary] In this chapter
> - What is the command linterface and how to execute commands
> - Managing work environment with Conda
> - Common file formats in bioinformatics
## How to use the command line

Command line interface or CLI is a text-based interface that allows you to execute commands. Executing programs in command line has multiples advantages, primarily the easy integration with other tools and the ability to execute in enviroment that lack a graphical interface (cloud/HPC). Most bioinformatics tools has only command line interace.

A command is a program that performs a specific action when executed in the CLI. Upon executing a command, the operating system run a process where commonly it reads an input, processes the data, and finally generates a result (output).

In general, a command have the following sintaxis:
```bash
command [subcommand] [options] <arguments>
```

Usually the `<arguments>` are the input and output files to the command, and are required. On the other hand, options are used to modify the behavior of the command and can be optional. Generally, they are indicated by the format `--option (long way)` or `-o` (short way), and may have an argument associated with the option. Depending on the tool, `[subcommands]` may exist.

For example, in the next command:
```bash
samtools view seq.sam --output seq.bam --bam --threads 4
```
- `samtools`: Command (tool). 
- `view`: Subcommand. This specific tool (sammtols) tool have several subcommands that have differents functions.
- `seq.sam`: Argument. For this command, is the input.
- `--output seq.bam`: Option `--output` which has the argument `seq.bam`. This tool supports short form of option, so you can indicate `-o` instead of `--output`.
- `--bam`: Option without arguments.
- `--threads 4`: Options with the argument `4`.

Typically, the sequence in which arguments and options are specified does not affect the outcome. In the previous example, the command could receive the options and arguments (the four elements following the subcommand) in any order, and the result would be identical. However, some tools mandate that options precede arguments. Therefore, it is crucial to consult the manual or help documentation of each tool you use.

Most commands offer help documentation where the options and arguments are detailed. While not mandatory, the common practice is to use `-h` or `--help` to access this help documentation.

### Remote access
#### Secure Shell (`ssh`)

Secure Shell (`SSH`) is a protocol that allows users to connect to and use remote Linux servers over the network. `ssh` conections are encrypted, which means that sending password, storing data and editing files are secure.

To connect to a remote server we use `ssh` command. `ssh` requires the username and the desired server to connect to. 

```bash
ssh <user>@<host>

ssh jaldridge@10.34.1.15
ssh dalvarez@10.9.0.163
```

To log in via SSH on Windows, you must first open the Windows console (`cmd`) and then execute the `ssh` command along with the username and server address.

Upon attempting to log in, the server will require a password. On many servers, when typing the password, the typical `*` symbols will not appear because the password entry is hidden.

Once the account is authenticated on the server, the user will gain access to their session within their `home` directory.

![[ssh.webm]]

### Basic file-related commands
#### Navigating between directories (`cd`)

The `cd` (change directory) command is essential for navigating through the file system in a command-line environment. To use this command, you must specify the destination directory immediately after `cd`. This command changes the current working directory to the one specified, allowing you to easily move through different parts of the file system.

```bash
cd ..                 # Moves to parent directory 
cd                    # Moves to 'home'
cd sequences          # Goes into the directory 'sequences'
cd sequences/fasta    # Goes into the directory 'fasta' inside of 'sequences'
```

![[cd_1.webm]]

The shortcut `..` can be used to move to the directory of the current directory. If the `cd` command is issued without a destination directory, it will move to the user's `home` directory.

![[cd_2.webm]]

>[!tip] Command line "shortcuts"
> In the command line interface (CLI), when specifying directories or file arguments for commands, you can use several shortcuts to streamline your navigation and command execution:
> - `.` (dot): Refers to the current directory.
> - `..`: Refers to the parent directory, one level up in the hierarchy from the current directory.
>- `~`: Represents the "home" directory of the current user. This is a quick way to return to or reference the base directory of the user, regardless of the current location in the file system.
>- `*`: Acts as a wildcard and is used to indicate that you want to perform an operation on all files within a directory. It can be combined with other patterns to match a specific set of files. For example, `*.txt` would match all files with a `.txt` extension in the current directory.

#### List content of a directory (`ls`)

The `ls` (list) command is used to list all the files and directories within a directory. This command supports various parameters that alter the presentation of the output (the list of files).
- The `-l` parameter displays detailed information about the files, including permissions, modification date, size, and more.
- Meanwhile, the `-h` parameter, often used in conjunction with `-l`, formats the size of the files into a human-readable form (KB, MB, GB, TB), making it easier to understand at a glance.
- The `-a`/ `--all` parameter allows you to include hidden files and directories in the output. Hidden files and directories are those whose names start with a dot (`.`) and are not shown by the default execution of the `ls` command.

```bash
ls 
ls sequences
ls -lh
```

If the `ls` command is executed without any parameters, the output will show the files in the directory without any extra information. 

![[ls.webm]]

#### Copying files (`cp`)

The `cp` (copy) command is a utility for copying files and directories from one location to another within the file system. When copying directories, the `-r` (or `--recursive`) option is neccesary, as it enables the `cp` command to copy directories and their contents recursively. This means that not only the directory itself but all its subdirectories and the files within them will be copied to the specified destination.

>[!danger] Risk of overwritting files
>By default, using `cp` to copy a file to a location where a file with the same name exists will overwrite the existing file without warning. It's crucial to check file names and paths carefully before executing the command, particularly in scripts or automated tasks.
>
> To prevent accidental overwrites, consider using the `-i` (or `--interactive`) option with `cp`. This prompts for confirmation before overwriting any files, adding a layer of protection against data loss.

```bash
cp sequences.fasta sequences # Copy the 'sequences.fasta' file to the 'sequences' directory (if exists, if not, it will copy the file as 'sequences')
cp -r sequences data # Copy the 'sequences' directory and all its contents to the 'data' directory
cp sequences.fasta .. # Copy 'sequences.fasta' to the parent directory
cp *.fasta sequences # Copy all files ending in '.fasta' to the sequences directory
```

![[cp_1.webm]]
  
You can copy multiple files at once with the `cp` command using the `*` wildcard. For instance, `*.fastq.xz` copies all files ending in `.fastq.xz`, ignoring the preceding characters. This method is efficient for targeting files with common patterns in their names.

![[cp_2.webm]]

#### Moving files (`mv`)

The `mv` (move) command is used to move files between directories, functioning similarly to the `cp` command.

```bash
mv sequences.fasta sequences # Moves the file sequences.fasta to the directory sequences
mv sequences data # Moves the directory sequences and all its contents into the data folder
mv sequences.fasta . # Moves the file sequences.fasta to the current directory
mv *.fasta sequences # Moves all files ending in .fasta to the directory sequences
```

Similar to the `cp` command, the `mv` command can move files one by one or multiple files or directories at once using a common pattern.

![[mv_1.webm]]

#### Rename files (`mv`)

The `mv` command isn't just for moving files and directories; it can also rename them. To rename a file or directory, you specify the current name followed by the new name. This action effectively moves the file or directory to the same location but under a different name.

```bash
mv secuences.fasta sequences.fasta 
```

![[mv_2.webm]]
#### Creating directories (`mkdir`)

The `mkdir` (make directory) command is used to create new directories. By specifying a name or path, you can quickly establish a new directory within the current or specified location. This command is fundamental for organizing files and directories in a structured manner.

```bash
mkdir analyses # Creates the directory analyses in the current directory
mkdir data/analyses # Creates the directory analyses inside the data directory
```

![[mkdir.webm]]
By default, `mkdir` will not create a directory if its parent directories do not exist. The `-p` option allows to create nested directories in one command, avoiding errors if some directories already exist, which is ideal for efficient, complex directory setups.

```bash
mkdir -p data/2023/analyses # Creates the nested directories, including all necessary parent directories.
```

#### Displaying current directory (`pwd`)

The `pwd` command print in the console the full path name of the working directory.

```bash
pwd 
```

![[pwd.webm]]
#### Deleting files `rm`
The `rm` (remove) command is used to delete files and directories.

>[!danger] Permanent deletion of files
>Files deleted with `rm` in Linux do not move to temporary storage like a recycle bin; they are immediately removed from the file system.Use this command with caution to prevent unintended data loss.

![[rm.webm]]

The `rm -r` option enhances the command to allow for the recursive deletion of directories and their contents.
#### Viewing file contents (`less`, `cat`, `head`, `tail`)

Several commands are available for viewing file contents, each suited for different needs:
- **`cat`**: Displays the entire file content to the standard output (terminal).
- **`head`**: Shows the first 10 lines of the file. Use the `-n` option to specify a different number of lines.
- **`tail`**: Reveals the last 10 lines. Like `head`, `-n` allows you to adjust the number of lines displayed.
- **`less`**: Provides a paginated view, making it easier to navigate through larger files. Key shortcuts include:
    - **`N`**: Previous page.
    - **`n`**: Next page.
    - **`q`**: Quit the view.
    - **`/[string]`**: Search forward for a string.
    - **`?[string]`**: Search backward.

These tools offer flexible ways to inspect file contents, from quick overviews to detailed examinations.

![[less.webm]]

#### Filtering lines (`grep`)

The `grep` command is a powerful tool used to search text or files for lines that contain a match to the specified pattern. It's particularly useful for parsing large files or streams to find specific information.

For instance, to filter and display lines that contain the text `chrX` in the file `multisample_anno_filtered.anno.vcf.csv`, you can use:
```bash
grep "chrX" multisample_anno_filtered.anno.vcf.csv
```
This searches the specified file for `chrX` and prints every line to the terminal where the pattern is found. `grep` can be invaluable for quickly locating information within files or across multiple files, making it a staple in text processing and data analysis tasks.

#### Editing files (`micro`)

Among the various options for editing files via the terminal, such as `nano`, `vim`, and others, we will focus on `micro` for its simplicity and intuitive use. `Micro` is user-friendly, featuring common shortcuts and allowing cursor movement for navigation within the file.

To edit a file, use the `micro` command followed by the file name. If the file doesn't exist, `micro` will create a new, empty one.

```bash
micro file.txt
```

![[micro.webm]]
`Micro` offers a GUI-like interface that supports mouse interactions and keyboard shortcuts for efficient file editing. Here are some essential shortcuts:
- **`Ctrl` + `q`**: Exit the editor.
- **`Ctrl` + `c`**: Copy selected text.
- **`Ctrl` + `v`**: Paste the copied text.
- **`Ctrl` + `z`**: Undo the last edit.
- **`Ctrl` + `y`**: Redo the last undo.
- **`Ctrl` + `s`**: Save changes.

### Advanced command line techniques

#### Pipe (`|`) operator
The pipe operator `|` enables chaining commands by directing the output of one command as input to another. This facilitates complex data processing tasks without the need to save intermediate results.

Example:
```bash
grep "chrX" multisample_anno_filtered.anno.vcf.csv | wc -l
```
This command filters lines containing `chrX` from a file, then counts these lines using `wc -l`.
#### Output redirection (`>`)
To save command output to a file, use the `>` operator. This is especially useful for capturing logs or output data that would normally be displayed in the terminal.

Example:
```bash
grep "chrX" multisample_anno_filtered.anno.vcf.csv > chrX_variants.vcf
```
This saves lines containing `chrX` to a new file, `chrX_variants.vcf`. Use `>` to overwrite existing files or `>>` to append to them.

#### Multiline commands (`\`)
Long commands can be split across multiple lines for readability using the `\` operator, which must be followed directly by a newline without spaces.

Examples:
```bash
grep "chrX" multisample_anno_filtered.anno.vcf.csv \
| wc -l
```

```bash
dorado basecaller \
	--recursive \
	--device 'auto' \
    --recursive \
    dna_r10.4.1_e8.2_400bps_sup@v4.3.0 \
    <directorio_pod5> \
    > <output_dir>
```

#### Reusing Previous Commands
- Press the up arrow ↑ to cycle through previously entered commands.
- The `history` command lists the last 500 commands used.
- Press `Ctrl+r` to search for commands containing a specific word, allowing for quick retrieval and reuse of commands.

## Managing the work environment

The work environment refers to the system for managing and executing various tools, an aspect crucial for efficient workflow. Given the myriad ways tools and their dependencies can be installed and used, managing this environment can become complex. Prominent solutions include `conda` and containerization technologies like `docker` or `apptainer`. For the purposes of these lessons, we'll focus on using conda to manage bioinformatics tools.
### Conda

Conda is an open-source, cross-platform package manager that simplifies the process of tool installation along with their dependencies. It utilizes repositories from [Anaconda](https://anaconda.org/), which host a vast array of tools and libraries for Python and R, among others.

A key feature of Conda is its ability to create virtual environments. This allows users to install different tools and versions within separate environments, maintaining their independence. Activating a specific environment when needed ensures that only the tools and versions required for a particular project are in use, avoiding conflicts and compatibility issues. This virtualization is integral for maintaining an organized and efficient working environment.

#### Acquiring Conda

When considering Conda for managing your environments and packages, you will find two primary options. The first is the Anaconda suite, a comprehensive collection of packages suited for data science, machine learning, and scientific computing, accessible from [their website](https://www.anaconda.com/download). Though extensive, it might include many packages you will not use. For a more streamlined option, "Miniconda" is the go-to, focusing on essential tools like Conda, Python, and the packages necessary to get started. This is ideal for those aiming to avoid unnecessary package accumulation. You can download Miniconda [here](https://docs.anaconda.com/free/miniconda/index.html).

![[conda.png]]

>[!tip] Micromamba
> For an even lighter solution, consider Micromamba, a slimmer alternative to Miniconda. Utilizing `mamba`, a faster, more efficient conda substitute built from scratch, Micromamba accelerates environment management and package installation. You can find installation details for Micromamba [here](https://mamba.readthedocs.io/en/latest/installation/micromamba-installation.html). Notably, replace `conda` commands with `micromamba` for operation.
> 
> It is also possible to install `mamba` using `conda`, and manage the packages using `mamba`.

#### Usage
##### Creating virtual enviroments

To initiate a new virtual environment, use the `create` subcommand with the `--name` (or `-n`) option to name your environment.
```bash
conda create --name bioinformatic-analyses
conda create -n bioinformatic-analyses
```

You can also create an environment with specific packages:
```bash
conda create -n python311 python=3.11
```

##### Listing virutal enviroments

To view all existing environments, use:
```bash
conda env list  # or
conda info --envs
```

##### Activating an environment

Activate an environment to use the tools installed in it. The enviroment will remain activated as long as the terminal session is active. Only one enviroment per terminal can be used, but if you need to change the enviroment, you can deactivate the current envioroment and activate the new one.
```bash
conda activate <name_env>
conda activate bioinformatic-analyses
```

##### Deactivating an enviroment

To deactivate the current environment, use the following command:
```bash
conda deactivate 
```

##### Deleting enviroments

The subcommand `remove` is used to remove an enviroment. The option `--name` or `-n` must be specified, indicating the name of the environment to delete.
```bash
conda env remove -n bioinformatic-analyses
conda env remove --name bioinformatic-analyses
```

##### Installing packages

To install a package, use the `install` subcommand followed by the name of the package to install. The Anaconda page has a secction where users can search available packages and the command to used for installation ([Anaconda Search](https://anaconda.org/search)). Another option is to use the subcommand `search`to locate packages.

There are differents repositories called "channels". In some cases it is necessary to use additional channels to install specific tools or newer versions of tools. In particular, the [bioconda](https://bioconda.github.io/) channel is a specialized channel in bioinformatics tools. If you often use tools from this repository, it may be useful to configure it permanently.

When installing a package, the channel can be specified using the  `-c` the option.
```bash
conda install -c bioconda flye
conda install -c bioconda seqkit
```

Additionally, a package version can be specified.
```bash
conda install -c bioconda flye=2.9.1
```

>[!warning] Package conflicts
>When installing packages, it is highly recommended to check the version of the package on the official site or repository, because sometimes conda will install older versions when there are conflicting dependencies. If you can't install the version you want in the current environment, consider installing it in a new environment.
##### Listing installed packages

To review installed packages and their versions within the active environment, use `list`.

```bash
conda list
```

To filter the output and see only relevant packages, use `|` and `grep`.
```bash
conda list | grep samtools
```

##### Updating packages
You can use the subcommand `update` to update packages, indicating the name of the package to update.
```bash
conda update multiqc
```

##### Deleting packages 
To delete packages, use `conda remove` indicating the name of the package to delete.
```bash
conda remove samtools
```
## File formats

### POD5 / FAST5

Nanopore sequencing technologies, which read DNA sequences by detecting electrical signals as DNA strands pass through nanopores, utilize specific file formats for storing this data: POD5 and FAST5.

- **POD5**: The newer `POD5` format stores unprocessed raw signals from Nanopore sequencing, based on the efficient Apache Arrow format. It's designed for better data storage and retrieval, addressing the large data volumes generated in sequencing.
    
- **FAST5**: Prior to POD5, `FAST5` was the standard format. Based on HDF5, it archives the raw electrical signals from the sequencing process. The shift to POD5 reflects advancements in data processing needs.

![[MinION_GIF_01.webm]]

These formats store the raw nanopore signal, and later they are processed into nucleotide sequences (typically in `fastq` format).

![[fast5_view.png]]

### FASTQ

The `fastq` format is a widely used file format for storing nucleotide sequences along with their quality scores in genomic sequencing. It is structured in a straightforward manner, with each sequence represented by four lines:
1. Sequence identifier, always begins with the `@` symbol, uniquely identifying each sequence, commonly including read metadata.
2. Sequence in nucleotides.
3.  Separator (+).
4. Quality Scores, encoded using ASCII characters, representing the Phred quality scores corresponding to each base in the sequence.

The following image shows the equivalence between quality and ASCII characters
![[qscores.png]]

To calculate the probability of error in a nucleotide, use the formula `10^(-Q/10)`.

| Phread Quality Score | Error probability | Accuracy |
| -------------------- | ----------------- | -------- |
| 10                   | 1 in 10           | 90%      |
| 20                   | 1 in 100          | 99%      |
| 30                   | 1 in 1000         | 99,9%    |
| 40                   | 1 in 10000        | 99,99%   |
A read looks in a FASTQ file like this example:
```
@ee15a423-b008-44be-a4b2-5441d11b0b94 runid=fa1d76e661ac2bbb sampleid=1 read=5087 ch=53 start_time=2019-10-18T22:14:05Z
GTTGTACTTCGTTCAATCGGTAGGTGTTTAACCGGATGGTCACGCCTACCGTGACAAAGAGATTGTCGGTGTCTTTGTGTTTC
+
:F$&$&/035881()'$0&*('-.=;685()$.%($$%%&&&)+..0,&+&%.-/+,%&()$3:0&@09BF=>CC8(78029F
```

### FASTA
The `fasta` format provides a simple and widely used method for storing biological sequence data. It follows a straightforward structure, consisting of two lines for each sequence entry:

1. **Header Line**: Begins with the `>` symbol, followed by the sequence identifier and description. The identifier typically extends until the first space character.
2. **Sequence Line**: Contains the actual nucleotide or amino acid sequence.

Optionally, the sequence can be split across multiple lines, often with a fixed width, which aids readability for large sequences.

Unlike `fastq` files, `fasta` files do not include quality information. Instead, they are primarily used for storing processed sequence data, making them suitable for a wide range of genomic and proteomic applications.

```
>tr|A0A0S4KA35|A0A0S4KA35_9BURK Nitrous-oxide reductase OS=Janthinobacterium sp. CG23_2 GN=BN2497_12073 PE=4 SV=1
MKKNSPAPSSPAIVGSRRDFLGKSALAGLATAGATIGLTGCKEEAAPGKAAVPAKAADNSDHIKPGQLDTYYAFISAGHGGEVRVLGLPSGRTLKRIPVFNMDCMVGWGITNESKAIIGTKADGSLQYNTGDTHHVHGSYKDGTYDSRWLFVNDKINARLARIRMDTMECDKITNLPNVMGFHGIFPDKRDPVDAAVNYTTRVFCGAEFHTPLPNDGRDLDDPTKWGCLFTCVDAASMEVRWQCRIDGNMELVATSYDGKLAAATQYNTEGGVDLAGMMSAERDACIFFNVARIEQAIKDGKATTIGNSKVPVVDGRKAANADPATAVTCYVPVGKNPHGVNASPDGKYFVCAGKLSPTATVIELTKVLAWFGGEVKLPRDCVVAEPELGLGPLHTAFDGKGNAF
>barcode01 MN908947.3
NNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNAGATCTGTTCTCTAAACGAACTTTAAAATCTGTGTGGCTGTCACTCGGCTGCATGCTTAGTGCACTCACGCAGTATAATTAATAACTAATTACTGTCGTTGACAGGACACGAGTAACTCGTCTATCTTCTGCAGGCTGCTTACGGTTTCGTCCGTGTTGCAGCCGATCATCAGCACATCTAGGTTTTGTCCGGGTGTGACCGAAAGGTAAGATGGAGAGCCTTGTCCCTGGTTTCAACGAGAAAACACACGTCCAACTCAGTTTGCCTGTTTTACAGGTTCGCGACGTGCTCGTACGTGGCTTTGGAGACTCCGTGGAGGAGGTCTTATCAGAGGCACGTCAACATCTTAAAGATGGCACTTGTGGCTTAGTAGAAGTTGAAAAAGGCGTTTTGCCTCAACTTGAACAGCCCTATGTGTTCATCAAACGTTCGGATGCTCGAACTGCACCTCATGGTCATGTTATGGTTGAGCTGGTAGCAGAACTCGAAGGCATTCAGTACGGTCGTAGTGGTGAGACACTTGGTGTCCTTGTCCCTCATGTGGGCGAAATACCAGTGGCTTACCGCAAGGTTCTTCTTCGTAAGAACGGTAATAAAGGAGCTGGTGGCCATAGTTACGGCGCCGATCTAAAGTCATTTGACTTAGGCGACGAGCTTGGCACTGATCCTTATGAAGATTTTCAAGAAAACTGGAACACTAAACATAGCAGTGGTGTTACCCGTGAACTCATGCGTGAGCTTAACGGAGGGGCATACACTCGCTATGTCGATAACAACTTCTGTGGCCCTGATGGCTACCCTCTTGAGTGCATTAAAGACCTTCTAGCACGTGCTGGTAAAGCTTCATGCACTTTGTCCGAACAACTGGACTTTATTGACACTAAGAGGGGTGTATACTGCTGCCGTGAACATGAGCATGAAATTGCTTGGTACACGGAACGTTCTGAAAAGAGCTATGAATTGCA
```
