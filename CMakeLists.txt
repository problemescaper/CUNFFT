#test cmake version
cmake_minimum_required(VERSION 3.16)


# Enable C language because of custom
# find NFFT module
project(
  CUNFFT
  VERSION 1.0.0
  LANGUAGES C CXX CUDA
)

# Include target definitions.
add_subdirectory(src)

find_package(CMakeshift 3.7 REQUIRED)
include(CMakeshift/InstallBasicPackageFiles)
cmakeshift_install_basic_package_files(
  CUNFFT
  COMPATIBILITY
  SameMajorVersion
  EXPORT
  CUNFFT
  DEPENDENCIES
  "gsl-lite 0.35.3"
)

