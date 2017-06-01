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
    endif()
endif()
    add_executable(AnsiCServer 
        ansicservermain.c
        browsenext.c
        browseservice.c
        init_variables_of_addressspace.c
        readservice.c
    )
    target_link_libraries(AnsiCServer PUBLIC uastack)
if(WIN32)
    # Place here the install rule for win32
else()
    install (TARGETS AnsiCServer LIBRARY DESTINATION bin)
endif()
