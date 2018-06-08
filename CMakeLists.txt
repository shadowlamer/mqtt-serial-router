cmake_minimum_required(VERSION 3.0)

set(TARGET mqtt-serial-router)

project(${TARGET})

set(CMAKE_MODULE_PATH "./cmake_modules")

#set(CMAKE_CXX_STANDARD 11) 
#set(CMAKE_CXX_STANDARD_REQUIRED on)
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wall -Wextra -Werror")

AUX_SOURCE_DIRECTORY(. C_SOURCES)
add_executable( ${TARGET} ${C_SOURCES} common/logging.c)

find_package (Mosquitto REQUIRED)
find_package (Libev REQUIRED)

include_directories(. ${MOSQUITTO_INCLUDE_DIR} ${LIBEV_INCLUDE_DIR})
target_link_libraries (${TARGET} ${MOSQUITTO_LIBRARIES} ${LIBEV_LIBRARIES})

install(TARGETS ${TARGET} DESTINATION bin)
