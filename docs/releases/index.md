# Builds and Releases

##ReportLab Build Process

In modern python the preferred distribution is the wheel.

Because ReportLab open source has some C extensions we need to build wheels for many OS/python combinations.

Travis has become almost impossible to use without payment. The appveyor builds were always a bit flaky. To
fix that we(I) have moved the wheel building to a github workflow.

ReportLab open source has two C extensions.

The first `_rl_accel` is used for speedups of some operations eg
stringWdth and similar. It is an optimisation. It is possible to run reportlab without it. There is a slow down.

The second extension is our `_renderPM` graphics backend. It is almost all replaceable by using rlPyCairo
our pure python interface to the cairo graphics library. I notice only minor changes to image pasting.
However, we have functionality in `_rl_accel.c` for obtaining text glyph curves. That is not present
so far as I can tell with cairo or at least it's not exposed to the python interfaces. It's possible to
access curves using eg package fonttools, but that may be costly in development and run time.

So at the moment we need to build wheels. How we do that is with the python package cibuild wheel
https://github.com/pypa/cibuildwheel or https://cibuildwheel.readthedocs.io/en/stable/. Our usage of it is
encapsulated in the action

https://hg.reportlab.com/hg/reportlab/file/tip/.github/workflows/buildwheels.yml

a yaml file which is executed by github on every push to https://github.com/MrBitBucket/reportlab-mirror

we have a cron job which updates the mirror whenever the mercurial repo gets changed.

The extension/wheel construction is done in the job build-wheels-linux-mac-windows (line 26)
which follows fairly closely on cibuildwheels examples.

The build job constructs artefacts that contain the wheel(s) that are built that is
the step near line 80 uses: actions/upload-artifact@v2, we could just download the artefacts
to get the wheels, but following our earlier practice we use a job clear-cache (line 10) to clear
an upload area on App4 and another job email: (line 84) to upload all the built wheels.
To prevent mis-use we have specific user & password stored secretly in the reportlab-mirror git repo.
These are inserted into the yaml using a construct like this

{% raw %}
```
           CITOOLS_USER: "${{secrets.CITOOLS_USER}}"
           CITOOLS_PASSWORD: "${{secrets.CITOOLS_PASSWORD}}"
```
{% endraw %}

the github job processing eliminates these from the log output.

To make things a bit easier much of the testing has been embedded into the setup.py test subcommand.
The cibuildwheel process uses environment variables to control behaviour inside the dockers that actually
carry out the construction/testing. The test command is using a python command to call setup.py with
a host of arguments. The project folder is inserted as r'{project}' which means the Windows usage of `\` will
not be a problem. The test command is in the yaml at line 41
{% raw %}
```
CIBW_TEST_COMMAND: python -u -c "import sys,os,subprocess;r=subprocess.call((sys.executable,'-u',os.path.join(r'{project}','setup.py'),'tests-postinstall','--show-env','--verbose','--pip-install=pyphen','--pip-install=rlPyCairo','--rl-index-url=https://${{secrets.CITOOLS_USER}}:${{secrets.CITOOLS_PASSWORD}}@www.reportlab.com/pypi','--rl-pip-install=pyfribidi'),stderr=subprocess.STDOUT,timeout=180);marker=20*(chr(33)
if r else '=');print('%s test command --> %s %s'%(marker,r,marker));sys.exit(r)" 2>&1
```
{% endraw %}
  which corresponds to executing a script like this

{% raw %}
```
import sys,os,subprocess
r=subprocess.call(
        (       #this is the command argument to subprocess call
        sys.executable,'-u', #we make it unbuffered
        os.path.join(r'{project}','setup.py'),
        'tests-postinstall','--show-env',
        '--verbose',
        '--pip-install=pyphen',
        '--pip-install=rlPyCairo',
        '--rl-index-url=https://${{secrets.CITOOLS_USER}}:${{secrets.CITOOLS_PASSWORD}}@www.reportlab.com/pypi',
        '--rl-pip-install=pyfribidi'
        ),
        stderr=subprocess.STDOUT,       #and combine stderr & stdout
        timeout=180,
        )
marker=20*(chr(33) if r else '=')
print('%s test command --> %s %s'%(marker,r,marker))
sys.exit(r)
```
{% endraw %}

probably we should create a simple runner script which does what the setup.py script now does.


Currently we have moved to using the manylinux2014 wheels for linux. Earlier runs eg August
used manylinux2010, but pillow has moved on for python 3.10.0 so I moved all the builds to
using that image for linux (see lines 34-35).

There are various CIBW env vars which control which things get built/skipped.

The OS's we build with are determined by line 45
{% raw %}
```
   strategy:
       fail-fast: true
       matrix:
         os: [ubuntu-latest, macos-latest, windows-latest]
```
{% endraw %}

The python versions are now 3.6 - 3.10 (determined by cibuildwheel).

We are building windows builds for win32/amd64. Linux builds for standard libc >=2.17 intel
amd64/i686 and arm aarch64. The macos builds for arm64 11.0 and 10.9 x86_64.

We are not building any musllinux (a libc replacement) library wheels or any of the exotic
architectures like s390x or ppc64le.

Using a handomatic local call of the cibuildwheel (see app4:devel/t39/reportlab/cibw for the code)
process I observe different behaviours running on my local archlinux versus the behaviour on
app4 ubuntu 18.04 lts. I suppose this means that the docker environment is not totally isolated
from the host properties.

## Release notes
[Release notes](https://www.reportlab.com/documentation/relnotes/) for rlextra and reportlab.

## Mercurial repo for shared-code customers

**Note** - it is advisable to pull and update before committing/pushing new changes

#### 1. Clone the repository
`hg clone {url path to repository}`

#### 2. Pull in the latest code changes and update to the latest revision
`hg pull`

`hg up`

*or* combine the pull and update in one command;

`hg pull -u`

#### 3. Commit new changes
`hg ci -m 'description of code changes'`

#### 4. Push changes back to the repository
`hg push`
