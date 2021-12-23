# auto-DRRAFTER

## About
This repository is intended for use with the chapter **Auto-DRRAFTER: Automated RNA modeling based on cryo-EM density** from the SpringerNature Methods in Molecular Biology: Protocols for RNA Structure and Dynamics Studies.

As an example, we will use the  *F. nucleatum* glycine riboswitch (PDB 6wll) that was previously solved by [Kappel et al. 2020](https://www.nature.com/articles/s41592-020-0878-9).

More Information TBD.

## Recommended Computing Requirement
Typical use of auto-DRRAFTER requires a high-performance computing cluster. While runs may be executed interactively, most auto-DRRAFTER runs benefit from using a job scheduler such as SLURM or Torque.

All jobs were executed on Sherlock 2.0, Stanford’s high computing cluster. It took 144 computing hours (24 hours per auto-DRRAFTER round) to model the F. nucleatum glycine riboswitch. Each of the 150 jobs per helix placement used a single processor with 4GB of memory from any of the following processors available on Sherlock: Intel E5-2640v4, Intel 5118, or AMD 7502. 

## Methods

The following are four python modules that will be used throughout the auto-DRRAFTER pipeline: 

1. auto-DRRAFTER_setup.py
2. submit_jobs.py
3. auto-DRRAFTER_setup_next_round.py
4. finalize_models.py

`auto-DRRAFTER_setup.py` is used to generate the low-pass density map to determine the `-map_thr` value and to place the initial probe helices. Both `submit_jobs.py` and `auto-DRRAFTER_setup_next_round.py` will then be used iteratively in each round; for example, there are six rounds in this example so both module will be ran six times. After reaching convergence, `finalize_models.py` is used to complete modeling.

### Selecting end nodes for probe helix placement
```shell
# Step 2.1 from the auto-DRRAFTER chapter.
python $ROSETTA/main/source/src/apps/public/DRRAFTER/auto-DRRAFTER_setup.py -map_thr 20 -full_dens_map input_files/FNG_riboswitch.mrc -full_dens_map_reso 10.0 -fasta input_files/FNG_riboswitch.fasta -secstruct input_files/FNG_riboswitch.txt -out_pref FNG_riboswitch -rosetta_directory $ROSETTA/main/source/bin/ -nstruct_per_job 200 -cycles 30000 -fit_only_one_helix -rosetta_extension .static.linuxgccrelease -just_low_pass
```

```shell
# Step 3.1 from the auto-DRRAFTER chapter.
python $ROSETTA/main/source/src/apps/public/DRRAFTER/auto-DRRAFTER_setup.py -map_thr 0.0499 -full_dens_map input_files/FNG_riboswitch.mrc -full_dens_map_reso 10.0 -fasta input_files/FNG_riboswitch.fasta -secstruct input_files/FNG_riboswitch.txt -out_pref FNG_riboswitch -rosetta_directory $ROSETTA/main/source/bin/ -nstruct_per_job 200 -cycles 30000 -fit_only_one_helix -rosetta_extension .static.linuxgccrelease
```
>	###### Output of Step 3.1
>	Using only one end node!\
>	Low-pass filtering the map to 20A.\
>	Converting density map to graph.\
>	Possible end nodes in the map:\
>	1 5 11\
>	You can visualize the end nodes in FNG_riboswitch_init_points.pdb\
>	You can specify which of these end nodes you'd like to use with ->use_end_node\
>	Converting secondary structure to graph.\
>	Mapping secondary structure to density map.\
>	Setting up DRRAFTER runs.\
>	Making full helix H7\
>	Making full helix H4\
>	Making full helix H10\
>	Making full helix H15

### Build initial models with different end nodes

#### R1 (Round 1)
```shell
# Step 4.1, first iteration from the auto-DRRAFTER chapter.
python $ROSETTA/main/source/src/apps/public/DRRAFTER/submit_jobs.py -out_pref FNG_riboswitch -curr_round R1 -njobs 150 -template_submission_script input_files/job_submission_template.sh -queue_command sbatch
```

```shell
# Step 5.1, first iteration from the auto-DRRAFTER chapter.
python $ROSETTA/main/source/src/apps/public/DRRAFTER/auto-DRRAFTER_setup_next_round.py -out_pref FNG_riboswitch -curr_round R1 -rosetta_directory $ROSETTA/main/source/bin/ -convergence_threshold 10 -rosetta_extension .static.linuxgccrelease
```
>###### Output of Step 5.1, first iteration
>	Setting up next round\
>	Overall convergence 29.115\
>	Density threshold: 0.051\
>	Density threshold: 0.051\
>	Density threshold: 0.051\
>	Density threshold: 0.051

### Iterative refinement of RNA models
#### R2 (Round 2)

```shell
# Step 4.1, second iteration from the auto-DRRAFTER chapter.
python $ROSETTA/main/source/src/apps/public/DRRAFTER/submit_jobs.py -out_pref FNG_riboswitch -curr_round R2 -njobs 150 -template_submission_script input_files/job_submission_template.sh -queue_command sbatch
```

```shell
# Step 5.1, second iteration from the auto-DRRAFTER chapter.
python $ROSETTA/main/source/src/apps/public/DRRAFTER/auto-DRRAFTER_setup_next_round.py -out_pref FNG_riboswitch -curr_round R2 -rosetta_directory $ROSETTA/main/source/bin/ -convergence_threshold 10 -rosetta_extension .static.linuxgccrelease
```
>###### Output of Step 5.1, second iteration
>	Setting up next round\
>	Overall convergence 11.007\
>	Density threshold: 0.051\
>	Density threshold: 0.051\
>	Density threshold: 0.051\
>	Density threshold: 0.051

#### R3 (Round 3)

```shell
# Step 4.1, third iteration from the auto-DRRAFTER chapter.
python $ROSETTA/main/source/src/apps/public/DRRAFTER/submit_jobs.py -out_pref FNG_riboswitch -curr_round R3 -njobs 150 -template_submission_script input_files/job_submission_template.sh -queue_command sbatch
```

```shell
# Step 5.1, third iteration from the auto-DRRAFTER chapter.
python $ROSETTA/main/source/src/apps/public/DRRAFTER/auto-DRRAFTER_setup_next_round.py -out_pref FNG_riboswitch -curr_round R3 -rosetta_directory $ROSETTA/main/source/bin/ -convergence_threshold 10 -rosetta_extension .static.linuxgccrelease
```
>###### Output of Step 5.1, third iteration
>	Overall convergence 11.781\
>	Density threshold: 0.051\
>	Density threshold: 0.051\
>	Density threshold: 0.051\
>	Density threshold: 0.051

#### R4 (Round 4)

```shell
# Step 4.1, fourth iteration from the auto-DRRAFTER chapter.
python $ROSETTA/main/source/src/apps/public/DRRAFTER/submit_jobs.py -out_pref FNG_riboswitch -curr_round R4 -njobs 150 -template_submission_script input_files/job_submission_template.sh -queue_command sbatch
```

```shell
# Step 5.1, fourth iteration from the auto-DRRAFTER chapter.
python $ROSETTA/main/source/src/apps/public/DRRAFTER/auto-DRRAFTER_setup_next_round.py -out_pref FNG_riboswitch -curr_round R4 -rosetta_directory $ROSETTA/main/source/bin/ -convergence_threshold 10 -rosetta_extension .static.linuxgccrelease
```
>###### Output of Step 5.1, fourth iteration
>	Setting up next round\
>	Overall convergence 8.421\
>	Density threshold: 0.051

### Final two rounds of modeling

#### FINAL_R5 (Final Round 5)

```shell
# Step 6.2 from the auto-DRRAFTER chapter.
python $ROSETTA/main/source/src/apps/public/DRRAFTER/submit_jobs.py -out_pref FNG_riboswitch -curr_round FINAL_R5 -njobs 150 -template_submission_script input_files/job_submission_template.sh -queue_command sbatch
```

```shell
# Step 6.3 from the auto-DRRAFTER chapter.
python $ROSETTA/main/source/src/apps/public/DRRAFTER/auto-DRRAFTER_setup_next_round.py -out_pref FNG_riboswitch -curr_round FINAL_R5 -rosetta_directory $ROSETTA/main/source/bin/ -rosetta_extension .static.linuxgccrelease
```
>###### Output of Step 6.3
>	Setting up next round\
>	Overall convergence 4.484\
>	Density threshold: 0.051

#### FINAL_R6 (Final Round 6)

```shell
# Step 6.4 from the auto-DRRAFTER chapter.
python $ROSETTA/main/source/src/apps/public/DRRAFTER/submit_jobs.py -out_pref FNG_riboswitch -curr_round FINAL_R6 -njobs 150 input_files/job_submission_template.sh -queue_command sbatch
```

```shell
# Step 6.5 from the auto-DRRAFTER chapter.
python $ROSETTA/main/source/src/apps/public/DRRAFTER/auto-DRRAFTER_setup_next_round.py -out_pref FNG_riboswitch -curr_round FINAL_R6 -rosetta_directory $ROSETTA/main/source/bin/ -rosetta_extension .static.linuxgccrelease
```
>###### Output of Step 6.5
>	DONE building models for FNG_riboswitch

### Finalizing Models

```shell
# Step 7.1 from the auto-DRRAFTER chapter.
python $ROSETTA/main/source/src/apps/public/DRRAFTER/finalize_models.py -fasta input_files/FNG_riboswitch.fasta -out_pref FNG_riboswitch -final_round FINAL_R6
```
>###### Output of Step 7.1
>	Done finalizing models
