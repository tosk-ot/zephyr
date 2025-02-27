.. _installation_linux:

Install Linux Host Dependencies
###############################

.. important::

   Go back to the main :ref:`getting_started` when you're done here.

Documentation is available for these Linux distributions:

* Ubuntu 16.04 LTS or 18.04 LTS 64-bit
* Fedora 28 64-bit
* Clear Linux
* Arch Linux

Update Your Operating System
****************************

Ensure your host system is up to date.

Ubuntu:

.. code-block:: console

   sudo apt-get update
   sudo apt-get upgrade

Fedora:

.. code-block:: console

   sudo dnf upgrade

Clear Linux:

.. code-block:: console

   sudo swupd update

Arch Linux:

.. code-block:: console

   sudo pacman -Syu

.. _linux_requirements:

Install Requirements and Dependencies
*************************************

.. NOTE FOR DOCS AUTHORS: DO NOT PUT DOCUMENTATION BUILD DEPENDENCIES HERE.

   This section is for dependencies to build Zephyr binaries, *NOT* this
   documentation. If you need to add a dependency only required for building
   the docs, add it to doc/README.rst. (This change was made following the
   introduction of LaTeX->PDF support for the docs, as the texlive footprint is
   massive and not needed by users not building PDF documentation.)

Note that both Ninja and Make are installed with these instructions; you only
need one.

Ubuntu:

.. code-block:: console

   sudo apt-get install --no-install-recommends git cmake ninja-build gperf \
     ccache dfu-util device-tree-compiler wget \
     python3-pip python3-setuptools python3-tk python3-wheel xz-utils file \
     make gcc gcc-multilib

Fedora:

.. code-block:: console


   sudo dnf group install "Development Tools" "C Development Tools and Libraries"
   dnf install git cmake ninja-build gperf ccache dfu-util dtc wget \
     python3-pip python3-tkinter xz file glibc-devel.i686 libstdc++-devel.i686

Clear Linux:

.. code-block:: console

   sudo swupd bundle-add c-basic dev-utils dfu-util dtc \
     os-core-dev python-basic python3-basic python3-tcl

The Clear Linux focus is on *native* performance and security and not
cross-compilation. For that reason it uniquely exports by default to the
:ref:`environment <env_vars>` of all users a list of compiler and linker
flags. Zephyr's CMake build system will either warn or fail because of
these. To clear the C/C++ flags among these and fix the Zephyr build, run
the following command as root then log out and back in:

.. code-block:: console

   # echo 'unset CFLAGS CXXFLAGS' >> /etc/profile.d/unset_cflags.sh

Note this command unsets the C/C++ flags for *all users on the
system*. Each Linux distribution has a unique, relatively complex and
potentially evolving sequence of bash initialization files sourcing each
other and Clear Linux is no exception. If you need a more flexible
solution, start by looking at the logic in
``/usr/share/defaults/etc/profile``.

Arch Linux:

.. code-block:: console

   sudo pacman -S git cmake ninja gperf ccache dfu-util dtc wget \
       python-pip python-setuptools python-wheel tk xz file make

**CMake version 3.13.1 or higher is required**. Check what version you have by
using ``cmake --version``. If you have an older version, there are several ways
of obtaining a more recent one:

* Use ``pip``:

  .. code-block:: console

     pip3 install --user cmake

* Download and install from the pre-built binaries provided by the CMake
  project itself in the `CMake Downloads`_ page.
  For example, to install version 3.13.1 in :file:`~/bin/cmake`:

  .. code-block:: console

     mkdir $HOME/bin/cmake && cd $HOME/bin/cmake
     wget https://github.com/Kitware/CMake/releases/download/v3.13.1/cmake-3.13.1-Linux-x86_64.sh
     yes | sh cmake-3.13.1-Linux-x86_64.sh | cat
     echo "export PATH=$PWD/cmake-3.13.1-Linux-x86_64/bin:\$PATH" >> $HOME/.zephyrrc

* Check your distribution's beta or unstable release package library for an
  update.

You might also want to uninstall the CMake provided by your package manager to
avoid conflicts.

.. _zephyr_sdk:

Install the Zephyr Software Development Kit (SDK)
*************************************************

Use of the Zephyr SDK is optional, but recommended. Some of the dependencies
installed above are only needed for installing the SDK.

Zephyr's :abbr:`SDK (Software Development Kit)` contains all necessary tools to
build Zephyr on all supported architectures. Additionally, it includes host
tools such as custom QEMU binaries and a host compiler. The SDK supports the
following target architectures:

* :abbr:`X86 (Intel Architecture 32 bits)`

* :abbr:`X86 IAMCU ABI (Intel Architecture 32 bits IAMCU ABI)`

* :abbr:`Arm (Advanced RISC Machine)`

* :abbr:`ARC (Argonaut RISC Core)`

* :abbr:`Nios II`

* :abbr:`Xtensa`

* :abbr:`RISC-V`

Follow these steps to install the Zephyr SDK:

#. Download the latest SDK as a self-extracting installation binary:

   .. code-block:: console

      wget https://github.com/zephyrproject-rtos/sdk-ng/releases/download/v0.10.0/zephyr-sdk-0.10.0-setup.run

   (You can change *0.10.0* to another version if needed; the `Zephyr
   Downloads`_ page contains all available SDK releases.)

#. Run the installation binary, installing the SDK at
   :file:`~/zephyr-sdk-0.10.0`:

   .. code-block:: console

      cd <sdk download directory>
      ./zephyr-sdk-0.10.0-setup.run -- -d ~/zephyr-sdk-0.10.0

   You can pick another directory if you want. If this fails, make sure
   Zephyr's dependencies were installed as described in `Install Requirements
   and Dependencies`_.

#. Set these :ref:`environment variables <env_vars>`:

   - set :envvar:`ZEPHYR_TOOLCHAIN_VARIANT` to ``zephyr``
   - set :envvar:`ZEPHYR_SDK_INSTALL_DIR` to :file:`$HOME/zephyr-sdk-0.10.0`
     (or wherever you chose to install the SDK)

If you ever want to uninstall the SDK, just remove the directory where you
installed it and unset these environment variables.

.. _sdkless_builds:

Building on Linux without the Zephyr SDK
****************************************

The Zephyr SDK is provided for convenience and ease of use. It provides
toolchains for all Zephyr target architectures, and does not require any extra
flags when building applications or running tests. In addition to
cross-compilers, the Zephyr SDK also provides prebuilt host tools. It is,
however, possible to build without the SDK's toolchain by using another
toolchain as as described in the main :ref:`getting_started` document.

As already noted above, the SDK also includes prebuilt host tools.  To use the
SDK's prebuilt host tools with a toolchain from another source, keep the
:envvar:`ZEPHYR_SDK_INSTALL_DIR` environment variable set to the Zephyr SDK
installation directory. To build without the Zephyr SDK's prebuilt host tools,
the :envvar:`ZEPHYR_SDK_INSTALL_DIR` environment variable must be unset before
you run ``source zephyr-env.sh`` later on in the Getting Started Guide.

To make sure this variable is unset, run:

.. code-block:: console

   unset ZEPHYR_SDK_INSTALL_DIR

.. _Zephyr Downloads: https://www.zephyrproject.org/developers/#downloads
.. _CMake Downloads: https://cmake.org/download
