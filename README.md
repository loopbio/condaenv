# condaenv
### a working replacement of the conda env command

`$ conda env -f environment.yaml` doesnt follow your instructions. It will
happily create an environment that does not respect the channels and versions
of dependencies (`channel-name::package=version`) you specified. This is unacceptable.
Maintaining a conda `environment.yaml` of any complexity feels like

![conda env workflow](.conda.gif?raw=true "Im Serious")

# Operation

* `conda install -n root pyyaml  # make sure yaml is installed`
* `condaenv -f environment.yaml -n environmentname`

If conda is unable to install the packages as specified it will
error instead of silently succeeding (which `conda env` would do, thus
ruining the next 3 weekends because your environment doesnt not contain what
you specified it to contain)

