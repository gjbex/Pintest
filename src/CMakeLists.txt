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

# The following tests simply ensure that the executables run without crashing,
# with exit status 0. They do not check the correctness of the results.

# Serial version
add_test(NAME pintest COMMAND pintest)

# OpenMP version
if(OpenMP_CXX_FOUND)
    add_test(NAME pintest_omp COMMAND pintest_omp)
    set_tests_properties(pintest_omp PROPERTIES ENVIRONMENT "OMP_NUM_THREADS=2")
endif()

# MPI version
if(MPI_CXX_FOUND)
    add_test(NAME pintest_mpi COMMAND ${MPIEXEC_EXECUTABLE} ${MPIEXEC_NUMPROC_FLAG} 2 pintest_mpi)  
endif()

# Hybrid version
if(OpenMP_CXX_FOUND AND MPI_CXX_FOUND)
    add_test(NAME pintest_hybrid COMMAND ${MPIEXEC_EXECUTABLE} ${MPIEXEC_NUMPROC_FLAG} 2 pintest_hybrid)    
    set_tests_properties(pintest_hybrid PROPERTIES ENVIRONMENT "OMP_NUM_THREADS=2")
endif()

# The following tests check that the exit status is 1 when the program is run
# with incorrect arguments.

# Duration should be a positive number
add_test(NAME pintest_wrong_duration_value COMMAND pintest --duration 0)
set_tests_properties(pintest_wrong_duration_value PROPERTIES WILL_FAIL TRUE)

# Cycles should not be negative
add_test(NAME pintest_wrong_cycle_value COMMAND pintest --cycles -1)
set_tests_properties(pintest_wrong_cycle_value PROPERTIES WILL_FAIL TRUE)

# A non-defined option should cause an error as well
add_test(NAME pintest_wrong_option COMMAND pintest --non-defined-option)
set_tests_properties(pintest_wrong_option PROPERTIES WILL_FAIL TRUE)
