# add_library(api api.cpp)
# target_link_libraries(api memory_pool fmt_cache fmt_spec_defect_handlers STLSoft::STLSoft)
#
# add_library(fmt_cache fmt_cache.cpp)
# target_link_libraries(fmt_cache STLSoft::STLSoft)
#
# add_library(fmt_spec_defect_handlers fmt_spec_defect_handlers.cpp)
# target_link_libraries(fmt_spec_defect_handlers STLSoft::STLSoft)
#
# add_library(init_code_strings init_code_strings.cpp)
# target_link_libraries(init_code STLSoft::STLSoft)
#
# add_library(memory_pool memory_pool.cpp)
# target_link_libraries(memory_pool STLSoft::STLSoft)
#
# add_library(replacements replacements.cpp)
# target_link_libraries(replacements api STLSoft::STLSoft)
#
# add_library(snprintf snprintf.cpp)
# target_link_libraries(snprintf STLSoft::STLSoft)

add_library(FastFormat
  api.cpp fmt_cache.cpp fmt_spec_defect_handlers.cpp
  init_code_strings.cpp memory_pool.cpp replacements.cpp
  snprintf.cpp)
target_link_libraries(FastFormat STLSoft::STLSoft)

set_target_properties(FastFormat PROPERTIES
    OUTPUT_NAME "fastformat")
target_include_directories(FastFormat PUBLIC
    $<BUILD_INTERFACE:${CMAKE_SOURCE_DIR}/include>
    $<INSTALL_INTERFACE:include>)

install(TARGETS FastFormat
    EXPORT project-targets
    ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
    LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
    RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR}
)
# There is so many headers, we have to manually copy them.
install(DIRECTORY
    ${CMAKE_SOURCE_DIR}/include/fastformat
    DESTINATION ${CMAKE_INSTALL_INCLUDEDIR})

