mace_md.py
Region-resolved RDFs. A pair can carry a suffix that restricts the centre atoms. For example, --rdf-pairs "Pt-Ow,Ow-Ow,Ow-Ow@near=Pt:3.5,Ow-Ow@near=C:3.5" gives water at the Pt surface versus water at graphene. There is also @z=lo:hi for a z-window. g(r) is still normalised with the mean cell density, so heights are not bulk-like. n(r) per selected centre is the number to trust.
Block-averaged error bars. RDFs are accumulated in blocks (--rdf-block-frames, default 500 samples for run; --rdf-nblocks, default 5, for rdf). The files gain gerr_ and nerr_ columns, and old files without them still load. Errors are underestimated if blocks are shorter than the correlation time.
NVE drift. In --ensemble nve the production total-energy drift (meV/atom/ps) is logged and written to summary.json. The warning threshold --nve-drift-warn (default 0.1) is a rule of thumb I picked, not a standard, so adjust it.
Bug fix. The existing diagnostics crashed when no atom pair was within 3.5 Å (for example after atoms fly apart). That case is now handled.
Already there. The Pt detachment and skeleton-integrity diagnostics were already in the script, so I left them as they were. Use --save-events in production runs. I did not add NPT.
rdf_summary.py
Error bars. Uncertainties show as ± in the table and as shaded bands in the plots.
Comparison against the first file. For each pair, z(g) and z(n) compare g(peak1) and n(min1) with the first file. A value below about 2 means the difference is within block noise. It says nothing about systematic error.
Peak finding. It is more robust: edge-padded smoothing instead of zero-padded, NaNs interpolated, smoothing width in Å, and a prominence check so shoulders and noise wiggles are rejected.
z-density overlay. --zgroups Ow,Pt,C overlays the z-density profiles and writes <out>_zprofile.png.
extract_frames.py

It writes one plain .xyz per frame (El x y z) for CP2K, with the atom order never changed. The comment line holds step, time and cell vectors.

Modes (--mode):
uniform (default) takes --n evenly spaced frames.
stride, random and fps are also available. fps is farthest-point sampling on a Pt-environment descriptor, for diverse frames.
events takes frames around changes in the number of detached Pt atoms (±--window frames, capped at --n).
Overlap filter. Frames with closest-approach ratio below --min-dmin-ratio (default 0.70) are rejected. This is aimed at the overlapping-atom frames you found in the master pool. --max-fmax filters on stored MLIP forces.
Other outputs. index.csv has source index, step, cell and the MLIP energy and max force. selected.extxyz has the same frames with cell and MLIP energy/forces, so you can merge the CP2K labels back later.
Input formats. It reads extxyz or a LAMMPS dump (--format lammps-dump-text --specorder Pt,C,O,H,F,S).

Things to know before using it for the validation tests:

Event mode only catches Pt detachment, not other chemistry. Use fps for the other chemistry.
FPS looks only at Pt coordination, so it won’t diversify water or Nafion configurations.
Cell vectors are in the comment line and index.csv, not in the xyz data. The script warns if the cell varies between frames.