---
layout: single
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


## Transfer learning through shared parameters


