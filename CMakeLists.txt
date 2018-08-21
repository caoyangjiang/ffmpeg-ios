cmake_minimum_required(VERSION 3.10)

project(ffmpeg-ios)

if(NOT TARGET uninstall)
  configure_file(
      ${CMAKE_CURRENT_SOURCE_DIR}/cmake_uninstall.cmake.in
      ${CMAKE_CURRENT_BINARY_DIR}/cmake_uninstall.cmake
      IMMEDIATE @ONLY)

  add_custom_target(uninstall
      COMMAND ${CMAKE_COMMAND} -P
      ${CMAKE_CURRENT_BINARY_DIR}/cmake_uninstall.cmake)
endif()


set(url "https://s3-us-west-2.amazonaws.com/ffmpeg-ios/ffmpeg-ios-3.4.2.zip")
set(md5 "0821e8b2058713194058c31d0fbc5036")

if( NOT EXISTS ${CMAKE_CURRENT_BINARY_DIR}/ffmpeg-ios.zip )
  message("Download from ${url}")
  file(DOWNLOAD ${url} ${CMAKE_CURRENT_BINARY_DIR}/ffmpeg-ios.zip
        EXPECTED_MD5 ${md5}
        SHOW_PROGRESS
  )
endif()

add_custom_command(OUTPUT ${CMAKE_BINARY_DIR}/ffmpeg-ios
  COMMAND ${CMAKE_COMMAND} -E tar xvf "${CMAKE_BINARY_DIR}/ffmpeg-ios.zip"
  DEPENDS ${CMAKE_CURRENT_BINARY_DIR}/ffmpeg-ios.zip
  WORKING_DIRECTORY "${CMAKE_BINARY_DIR}/"
  COMMENT "Unpacking ${CMAKE_CURRENT_BINARY_DIR}/ffmpeg-ios.zip"
  VERBATIM
)

add_custom_target(
  ffmpeg_ios_library ALL
  DEPENDS ${CMAKE_BINARY_DIR}/ffmpeg-ios
)

install(DIRECTORY ${CMAKE_BINARY_DIR}/include
  DESTINATION ${CMAKE_INSTALL_PREFIX}
  FILES_MATCHING PATTERN "*")

file(WRITE ${CMAKE_BINARY_DIR}/${CMAKE_PROJECT_NAME}Config.cmake
  "set(FFMPEG_IOS_INCLUDE_DIRS \"${CMAKE_INSTALL_PREFIX}/include\")\n"
  "set(FFMPEG_IOS_LIBRARY_DIR \"${CMAKE_INSTALL_PREFIX}/lib\")\n"
  "\n"
  "find_library(AVCODEC_IOS\n"
  "  NAMES avcodec\n"
  "  PATHS \${FFMPEG_IOS_LIBRARY_DIR}\n"
  "  NO_DEFAULT_PATH\n"
  "  NO_CMAKE_FIND_ROOT_PATH)\n"
  "\n"
  "find_library(AVDEVICE_IOS\n"
  "  NAMES avdevice\n"
  "  PATHS \${FFMPEG_IOS_LIBRARY_DIR}\n"
  "  NO_DEFAULT_PATH\n"
  "  NO_CMAKE_FIND_ROOT_PATH)\n"
  "\n"
  "find_library(AVFILTER_IOS\n"
  "  NAMES avfilter\n"
  "  PATHS \${FFMPEG_IOS_LIBRARY_DIR}\n"
  "  NO_DEFAULT_PATH\n"
  "  NO_CMAKE_FIND_ROOT_PATH)\n"
  "\n"
  "find_library(AVFORMAT_IOS\n"
  "  NAMES avdevice\n"
  "  PATHS \${FFMPEG_IOS_LIBRARY_DIR}\n"
  "  NO_DEFAULT_PATH\n"
  "  NO_CMAKE_FIND_ROOT_PATH)\n"
  "\n"
  "find_library(AVUTIL_IOS\n"
  "  NAMES avutil\n"
  "  PATHS \${FFMPEG_IOS_LIBRARY_DIR}\n"
  "  NO_DEFAULT_PATH\n"
  "  NO_CMAKE_FIND_ROOT_PATH)\n"
  "\n"
  "find_library(SWRESAMPLE_IOS\n"
  "  NAMES swresample\n"
  "  PATHS \${FFMPEG_IOS_LIBRARY_DIR}\n"
  "  NO_DEFAULT_PATH\n"
  "  NO_CMAKE_FIND_ROOT_PATH)\n"
  "\n"
  "find_library(SWSCALE_IOS\n"
  "  NAMES swscale\n"
  "  PATHS \${FFMPEG_IOS_LIBRARY_DIR}\n"
  "  NO_DEFAULT_PATH\n"
  "  NO_CMAKE_FIND_ROOT_PATH)\n"
  "\n"
  "set(FFMPEG_IOS_LIBRARIES\n"
  "  \${AVCODEC_IOS}\n"
  "  \${AVDEVICE_IOS}\n"
  "  \${AVFILTER_IOS}\n"
  "  \${AVFORMAT_IOS}\n"
  "  \${AVUTIL_IOS}\n"
  "  \${SWRESAMPLE_IOS}\n"
  "  \${SWSCALE_IOS}\n"
  "  CACHE PATH \"FFMPEG IOS libraries\")")


install(FILES ${CMAKE_BINARY_DIR}/${CMAKE_PROJECT_NAME}Config.cmake
        DESTINATION lib/cmake/${CMAKE_PROJECT_NAME})


install(FILES ${CMAKE_BINARY_DIR}/lib/libavcodec.a DESTINATION lib)
install(FILES ${CMAKE_BINARY_DIR}/lib/libavdevice.a DESTINATION lib)
install(FILES ${CMAKE_BINARY_DIR}/lib/libavfilter.a DESTINATION lib)
install(FILES ${CMAKE_BINARY_DIR}/lib/libavformat.a DESTINATION lib)
install(FILES ${CMAKE_BINARY_DIR}/lib/libavutil.a DESTINATION lib)
install(FILES ${CMAKE_BINARY_DIR}/lib/libswresample.a DESTINATION lib)
install(FILES ${CMAKE_BINARY_DIR}/lib/libswscale.a DESTINATION lib)
