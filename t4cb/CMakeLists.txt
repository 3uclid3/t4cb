# Separate library part and executable part (`main()` function) to simplify testing.

# ----------------------------------------------------------------------------------------------------------------------
# library part
set(T4CBLIB_INCLUDES
	t4cb/run.hpp
)

set(T4CBLIB_SOURCES
	t4cb/run.cpp
)

add_library(t4cblib STATIC ${T4CBLIB_INCLUDES} ${T4CBLIB_SOURCES})

# Includes
target_include_interface_directories(t4cblib .)

# link project_options/warnings
target_link_libraries(t4cblib INTERFACE project_options project_warnings)

# Find dependencies:
target_find_dependencies(t4cblib
  PRIVATE_CONFIG 
    fmt
    imgui
    ImGui-SFML)
find_package(SFML COMPONENTS system window graphics CONFIG REQUIRED)

# Link dependencies:
target_link_system_libraries(t4cblib
  PRIVATE 
    fmt::fmt
    sfml-system sfml-network sfml-graphics sfml-window
    imgui::imgui
    ImGui-SFML::ImGui-SFML)


# ----------------------------------------------------------------------------------------------------------------------
# executable part
add_executable(t4cb t4cb/main.cpp)
target_link_libraries(t4cb PRIVATE project_options project_warnings)
target_link_libraries(t4cb PRIVATE t4cblib)
