cmake_minimum_required(VERSION 3.14)
project(hw1 LANGUAGES C)

find_package(BLAS REQUIRED)

# On Cori
# 2.3 GHz * 8 vector width * 2 flops for FMA = 36.8 GF/s
set(MAX_SPEED 36.8 CACHE STRING "The max speed of the CPU in GF/s")

foreach (BENCHMARK IN ITEMS naive blocked blas team)
    add_executable(benchmark-${BENCHMARK}
            benchmark.c
            dgemm-${BENCHMARK}.c)

    target_link_libraries(benchmark-${BENCHMARK} PRIVATE ${BLAS_LIBRARIES})
    target_compile_features(benchmark-${BENCHMARK} PRIVATE c_std_11 c_restrict)
    target_compile_options(benchmark-${BENCHMARK} PRIVATE -Wall -pedantic -march=haswell)
    target_compile_definitions(benchmark-${BENCHMARK} PRIVATE MAX_SPEED=${MAX_SPEED})
endforeach ()