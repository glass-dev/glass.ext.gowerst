# GLASS extension for loading Gower St simulations

This repository contains a GLASS extension for loading Gower St simulations.

## Quick start

Load a GowerSt simulation by pointing the `glass.ext.gowerst.load()` function
to the simulations `.par` file:

```py
sim = glass.ext.gowerst.load("~/data/gowerst/run014/control.par")
```

The resulting object has attributes such as `sim.parameters`, `sim.cosmology`,
and `sim.shells` that describe the simulation.

The matter shells can be loaded with the `sim.lightcone()` function.

## Cosmology

The simulation cosmology is returned from the stored CLASS file, without
computing any new cosmological quantities.

The returned cosmology object follows the Cosmology API standard and can be
passed directly into GLASS functions that require it.

## Worked example

```py
import numpy as np
import healpy as hp

import glass
import glass.ext.gowerst

# nside for computation
nside = 1024

# load simulation
sim = glass.ext.gowerst.load("~/data/gowerst/run014/control.par")

# get simulation parameters
cosmo = sim.cosmology
shells = sim.shells

# localised redshift distribution with arbitrary normalisation
z = np.linspace(0.0, 2.0, 101)
dndz = np.exp(-0.5 * (z - 0.5)**2 / (0.1)**2)

# partition the redshift distribution over the shells
ngal = glass.partition(z, dndz, shells)

# this will load the lightcone iteratively
# up to redshift 2 and rescaled to nside
matter = sim.lightcone(zmax=2.0, nside=nside)

# this will compute the convergence field iteratively
convergence = glass.MultiPlaneConvergence(cosmo)

# the integrated convergence and shear field over the redshift distribution
kappa_bar = np.zeros(12 * nside**2, dtype=float)
gamma_bar = np.zeros(12 * nside**2, dtype=complex)

# load each delta map and process
for i, delta in enumerate(matter):
    # add lensing plane from the window function of this shell
    convergence.add_window(delta, shells[i])

    # get convergence field
    kappa = convergence.kappa

    # compute shear field
    gamma, = glass.from_convergence(kappa, shear=True)

    # add to mean fields using the galaxy number density as weight
    kappa_bar += ngal[i] * kappa
    gamma_bar += ngal[i] * gamma

# compute the overall galaxy density
ngal = np.trapezoid(dndz, z)

# normalise mean fields by the total galaxy number density
kappa_bar /= np.sum(ngal)
gamma_bar /= np.sum(ngal)

# save the resulting mean kappa and gamma maps
np.savez("nbody.npz", kappa=kappa_bar, gamma1=gamma_bar.real, gamma2=gamma_bar.imag)
```
