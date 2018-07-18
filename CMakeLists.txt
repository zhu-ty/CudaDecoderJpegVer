# CMake entry point.
# Note that this CMakeLists.txt in fully independent from the one in the parent directory
# (since that's the goal of this tutorial !)

# For an introduction to CMake, see
# http://www.cmake.org/cmake/help/cmake_tutorial.html (at least steps 1 and 2)

cmake_minimum_required (VERSION 3.10)
set(name "NvDec")
project ( ${name} )

# cmake module
set(CMAKE_MODULE_PATH ${PROJECT_SOURCE_DIR}/cmake)



# c++ version
set (CMAKE_CXX_STANDARD 11)

set (WIN_LIB_PATH "E:/libs")

# #opencv 
# if ( WIN32 )
# 	set(OpenCV_DIR ${WIN_LIB_PATH}/OpenCV/x64/vc14/lib)
# 	set(OpenCV_CONFIG_PATH ${WIN_LIB_PATH}/OpenCV)
# 	#set(WIN_HEADER_PATH "${PROJECT_SOURCE_DIR}/winheadfile")
#     #include_directories(${WIN_HEADER_PATH})
# endif()

# # OpenCV
# find_package(OpenCV REQUIRED)
# include_directories(${OpenCV_INCLUDE_DIRS})

# # Eigen3
# if (WIN32)
# 	set(EIGEN_INCLUDE_DIR ${WIN_LIB_PATH}/Eigen/include/eigen3)
# elseif ( UNIX )
# 		set(EIGEN_INCLUDE_DIR "/usr/local/include/eigen3")
# endif()
# include_directories(${EIGEN_INCLUDE_DIR})

# # opengl
# find_package(OpenGL REQUIRED)
# # Ask the user to give the paths to GLFW, GLEW and GLM.
# # On Linux this can be found automatically with pkg-config; 
# # see http://stackoverflow.com/questions/6889264/cmake-flags-for-opengl-using-glew-and-glfw, for instance.
# if ( WIN32 )
# 	set(GLFW_INCLUDE_PATH ${WIN_LIB_PATH}/OpenGL/include/GLFW)
# 	set(GLFW_LIBRARY ${WIN_LIB_PATH}/OpenGL/lib/glfw3.lib )
# 	set(GLEW_INCLUDE_PATH ${WIN_LIB_PATH}/OpenGL/include)
# 	set(GLEW_LIBRARY_DEBUG ${WIN_LIB_PATH}/OpenGL/lib/glew32d.lib)
# 	set(GLEW_LIBRARY_RELEASE ${WIN_LIB_PATH}/OpenGL/lib/glew32.lib)
# 	set(GLM_INCLUDE_PATH ${WIN_LIB_PATH}/OpenGL/include)

# 	# add_definitions(
# 	# 	#-DTW_STATIC
# 	# 	#-DTW_NO_LIB_PRAGMA
# 	# 	#-DTW_NO_DIRECT3D
# 	# 	-DGLEW_STATIC
# 	# 	#-D_CRT_SECURE_NO_WARNINGS
# 	# )
	
# #	set(CMAKE_CXX_FLAGS_RELEASE "${CMAKE_CXX_FLAGS_RELEASE} /MT")
# endif()

#cuda
find_package(CUDA)
set(CUDA_NVCC_FLAGS
    ${CUDA_NVCC_FLAGS}
#    -O3 
	-gencode=arch=compute_61,code=\"sm_61,compute_61\"
	)


#external
set(EXTERNAL_OPENGL_DIR ${PROJECT_SOURCE_DIR}/External/GL)
set(EXTERNAL_OPENGL_LIB_DEBUG
	${EXTERNAL_OPENGL_DIR}/lib/x64/freeglut_staticd.lib
	${EXTERNAL_OPENGL_DIR}/lib/x64/freeglutd.lib
	${EXTERNAL_OPENGL_DIR}/lib/x64/glew32.lib
	${EXTERNAL_OPENGL_DIR}/lib/x64/glew32s.lib
)

set(EXTERNAL_OPENGL_LIB_RELEASE
	${EXTERNAL_OPENGL_DIR}/lib/x64/freeglut_static.lib
	${EXTERNAL_OPENGL_DIR}/lib/x64/freeglut.lib
	${EXTERNAL_OPENGL_DIR}/lib/x64/glew32.lib
	${EXTERNAL_OPENGL_DIR}/lib/x64/glew32s.lib
)

set(EXTERNAL_FFMPEG_FIR ${PROJECT_SOURCE_DIR}/External/FFmpeg)
set(EXTERNAL_FFMPEG_LIB
	${EXTERNAL_FFMPEG_FIR}/lib/x64/avcodec.lib
	${EXTERNAL_FFMPEG_FIR}/lib/x64/avformat.lib
	${EXTERNAL_FFMPEG_FIR}/lib/x64/avutil.lib
	${EXTERNAL_FFMPEG_FIR}/lib/x64/swresample.lib
)



# NvDecoder
set(NV_DECODER_DIR ${PROJECT_SOURCE_DIR}/NvCodec/NvDecoder)
set(NV_DECODER_HEADER_FILES
	${NV_DECODER_DIR}/cuviddec.h
	${NV_DECODER_DIR}/nvcuvid.h
	${NV_DECODER_DIR}/NvDecoder.h
)
set(NV_DECODER_SOURCE_FILES
	${NV_DECODER_DIR}/NvDecoder.cpp
)
set(NV_DECODER_LIB
	${PROJECT_SOURCE_DIR}/NvCodec/Lib/x64/nvcuvid.lib
)

set(UTIL_DIR Utils)
set(UTIL_SOURCE
	${UTIL_DIR}/BitDepth.cu
	${UTIL_DIR}/ColorSpace.cu
	${UTIL_DIR}/Resize.cu
)


set(MY_HEADER_FILES
	# C++ header file
	AppDecUtils.h
)
set(MY_SOURCE_FILES 
	# C++ code
	AppDec.cpp
)



include_directories(
	${EXTERNAL_OPENGL_DIR}
	${NV_DECODER_DIR}
	${UTIL_DIR}
	${PROJECT_SOURCE_DIR}	
	${EXTERNAL_FFMPEG_FIR}/include
)

cuda_add_executable(${name}
	${NV_DECODER_HEADER_FILES}
	${NV_DECODER_SOURCE_FILES}
	${UTIL_SOURCE}
	${MY_HEADER_FILES}
	${MY_SOURCE_FILES}
	)


target_link_libraries(${name}

	${CUDA_LIBRARY}

	${CUDA_TOOLKIT_ROOT_DIR}/lib/x64/cuda.lib

	${NV_DECODER_LIB}
	${EXTERNAL_FFMPEG_LIB}
	debug  ${EXTERNAL_OPENGL_LIB_DEBUG}
	optimized  ${EXTERNAL_OPENGL_LIB_RELEASE}
	)
