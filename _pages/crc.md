---
layout: archive
title: "TensorMol 2.0: A fully transferable high-dimensional neural network potential"
permalink: /crc/
author_profile: true
---


## Atomic fingerprint encoding

![](/files/element_coder.png)

The atomic fingerprint is encoded from a set of physical properties of each element. The encoding is performed
by training an autoencoder neural network on the reconstruction loss of the properties for each element. This
forces the latent features to contain a compressed representation of the set of properties by creating an
information bottleneck. 

The physical properties used to represent each element include atomic number, atomic mass, the number of 
electrons in the valence s-, p-, and d-orbitals, electronegativity, atomic radius, first ionization energy, 
first electron affinity, and polarizability. These are properties which chemist use to guide their earliest
intuitions about how the elements behave in comparison with one another. Using an autoencoder enables us to
derive a fingerprint vector from the latent features which contains information about the physical laws the elements
abide by. We refer to this fingerprint vector as the elemental modes, which is a vector of four encoded features. 

The encoded representation from the autoencoder is not interpretable directly, but to gain an understanding of
what information is encoded, we performed principal component analysis on the elemental modes vectors and plotted
first and second principal components.

![](/files/pca_modes.png)
 
The elements are grouped by color into common subsets which represent similarities among the group. 
The trends of the principal components are strikingly similar to the periodic table, despite never having 
been given the group or period number of each element during training of the autoencoder. Indeed the
elemental modes contain information which may be used to describe similarities and differences among
the elements.
 


## Geometric features


The geometric features describe the local environment of an atom. In the example figure, the uracil molecule is
shown with the local environment around the nitrogen atom in the center of the highlighted circle. The geometric
features use a basis of two-body and three-body correlations to describe the distances and angles between atoms
that fall within the sensory range of the basis.

![](/files/acsfs.png)

The geometric features used for TensorMol are called the atom-centered symmetry functions (ACSFs). The equations 
to derive the features are given below. The equations use a basis of Gaussian function centers and angles to 
probe the local environment of the atom at different distances and reference angles. The first two equations 
are called the radial and angular functions, respectively. The radial functions purely encode the distance of 
an atom in the local environment. The angular functions encode the average distance of a pair of atoms, and 
the angle between the atoms with the vertex at the central atom. The cutoff function is used with both sets 
of the symmetry functions to ensure that the signal from the features smoothly drops to zero as an atom moves 
beyond the sensory range. The results are summed over all neighbors in the local environment to keep the features
invariant with respect to ordering of the neighbor atoms.

![](/files/acsfs_eqns.png)

## Full feature vector

The ACSFs only encode the geometric environment. The identity of atoms must also be encoded in the final feature
vector used to represent the atom. The previous iteration of TensorMol used channels to differentiate the identity
of atoms in the local environment. The radial functions contain channels for each unique element, but the angular
functions must contain channels for each unique pair of elements (i.e. HH, HC, HN, HO, CC, etc.) which scales
the number of input features exponentially with the number of unique elements.

![](/files/tmol1_features.png)

In contrast to its predecessor, TensorMol 2.0 avoids scaling the size of the feature vectors by using the
elemental modes to scale the geometric features across channels. The radial function uses the elemental mode
vector of the atom, and the angular symmetry functions first take the element-wise product of the elemental
mode vectors for the pair of atoms, which is then used to encoded the angular functions


![](/files/tmol2_features.png)

## Energy and charge networks

The previous version of TensorMol uses an array of neural networks, one for each unique element in the training
data. Additionally, there are two subsets of networks; one for predicting partial charges of each atom, and one
for predicting a contribution to the total molecular energy. The partial charges are used to include a long-range
Coulomb energy, which is added to the contributions predicted by the energy networks. Dispersion energy is also
included using Grimme's semi-emiprical C6 scheme.

![](/files/tmol1_enn.png){:height="55%" width="55%"}
![](/files/tmol1_cnn.png){:height="43%" width="43%"}

The updated TensorMol model instead uses a single neural network for all elements. The advantages are a reduced
number of overall parameters, improvements to dataset imbalances with respect to the frequency of each element
in the training data, and parameter sharing can incorporate transfer learning between the elements.

![](/files/tmol2_networks.png){:height="50%" width="50%"}

## Training dataset

The training data includes ~1.35 million samples from a diverse set of small organic molecules including eleven 
unique elements. To put this into perspective, the first iteration of TensorMol included four unique elements. 
The total number of parameters used for TensorMol 1.0 was ~7.3 million. To train an equivalent model for eleven 
elements would increase the number of parameters to ~63 million. The changes made in TensorMol 2.0 only required 
~1.4 million parameters. Additionally, the energy and force errors on the test set after training the model was 
0.098 kcal/mol/atom and 3.7 kcal/mol/Å, respectively. These errors are similar to the previous model, despite
a more challenging training set and fewer overall parameters.

## Transfer learning through shared parameters

To explore the degree to which the parameter sharing may improve predictions, we trained an identical model by
removing all samples including any Cl atoms from the datasets. Then we examined how the two models differ in
their predictions of the atomic energies. First, a comparison for the distribution of N atom energies is given
to show that the distributions are expected to have minor differences, even for elements which have significant
numbers of samples, but that the distributions are highly similar.

![](/files/n_atomization.png)

Comparing the distributions for Cl atoms from the independent test data between the two models, it can be seen
that there is a larger discrepancy, however, qualitatively the distributions are highly similar. This is despite
one of the models never having trained on this element. The error in the predicted total energy for these 
molecules which all include at minimum one Cl atom, is about 10 kcal/mol. TensorMol 1.0 by contrast is unable 
to make predictions for elements it has not trained on. 

![](/files/cl_atomization.png)

