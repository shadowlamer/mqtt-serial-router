cmake_minimum_required(VERSION 3.0)

project("mqtt-serial-router" LANGUAGES C VERSION "0.0.8")

set(CMAKE_MODULE_PATH "${CMAKE_SOURCE_DIR}/cmake_modules")

set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wall -Wextra -Werror")

AUX_SOURCE_DIRECTORY(. C_SOURCES)
add_executable( ${PROJECT_NAME} ${C_SOURCES} common/logging.c)

find_package (Mosquitto REQUIRED)
find_package (Libev REQUIRED)

include_directories(. ${MOSQUITTO_INCLUDE_DIR} ${LIBEV_INCLUDE_DIR})
target_link_libraries (${PROJECT_NAME} ${MOSQUITTO_LIBRARIES} ${LIBEV_LIBRARIES})

install(TARGETS ${PROJECT_NAME} DESTINATION bin)
install(FILES ${CMAKE_SOURCE_DIR}/deb/${PROJECT_NAME}@.service DESTINATION /lib/systemd/system)
install(FILES ${CMAKE_SOURCE_DIR}/deb/mosquitto.service DESTINATION /lib/systemd/system)

if (NOT DEFINED PACKAGE_ARCHITECTURE)
    execute_process (
            COMMAND dpkg --print-architecture
            OUTPUT_VARIABLE PACKAGE_ARCHITECTURE
            OUTPUT_STRIP_TRAILING_WHITESPACE
    )
endif()

set(CPACK_GENERATOR "DEB")
set(CPACK_PACKAGE_NAME "${PROJECT_NAME}")
set(CPACK_PACKAGE_VERSION "${PROJECT_VERSION}")
set(CPACK_PACKAGE_FILE_NAME "${CPACK_PACKAGE_NAME}_${CPACK_PACKAGE_VERSION}_${PACKAGE_ARCHITECTURE}")
set(CPACK_DEBIAN_PACKAGE_SECTION "non-free")
set(CPACK_DEBIAN_PACKAGE_MAINTAINER "Vadim Cherenev <sl@anhot.ru>")
set(CPACK_DEBIAN_PACKAGE_DESCRIPTION "MQTT serial router")
set(CPACK_DEBIAN_PACKAGE_DEPENDS "libmosquitto1:${PACKAGE_ARCHITECTURE},libev4:${PACKAGE_ARCHITECTURE},mosquitto:${PACKAGE_ARCHITECTURE}")
set(CPACK_DEBIAN_PACKAGE_ARCHITECTURE "${PACKAGE_ARCHITECTURE}")
set(CPACK_PACKAGING_INSTALL_PREFIX "usr")

set(CPACK_DEBIAN_PACKAGE_CONTROL_EXTRA
        ${CMAKE_SOURCE_DIR}/deb/postinst
        ${CMAKE_SOURCE_DIR}/deb/prerm
        ${CMAKE_SOURCE_DIR}/deb/postrm)

include(CPack)