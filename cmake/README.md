## CMake

### Quick definitions:  
**set(MY_VAR "hello world")**: Create a variable `MY_VAR` with the value `hello world`.

**include_directories(../include)**: Include header files of a project so the compiler can refer to them with `<file.h>` instead of relative path `"../include/file.h"`. This is equivalent to the `-I` flag.

**target_link_libraries(${YOUR_EXECUTABLE} gtest gtest_main)**: Link `libgtest.a` and `libgtest_main.a` to the executable `${YOUR_EXECUTABLE}`. Also this can be used with the `l` flag (e.g. `target_link_libraries(${YOUR_EXECUTABLE} -lpthread)`).

**link_libraries(gtest gtest_main)**: Similar to target_link_libraries(${YOUR_EXECUTBALE} gtest gtest_main) but instead of linking the libraries for a single target, it will be applied to all the ones declared in the CMakeLists.txt.

**add_subdirectory(project)**: Links the main CMakeLists.txt to another one inside `project/` directory. This will automatically build the added subdirectory CMakeLists.txt.

**add_library(${PROJECT_LIB} ${CPP_FILES})**: Create a library `.a` target.

**add_exectuable(${PROJECT_EXEC} ${CPP_FILES})**: Create an executable target.

**file(GLOB_RECURSE MY_VAR src/*.h src/*.cpp)**: Store all the `.cpp` files in `src/` into the variable `MY_VAR`.

**file(COPY "file1.txt" "file2.txt" DESTINATION "to_dir/")**: Copy `file1.txt` and `file2.txt` to `to_dir/`. 

**set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY lib/path)**: Set `.a` output path.

**set(CMAKE_LIBRARY_OUTPUT_DIRECTORY lib/path)**: Set `.so` output path.

**set(CMAKE_RUNTIME_OUTPUT_DIRECTORY bin/path)**: Set executable output path.

**set(CMAKE_VERBOSE_MAKEFILE ON)**: Enable cmake verbose.

**project(my_project)**: Set the project name. Later can be used with `${PROJECT_NAME}`.

**cmake_minimum_required(VERSION 3.5)**: Set the minimum cmake version required.

