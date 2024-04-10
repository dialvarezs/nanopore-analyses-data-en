---
title: Appendix. Working in Metacentrum
---
## Software

### Modules
The cluster provides a variety of software installed as modules. Modules are accessible programs that can be loaded at the user's discretion.

To list all the available modules, use
```bash
module avail
```

Modules follow the notation `<name>/<version>`, allowing for the loading of specific software versions, if available.

To load a module into the current shell, use `module load` or the abbreviated form `ml`. If the version is not specified, the system will load the most recent version available.
```bash
module load mambaforge  # normal form
ml mambaforge           # short form
ml mambaforge/22.9.0    # with version
```

You can load multiple modules in a shell session, but be aware that some modules may have incompatibilities with others.

To unload a module, use `module unload` or `ml` with a `-` preceding the module name. For example:
```bash
module unload mambaforge
ml -mambaforge
```
To remove all loaded modules, use `ml purge`.

Keep in mind that each new terminal session starts with no loaded modules, so you'll need to load any necessary modules for each new session.

### Conda packages

To install software with conda, first load either the `mambaforge` or the `micromamba` module.
Then, you can create a new environment with `mamba create ...` if using mamba, or `micromamba create ...` if using micromamba. Finally, you can install the software, and on new sessions, you can load your environments. For example:
```bash
ml mambaforge                       # load mamba
mamba create -n qc                  # create env
mamba activate qc                   # activate env
mamba install fastp fastqc multiqc  # install software
```

Reference: https://docs.metacentrum.cz/software/install-software/#conda-packages

## Running jobs

Metacentrum utilizes the Portable Batch System (PBS) for managing resources and scheduling jobs.
#### Checking Cluster Nodes

To view all nodes within the cluster, execute:
```bash
pbsnodes -aS
```
This command lists all the nodes along with their status, helping you understand the cluster's current load and availability.

### Submitting Jobs

To submit a job, you must create a script file specifying the requested resources and the command(s) to execute. Below is an example of a PBS script file:

To submit a job, you should create a script file, indicating the resources you want and the command to execute. A PBS script file should look like this:
```bash
#!/bin/bash
#PBS -N my_asm          # name of the script
#PBS -l ncpus=8         # number of CPUs requested
#PBS -l mem=16gb        # memory requested
#PBS -l walltime=2:0:0  # time requested (2 days)

ml flye                 # load a module for my job

flye \
	--nano-hq my_data.fastq.gz \
	-o my_asm \
	-t 8                
```

Save this script as `my_asm.qsub`. To submit the job to the queue, run:
```bash
qsub my_asm.qsub
```

Jobs run with default parameters if not explicitly specified. For details on these defaults, refer to the [Metacentrum's documentation on default qsub parameters](https://docs.metacentrum.cz/troubleshooting/faqs/faqs-content/qsub-default-parameters/).

### Interactive Sessions

Interactive sessions allow you to connect to a computing node directly, providing access to a terminal to execute commands on the remote machine. This feature is particularly useful for software testing, exploratory data analysis, or any task that doesn't require automation.

To start an interactive session, use the command:
```bash
qsub -I -l select=1:ncpus=2:mem=4gb
```
Adjust the resource specifications (`ncpus`, `mem`, and `walltime`) based on your requirements. Interactive sessions should not be used for long-running jobs or tasks that can be automated.

>[!tip] When to use interactive sessions?
>Interactive sessions are great for testing and troubleshooting, as they give you immediate feedback as commands run on computing nodes.
>
>However, it's important to use these sessions judiciously. Running long or non-interactive tasks in these sessions can unnecessarily tie up resources, impacting the efficiency of shared computing environments. For tasks that don't require real-time interaction, consider using batch scripts.

### Checking running jobs

To monitor the status of running jobs in the Metacentrum, the `qstat` command is used. This tool provides information about the job queue, including jobs that are currently running, waiting, or in a hold state.

- **Viewing All Running Jobs**: To see all jobs currently running on the cluster, you can use:
	```bash
	qstat -n
	```
	This command lists all running jobs along with their node allocations.
- **Filtering Jobs by User**: If you're interested in viewing only your jobs, you can limit the output with the `-u` option, followed by your username. For example:
	```bash
	qstat -n -u <username>
	```
	Replace `<username>` with your actual username on the cluster. This will display only the jobs submitted by you, making it easier to track your own processes.


## Recommendations

### Tmux

Tmux is a terminal multiplexer, allowing a user to access multiple separate terminal sessions inside a single terminal window or remote terminal session. It's particularly useful for running more than one command-line program at the same time within the same terminal, or for keeping terminal sessions running even after disconnecting from a server.
#### Basic Usage

1. **Starting tmux**: Open a terminal and type `tmux` to start a new session.
2. **Creating New Windows**: You can create a new window within tmux by pressing `Ctrl+b` followed by `c`.
3. **Switching Between Windows**: To switch between your tmux windows, press `Ctrl+b` followed by the window number or use `Ctrl+b` and `n` for the next window and `Ctrl+b` and `p` for the previous window.
4. **Splitting Windows**: Split the window horizontally with `Ctrl+b` then `"` and vertically with `Ctrl+b` then `%`.
5. **Detaching and Attaching Sessions**: You can detach from a tmux session and leave it running in the background by pressing `Ctrl+b` then `d`. To reattach to a session, use `tmux attach-session` or `tmux a` for short.

For more commands, you can check [this cheatsheet](https://tmuxcheatsheet.com/).