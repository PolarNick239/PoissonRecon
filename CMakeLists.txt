cmake_minimum_required(VERSION 2.8.9)

set (PROJECT PoissonRecon)

set (SOURCES
	Src/PlyFile.cpp
)

set(PR_TARGET PoissonRecon)
set(SR_TARGET SSDRecon)
set(ST_TARGET SurfaceTrimmer)
set(EH_TARGET EDTInHeat)
set(IS_TARGET ImageStitching)
set(AV_TARGET AdaptiveTreeVisualization)

set(PR_SOURCE ${SOURCES} Src/PoissonRecon.cpp)
set(SR_SOURCE ${SOURCES} Src/SSDRecon.cpp)
set(ST_SOURCE ${SOURCES} Src/SurfaceTrimmer.cpp)
set(EH_SOURCE ${SOURCES} Src/EDTInHeat.cpp)
set(IS_SOURCE Src/ImageStitching.cpp)
set(AV_SOURCE ${SOURCES} Src/AdaptiveTreeVisualization.cpp)

if("${CMAKE_CXX_COMPILER_ID}" STREQUAL "GNU")
	find_package(OpenMP REQUIRED)
	set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} ${OpenMP_CXX_FLAGS} -Wno-deprecated -Wno-write-strings -std=c++11")
	set(LIBRARIES ${LIBRARIES} ${OPENMP_LIBRARIES})
elseif("${CMAKE_CXX_COMPILER_ID}" STREQUAL "Clang")
	set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wno-deprecated -Wno-write-strings -std=c++11 -Wno-invalid-offsetof")
endif()

find_package(ZLIB REQUIRED)
find_package(PNG REQUIRED)
find_package(JPEG REQUIRED)

set(LIBRARIES ${LIBRARIES} ${ZLIB_LIBRARIES} ${PNG_LIBRARIES} ${JPEG_LIBRARIES})

message("type: ${CMAKE_BUILD_TYPE}")
if(CMAKE_BUILD_TYPE MATCHES "Debug" OR CMAKE_BUILD_TYPE MATCHES "DEBUG")
	set(CMAKE_CXX_FLAGS_DEBUG "${CMAKE_CXX_FLAGS_DEBUG} -DDEBUG -g3")
else()
	set(CMAKE_CXX_FLAGS_RELEASE "${CMAKE_CXX_FLAGS_RELEASE} -O3 -DRELEASE -funroll-loops -ffast-math -g")
	set(CMAKE_STATIC_LINKER_FLAGS "${CMAKE_STATIC_LINKER_FLAGS} -O3 -g")
endif()

add_executable(${PR_TARGET} ${PR_SOURCE})
target_link_libraries(${PR_TARGET} ${LIBRARIES})

add_executable(${SR_TARGET} ${SR_SOURCE})
target_link_libraries(${SR_TARGET} ${LIBRARIES})

add_executable(${ST_TARGET} ${ST_SOURCE})
target_link_libraries(${ST_TARGET} ${LIBRARIES})

add_executable(${EH_TARGET} ${EH_SOURCE})
target_link_libraries(${EH_TARGET} ${LIBRARIES})

add_executable(${IS_TARGET} ${IS_SOURCE})
target_link_libraries(${IS_TARGET} ${LIBRARIES})

add_executable(${AV_TARGET} ${AV_SOURCE})
target_link_libraries(${AV_TARGET} ${LIBRARIES})
