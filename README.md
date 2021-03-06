## MaxMindDB Nuget Package

This project builds a MaxMindDB Nuget package with static MaxMindDB
libraries and header files  for `Win32`/`x64` platforms and
`Debug`/`Release` configurations.

Visit MaxMindDB project for MaxMindDB library documentation:

https://github.com/maxmind/libmaxminddb/

## Package Configuration

This package contains only static libraries for all platforms
and configurations listed above. There are no dynamic libraries
included.

The MaxMindDB static library appropriate for the platform and
configuration selected in a Visual Studio solution is explicitly
referenced within this package and will appear within the solution
folder tree after the package is installed. The solution may need
to be reloaded to make the library file visible. This library may
be moved into any solution folder after the installation.

Note that the MaxMindDB library path in this package is valid only
for build configurations named `Debug` and `Release` and will not
work for any other configuration names. Do not install this package
for projects with configurations other than `Debug` and `Release`.

See `StoneSteps.MaxMindDB.Static.props` and `StoneSteps.MaxMindDB.Static.targets`
for specific package configuration details and file locations.

## Package Version

### Package Revision

Nuget packages lack package revision and in order to repackage
the same upstream software version, such as MaxMindDB v1.6.0, the
4th component of the Nuget version is used to track the Nuget
package revision.

Nuget package revision is injected outside of the Nuget package
configuration, during the package build process, and is not present
in the package specification file.

Specifically, `nuget.exe` is invoked with `-Version=1.6.0.123` to
build a package with the revision `123`.

### Version Locations

MaxMindDB version is located in a few places in this repository and
needs to be changed in all of them for a new version of MaxMindDB.

  * nuget/StoneSteps.MaxMindDB.Static.nuspec (`version`)
  * devops/make-package.bat (`PKG_VER`, `PKG_REV`, `SRC_TAG`)
  * .github/workflows/nuget-mmdb-1.6.0.yml (`name`, `PKG_VER`,
    `PKG_REV`, `SRC_TAG`)

In the GitHub workflow YAML, `PKG_REV` must be reset to `1` (one)
every time MaxMindDB version is changed. The workflow file must
be renamed with the new version in the name. This is necessary
because GitHub maintains build numbers per workflow file name.

For local builds package revision is supplied on the command line
and should be specified as `1` (one) for a new version of MaxMindDB.

### GitHub Build Number

Build number within the GitHub workflow YAML is maintained in an
unconventional way because of the lack of build maturity management
between GitHub and Nuget.

For example, using a build management system, such as Artifactory,
every build would generate a Nuget package with the same version
and package revision for the upcoming release and build numbers
would be tracked within the build management system. A build that
was successfully tested would be promoted to the production Nuget
repository without generating a new build.

Without a build management system, the GitHub workflow in this
repository uses the pre-release version as a surrogate build
number for builds that do not publish packages to nuget.org, so
these builds can be downloaded and tested before the final build
is made and published to [nuget.org][]. This approach is not
recommended for robust production environments because even
though the final published package is built from the exact same
source, the build process may still potentially introduce some
unknowns into the final package (e.g. build VM was updated).

## Building Package Locally

You can build a Nuget package locally with `make-package.bat`
located in `devops`. This script expects VS2019 Community Edition
installed in the default location. If you have other edition of
Visual Studio, edit the file to use the correct path to the
`vcvarsall.bat` file.

Run `make-package.bat` from the repository root directory with
a package revision as the first argument. There is no provision
to manage build numbers from the command line and other tools
should be used for this (e.g. Artifactory).

## Sample Application

A Visual Studio project is included in this repository under
`sample-mmdblookup` to test the Nuget package built by this project.
Parts of this application are copied from `bin/mmdblookup.c` and
adapted to build as a simple C++ project for testing.

The original C source of this sample application is subject to
MaxMind's copyright and terms of use.

https://github.com/maxmind/libmaxminddb/blob/main/bin/mmdblookup.c

This application does not do anything useful and merely calls
MaxMindDB functions to verify that the package is installed
properly for all platforms and configurations.

[nuget.org]: https://www.nuget.org/packages/StoneSteps.MaxMindDB.Static/
