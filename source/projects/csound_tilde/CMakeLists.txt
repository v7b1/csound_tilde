include(${CMAKE_CURRENT_SOURCE_DIR}/../../max-sdk-base/script/max-pretarget.cmake)


include_directories( 
	"${C74_INCLUDES}"
)


find_package(Boost)


### csound~ sources
set(MAXCS_SRCS
    src/Args.cpp
    src/atom_buffer.cpp
    src/channel.cpp
    src/csound~.cpp
    src/CsoundObject.cpp
    src/CsoundTable.cpp
    src/Lock.cpp
    src/memory.cpp
    src/message_buffer.cpp
    src/midi.cpp
    src/PatchScripter.cpp
    src/sequencer.cpp
    src/util.cpp
)

set(MAXCS_HEADERS
    src/Args.h
    src/atom_buffer.h
    src/channel.h
    src/csound~.h
    src/CsoundObject.h
    src/CsoundTable.h
    src/definitions.h
    src/eksepshun.h
    src/includes.h
    src/Lock.h
    src/max_headers.h
    src/memory.h
    src/message_buffer.h
    src/midi.h
    src/Parser.h
    src/PatchScripter.h
    src/sequencer.h
    src/util.h
)


set(BUILD_SOURCES
    ${MAXCS_SRCS}
    ${MAXCS_HEADERS}
)

add_library( 
	${PROJECT_NAME} 
	MODULE
    ${BUILD_SOURCES}
)


if(APPLE)
    ### find and link CoreServices and LaunchServices ###
    find_library(CORE_SERVICES_FRAMEWORK "CoreServices" HINTS "/System/Frameworks/" )
    target_link_libraries(${PROJECT_NAME} PUBLIC ${CORE_SERVICES_FRAMEWORK})
    find_library(LAUNCH_SERVICES_FRAMEWORK LaunchServices PATHS ${CORE_SERVICES_FRAMEWORK}/Frameworks)
    include_directories(${CORE_SERVICES_FRAMEWORK}/Frameworks/CarbonCore.framework/Headers)
    include_directories(${LAUNCH_SERVICES_FRAMEWORK}/Headers)

    ### find and link CsoundLib64
    find_library(CSOUNDLIB "CsoundLib64" HINTS "/Library/Frameworks/" )
    target_link_libraries(${PROJECT_NAME} PUBLIC ${CSOUNDLIB})
    include_directories(${PROJECT_NAME} "${CSOUNDLIB}/Headers")

    add_definitions(-DMACOSX)


    if(USE_LIBSNDFILE)
        SET(CMAKE_LIBRARY_PATH ${CMAKE_LIBRARY_PATH} "/usr/local/lib")
        #find_package(SndFile)
        message(STATUS "trying to use libsndfile")
        #target_link_libraries(${PROJECT_NAME} PUBLIC ${LIBSNDFILE_LIBRARY})

        
        ### dynamically linking libsndfile
        #target_link_libraries(${PROJECT_NAME} PUBLIC "${CMAKE_LIBRARY_PATH}/libsndfile.1.dylib")

        ### statically linking libsndfile
        target_link_libraries(${PROJECT_NAME} PUBLIC "${CMAKE_LIBRARY_PATH}/libsndfile.a")
        target_link_libraries(${PROJECT_NAME} PUBLIC "${CMAKE_LIBRARY_PATH}/libogg.a")
        target_link_libraries(${PROJECT_NAME} PUBLIC "${CMAKE_LIBRARY_PATH}/libFLAC.a")
        target_link_libraries(${PROJECT_NAME} PUBLIC "${CMAKE_LIBRARY_PATH}/libvorbis.a")
        target_link_libraries(${PROJECT_NAME} PUBLIC "${CMAKE_LIBRARY_PATH}/libvorbisenc.a")
        
        add_definitions(-DLIBSNDFILE)
        

    else()
        message(STATUS "not using libsndfile")
    endif()

