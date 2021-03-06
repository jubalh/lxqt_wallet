cmake_minimum_required(VERSION 2.6)
cmake_policy(SET CMP0017 OLD)

add_definitions(-Wno-unused-result)

find_file(GCRYPT_INCLUDE_FILE gcrypt.h)

find_library(GCRYPT_LIBRARY gcrypt)

if(NOT GCRYPT_INCLUDE_FILE)
    MESSAGE(FATAL_ERROR "Could not find gcrypt header file")
else()
    MESSAGE(STATUS "Found gcrypt header file: ${GCRYPT_INCLUDE_FILE}")
endif()

if(NOT GCRYPT_LIBRARY)
    MESSAGE(FATAL_ERROR "Could not find gcrypt library(libgcrypt.so)")
else()
    MESSAGE(STATUS "Found gcrypt library: ${GCRYPT_LIBRARY}")
endif()

add_library(lxqtwallet-backend STATIC lxqtwallet.c)
set_target_properties(lxqtwallet-backend PROPERTIES COMPILE_FLAGS "-D_FILE_OFFSET_BITS=64 -Wall -s -fPIC -pedantic ")
set_target_properties(lxqtwallet-backend PROPERTIES LINK_FLAGS "-pie")

target_link_libraries(lxqtwallet-backend "${GCRYPT_LIBRARY}")

install(FILES lxqtwallet.h DESTINATION "${CMAKE_INSTALL_PREFIX}/include/lxqt")

add_executable(lxqt_wallet-cli lxqt_wallet-cli.c lxqtwallet.c)
set_target_properties(lxqt_wallet-cli PROPERTIES COMPILE_FLAGS "-D_FILE_OFFSET_BITS=64 -Wextra -Wall -s -fPIE -pthread  -pedantic")
set_target_properties(lxqt_wallet-cli PROPERTIES LINK_FLAGS "-pie")
TARGET_LINK_LIBRARIES(lxqt_wallet-cli "${GCRYPT_LIBRARY}")

install(TARGETS lxqt_wallet-cli RUNTIME DESTINATION bin PERMISSIONS OWNER_READ OWNER_WRITE OWNER_EXECUTE GROUP_READ GROUP_EXECUTE WORLD_READ WORLD_EXECUTE)
