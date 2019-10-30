cmake_minimum_required(VERSION 3.12)

project(SDL_mixer)
list(APPEND CMAKE_MODULE_PATH ${CMAKE_CURRENT_SOURCE_DIR}/cmake)

option(SDL_MIXER_SHARED
    "Build shared library" ON
)
option(SDL_MIXER_ALLOW_FIND_SDL
    "Allow searching for prebuilt SDL2 if target is not present" ON
)
option(SDL_MIXER_FORMAT_OGG
    "Support OGG format (builds libvorbis and libogg)" ON
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

# SDL mixer files
set(SRC_C_FILES
    effect_position.c
    effects_internal.c
    effect_stereoreverse.c
    load_aiff.c
    load_voc.c
    mixer.c
    music.c
    music_cmd.c
    music_flac.c
    music_fluidsynth.c
    music_mad.c
    music_mikmod.c
    music_modplug.c
    music_mpg123.c
    music_nativemidi.c
    music_ogg.c
    music_opus.c
    music_timidity.c
    music_wav.c
)

set(SRC_H_MASTER_FILE SDL_mixer.h)

set(SRC_H_FILES
    effects_internal.h
    load_aiff.h
    load_voc.h
    mixer.h
    music_cmd.h
    music_flac.h
    music_fluidsynth.h
    music.h
    music_mad.h
    music_mikmod.h
    music_modplug.h
    music_mpg123.h
    music_nativemidi.h
    music_ogg.h
    music_opus.h
    music_timidity.h
    music_wav.h
)

if (SDL_MIXER_FORMAT_OGG)
# OGG dependency files
    set(DEP_OGG_DIR "external/libogg-1.3.2")

    set(DEP_OGG_C_FILES
        ${DEP_OGG_DIR}/src/bitwise.c
        ${DEP_OGG_DIR}/src/framing.c
    )

    set(SIZE16 int16_t)
    set(USIZE16 uint16_t)
    set(SIZE32 int32_t)
    set(USIZE32 uint32_t)
    set(SIZE64 int64_t)
    set(USIZE64 uint64_t)

    configure_file(${DEP_OGG_DIR}/include/ogg/config_types.h.in
                   ${DEP_OGG_DIR}/include/ogg/config_types.h @ONLY)

    set(DEP_OGG_H_FILES
        ${CMAKE_CURRENT_BINARY_DIR}/${DEP_OGG_DIR}/include/ogg/config_types.h
        ${DEP_OGG_DIR}/include/ogg/ogg.h
        ${DEP_OGG_DIR}/include/ogg/os_types.h
    )

    set(DEP_OGG_INCLUDES ${DEP_OGG_DIR}/include)


# Vorbis + Vorbisfile dependency files
    set(DEP_VORBIS_DIR "external/libvorbis-1.3.6")

    set(DEP_VORBIS_H_FILES
        ${DEP_VORBIS_DIR}/include/vorbis/codec.h
        ${DEP_VORBIS_DIR}/include/vorbis/vorbisenc.h
        ${DEP_VORBIS_DIR}/include/vorbis/vorbisfile.h

        ${DEP_VORBIS_DIR}/lib/envelope.h
        ${DEP_VORBIS_DIR}/lib/lpc.h
        ${DEP_VORBIS_DIR}/lib/lsp.h
        ${DEP_VORBIS_DIR}/lib/codebook.h
        ${DEP_VORBIS_DIR}/lib/misc.h
        ${DEP_VORBIS_DIR}/lib/psy.h
        ${DEP_VORBIS_DIR}/lib/masking.h
        ${DEP_VORBIS_DIR}/lib/os.h
        ${DEP_VORBIS_DIR}/lib/mdct.h
        ${DEP_VORBIS_DIR}/lib/smallft.h
        ${DEP_VORBIS_DIR}/lib/highlevel.h
        ${DEP_VORBIS_DIR}/lib/registry.h
        ${DEP_VORBIS_DIR}/lib/scales.h
        ${DEP_VORBIS_DIR}/lib/window.h
        ${DEP_VORBIS_DIR}/lib/lookup.h
        ${DEP_VORBIS_DIR}/lib/lookup_data.h
        ${DEP_VORBIS_DIR}/lib/codec_internal.h
        ${DEP_VORBIS_DIR}/lib/backends.h
        ${DEP_VORBIS_DIR}/lib/bitrate.h
    )

    set(DEP_VORBIS_C_FILES
        ${DEP_VORBIS_DIR}/lib/mdct.c
        ${DEP_VORBIS_DIR}/lib/smallft.c
        ${DEP_VORBIS_DIR}/lib/block.c
        ${DEP_VORBIS_DIR}/lib/envelope.c
        ${DEP_VORBIS_DIR}/lib/window.c
        ${DEP_VORBIS_DIR}/lib/lsp.c
        ${DEP_VORBIS_DIR}/lib/lpc.c
        ${DEP_VORBIS_DIR}/lib/analysis.c
        ${DEP_VORBIS_DIR}/lib/synthesis.c
        ${DEP_VORBIS_DIR}/lib/psy.c
        ${DEP_VORBIS_DIR}/lib/info.c
        ${DEP_VORBIS_DIR}/lib/floor1.c
        ${DEP_VORBIS_DIR}/lib/floor0.c
        ${DEP_VORBIS_DIR}/lib/res0.c
        ${DEP_VORBIS_DIR}/lib/mapping0.c
        ${DEP_VORBIS_DIR}/lib/registry.c
        ${DEP_VORBIS_DIR}/lib/codebook.c
        ${DEP_VORBIS_DIR}/lib/sharedbook.c
        ${DEP_VORBIS_DIR}/lib/lookup.c
        ${DEP_VORBIS_DIR}/lib/bitrate.c

        ${DEP_VORBIS_DIR}/lib/vorbisfile.c
    )

    set(DEP_VORBIS_INCLUDES_INTERNAL ${DEP_VORBIS_DIR}/lib)
    set(DEP_VORBIS_INCLUDES_EXTERNAL ${DEP_VORBIS_DIR}/include)
endif()


set(SRC_FILES
    ${SRC_C_FILES}
    ${SRC_H_FILES}
)

if (SDL_MIXER_FORMAT_OGG)
    set(SRC_FILES
        ${SRC_FILES}
        ${DEP_OGG_C_FILES}
        ${DEP_OGG_H_FILES}
        ${DEP_VORBIS_C_FILES}
        ${DEP_VORBIS_H_FILES}
    )
endif()

add_library(SDL_mixer OBJECT ${SRC_FILES})
set_target_properties(SDL_mixer PROPERTIES POSITION_INDEPENDENT_CODE ON)

target_link_libraries(SDL_mixer PRIVATE SDL2)

target_compile_definitions(SDL_mixer PRIVATE -DMUSIC_WAV)

if (SDL_MIXER_FORMAT_OGG)
    target_compile_definitions(SDL_mixer PRIVATE -DMUSIC_OGG)
endif()

target_include_directories(SDL_mixer PUBLIC
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}>
    $<INSTALL_INTERFACE:include/SDL2>
)

if (SDL_MIXER_FORMAT_OGG)
    target_include_directories(SDL_mixer PRIVATE
        $<BUILD_INTERFACE:${CMAKE_CURRENT_BINARY_DIR}/${DEP_OGG_DIR}/include>
        $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/${DEP_OGG_INCLUDES}>
        $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/${DEP_VORBIS_INCLUDES_INTERNAL}>
        $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/${DEP_VORBIS_INCLUDES_EXTERNAL}>
    )
endif()

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
