# CRNSeSi
Package for "Chemical Reaction Network SEnsitivity SIgns" as used in 

E.D. Sontag. A technique for determining the signs of sensitivities of steady states in chemical reaction networks. IET Systems Biology, 8:251-267, 2014. 

CRNSeSi.zip has all files.

The readme file in this depository is repeated here for convenience:

CRNSENSI quick readme file

TESTING: open MATLAB in same folder and type
   
find_signs

The answer should be as follows:

possible_signs =

    -1     1    -1     1    -1     1    -1    -1

    L1    L1p   L2    L2p   L3    L3p    L4   L4p


===========================================

DETAILS:

A. I first run a shallscript to 

1. erase links to files from previous runs
   (obviously, this will give an error if there are no such old files)
2. symbolic link the problem-specific files

for example,
  make_phospho_4stages.sh 
executes:

/bin/rm ABGN.m
/bin/rm make_virtual_constraints.m
/bin/rm make_initial_stoichiometric_constraints.m
/bin/rm names_vars.m
ln -s ABGN_phosphotransfer_4stages.m ABGN.m
ln -s make_initial_stoichiometric_constraints_phosphotransfer_4stages.m make_ini
tial_stoichiometric_constraints.m
ln -s names_vars_phosphotransfer_4stages.m names_vars.m
ln -s make_virtual_constraints_phosphotransfer_4_steps.m make_virtual_constraint
s.m

(of course, one could just rename files by hand; I do everything in linux and
like implementing in this fashion).

When there are no virtual constraints, I use:

  make_virtual_constraints_empty.m

whose contents are just:

display('adding virtual constraint(s), if any')
virtual = [];

===========================================

The linked files in this example are as follows:

1.  ABGN_phosphotransfer_4stages.m

Its contents are:

N=8;
% rows:  L1 L1p L2 L2p L3 L3p L4 L4p
G = [...
-1 1 0 0 0;
1 -1 0 0 0;
0 -1 1 0 0;
0 1 -1 0 0;
0 0 -1 1 0;
0 0 1 -1 0;
0 0 0 -1 1;
0 0 0 1 -1]

B =  (G>0).*G;
A = -(G<0).*G;
% debug, should be zero: 
%G - (B-A)

i.e. say how any species there are,
write stoihiometry matrix
and pick its positive and negative parts

2. make_initial_stoichiometric_constraints_phosphotransfer_4stages.m

% keep L1 constant
% rows:  L1 L1p L2 L2p L3 L3p L4 L4p
  L1T  = [1   1  0   0  0   0  0   0];

% keep L2 constant
% rows:  L1 L1p L2 L2p L3 L3p L4 L4p
  L2T  = [0   0  1   1  0   0  0   0];

% keep L3 constant
% rows:  L1 L1p L2 L2p L3 L3p L4 L4p
  L3T  = [0   0  0   0  1   1  0   0];

stoichiometry_constraints = [L1T;L2T;L3T];

It is the obvious thing: each row of stoichiometry_constraints gas the
coefficients of a conservation law.

3. names_vars.m

varnames = '     L     T    X1    X2    X3    X4';

Used only for printing purposes

4. make_virtual_constraints_phosphotransfer_4_steps.m
display('adding virtual constraint(s), if any')

% rows:  L1  L1p L2  L2p  L3 L3p L4 L4p
virtual= [1   0   0   0   0  0   0  -1];

In this example, these came from:

k1 L1 = k2 L1p L2
k2 L1p L2 = k3 L2p L3
k3 L2p L3 = k4 L3p L4
k4 L3p L4 = k5 L4p

which implies k1 L1 - k5 L4p = 0

===========================================

B. Now just run 
find_signs.m
or
find_signs_triples.m
(if I need to search more)
I had no time to write a simple script to keep adding more combinations, but
this worked...)

===========================================

Files needed (called by find_signs):

test_n.m
generate_additional_n.m
generate_additional_n_triples.m
generate_combinations_stoichiometry.m
checkcontraint.m
testsp.m
generate_combinations_stoichiometry_triples.m

===========================================
Another example:

make_cascade_fix_GT_FT_MT_NT.sh
/bin/rm ABGN.m
/bin/rm make_virtual_constraints.m
/bin/rm make_initial_stoichiometric_constraints.m
/bin/rm names_vars.m

ln -s ABGN_cascade.m ABGN.m
ln -s make_initial_stoichiometric_constraints_cascade_fix_GT_FT_MT_NT.m make_ini
tial_stoichiometric_constraints.m
ln -s names_vars_cascade.m names_vars.m
ln -s make_virtual_constraints_empty.m make_virtual_constraints.m

NOTE: this one takes forever to run.