else()
    SET(CMAKE_INCLUDE_PATH ${CMAKE_INCLUDE_PATH} "${BOOST_DIR}")
    SET(CMAKE_LIBRARY_PATH ${CMAKE_LIBRARY_PATH} "${BOOST_DIR}/lib")

    SET(CMAKE_INCLUDE_PATH ${CMAKE_INCLUDE_PATH} "${VCPKG_PACKAGES_DIR}/libsndfile_x64-windows-static/include")
    SET(CMAKE_INCLUDE_PATH ${CMAKE_INCLUDE_PATH} "${VCPKG_PACKAGES_DIR}/libvorbis_x64-windows-static/include")
    SET(CMAKE_INCLUDE_PATH ${CMAKE_INCLUDE_PATH} "${VCPKG_PACKAGES_DIR}/pthreads_x64-windows/include")
    SET(CMAKE_INCLUDE_PATH ${CMAKE_INCLUDE_PATH} "C:/Program Files/Csound6_x64/include/csound")
    SET(CMAKE_INCLUDE_PATH ${CMAKE_INCLUDE_PATH} "${VCPKG_PACKAGES_DIR}/libogg_x64-windows-static/include")
    SET(CMAKE_INCLUDE_PATH ${CMAKE_INCLUDE_PATH} "${VCPKG_PACKAGES_DIR}/libflac_x64-windows-static/include")
    
    SET(CMAKE_LIBRARY_PATH ${CMAKE_LIBRARY_PATH} "${VCPKG_PACKAGES_DIR}/libsndfile_x64-windows-static/lib")
    SET(CMAKE_LIBRARY_PATH ${CMAKE_LIBRARY_PATH} "${VCPKG_PACKAGES_DIR}/libvorbis_x64-windows-static/lib")
    SET(CMAKE_LIBRARY_PATH ${CMAKE_LIBRARY_PATH} "${VCPKG_PACKAGES_DIR}/pthreads_x64-windows/lib")
    SET(CMAKE_LIBRARY_PATH ${CMAKE_LIBRARY_PATH} "C:/Program Files/Csound6_x64/lib")
    SET(CMAKE_LIBRARY_PATH ${CMAKE_LIBRARY_PATH} "${VCPKG_PACKAGES_DIR}/libogg_x64-windows-static/lib")
    SET(CMAKE_LIBRARY_PATH ${CMAKE_LIBRARY_PATH} "${VCPKG_PACKAGES_DIR}/libflac_x64-windows-static/lib")

    #set(MSP_LIB_DIR "${MAX_SDK_ROOT}/source/c74support/msp-includes/x64")
    #set(MAX_LIB_DIR "${MAX_SDK_ROOT}/source/c74support/max-includes/x64")


    find_library(LIBSNDFILE_LIBRARY libsndfile PATHS ${CMAKE_INCLUDE_PATH})
    find_library(LIBOGG_LIBRARY ogg PATHS ${CMAKE_INCLUDE_PATH})
    find_library(LIBVORBIS_LIBRARY vorbis PATHS ${CMAKE_INCLUDE_PATH})
    find_library(LIBPTHREAD_LIBRARY pthreadVC3 PATHS ${CMAKE_INCLUDE_PATH})
    find_library(LIBFLAC_LIBRARY flac PATHS ${CMAKE_INCLUDE_PATH})
    find_library(LIBCSOUND_LIBRARY libcsound64 PATHS ${CMAKE_INCLUDE_PATH})

    link_directories(${CMAKE_LIBRARY_PATH})

    target_link_libraries(${PROJECT_NAME}  ws2_32
    ${LIBSNDFILE_LIBRARY} ${LIBFLAC_LIBRARY} ${LIBOGG_LIBRARY} ${LIBCSOUND_LIBRARY} ${LIBVORBIS_LIBRARY} ${LIBPTHREAD_LIBRARY})
    include_directories(${CMAKE_INCLUDE_PATH})


endif()



include_directories(${Boost_INCLUDE_DIRS})


include(${CMAKE_CURRENT_SOURCE_DIR}/../../max-sdk-base/script/max-posttarget.cmake)
