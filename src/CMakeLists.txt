cmake_minimum_required(VERSION 3.15)
find_package(gsl-lite 0.35.3 REQUIRED)
find_package(cuda-api-wrappers REQUIRED)

set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH}
                      "${PROJECT_SOURCE_DIR}/cmake/modules/")

find_package(NFFT REQUIRED)

# targets
add_library(CUNFFT 
	"./cunfft_wrapper.cu"
	"./cunfft_kernel.cu"
	"./cunfft.cu"
	"./c_util.cu"
	"./cuda_util.cu"
)

# library properties
target_include_directories(
  CUNFFT
  PUBLIC
  "$<BUILD_INTERFACE:${PROJECT_SOURCE_DIR}/include>"
)

target_compile_features(CUNFFT INTERFACE cxx_std_14)
# set_target_properties(CUNFFT PROPERTIES CUDA_STANDARD 14)

# library dependencies
target_link_libraries(
  CUNFFT
  INTERFACE
    gsl::gsl-lite-v1
    nfft
    cuda-api-wrappers::cuda-api-wrappers
)

# installation: public header files, target
install(
  DIRECTORY
    "${PROJECT_SOURCE_DIR}/include/" # the trailing '/' is significant
  TYPE INCLUDE
)
install(TARGETS CUNFFT EXPORT CUNFFT ARCHIVE INCLUDES)
