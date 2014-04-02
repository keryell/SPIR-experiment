Experimentations with SPIR
++++++++++++++++++++++++++



The following projects are known to use SPIR
============================================

From http://www.khronos.org/faq/spir

- OpenCL C 1.2 to SPIR compiler (Clang 3.2 patched)
  https://github.com/KhronosGroup/SPIR

- SPIR Tools SPIR module verifier, etc. (empty on 2014/04/01)
  https://github.com/KhronosGroup/SPIR-Tools

- Multicoreware C++AMP compiler
  https://bitbucket.org/multicoreware/cppamp-driver-ng/wiki/Home


Compilation of the OpenCL C 1.2 to SPIR 1.2 compiler
====================================================

.. code-block:: bash

  # Contains the Clang version that can produce SPIR
  git clone git@github.com:KhronosGroup/SPIR.git Clang3.2-SPIR
  # Contains the headers/opencl_spir.h required to compile
  git clone git@github.com:KhronosGroup/SPIR-Tools.git
  # To optimize the bandwidth since I have already a local LLVM git
  git clone --local ../../LLVM/llvm
  cd llvm
  git remote add llvm http://llvm.org/git/llvm.git
  git fetch llvm
  git checkout release_32
  cd tools
  ln -s ../../Clang3.2-SPIR clang
  cd clang
  git checkout spir_12
  cd ../../..
  mkdir build
  cd build
  # Note that this use clang by default if available.
  # Use for example "CC=gcc-4.8 ../llvm/configure" to select another compiler
  ../llvm/configure
  make -j4
  make install
  sudo make install
  cd tools/clang
  make test

For the tests I get: ::

  ********************
  Testing Time: 34.97s
  ********************
  Failing Tests (6):
      Clang :: PCH/cxx-typeid.cpp
      Clang :: Parser/attributes.c
      Clang :: Parser/opencl-image-access.cl
      Clang :: Parser/opencl-storage-class.cl
      Clang :: SemaOpenCL/address-spaces.cl
      Clang :: SemaOpenCL/extension-fp64.cl


Using the OpenCL C 1.2 to SPIR 1.2 compiler
===========================================

From the compiler `README.md <https://github.com/KhronosGroup/SPIR>`_

.. code-block:: bash

  clang -cc1 -emit-llvm-bc -triple <triple> <OpenCL compile options> -cl-spir-compile-options "<OpenCL compile options>" -include <opencl_spir.h> -o <output> <input>

- ``<triple>``: for 32-bit SPIR use ``spir-unknown-unknown``, for 64-bit
  SPIR use ``spir64-unknown-unknown``;

- ``<OpenCL compile options>`` appears twice, once as real compile
  options, and ``-cl-spir-compile-options "<OpenCL compile options>"`` to
  include the options in the SPIR ``!opencl.compiler.options`` metadata.

Without ``-o <output>`` it generates a ``<input>.bc`` file in the same
directory as the input file.

Example with some OpenCV code:

.. code-block:: bash

  clang -cc1 -emit-llvm-bc -triple spir64-unknown-unknown -DVT=float -DT=float -O3 -include SPIR-Tools/headers/opencl_spir.h -o arithm_pow.bc opencv/modules/ocl/src/opencl/arithm_pow.cl
  llvm-dis arithm_pow.bc
  less arithm_pow.ll


Improvements to SPIR repository
===============================

On http://www.khronos.org/spir the link in "The SPIR Verifier from Khronos
Github (LLVM pass checks SPIR validity)" point to
https://github.com/KhronosGroup/SPIR/tree/spir_12 which is indeed the
OpenCL-to-SPIR Clang-3.2 compiler.

The FAQ http://www.khronos.org/faq/spir correcly directs it to
https://github.com/KhronosGroup/SPIR-Tools instead.


The https://github.com/KhronosGroup/SPIR that hosts the OpenCL-to-SPIR
Clang-3.2 compiler should be renamed to something more explicit than
an elusive "SPIR".


..
  # Automatic section numbering:
.. sectnum::

..
  # Some Emacs stuff:
  ### Local Variables:
  ### mode: rst,flyspell
  ### ispell-local-dictionary: "american"
  ### End:
