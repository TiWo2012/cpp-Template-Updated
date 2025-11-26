cmake_minimum_required(VERSION 3.16)

project(name
    VERSION 1.0
    LANGUAGES C CXX
)

set(CMAKE_EXPORT_COMPILE_COMMANDS ON)

set(CMAKE_CXX_STANDARD 23)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

file(GLOB_RECURSE PROJECT_SOURCES
    CONFIGURE_DEPENDS
    ${CMAKE_CURRENT_SOURCE_DIR}/src/*.cpp
    ${CMAKE_CURRENT_SOURCE_DIR}/src/*.c
    ${CMAKE_CURRENT_SOURCE_DIR}/src/*.h
)

add_executable(${PROJECT_NAME}
    ${PROJECT_SOURCES}
)

target_include_directories(${PROJECT_NAME}
    PRIVATE
        ${CMAKE_CURRENT_SOURCE_DIR}/includes
        ${CMAKE_CURRENT_SOURCE_DIR}/src
)

if(APPLE)
    target_link_libraries(${PROJECT_NAME}
        PRIVATE
            "-framework IOKit"
            "-framework Cocoa"
            "-framework OpenGL"
            "-framework CoreVideo"
    )
elseif(UNIX AND NOT APPLE)
    find_package(X11 REQUIRED)
    target_link_libraries(${PROJECT_NAME}
        PRIVATE
            m pthread dl X11
    )
elseif(WIN32)
endif()

message(STATUS "Sources:")
foreach(src ${PROJECT_SOURCES})
    message(STATUS "  ${src}")
endforeach()

message(STATUS "Include dirs:")
get_target_property(incs ${PROJECT_NAME} INCLUDE_DIRECTORIES)
foreach(inc ${incs})
    message(STATUS "  ${inc}")
endforeach()

install(TARGETS ${PROJECT_NAME}
    RUNTIME DESTINATION bin
)
