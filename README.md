# radioactivedecay

[![PyPI](https://img.shields.io/pypi/v/radioactivedecay)](https://pypi.org/project/radioactivedecay/)
[![Python Version](https://img.shields.io/pypi/pyversions/radioactivedecay)](https://pypi.org/project/radioactivedecay/)
[![Latest Documentation](https://img.shields.io/badge/docs-latest-brightgreen)](https://alexmalins.com/radioactivedecay/)
[![Test Coverage](https://codecov.io/gh/alexmalins/radioactivedecay/branch/master/graph/badge.svg)](https://codecov.io/gh/alexmalins/radioactivedecay)

``radioactivedecay`` is a Python package for radioactive decay calculations.
It fully supports radionuclide decay chains, including those with branching
decays or chains passing through metastable states. By default
``radioactivedecay`` uses the decay data from ICRP Publication 107, which
covers 1252 radionuclides of 97 elements.

- **Full Documentation**: [https://alexmalins.com/radioactivedecay](https://alexmalins.com/radioactivedecay/)


## Installation

``radioactivedecay`` requires Python 3.6+, NumPy and SciPy.

The easiest way to install ``radioactivedecay`` is via the
[Python Package Index](https://pypi.org/project/radioactivedecay/) using `pip`:

```console
$ pip install radioactivedecay
```


## Usage

### Decay calculations
Create an `Inventory` of radionuclides and decay it as follows:

```pycon
>>> import radioactivedecay as rd
>>> inv = rd.Inventory({'I-123': 1.0, 'Tc-99m': 2.0})
>>> inv = inv.decay(20.0, 'h')
>>> inv.contents
{'I-123': 0.35180331802323694,
 'Tc-99': 5.852125859801924e-09,
 'Tc-99m': 0.19957172182663926,
 'Te-123': 1.6353735405592892e-18,
 'Te-123m': 1.3312369019952352e-07}
```

Here we created an inventory of 1.0 Bq of <sup>123</sup>I and 2.0 Bq of
<sup>99m</sup>Tc and decayed it for 20 hours. The decayed inventory contains
<sup>99</sup>Tc, which is the progeny of <sup>99m</sup>Tc, and 
<sup>123</sup>Te and <sup>123m</sup>Te, which are progeny of <sup>123</sup>I.

Note that ``radioactivedecay`` does not require you specify the activity units.
This is because its calculations are agnostic of the activity units: units out
are the same as units in. So this calculation could have also represented the
decay of 1.0 Ci of <sup>123</sup>I, or 1.0 dpm, or 1.0 kBq, etc.

However, you have to specify the units of the decay time. In the example we
supplied 'h' as an argument to the `decay()` method to specify the decay time
period (20.0) had units of hours. Accepted time units include 'ms', 's', 'm',
'h', 'd', 'y' etc. Note seconds ('s') is the default if you do not supply a
time unit to `decay()`

Radionuclides can be specified in three equivalent ways in
``radioactivedecay``. The strings
* 'Rn-222', 'Rn222' or '222Rn',
* 'Ir-192n', 'Ir192n' or '192nIr'
are all equivalent ways of specifying <sup>222</sup>Rn and <sup>192n</sup>Ir to
the program.


### Fetching decay data
``radioactivedecay`` includes a `Radionuclide` class which can be used to fetch
decay information for individual radionuclides.

```pycon
>>> nuc = rd.Radionuclide('I-123')
>>> nuc.half_life('d')
13.27
>>> nuc.progeny()
['Te-123', 'Te-123m']
>>> nuc.branching_fractions()
[0.99996, 4.442e-05]
>>> nuc.decay_modes()
['EC', 'EC']
```

The half-life for <sup>123</sup>I is thus 13.27 days. Its direct progeny
are <sup>123</sup>Te and <sup>123m</sup>Te, with branching fractions 0.99996
and 4.442e-05 respectively. Both of the decay modes occur via electron capture
(EC).

The default decay dataset in ``radioactivedecay``  is ICRP-107. Its data can be
queried directly as follows:

```pycon
>>> rd.DEFAULTDATA.dataset
'icrp107'
>>> rd.DEFAULTDATA.half_life('Cs-137', 'y')
30.1671
>>> rd.DEFAULTDATA.branching_fraction('Cs-137', 'Ba-137m')
0.94399
>>> rd.DEFAULTDATA.decay_mode('Cs-137', 'Ba-137m')
'��-'

## How radioactivedecay works

``radioactivedecay`` calculates an analytical solution to the decay chain
differential equations using matrix multiplications. It implements the
method described in this paper:
[M Amaku, PR Pascholati & VR Vanin, Comp. Phys. Comm. 181, 21-23 (2010)](https://doi.org/10.1016/j.cpc.2009.08.011).
It calls NumPy and SciPy for the matrix operations.

By default ``radioactivedecay`` uses decay data from
[ICRP Publication 107 (2008)](https://journals.sagepub.com/doi/pdf/10.1177/ANIB_38_3).

The [notebooks folder](https://github.com/alexmalins/radioactivedecay/tree/main/notebooks)
in the GitHub repository contains Jupyter Notebooks for creating the processed
decay datasets that are read in by radioactive decay, e.g.
[ICRP 107](https://github.com/alexmalins/radioactivedecay/tree/main/notebooks/icrp107_dataset/icrp107_dataset.ipynb).
It also contains some comparisons of decay calculations against the
[PyNE](https://github.com/alexmalins/radioactivedecay/tree/main/notebooks/comparisons/pyne/rd_pyne_truncated_compare.ipynb)
and
[Radiological Toolbox](https://github.com/alexmalins/radioactivedecay/tree/main/notebooks/comparisons/radiological_toolbox/radiological_toolbox_compare.ipynb)
codes.


## Tests

From the base directory run:

```console
$ python -m unittest discover
```


## License

``radioactivedecay`` is open source software released under the MIT License. The
ICRP-107 decay data is copyright 2008 A. Endo and K.F. Eckerman. See
[LICENSE](https://github.com/alexmalins/radioactivedecay/blob/main/LICENSE) for
details. 


## Contributing

Contributors are welcome to fix bugs, add new features or make feature 
requests. Please open a pull request or a new issue on the
[GitHub repository](https://github.com/alexmalins/radioactivedecay).


## Acknowledgements

Special thanks to
* [Center for Computational Science & e-Systems](https://ccse.jaea.go.jp/index_eng.html),
Japan Atomic Energy Agency
* [Kenny McKee](https://github.com/Rolleroo)

for their help and support to this project.
