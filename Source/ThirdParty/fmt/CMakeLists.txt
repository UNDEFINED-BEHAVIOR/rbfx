# core.h has following additions:
#    #include <EASTL/string.h>
#
# Conditionally define some macros to make fmt use types from alternate stl implementation
#    #if EASTL_URHO3D_EXTENSIONS
#    # define FMT_STRING_VIEW eastl::basic_string_view
#    # define FMT_BASIC_STRING eastl::basic_string
#    # define FMT_STRING_T eastl::string
#    # define FMT_WSTRING_T eastl::wstring
#    #else
#    // libc++ supports string_view in pre-c++17.
#    <...>
#    #endif
#    #endif
#
# Replace all instances of `std::basic_string` with `FMT_BASIC_STRING`.
# Replace all instances of `std::string` with `FMT_STRING_T`.
# Replace all instances of `std::wstring` with `FMT_WSTRING_T`.
#
# Some std::runtime_error() require extra `.c_str()` because this function consumes `std::string`.
#

include(CheckSymbolExists)
if (WIN32)
  check_symbol_exists(open io.h HAVE_OPEN)
else ()
  check_symbol_exists(open fcntl.h HAVE_OPEN)
endif ()
# Define the fmt library, its includes and the needed defines.
# *.cc are added to FMT_HEADERS for the header-only configuration.
file(GLOB FMT_HEADERS fmt/*.h)
set(FMT_SOURCES fmt/format.cc)
if (HAVE_OPEN)
  set(FMT_HEADERS ${FMT_HEADERS} fmt/posix.h)
  set(FMT_SOURCES ${FMT_SOURCES} fmt/posix.cc)
endif ()

add_library(fmt STATIC ${FMT_HEADERS} ${FMT_SOURCES})
target_link_libraries(fmt PUBLIC EASTL)
target_compile_definitions(fmt INTERFACE FMT_USE_RVALUE_REFERENCES=1 FMT_USE_VARIADIC_TEMPLATES=1)
target_include_directories(fmt INTERFACE
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}>
    $<INSTALL_INTERFACE:${DEST_THIRDPARTY_HEADERS_DIR}>
)
if (BUILD_SHARED_LIBS)
    target_compile_definitions(fmt PUBLIC -DFMT_SHARED PRIVATE -DFMT_EXPORT)
endif ()
install (DIRECTORY fmt DESTINATION ${DEST_THIRDPARTY_HEADERS_DIR}/ FILES_MATCHING PATTERN *.h)

if (NOT URHO3D_MERGE_STATIC_LIBS)
    install(TARGETS fmt EXPORT Urho3D ARCHIVE DESTINATION ${DEST_ARCHIVE_DIR})
endif ()
