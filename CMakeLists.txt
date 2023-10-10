cmake_minimum_required(VERSION 3.0.0)
project(mcc VERSION 0.1.0 LANGUAGES C CXX)

set(CMAKE_CXX_STANDARD 20)
add_compile_options($<$<CXX_COMPILER_ID:MSVC>:/MP>)

add_executable(mcc main.cpp
  collectjson.cpp
  generator.cpp
  moc.cpp
  parser.cpp
  preprocessor.cpp)

target_compile_definitions(${PROJECT_NAME} PUBLIC 
  QT_USE_QSTRINGBUILDER                                       #for QStringBuilder operator%
  UNICODE
)

find_package(QMetaClass REQUIRED)
target_link_libraries(${PROJECT_NAME} QMetaClass::QMetaClass)
get_target_property(QMetaClass_LIBRARY_DLL QMetaClass::QMetaClass LOCATION)
message(STATUS "Path to the library's DLL file: ${QMetaClass_LIBRARY_DLL}")
add_custom_command(TARGET ${PROJECT_NAME} POST_BUILD
    COMMAND ${CMAKE_COMMAND} -E copy_if_different
        ${QMetaClass_LIBRARY_DLL}
        $<TARGET_FILE_DIR:mcc>
)

target_include_directories(${PROJECT_NAME}                           #private header files
    PRIVATE "$<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/>"
)

include(CTest)
enable_testing()

set(CPACK_PROJECT_NAME ${PROJECT_NAME})
set(CPACK_PROJECT_VERSION ${PROJECT_VERSION})
include(CPack)
