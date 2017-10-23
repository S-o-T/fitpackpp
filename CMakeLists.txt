cmake_minimum_required(VERSION 3.0.0)

# FITPACK FORTRAN code
enable_language(Fortran)

if(${CMAKE_Fortran_COMPILER} MATCHES "ifort.*")
    set(CMAKE_Fortran_FLAGS_RELEASE "${CMAKE_Fortran_FLAGS} -fltconsistency")
    set(CMAKE_Fortran_FLAGS_DEBUG   "${CMAKE_Fortran_FLAGS} -fltconsistency")
endif()

if (CMAKE_COMPILER_IS_GNUCXX)
  set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11")
endif()

file(GLOB fitpack_SRC fitpack/*.f)

add_library(fitpack 
  STATIC ${fitpack_SRC}
)

enable_language(CXX)

# C++ interface
include(FortranCInterface)
FortranCInterface_HEADER(FCMangle.h
  MACRO_NAMESPACE "FC_"
  SYMBOL_NAMESPACE ""
  SYMBOLS curfit splev splder surfit bispev parder regrid)
FortranCInterface_VERIFY(CXX)

# For FCMangle.h
include_directories(${CMAKE_CURRENT_BINARY_DIR})

include_directories(${CMAKE_SOURCE_DIR}/include)

file(GLOB fitpackpp_SRC src/*.cpp)

add_library(fitpackpp 
  STATIC ${fitpackpp_SRC}
)

target_link_libraries(fitpackpp fitpack)

install(TARGETS fitpack fitpackpp
  LIBRARY DESTINATION lib
  ARCHIVE DESTINATION lib
)

file(GLOB fitpackpp_INC ${CMAKE_SOURCE_DIR}/include/fitpackpp/*.h)

install(FILES ${fitpackpp_INC}
  DESTINATION include/fitpackpp
)
