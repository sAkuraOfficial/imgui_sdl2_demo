cmake_minimum_required(VERSION 3.14)
project(SDL2ImGuiHello LANGUAGES CXX)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

include(FetchContent)

# 拉取 SDL2
FetchContent_Declare(
  SDL2
  GIT_REPOSITORY https://github.com/libsdl-org/SDL.git
  GIT_TAG release-2.30.2
)
FetchContent_MakeAvailable(SDL2)

# 拉取 ImGui
FetchContent_Declare(
  imgui
  GIT_REPOSITORY https://github.com/ocornut/imgui.git
  GIT_TAG v1.90.4 # 可以选择最新版本
)
FetchContent_MakeAvailable(imgui)

# 拉取 GLAD
FetchContent_Declare(
  glad
  GIT_REPOSITORY https://github.com/Dav1dde/glad.git
  GIT_TAG v0.1.36
)
FetchContent_MakeAvailable(glad)

# 添加 ImGui 的 SDL2 和 OpenGL3 封装文件
add_library(imgui_sdl_gl3
    ${imgui_SOURCE_DIR}/imgui.cpp
    ${imgui_SOURCE_DIR}/imgui_draw.cpp
    ${imgui_SOURCE_DIR}/imgui_tables.cpp
    ${imgui_SOURCE_DIR}/imgui_widgets.cpp
    ${imgui_SOURCE_DIR}/imgui_demo.cpp
    ${imgui_SOURCE_DIR}/backends/imgui_impl_sdl2.cpp
    ${imgui_SOURCE_DIR}/backends/imgui_impl_opengl3.cpp
)
target_include_directories(imgui_sdl_gl3 PUBLIC
    ${imgui_SOURCE_DIR}
    ${imgui_SOURCE_DIR}/backends
    ${sdl2_SOURCE_DIR}/include
)

add_executable(hello_sdl_imgui main.cpp)
target_link_libraries(hello_sdl_imgui
    PRIVATE
        SDL2-static
        imgui_sdl_gl3
        glad
)

target_include_directories(hello_sdl_imgui PRIVATE
    ${sdl2_SOURCE_DIR}/include
    ${imgui_SOURCE_DIR}
    ${imgui_SOURCE_DIR}/backends
)

if (WIN32)
    target_link_libraries(hello_sdl_imgui PRIVATE SDL2main)
endif()

# 链接 OpenGL
find_package(OpenGL REQUIRED)
target_link_libraries(hello_sdl_imgui PRIVATE OpenGL::GL)