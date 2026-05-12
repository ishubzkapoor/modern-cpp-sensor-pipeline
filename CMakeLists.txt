# CMakeList.txt : Top-level CMake project file, do global configuration
# and include sub-projects here.


cmake_minimum_required(VERSION 3.16)
project(SensorPipeline)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

# ── Main Executable ──
add_executable(sensor_pipeline main.cpp)
#target_compile_options(sensor_pipeline PRIVATE -Wall -Wextra -Werror -Wconversion)
if(MSVC)
    target_compile_options(sensor_pipeline PRIVATE /W4)
else()
    target_compile_options(sensor_pipeline PRIVATE -Wall -Wextra -Werror -Wconversion)
endif()


# ── Threads (Required on Linux) ──
find_package(Threads REQUIRED)
target_link_libraries(sensor_pipeline PRIVATE Threads::Threads)

# ── Sanitizers (Development Only — Module 13) ──
option(ENABLE_ASAN "Enable AddressSanitizer" OFF)
option(ENABLE_TSAN "Enable ThreadSanitizer" OFF)

if(ENABLE_ASAN)
    target_compile_options(sensor_pipeline PRIVATE -fsanitize=address -g)
    target_link_options(sensor_pipeline PRIVATE -fsanitize=address -g)
endif()

if(ENABLE_TSAN)
    target_compile_options(sensor_pipeline PRIVATE -fsanitize=thread -g)
    target_link_options(sensor_pipeline PRIVATE -fsanitize=thread -g)
endif()

# ── Unit Tests (Module 09: GTest) ──
option(BUILD_TESTS "Build unit tests" ON)
if(BUILD_TESTS)
    enable_testing()
    find_package(GTest REQUIRED)

    add_executable(pipeline_tests test_pipeline.cpp)
    target_link_libraries(pipeline_tests PRIVATE GTest::GTest GTest::Main Threads::Threads)
    add_test(NAME PipelineTests COMMAND pipeline_tests)
endif()
