# Source file
set(SOURCE_FILE pintest.cpp)

# Targets

# Serial version
add_executable(pintest ${SOURCE_FILE})
target_include_directories(pintest PRIVATE Boost::Boost)
target_link_libraries(pintest PRIVATE Boost::program_options)
install(TARGETS pintest RUNTIME DESTINATION bin)

# OpenMP version
if(OpenMP_CXX_FOUND)
    add_executable(pintest_omp ${SOURCE_FILE})
    target_include_directories(pintest_omp PRIVATE Boost::Boost)
    target_link_libraries(pintest_omp PRIVATE Boost::program_options  OpenMP::OpenMP_CXX)
    target_compile_options(pintest_omp PRIVATE ${OMP_FLAGS})
endif()

# MPI version
if (MPI_CXX_FOUND)
    include_directories(${MPI_CXX_INCLUDE_DIRS})

    add_executable(pintest_mpi ${SOURCE_FILE})
    target_compile_options(pintest_mpi PRIVATE ${MPI_FLAGS})
    target_include_directories(pintest_mpi PRIVATE MPI::MPI_CXX Boost::Boost)
    target_link_libraries(pintest_mpi PRIVATE MPI::MPI_CXX Boost::program_options)
    install(TARGETS pintest_mpi RUNTIME DESTINATION bin)
endif()

# Hybrid version
if (OpenMP_CXX_FOUND AND MPI_CXX_FOUND)
        add_executable(pintest_hybrid ${SOURCE_FILE})
        target_compile_options(pintest_hybrid PRIVATE ${OMP_FLAGS})
        target_include_directories(pintest_hybrid PRIVATE MPI::MPI_CXX Boost::Boost)
        target_link_libraries(pintest_hybrid PRIVATE MPI::MPI_CXX Boost::program_options OpenMP::OpenMP_CXX)
        install(TARGETS pintest_hybrid RUNTIME DESTINATION bin)
endif()

# Tests
add_test(NAME pintest COMMAND pintest)
if(OpenMP_CXX_FOUND)
    add_test(NAME pintest_omp COMMAND pintest_omp)
    set_tests_properties(pintest_omp PROPERTIES ENVIRONMENT "OMP_NUM_THREADS=2")
endif()
if(MPI_CXX_FOUND)
    add_test(NAME pintest_mpi COMMAND ${MPIEXEC_EXECUTABLE} ${MPIEXEC_NUMPROC_FLAG} 2 pintest_mpi)  
endif()
if(OpenMP_CXX_FOUND AND MPI_CXX_FOUND)
    add_test(NAME pintest_hybrid COMMAND ${MPIEXEC_EXECUTABLE} ${MPIEXEC_NUMPROC_FLAG} 2 pintest_hybrid)    
    set_tests_properties(pintest_hybrid PROPERTIES ENVIRONMENT "OMP_NUM_THREADS=2")
endif()
