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

set(fitpack_SRC
    fitpack/bispev.f
    fitpack/curfit.f
    fitpack/fpback.f
    fitpack/fpbisp.f
    fitpack/fpchec.f
    fitpack/fpcurf.f
    fitpack/fpbspl.f
    fitpack/fpdisc.f
    fitpack/fpgivs.f
    fitpack/fpknot.f
    fitpack/fporde.f
    fitpack/fprank.f
    fitpack/fprati.f
    fitpack/fprota.f
    fitpack/fpsurf.f
    fitpack/parder.f
    fitpack/splder.f
    fitpack/splev.f
    fitpack/surfit.f
    fitpack/regrid.f
)

add_library(fitpack STATIC 
            ${fitpack_SRC}
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

include_directories(
  ${CMAKE_SOURCE_DIR}/include
)

set(fitpackpp_SRC
	src/BSplineCurve.cpp
    src/BSplineSurface.cpp)

add_library(fitpackpp STATIC
	        ${fitpackpp_SRC})

target_link_libraries(fitpackpp fitpack)
