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

# Why
 
 TL/DR: conda <4.6 , when calling `conda env` install/force ignores the channel pin. 
 
 We, and many other people use conda environment yamls, such as below, to describe self contained environments
 using packages from different channels. I'm not sure if the conda developers are deprecating the `conda env` command, but it is common in conda version <4.6 that conda will simply ignore the channel pins `channel::package` and take the most recent build across all channels listed.

```
name: stitchup

channels:
  - loopbio
  - conda-forge
  - defaults

dependencies:
  - conda-forge::python=2.7
  - loopbio::ffmpeg=4.0.2=nonvidia_gpl*
  - loopbio::opencv=3.4.3
```

Depending on who (which channel) has the most recent opencv build of that version, a different package will be installed. When combined with an environment of any decent complexty, and with the fact that conda(forge) developers often rebuild minor or micro build increments **with new or changed dependencies!!!** will often lead to environments being installed that do not contain what you specified them to contain.

To make matters worse, `conda env` often **silently succeeds**, which means you will just get random crashes because you have mixed incompatible and/or poorly specified (by other packages) dependencies.

If you want to use conda envs `environment.yaml` then the only way to guarentee that you get the build from the channel you requested is to use hashed build strings. But if you are doing this, you might as well use explicit environment / lists of packages anyway.

A 'known' [workaround](https://github.com/conda/conda/issues/6385#issuecomment-361787360) has been to just call `conda create` **not conda env** with all the packages in the environment yaml. **That's what this tool does**

TL/DR: let's use the (hashed) build string to ensure conda gets our packages until channel pins in environment.yaml files get respected. From there, we should also try to use "locked" (aka "explicit") environments - although that is more of an upcoming conda feature than a reality in the conda 4.5 series.

In principle this is fixed in conda 4.6, but moving to a major new version of a tool which you already do not have the most trust in, is a scary affair. For now this tool does the same thing.

## Other Notes

In this particular case, if no channel pin is present, I think opencv from conda-forge is preferred because they start their build numbers on 200, which is a trick used to enforce a preferred default for packages with variants specified via "conda features". It is inherited from the blas package and hopefully won't be needed once conda features are phased out themselves (a lot of work has been done and conda features are now finally and happily discouraged as a mechanism to differentiate package variants).


