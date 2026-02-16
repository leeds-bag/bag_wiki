# Guide to SLURM Job Queuing

Author: Ben Bradley, Feb 2026 (last updated: Feb 2026)


## What is SLURM?

SLURM stands for Simple Linux Utility for Resource Management. It is a job management system used to manage compute resources. Many modern HPCs use SLURM, meaning it's useful to understand its basic features. Guides to using SLURM exist for both [JASMIN](https://help.jasmin.ac.uk/docs/batch-computing/slurm-scheduler-overview/) and [Aire](https://arcdocs.leeds.ac.uk/aire/usage/job_type.html), which are worth consulting before running any jobs on those machines.


## Why use SLURM?

- **Necessity**: many HPC systems will not allow users to run intensive jobs without submitting to the SLURM queue.
- **Fairness**: SLURM job queues aim to share compute resources fairly among all users.
- **Efficiency**: short, repetitive jobs can be parallelised (e.g. [JASMIN allows users to submit up to 2000 jobs at once](https://help.jasmin.ac.uk/docs/batch-computing/slurm-queues/))


## Basics

Jobs can be submitted to the queue through the `sbatch` command in the terminal.

```bash
sbatch myjobscript.sh
```
The `myjobscript.sh` file contains two things:
- **SLURM directives**: information to pass to the SLURM queue about the type of job that you are submitting. These are prefixed by `#SBATCH` and are placed at the top of the jobscript.
- **Your job**: a list of instructions for the computer to perform after the job is finished waiting in the queue.

A minimal jobscript file to run some Python code might look something like:
```bash
#!/bin/bash
#SBATCH --job-name=python_job   # Job name
#SBATCH --time=01:00:00         # Request runtime (hh:mm:ss)
#SBATCH --mem=1G                # Request memory

# Executable
python mycode.py
```

Note that if you don't request enough time and/or memory, the job will be terminated early when these resources run out. However, the more you request, the longer you are likely to be queued. Consistently requesting too many resources will often reduce the priority you hold in the system, making all your jobs queue longer.

After submitting, you can check the status of your queued jobs using the `squeue` command, followed by your username (otherwise you see _everyone's_ jobs).

```bash
squeue -u $USER
```


## Job Template (for JASMIN)

Complex code with lots of tasks can quickly result in messy jobdecks. It can be hard to understand what's going on in these and make appropriate changes without introducing bugs. I've put together a template jobscript, designed for submissions to the JASMIN SLURM queue. I hope it's useful and please let me know if you have any suggestions to improve it!

```bash
#!/bin/bash
###############################################################################
# SLURM Job Script
###############################################################################

#SBATCH --job-name="xxx"            # Replace "xxx" with your job name
#SBATCH --time=00:01:00             # Set the time limit for the job (HH:MM:SS)
#SBATCH --mem=100M                  # Set the memory limit for the job
#SBATCH --account=xxx               # Set to the appropriate group workspace
#SBATCH --partition=debug           # standard or debug
#SBATCH --qos=debug                 # debug. standard, short, long, high. (see https://help.jasmin.ac.uk/docs/batch-computing/slurm-queues/)
#SBATCH -o %x_%j.out                # output file (print statements saved in here)
#SBATCH -e %x_%j.err                # error file (error details saved in here)

###############################################################################
#  0. JOB INFORMATION
###############################################################################

# Assumes code saved in ~/project/scripts/
HOME_DIR="/home/users/xxx"          # Replace xxx with your username
PROJECT="project_wetland"           # Replace with your project name
SCRIPT_DIR="${HOME_DIR}/${PROJECT}/scripts"
SCRIPT_NAME="test_job.py"           # Replace with your script name

# Arguments passed to the script, if needed
# : "${INPUT_1:?Error: INPUT_1 variable not set. Use sbatch --export=INPUT_1=input_1 xxx.sh}"
# INPUT_1=$1
# INPUT_2=$2

JOB_TIME=$(date +"%Y-%m-%d_%H-%M-%S")

echo "========== SLURM JOB INFO =========="
echo "Job Name:   ${SLURM_JOB_NAME}"
echo "Job ID:     ${SLURM_JOB_ID}"
echo "User:       ${USER}"
echo "Node List:  ${SLURM_NODELIST}"
echo "CPUs:       ${SLURM_CPUS_ON_NODE:-N/A}"
echo "Memory:     ${SLURM_MEM_PER_NODE:-N/A} MB"
echo "Start Time: ${JOB_TIME}"
echo "===================================="

###############################################################################
#  1. LOAD MODULES & ENVIRONMENT
###############################################################################

# 1.1 jaspy environment setup                      <-- Uncomment if using Jaspy
# module load jaspy

# 1.2 conda environment setup                      <-- Uncomment if using conda
# source ~/miniforge3/bin/activate
# conda activate xxx                    # Replace xxx with environment name
# echo "Current Conda environment: $CONDA_DEFAULT_ENV"

###############################################################################
#  2. RUN SCRIPT
###############################################################################

echo "Starting main job script..."
SECONDS=0

# Run the Python script                            <-- Uncomment and modify arguments if needed
python ${SCRIPT_DIR}/${SCRIPT_NAME} # --input1 "$INPUT_1" --input2 "$INPUT_2"

# Smart runtime display
runtime=$SECONDS
if [ $runtime -lt 60 ]; then
    echo "Job finished in ${runtime} seconds"
elif [ $runtime -lt 3600 ]; then
    mins=$(( runtime / 60 ))
    secs=$(( runtime % 60 ))
    echo "Job finished in ${mins} min ${secs} sec"
else
    hours=$(( runtime / 3600 ))
    mins=$(( (runtime % 3600) / 60 ))
    echo "Job finished in ${hours} hr ${mins} min"
fi

###############################################################################
#  3. CLEANUP
###############################################################################

# 3.2 conda environment cleanup                    <-- Uncomment if using conda
# conda deactivate
# conda deactivate

# move output and error files to logs directory
LOGFILE_NAME="${SLURM_JOB_NAME}_${SLURM_JOB_ID}"
mv $LOGFILE_NAME.out ${HOME_DIR}/${PROJECT}/jobs/logs/out/${LOGFILE_NAME}_${JOB_TIME}.out
# Handle error file: delete if empty, else move
if [ -s "${LOGFILE_NAME}.err" ]; then
    mv "${LOGFILE_NAME}.err" "${HOME_DIR}/${PROJECT}/jobs/logs/err/${SLURM_JOB_NAME}_${DATE}.err"
else
    echo "No errors found — deleting empty .err file."
    rm -f "${LOGFILE_NAME}.err"
fi
```


## Advanced tips

### Simplifying queue checking

I like to create an alias in my `.bashrc` file to simplify queue checking.
```bash
alias q="squeue -u $USER"
```
I can then check my jobs in the queue by simply typing `q` into the terminal.

### Stats on completed jobs

To get the resource usage information from a complete job, you can use the `seff` command in combination with the job id. This can help you tailor the memory and time you request for jobs.

```bash
seff <job ID>
```

### Arguments for jobs


### Jobscript wrappers

When I want to submit a large number of jobs to be run in parallel, I will write a jobscript and then also write a "wrapper" script to submit this job lots of times (e.g. for different days). An example for submitting daily jobs between a specified start and end date is shown below:

```bash
#!/bin/bash
# daily_job_wrapper.sh
# Usage: ./daily_job_wrapper.sh START_DATE END_DATE
# Example: ./daily_job_wrapper.sh 2021-09-01 2021-10-31
# Note: If END_DATE is not provided, it defaults to START_DATE (i.e. single day)

START_DATE=$1
END_DATE=${2:-$START_DATE}

curr=$START_DATE
while [[ $(date -d "$curr" +%s) -le $(date -d "$END_DATE" +%s) ]]; do
  # Format into DD-MM-YYYY for daily_job.sh
  DATE=$(date -d "$curr" +%d-%m-%Y)
  echo "Submitting job for $DATE"
  sbatch --export=DATE="$DATE" apply_AKs.sh
  curr=$(date -I -d "$curr + 1 day")  # advance by one calendar day
done
```