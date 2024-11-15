cmake_minimum_required(VERSION 3.16)
set(PROG_NAME "jnxutil")

# Use cache to be friendly to cmake-gui and Qt Creator
set(USE_GDAL On CACHE BOOL "Enable/Disable GDAL support")

project(${PROG_NAME}
	LANGUAGES C
	VERSION 0.0.1
	HOMEPAGE_URL "https://github.com/apr2504/jnxutil")

file(GLOB SOURCES "*.c")
add_executable(${PROJECT_NAME} ${SOURCES})
target_compile_definitions(${PROJECT_NAME} PRIVATE PROG_NAME="${PROG_NAME}" PROG_VERSION="${PROJECT_VERSION}")

# do not override provided build type
if (NOT CMAKE_BUILD_TYPE)
    set(CMAKE_BUILD_TYPE "Release")
endif()

if (USE_GDAL)
    find_package(GDAL REQUIRED)
    target_include_directories(${PROJECT_NAME} PRIVATE ${GDAL_INCLUDE_DIR})
    target_link_libraries(${PROJECT_NAME} PRIVATE ${GDAL_LIBRARY})
    target_compile_definitions(${PROJECT_NAME} PRIVATE WITH_GDAL)
else ()
    message(WARNING "!!! ${PROG_NAME} will be build without tiles export feature !!!")
endif ()

install (TARGETS ${PROG_NAME} DESTINATION bin)
install (FILES README.md LICENSE DESTINATION share/${PROG_NAME})

add_custom_target(cppcheck COMMAND cppcheck --language=c --enable=all --template=gcc ${SOURCES})
add_custom_target(uninstall COMMAND xargs rm < install_manifest.txt)
