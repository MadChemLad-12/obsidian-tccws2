A document to keep all my thoughts on the progress of the documetn. 


### NOTES FOR FUTURE SELF

AT the moment I am working on establishing a framework to use plummed and mace together.
as of 11/20 I am running

```
ROOT=~/MACE_train/LAMMPS_MACE_degradation

cd $ROOT/pipeline_runs/20261119_145955_prod

for d in cov*/; do ( cd "$d" && python $ROOT/analysis/analyze_run.py traj md.extxyz --out md --dt 0.0005 > analyze_traj.txt 2>&1 ); done

for d in cov*/; do ( cd "$d" && python $ROOT/analysis/analyze_run.py bonds md.extxyz --out md > analyze_bonds.txt 2>&1 ); done

python $ROOT/summarize_pipeline.py .

  

cd $ROOT/pipeline_runs/20261119_145955_prod/cov0.375_mixed_s426_graphene__octahedron_055pt_O0.391ML_s426

lmp -k on g 1 -sf kk -pk kokkos newton on neigh half \

  -in $ROOT/lammps/ptgr_naf/ptgr_naf.lmp -log md2.log -screen md2.screen \

  -var data md_equil.data -var userestart 1 -var restartfile md_equil.restart \

  -var model $ROOT/mace_V11_active_learning.model-mliap_lammps.pt \

  -var pairfile $ROOT/lammps/ptgr_naf/pair_mace.lmp \

  -var nmin 0 -var nheat 0 -var neq 0 -var nprod 200000 -var useplumed 0 \

  -var tag md2 -var extxyz 1 -var ndump 200

python $ROOT/analysis/analyze_run.py traj md2.extxyz --out md2 --dt 0.0005

  

cd $ROOT/pipeline_runs/20261119_145955_prod/cov0.000_none_s426_pt__octahedron_019pt

lmp -k on g 1 -sf kk -pk kokkos newton on neigh half \

  -in $ROOT/lammps/ptgr_naf/ptgr_naf.lmp -log md2.log -screen md2.screen \

  -var data md_equil.data -var userestart 1 -var restartfile md_equil.restart \

  -var model $ROOT/mace_V11_active_learning.model-mliap_lammps.pt \

  -var pairfile $ROOT/lammps/ptgr_naf/pair_mace.lmp \

  -var nmin 0 -var nheat 0 -var neq 0 -var nprod 200000 -var useplumed 0 \

  -var tag md2 -var extxyz 1 -var ndump 200

python $ROOT/analysis/analyze_run.py traj md2.extxyz --out md2 --dt 0.0005

  

cd /home/h.jack/MACE_train/LAMMPS_MACE_degradation/pipeline_runs/20261119_145955_prod/cov0.375_mixed_s426_graphene__octahedron_019pt_O0.333ML_s426

lmp -k on g 1 -sf kk -pk kokkos newton on neigh half \

  -in $ROOT/lammps/ptgr_naf/ptgr_naf.lmp -log md2.log -screen md2.screen \

  -var data md_equil.data -var userestart 1 -var restartfile md_equil.restart \

  -var model $ROOT/mace_V11_active_learning.model-mliap_lammps.pt \

  -var pairfile $ROOT/lammps/ptgr_naf/pair_mace.lmp \

  -var nmin 0 -var nheat 0 -var neq 0 -var nprod 200000 -var useplumed 0 \

  -var tag md2 -var extxyz 1 -var ndump 200

python $ROOT/analysis/analyze_run.py traj md2.extxyz --out md2 --dt 0.0005

  

cd /home/h.jack/MACE_train/LAMMPS_MACE_degradation/pipeline_runs/20261119_145955_prod/cov0.500_mixed_s426_graphene__octahedron_055pt_O0.522ML_s426

lmp -k on g 1 -sf kk -pk kokkos newton on neigh half \

  -in $ROOT/lammps/ptgr_naf/ptgr_naf.lmp -log md2.log -screen md2.screen \

  -var data md_equil.data -var userestart 1 -var restartfile md_equil.restart \

  -var model $ROOT/mace_V11_active_learning.model-mliap_lammps.pt \

  -var pairfile $ROOT/lammps/ptgr_naf/pair_mace.lmp \

  -var nmin 0 -var nheat 0 -var neq 0 -var nprod 200000 -var useplumed 0 \

  -var tag md2 -var extxyz 1 -var ndump 200

python $ROOT/analysis/analyze_run.py traj md2.extxyz --out md2 --dt 0.0005

```

This will give me an analyusis of how it went.

Once this is done I would like to also like to do a comparision with mace and dft maybe. I might also add an rdf function to bonds section of analyse run.py.

## 26-11-2026 
Working with cluade to figure out next step and picture what the final job will be. 
Ok I think I better understand the anlaysis run now. It is a way to show that the properties of MACE are actually useable. So far V11 seems to be quite good actually. 

For a rpoduction run this is what it will look like:
### What production looks like

Production means metadynamics on one target Pt atom at a time, starting from the equilibrated state:

1. **Starting point.** Use `md2_final.data` and `md2_final.restart` (written at the end of your run), not the old `md_equil` files.
2. **Choose the conditions.** For example 0.8 and 1.2 nm, bare and about 0.5 ML oxygen, and the `pt` head mode. You pick the grid after the checks below.
3. **Choose target sites** for each (vertex, edge, perimeter; perimeter is the Pt/graphene contact line).
4. **Pilot for SIGMA.** Run an unbiased PLUMED pilot of 20–50 ps and use the SIGMA it suggests.
5. **Run metadynamics.** Typically two walkers sharing the bias, 1–3 ns each, one per GPU.
    
    ```
    python $ROOT/structure_gen/prep_plumed.py md2_final.data --site vertex --mode metad --sigma <a,b> \
        --walkers 2 --walker-id 0 --outdir walker0     # and --walker-id 1 for walker1
    ```
    
6. **Outputs and analysis.** Each run gives `COLVAR`, `HILLS`, trajectory and log. You then run `colvar`, `fes` (a plateau in ΔF over time is the convergence test), and `merge` followed by `extract_frames.py --where` for the saddle-region frames. Those frames go to CP2K single points and to the mechanism analysis.

Budget at your speeds (about 17–19 h per ns per GPU): a surface with two walkers of 1.5 ns each is about a day of wall time on two GPUs. The whole project fits about 30–50 such surfaces at best, before replicas, so choose the grid with that in mind.

