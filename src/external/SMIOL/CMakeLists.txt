
cmake_minimum_required(VERSION 3.10)

find_package(MPI REQUIRED COMPONENTS C Fortran)
find_package(NetCDF REQUIRED COMPONENTS Fortran C)
find_package(PnetCDF REQUIRED COMPONENTS Fortran C)

# Specify the library source files
set(SMIOL_C_SOURCES smiol.c smiol_utils.c)
set(SMIOLF_SOURCES smiolf.F90)

# Create the C library
add_library(smiol ${SMIOL_C_SOURCES})
add_library(${PROJECT_NAME}::external::smiol ALIAS smiol)
target_compile_definitions(smiol PRIVATE SMIOL_PNETCDF SINGLE_PRECISION)

# Create the Fortran library
add_library(smiolf ${SMIOLF_SOURCES})
enable_language(Fortran)
mpas_fortran_target(smiolf)
add_library(${PROJECT_NAME}::external::smiolf ALIAS smiolf)
target_compile_definitions(smiolf PRIVATE SMIOL_PNETCDF )
# fortran lib requires the c lib
target_link_libraries(smiolf PUBLIC smiol)
target_include_directories(smiol PUBLIC $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}>)

install(TARGETS smiol EXPORT ${PROJECT_NAME}ExportsExternal
        ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
        LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR})
install(TARGETS smiolf EXPORT ${PROJECT_NAME}ExportsExternal
        ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
        LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR})
