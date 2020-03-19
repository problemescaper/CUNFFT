
# src inc header 
INCLUDE_DIRECTORIES(${PROJ_INCLUDE_DIR})
#CUDA_INCLUDE_DIRECTORIES("/usr/local/cuda-5.5/samples/common/inc/")

# check included cuda header can be found
set(CUFFT_INC_DIR "${CUFFT_INC_DIR}-NOTFOUND")
# cufft.h
FIND_PATH(CUFFT_INC_DIR 
	NAMES cufft.h 
	PATHS  
		${CUDA_SDK_ROOT_DIR}
	 	${CUDA_SDK_ROOT_DIR}/C/common/inc
		${CUDA_SDK_ROOT_DIR}/CUDALibraries/common/inc
    	DOC "cufft header location."
     )

if(CUFFT_INC_DIR)
	message(STATUS "CHECK PATH cufft.h: ${CUFFT_INC_DIR}/cufft.h")
else(CUFFT_INC_DIR)
	EXECUTE_PROCESS(COMMAND locate cufft.h OUTPUT_VARIABLE FILE_LIST )
	FIND_PATH(CUFFT_INC_DIR 
	NAMES cufft.h 
	PATHS  
		${FILE_LIST}/..
    	DOC "cufft header location."
     )
	message(STATUS "CHECK PATH cufft.h: ${CUFFT_INC_DIR}/cufft.h\n")
endif(CUFFT_INC_DIR)



ADD_LIBRARY(${PROJ_NAME} 
	SHARED 
	"./cunfft_wrapper.cu"
	"./cunfft_kernel.cu"
	"./cunfft.cu"
	"./c_util.cu"
	"./cuda_util.cu"
	)

message(STATUS "PRJOECT NAME = ${PROJ_NAME}")
#target_compile_options(${PROJ_NAME} PRIVATE -arch sm_61)

target_link_libraries(${PROJ_NAME} nfft3 )

#Linking with libcufft (expected to be in 

SET_TARGET_PROPERTIES(${PROJ_NAME} 
	PROPERTIES
	VERSION ${cunfft_VERSION}
  	SOVERSION ${cunfft_VERSION_MAJOR}.${cunfft_VERSION_MINOR}
)

#create executable demo

#ADD_EXECUTABLE(${PROJ_EXAMPLE_FILE} ${PROJ_EXAMPLE_DIR}/${PROJ_EXAMPLE_FILE}.cpp)
#TARGET_LINK_LIBRARIES(${PROJ_EXAMPLE_FILE} ${PROJ_NAME})




################################################################
## installation of library
################################################################

INSTALL(PROGRAMS ${CMAKE_CURRENT_BINARY_DIR}/${PROJ_EXAMPLE_FILE} DESTINATION ${PROJ_EXAMPLE_DIR})
INSTALL(TARGETS ${PROJ_NAME} LIBRARY DESTINATION ${PROJ_LIBRARY_DIR})





