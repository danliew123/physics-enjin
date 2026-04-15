cmake_minimum_required(VERSION 3.16)
project(PhysicsEngine CXX C)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

# ── GLFW ──────────────────────────────────────────────────────────────────────
set(GLFW_BUILD_DOCS     OFF CACHE BOOL "" FORCE)
set(GLFW_BUILD_TESTS    OFF CACHE BOOL "" FORCE)
set(GLFW_BUILD_EXAMPLES OFF CACHE BOOL "" FORCE)
add_subdirectory(external/glfw)

# ── GLAD ──────────────────────────────────────────────────────────────────────
add_library(glad STATIC external/glad/src/glad.c)
target_include_directories(glad PUBLIC external/glad/include)

# ── GLM ───────────────────────────────────────────────────────────────────────
add_library(glm INTERFACE)
target_include_directories(glm INTERFACE external/glm)

# ── Dear ImGui ────────────────────────────────────────────────────────────────
add_library(imgui STATIC
    external/imgui/imgui.cpp
    external/imgui/imgui_draw.cpp
    external/imgui/imgui_tables.cpp
    external/imgui/imgui_widgets.cpp
    external/imgui/backends/imgui_impl_glfw.cpp
    external/imgui/backends/imgui_impl_opengl3.cpp
)
target_include_directories(imgui PUBLIC
    external/imgui
    external/imgui/backends
)
target_link_libraries(imgui PUBLIC glfw)

# ── Your engine ───────────────────────────────────────────────────────────────
file(GLOB_RECURSE ENGINE_SOURCES
    src/*.cpp
    src/*.h
)

add_executable(PhysicsEngine ${ENGINE_SOURCES})

target_include_directories(PhysicsEngine PRIVATE src)

target_link_libraries(PhysicsEngine PRIVATE
    glfw
    glad
    glm
    imgui
    ${CMAKE_DL_LIBS}   # needed for GLAD on Linux
)

# Copy assets folder next to the executable after build
add_custom_command(TARGET PhysicsEngine POST_BUILD
    COMMAND ${CMAKE_COMMAND} -E copy_directory
    ${CMAKE_SOURCE_DIR}/assets
    $<TARGET_FILE_DIR:PhysicsEngine>/assets
)