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


## Geometric features


## Full feature vector


## Energy and charge networks


## Transfer learning through shared parameters


