cmake_minimum_required(VERSION 3.15)
find_package(gsl-lite 0.35.3 REQUIRED)
find_package(cuda-api-wrappers REQUIRED)
find_package(CMakeshift 3.7 REQUIRED)

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

if(CMAKE_CUDA_COMPILER_ID MATCHES "NVIDIA")
#    target_compile_definitions(CUNFFT PRIVATE
#                               $<$<COMPILE_LANGUAGE:CUDA>:BOOST_PP_VARIADICS=1>)
    target_compile_options(
      CUNFFT
      PRIVATE
        $<$<COMPILE_LANGUAGE:CUDA>:--gpu-architecture=${CUDA_ARCHITECTURE}>
    )
    target_compile_options(
      CUNFFT
      PRIVATE $<$<COMPILE_LANGUAGE:CUDA>:--gpu-code=${CUDA_CODE}>
    )
endif()

#cmakeshift_target_compile_settings(
#    CUNFFT
#    PRIVATE
#    default
#    diagnostics-pedantic
#  )

target_compile_features(CUNFFT PUBLIC cxx_std_14)
# set_target_properties(CUNFFT PROPERTIES CUDA_STANDARD 14)
set_target_properties(CUNFFT PROPERTIES CUDA_ARCHITECTURES 61)

# library dependencies
target_link_libraries(
  CUNFFT
  PUBLIC
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
