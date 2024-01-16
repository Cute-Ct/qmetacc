cmake_minimum_required(VERSION 3.0.0)
project(qmetacc VERSION 0.1.0 LANGUAGES C CXX)

set(CMAKE_CXX_STANDARD 20)
add_compile_options($<$<CXX_COMPILER_ID:MSVC>:/MP>)

add_executable(${PROJECT_NAME} main.cpp
  collectjson.cpp
  generator.cpp
  moc.cpp
  parser.cpp
  preprocessor.cpp)

target_compile_definitions(${PROJECT_NAME} PUBLIC 
  QT_USE_QSTRINGBUILDER                                       #for QStringBuilder operator%
  UNICODE
)

if(EXISTS "${CMAKE_CURRENT_SOURCE_DIR}/../qobject")
  message(STATUS "Building with qcorelib source code: " "${CMAKE_CURRENT_SOURCE_DIR}/../qobject")
  #build with qcorelib source code
  target_link_libraries(${PROJECT_NAME} qcorelib)

  #output the bin to be same as the library of qcorelib, easier to debug shared/dll library
  set_target_properties( ${PROJECT_NAME}
    PROPERTIES
    ARCHIVE_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/"
    LIBRARY_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/"
    RUNTIME_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/"
  )
else()
  #build with installed qcorelib
  find_package(QMetaClass REQUIRED)
  target_link_libraries(${PROJECT_NAME} QMetaClass::QMetaClass)
  get_target_property(QMetaClass_LIBRARY_DLL QMetaClass::QMetaClass LOCATION)
  message(STATUS "Path to the library's DLL file: ${QMetaClass_LIBRARY_DLL}")
  add_custom_command(TARGET ${PROJECT_NAME} POST_BUILD
      COMMAND ${CMAKE_COMMAND} -E copy_if_different
          ${QMetaClass_LIBRARY_DLL}
          $<TARGET_FILE_DIR:${PROJECT_NAME}>
  )  
endif()

target_include_directories(${PROJECT_NAME}                           #private header files
    PRIVATE "$<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/>"
)

include(CTest)
enable_testing()

set(CPACK_PROJECT_NAME ${PROJECT_NAME})
set(CPACK_PROJECT_VERSION ${PROJECT_VERSION})
include(CPack)
