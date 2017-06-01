#
# Build samples
#
if (CMAKE_VERSION VERSION_LESS "3.1")
    if (CMAKE_C_COMPILER_ID STREQUAL "GNU")
        set (CMAKE_C_FLAGS "--std=c99 ${CMAKE_C_FLAGS}")
        set (CMAKE_CXX_FLAGS "--std=c++11 ${CMAKE_CXX_FLAGS}")
    endif()
else()
    set (CMAKE_C_STANDARD 99)
    set (CMAKE_CXX_STANDARD 11)
endif()
    include(FindOpenSSL)

    add_executable(AnsiCServer 
        ansicservermain.c
        browsenext.c
        browseservice.c
        init_variables_of_addressspace.c
        readservice.c
    )
    target_include_directories(AnsiCServer
        PUBLIC ${_PROJECT_INC}
        PUBLIC ${_IOTSDK_INC}
        PUBLIC ${_UMQTT_INC}
        PUBLIC ${_CMP_ROOT}
        PUBLIC ${_HASHTABLE_ROOT}
        PUBLIC ${_PARSON_ROOT}
        PUBLIC ${OPENSSL_INCLUDE_DIR}
    )
    target_compile_definitions(AnsiCServer
        PUBLIC _POSIX_C_SOURCE=200112L
        PUBLIC HAVE_IN6_U=1
    )
if(WIN32)
    #
    # Direct msvc target to be linked with static crt
    #
    if(MSVC)
        if(not ${link_dynamic_msvcrt})
            foreach(flag_var
                    CMAKE_CXX_FLAGS 
                    CMAKE_C_FLAGS
                    CMAKE_CXX_FLAGS_DEBUG 
                    CMAKE_C_FLAGS_DEBUG
                    CMAKE_CXX_FLAGS_RELEASE 
                    CMAKE_C_FLAGS_RELEASE
                    CMAKE_CXX_FLAGS_MINSIZEREL 
                    CMAKE_C_FLAGS_MINSIZEREL
                    CMAKE_CXX_FLAGS_RELWITHDEBINFO 
                    CMAKE_C_FLAGS_RELWITHDEBINFO
                )
                if(${flag_var} MATCHES "/MD")
                    string(REGEX REPLACE "/MD" "/MT" ${flag_var} "${${flag_var}}")
                endif(${flag_var} MATCHES "/MD")
            endforeach(flag_var)
            set (CMAKE_C_FLAGS_DEBUG "${CMAKE_C_FLAGS_DEBUG}" 
                CACHE STRING "MSVC C Debug MT flags " FORCE)    
            set (CMAKE_CXX_FLAGS_DEBUG "${CMAKE_CXX_FLAGS_DEBUG}" 
                CACHE STRING "MSVC CXX Debug MT flags " FORCE)
            set (CMAKE_C_FLAGS_RELEASE "${CMAKE_C_FLAGS_RELEASE}" 
                CACHE STRING "MSVC C Release MT flags " FORCE)
            set (CMAKE_CXX_FLAGS_RELEASE "${CMAKE_CXX_FLAGS_RELEASE}" 
                CACHE STRING "MSVC CXX Release MT flags " FORCE)
            set (CMAKE_C_FLAGS_MINSIZEREL "${CMAKE_C_FLAGS_MINSIZEREL}" 
                CACHE STRING "MSVC C Debug MT flags " FORCE)    
            set (CMAKE_CXX_FLAGS_MINSIZEREL "${CMAKE_CXX_FLAGS_MINSIZEREL}" 
                CACHE STRING "MSVC C Release MT flags " FORCE)
            set (CMAKE_C_FLAGS_RELWITHDEBINFO "${CMAKE_C_FLAGS_RELWITHDEBINFO}" 
                CACHE STRING "MSVC CXX Debug MT flags " FORCE)    
            set (CMAKE_CXX_FLAGS_RELWITHDEBINFO "${CMAKE_CXX_FLAGS_RELWITHDEBINFO}" 
                CACHE STRING "MSVC CXX Release MT flags " FORCE)
        endif()

        target_compile_definitions(AnsiCServer
            PUBLIC _CRT_SECURE_NO_WARNINGS
            PUBLIC STRICT
        )
        if ("${CMAKE_BUILD_TYPE}" STREQUAL "Debug")
            target_compile_definitions(AnsiCServer PUBLIC _CRTDBG_MAP_ALLOC)
        else()
            target_compile_definitions(AnsiCServer PUBLIC NDEBUG)
        endif()
    endif()
    target_link_libraries(AnsiCServer
        PUBLIC ws2_32 
        PUBLIC rpcrt4 
        PUBLIC crypt32 
    )
else()
    target_link_libraries(AnsiCServer PUBLIC pthread m)
endif()
    target_link_libraries(AnsiCServer PUBLIC uastack)
    target_link_libraries(AnsiCServer PUBLIC ${OPENSSL_LIBRARIES})
if ("${CMAKE_BUILD_TYPE}" STREQUAL "Debug")
    target_compile_definitions(AnsiCServer PUBLIC _DEBUG)
endif()

if(WIN32)
  # Place here the install rule for win32
elseif(LINUX)
    install (TARGETS AnsiCServer LIBRARY DESTINATION bin)
endif()
