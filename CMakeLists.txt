cmake_minimum_required(VERSION 3.22)

set(BUILD_SHARED_LIBS OFF)

set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_EXTENSIONS ON)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(EXPORT_COMPILE_COMMANDS ON)
set(CMAKE_COLOR_DIAGNOSTICS ON)
set(CMAKE_DIRECTORY_LABELS ON)

project(Game
        LANGUAGES
        C CXX
        VERSION
        0.0.1
)

# SDL build options
set(SDL_LIBC OFF)
set(SDL_SHARED OFF)
set(SDL_STATIC ON)

set(SDL_TESTS OFF)
set(SDL_TEST_LIBRARY OFF)

add_subdirectory("${CMAKE_CURRENT_SOURCE_DIR}/extern/sdl2")

file(GLOB_RECURSE
        project_sources
        CONFIGURE_DEPENDS
        "${CMAKE_CURRENT_SOURCE_DIR}/src/*.[hc]"
        "${CMAKE_CURRENT_SOURCE_DIR}/src/*.[hc]pp"
)

add_executable(${PROJECT_NAME}
        ${project_sources}
)

target_compile_features(${PROJECT_NAME} PRIVATE
        PRIVATE cxx_std_20
)

target_include_directories(${PROJECT_NAME}
        PRIVATE "${CMAKE_CURRENT_SOURCE_DIR}/src"
)

target_link_libraries(${PROJECT_NAME}
        PRIVATE $<TARGET_NAME_IF_EXISTS:SDL2::SDL2main>
        PRIVATE $<IF:$<TARGET_EXISTS:SDL2::SDL2>,SDL2::SDL2,SDL2::SDL2-static>
)

add_custom_command(TARGET ${PROJECT_NAME}
        POST_BUILD COMMAND
        ${CMAKE_COMMAND} -E copy_directory_if_different
        ${CMAKE_CURRENT_SOURCE_DIR}/resources
        $<TARGET_FILE_DIR:${PROJECT_NAME}>/resources
)