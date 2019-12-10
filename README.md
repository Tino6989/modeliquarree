# modeliquarree (Modelica-Quarree100)

Repository for work with Modelica in the project Quarree100.

## Examples

This directory contains general examples for the work with OpenModelica and
Python. BouncingBall.py implements several ways of connecting Python to
Modelica Simulations and Functional Mock-up Units (FMUs), for example through
OMPython (OpenModelica Python Interface). It uses the standard BouncingBall
Modelica example project.

## HP_testbench

This directory contains a Python package called ``hptestbench``, which allows
simulating heatpumps with OpenModelica or FMUs, in order to evaluate their
performance and integrate the results in other workflows. The main purpose is
to provide e.g. COP timeseries for use in oemof optimizations.
``hptestbench`` is designed to be a self-contained Python package. It provides
the necessary files for installation from source, as well as both an
OpenModelica model and an equivalent FMU. Private data required for
Quarree100 and not shared on GitHub can be automatially loaded, too.
``hptestbench`` makes sure the necessary replacements are made.

### Installation
After cloning the repository from GitHub, you can install the package from
source with either pip:
```
python setup.py install
```
... or conda (making sure all dependency packages are installed):
```
conda build conda.recipe
conda install hptestbench --use-local
```
... or you can "install" it with conda's development mode:
```
conda develop .
```
This may be the preferred option, since it just registers a link to the Git
directory. After any changes are made, no new installation is required.

You can now simulate FMUs with [``pyfmi``](https://pypi.org/project/PyFMI/).
If you want to simulate via OpenModelica, both
[OpenModelica](https://openmodelica.org/) itself and
[OMPython](https://github.com/OpenModelica/OMPython) have to be installed
manually (since ``OMPython`` is not available through conda).

> Please note:
>
> OMPython changed the input style for functions like
> ``setSimulationOptions()``, ``setParameters()``, etc. dramatically
> after v3.1.2. In order to be future-proof, this package requires the latest
> version from https://github.com/OpenModelica/OMPython/archive/master.zip
> (which is different from the version shipped with the Windows installer of
> OpenModelica!)

### Actions
Please refer to the workflow implemented as a GitHub action for the complete
installation instructions:
```
.github\workflows\heatpump_testbench.yml
```
This action serves as a test that is executed on each commit, making sure
both installation and simulation are working properly.

### Usage
Examples for how to use the testbench can be found in
``HP_testbench\conda.recipe\run_test.py`` and
``HP_testbench\hptestbench\__main__.py``

```python
import hptestbench
# Create our testbench
bench = hptestbench.testbench()

# Optionally, install a specific heat pump
bench.install_HP('Q100_air_water_001')  # optional

# Run the simulation with Modelica
bench.test_with_modelica(make_FMU=True)

# (or) Run the simulation via the functional mockup interface (FMI)
bench.test_with_FMU()

# Evaluate the results
data = bench.evaluate()
COP_mean = data['heatPump1.CoP_out'].mean()
```