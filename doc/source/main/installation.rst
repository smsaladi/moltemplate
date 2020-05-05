Installation
============

There are three ways to install moltemplate:

Installation Method 1 (pip)
---------------------------

*If you are familiar with pip*, you can install moltemplate by typing
following command in the terminal/shell:

::

   pip install moltemplate

If you receive an error regarding permissions, then run pip this way
instead:

::

   pip install moltemplate --user

Make sure that your default pip install bin directory is in your *PATH*.
(This is usually something like /.local/bin/ or /anaconda3/bin/. If you
have installed anaconda, your PATH should have been updated for you
automatically.) Later, you can uninstall moltemplate using:

::

   pip uninstall moltemplate

*Note: There are a large variety of detailed moltemplate examples which
will be omitted if you install moltemplate this way.*\ **Downloading the
examples is strongly recommended.** You can do this either by using
*git*, or by downloading them from the web page. (See below.) (The
examples are located in the *examples/* subdirectory included with the
download.)

Installation Method 2 (git/webpage + pip)
-----------------------------------------

Obtaining Moltemplate
---------------------

The most up-to-date version of moltemplate can be downloaded using
**git**.

::

   git clone https://github.com/jewettaij/moltemplate ~/moltemplate

Later, you can update the download to the latest version of moltemplate
using:

::

   git pull

(This is the recommended way to download moltemplate.)

Alternatively, if you don’t have **git** installed, you can download
moltemplate as a .tar.gz archive from http://www.moltemplate.org and
then unpack it using:

::

   tar -xzvf moltemplate_2020-2-22.tar.gz

(The date will vary from version to version.)

Then move the unpacked moltemplate directory to your home directory (/)
and run:

::

   cd  ~/moltemplate
   pip install .  # (or "pip install --user", if that fails)

*If you run into difficulty with pip*, then try installing moltemplate
into a temporary virtual environment by installing “virtualenv”, and
running these commands:

::

   cd ~/moltemplate
   python -m venv venv     #(or "virtualenv venv" if using python2)
   source venv/bin/activate
   pip install .
   #(now do something useful with moltemplate...)

(You will have to enter “source /moltemplate/venv/bin/activate” into a
terminal beforehand every time you want to run moltemplate.) If all this
fails, then try installing moltemplate by manually updating your $PATH
environment variable. Instructions for doing that are included below.

Installation Method 3 (updating your PATH)
------------------------------------------

Alternatively, you can download the moltemplate files (as explained
above) and edit your *PATH* variable. The *PATH* variable helps the
shell (eg. *BASH*) find the programs you type into the terminal. You
must edit the PATH variable to include the subdirectory where the
moltemplate.sh script is located (eg.
“/moltemplate/moltemplate/scripts/”), as well as the directory
containing the most of the python scripts (eg.
“/moltemplate/moltemplate/”).

If you use the **BASH** shell, typically you would edit your file (or
your , or file) and append the following lines to that file:

::

   export PATH="$PATH:$HOME/moltemplate/moltemplate"
   export PATH="$PATH:$HOME/moltemplate/moltemplate/scripts"

If instead you use the **TCSH** shell, typically you would edit your , ,
or files and append the following lines:

::

   setenv PATH "$PATH:$HOME/moltemplate/moltemplate"
   setenv PATH "$PATH:$HOME/moltemplate/moltemplate/scripts"

*Note: You may need to log out and then log back in again for the
changes to take effect.*

*Warning: Do not install moltemplate this way if you you plan to invoke
moltemplate from within the python environment,*\ **or**\ *if you are
using “vipster”, “cellpack2moltemplate” or other software that has a
moltemplate python dependency. In order to be able to be able to run
"import moltemplate", as these programs do, moltemplate must be
installed using pip (or setuptools).*

WINDOWS installation suggestions
--------------------------------

You can install both moltemplate and LAMMPS in windows, but you will
first need to install the *BASH* shell environment on your computer. I
recommend installing **virtualbox**\ (https://www.virtualbox.org) in
windows together with a (debian-based) linux distribution with a
lightweight desktop such as **xubuntu**\ (https://xubuntu.org).
Alternatively, if you are using Windows 10 or later, you can try
installing the **Windows Subsystem for Linux (WSL)** (*which is text
only*, https://docs.microsoft.com/en-us/windows/wsl) or **Hyper-V**
(https://www.nakivo.com/blog/run-linux-hyper-v/). Otherwise, if you are
using an older version of windows, try installing **CYGWIN**
(https://www.cygwin.com/) instead.

To use LAMMPS and moltemplate, you will also need to install (and learn
how to use) a text editor. (Word, Wordpad, and Notepad will not work.)
If you are *NOT using WSL*, then you can use popular graphical text
editors such as *Atom*, *Sublime*, *Notepad++*, *VSCode*, and the
graphical versions of *emacs* and *vim*. (*Note:* Don’t use these
editors if you are using the WSL environment. Under WSL, these editors
may cause file system corruption. Avoid them for now.
(https://www.reddit.com/r/bashonubuntuonwindows/comments/6bu1d1/since_we_shouldnt_edit_files_stored_in_wsl_with/)
If you *ARE using WSL* then you are restricted to using non-graphical
text editors which you can safely install and run from within the WSL
terminal. These include: *nano*, *ne*, *emacs* (text version), *vim*
(text version), and *jove*.