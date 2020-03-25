# ENet
Please visit the ENet homepage at [http://enet.bespin.org](http://enet.bespin.org) for installation
and usage instructions.

If you obtained this package from github, the quick description on how to build
is:

## Generate the build system.

`mkdir build && cmake ...` builds the static version

or `mkdir build && cmake ... -DBUILD_SHARED` builds the shared version

## Compile and install the library.

`cd build && make && make install`

## Generate the build system. (autoreconf)

autoreconf -vfi

## Compile and install the library. (autoreconf)

./configure && make && make install


