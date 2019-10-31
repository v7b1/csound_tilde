cmake_minimum_required(VERSION 3.0)


include(${CMAKE_CURRENT_SOURCE_DIR}/../../max-api/script/max-pretarget.cmake)


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
    #${PROJECT_NAME}.cpp
    ${MAXCS_SRCS}
    ${MAXCS_HEADERS}
)

add_library( 
	${PROJECT_NAME} 
	MODULE
    ${BUILD_SOURCES}
)


if(APPLE)
    ### find CoreServices ###
    # target_link_libraries(${PROJECT_NAME} PUBLIC "-framework CoreServices")

    find_library(CORE_SERVICES_FRAMEWORK "CoreServices" HINTS "/System/Frameworks/" )
    target_link_libraries(${PROJECT_NAME} PUBLIC ${CORE_SERVICES_FRAMEWORK})

    find_library(LAUNCH_SERVICES_FRAMEWORK LaunchServices PATHS ${CORE_SERVICES_FRAMEWORK}/Frameworks)
    include_directories(${CORE_SERVICES_FRAMEWORK}/Frameworks/CarbonCore.framework/Headers)
    include_directories(${LAUNCH_SERVICES_FRAMEWORK}/Headers)

    find_library(CSOUNDLIB "CsoundLib64" HINTS "/Library/Frameworks/" )
    target_link_libraries(${PROJECT_NAME} PUBLIC ${CSOUNDLIB})
    include_directories(${PROJECT_NAME} "${CSOUNDLIB}/Headers")

    add_definitions(-DMACOSX)
endif()

include_directories(${Boost_INCLUDE_DIRS})


include(${CMAKE_CURRENT_SOURCE_DIR}/../../max-api/script/max-posttarget.cmake)
