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
set(md5 "0964b77d1f94243f93eea7cdf151fa9c")

if( NOT EXISTS ${CMAKE_CURRENT_BINARY_DIR}/ffmpeg-ios.zip )
  message("Download from ${url}")
  file(DOWNLOAD ${url} ${CMAKE_CURRENT_BINARY_DIR}/ffmpeg-ios.zip
        EXPECTED_MD5 ${md5}
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
  "find_library(FFMPEG_IOS_LIBRARIES NAMES
  \"libavcodec_ios.a\"
  \"libavdevice_ios.a\"
  \"libavfilter_ios.a\"
  \"libformat_ios.a\"
  \"libavutil_ios.a\"
  \"libswresample_ios\"
  \"libswscale_ios.a\"\n"
  "PATHS \${FFMPEG_IOS_LIBRARY_DIR} NO_DEFAULT_PATH)\n")

install(FILES ${CMAKE_BINARY_DIR}/${CMAKE_PROJECT_NAME}Config.cmake
        DESTINATION lib/cmake/${CMAKE_PROJECT_NAME})


install(FILES ${CMAKE_BINARY_DIR}/lib/libavcodec_ios.a DESTINATION lib)
install(FILES ${CMAKE_BINARY_DIR}/lib/libavdevice_ios.a DESTINATION lib)
install(FILES ${CMAKE_BINARY_DIR}/lib/libavfilter_ios.a DESTINATION lib)
install(FILES ${CMAKE_BINARY_DIR}/lib/libavformat_ios.a DESTINATION lib)
install(FILES ${CMAKE_BINARY_DIR}/lib/libavutil_ios.a DESTINATION lib)
install(FILES ${CMAKE_BINARY_DIR}/lib/libswresample_ios.a DESTINATION lib)
install(FILES ${CMAKE_BINARY_DIR}/lib/libswscale_ios.a DESTINATION lib)
