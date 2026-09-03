# -Public-CL-Performance
Calculate performance across all CL tasks for all validators active over the period. Apply labeling to compare performance to benchmark. This is a public repo and only contains the readme.

# Steps

# Data Acquisition

Assumes this is running on an Ubuntu 24.04.4 LTS machine, all scripts in this section are bash scripts and run from the terminal (only the master_script.sh needs to be run - the rest are called by this script). Also requires access to an Ethereum node, either through a node provider or your own.


1. Update the pipeline file - this will dictate the performance period - takes slots. You can also modify step size - this dictates how much data goes into each file, i.e., a sync-committee period's worth, a day's worth, an hour, etc. Note that attestations use epochs instead. Note that there are 32 slots in an epoch and there are 8192 slots in a sync committee (or 256 epochs); a day contains 225 epochs. This file might be helpful: https://docs.google.com/spreadsheets/d/1FSNkBFta93j0e79G_PtCrv1LETWHhhtfvkD2B2AyeXA/edit?gid=835125837#gid=835125837

2. Review the files:
   
   master_script.sh
   
   attestation_data.sh
   
   proposal_data.sh
   
   sync_data.sh

   Change directory names as necessary and update with your endpoint info (can use a node provider or your own node).

4. Run the master_script.sh file from the terminal; i.e., bash master_script.sh


This process will run the three scripts attestation_data.sh, proposal_data.sh and sync_data.sh in parallel.

You may want to adjust master_script.sh to your machine specs (was run with 32gb RAM machine).

# Analysis

This part of the process calculates performance across all validators during the period. The heuristic here is to identify validators with the status "active_ongoing" at the start and end of the period, all other validators are excluded from the analysis.

1. Run in virtual environment (VENV). At a terminal type the following (in the same directory that you intend to run the analysis scripts):

sudo apt update

sudo apt install python3-venv python3-pip -y

python3 -m venv venv

source venv/bin/activate

pip install --upgrade pip

pip install pandas tqdm numpy requests

(when you're done type deactivate to return)

2. Run the following scripts in this order:

analyze-attestations.py

analyze-sync.py

aggregateProposalFiles.py

missingSlotsProps2.py

3. Once completed, review the output of missingSlotsProps2.py, i.e., 'final_resolved_slots-missed_slots.csv'. Filter by column 'Originally_Missing', i.e., = 1. These entries need to be manually updated. Use a public explorer like beaconcha.in.

# Labeling

This part of the analysis takes the output from the analysis section and aggregates validators by labels. The mapping exercise itself, i.e., associating operator/pool labels to validator pubkeys/indices, is outside the scope of this process. See Lido Curated Module Labeling repo for an example of mapping.

# TO DOs

Improvements to above process
1. Incorporate active set of validators - as a starting point this script needs a list of validators; this should be baked into the process
2. Update all scripts to be directory agnostic (use pwd for instance)
3. Move to daily calculation period - this does not affect pipeline file (i.e., user still chooses the period for the performance window), but daily aggregation will give more accurate performance measurements - use daily aggregation (decide on additive or multiplicative - hysteresis and low reward rate argues for additive; 0x02 and higher balance valds argues for multiplicative).
4. Add toggle for using either node provider or local node access.
