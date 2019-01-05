cmake_minimum_required(VERSION 3.12)

project(SDL_mixer)
list(APPEND CMAKE_MODULE_PATH ${CMAKE_CURRENT_SOURCE_DIR}/cmake)

option(SDL_MIXER_SHARED
    "Build shared library" ON
)
option(SDL_MIXER_ALLOW_FIND_SDL
    "Allow searching for prebuilt SDL2 if target is not present" ON
)

if (NOT TARGET SDL2)
    if (SDL_MIXER_ALLOW_FIND_SDL)
        find_package(SDL2 REQUIRED)
        add_library(SDL2 SHARED IMPORTED)
        set_target_properties(SDL2 PROPERTIES IMPORTED_LOCATION ${SDL2_LIBRARY})
        target_include_directories(SDL2 INTERFACE ${SDL2_INCLUDE_DIR})
    else()
        message(FATAL_ERROR "SDL2 target is not present")
    endif()
endif()

set(SRC_C_FILES
    dynamic_flac.c
    dynamic_fluidsynth.c
    dynamic_mod.c
    dynamic_modplug.c
    dynamic_mp3.c
    dynamic_ogg.c
    effect_position.c
    effect_stereoreverse.c
    effects_internal.c
    fluidsynth.c
    load_aiff.c
    load_flac.c
    load_mp3.c
    load_ogg.c
    load_voc.c
    mixer.c
    music.c
    music_cmd.c
    music_flac.c
    music_mad.c
    music_mod.c
    music_modplug.c
    music_ogg.c
    wavestream.c
)

set(SRC_H_MASTER_FILE SDL_mixer.h)

set(SRC_H_FILES
    dynamic_flac.h
    dynamic_fluidsynth.h
    dynamic_mod.h
    dynamic_modplug.h
    dynamic_mp3.h
    dynamic_ogg.h
    effects_internal.h
    fluidsynth.h
    load_aiff.h
    load_flac.h
    load_mp3.h
    load_ogg.h
    load_voc.h
    music_cmd.h
    music_flac.h
    music_mad.h
    music_mod.h
    music_modplug.h
    music_ogg.h
    wavestream.h
)

set(SRC_FILES ${SRC_C_FILES} ${SRC_H_FILES})

# TODO more configuration options
add_definitions(-DWAV_MUSIC)

add_library(SDL_mixer OBJECT ${SRC_FILES})
set_target_properties(SDL_mixer PROPERTIES POSITION_INDEPENDENT_CODE ON)

target_link_libraries(SDL_mixer PRIVATE SDL2)

target_include_directories(SDL_mixer PUBLIC
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}>
    $<INSTALL_INTERFACE:include/SDL2>
)

if (SDL_MIXER_SHARED)
    add_library(SDL_mixer-shared SHARED $<TARGET_OBJECTS:SDL_mixer>)
    list(APPEND SDL_MIXER_LIB_TARGETS SDL_mixer-shared)
endif()

add_library(SDL_mixer-static STATIC $<TARGET_OBJECTS:SDL_mixer>)
list(APPEND SDL_MIXER_LIB_TARGETS SDL_mixer-static)

set_target_properties(${SDL_MIXER_LIB_TARGETS} PROPERTIES OUTPUT_NAME SDL2_mixer)

install(TARGETS ${SDL_MIXER_LIB_TARGETS}
        EXPORT SDL_mixer-config
        LIBRARY DESTINATION lib
        ARCHIVE DESTINATION lib)
install(FILES ${SRC_H_MASTER_FILE} DESTINATION include/SDL2)

install(EXPORT SDL_mixer-config DESTINATION lib/cmake/SDL_mixer)
export(TARGETS ${SDL_MIXER_LIB_TARGETS} FILE SDL_mixer-config.cmake)
