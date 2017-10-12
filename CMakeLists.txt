cmake_minimum_required(VERSION 3.0)

project(SDL_mixer)
set(CMAKE_MODULE_PATH ${CMAKE_CURRENT_SOURCE_DIR}/cmake)

find_package(SDL2 REQUIRED)

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

add_library(SDL_mixer-shared SHARED ${SRC_FILES})
set_target_properties(SDL_mixer-shared PROPERTIES OUTPUT_NAME SDL2_mixer)
add_library(SDL_mixer-static STATIC ${SRC_FILES})
set_target_properties(SDL_mixer-static PROPERTIES OUTPUT_NAME SDL2_mixer)

target_include_directories(SDL_mixer-shared PUBLIC
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}>
    $<INSTALL_INTERFACE:include/SDL2>
    ${SDL2_INCLUDE_DIR}
)

target_include_directories(SDL_mixer-static PUBLIC
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}>
    $<INSTALL_INTERFACE:include/SDL2>
    ${SDL2_INCLUDE_DIR}
)

target_link_libraries(SDL_mixer-shared ${SDL2_LIBRARY})
target_link_libraries(SDL_mixer-static ${SDL2_LIBRARY})

message(STATUS "SDL2_FOUND: ${SDL2_FOUND}")
message(STATUS "SDL2_LIBRARY: ${SDL2_LIBRARY}")
message(STATUS "SDL2_INCLUDE_DIR: ${SDL2_INCLUDE_DIR}")

install(TARGETS SDL_mixer-shared SDL_mixer-static
        EXPORT SDL_mixerConfig
        LIBRARY DESTINATION lib
        ARCHIVE DESTINATION lib)
install(FILES ${SRC_H_MASTER_FILE} DESTINATION include/SDL2)

install(EXPORT SDL_mixerConfig DESTINATION share/SDL_mixer/cmake)
export(TARGETS SDL_mixer-shared SDL_mixer-static FILE SDL_mixerConfig.cmake)
