cmake_minimum_required(VERSION 3.0.2)
project(roscap)

if(NOT WIN32)
  set_directory_properties(PROPERTIES COMPILE_OPTIONS "-Wall;-Wextra")
endif()

## Compile as C++17
add_compile_options(-std=c++17)

find_package(console_bridge REQUIRED)
find_package(catkin REQUIRED COMPONENTS cpp_common roscpp_serialization roscpp_traits rostime roslz4 std_msgs rosbag_storage rosconsole roscpp std_srvs topic_tools xmlrpcpp)
find_package(Boost REQUIRED COMPONENTS date_time filesystem program_options regex thread)
# find_package(crypto++ REQUIRED COMPONENTS crypto_crc)
# find_package(Zstd REQUIRED)
# find_package(LZ4 REQUIRED)
# find_package(fmt REQUIRED)

# catkin_python_setup()

include_directories(include
  ${catkin_INCLUDE_DIRS}
  ${Boost_INCLUDE_DIRS}
  ${console_bridge_INCLUDE_DIRS}
  # ${crypto++_INCLUDE_DIR}
  # ${Zstd_INCLUDE_DIR}
  # ${LZ4_INCLUDE_DIR}
  # ${fmt_INCLUDE_DIR}
)

catkin_package(
  LIBRARIES roscap
  INCLUDE_DIRS include
  CATKIN_DEPENDS roslz4 rosbag_storage rosconsole roscpp std_srvs topic_tools xmlrpcpp
  DEPENDS crypto++ fmt lz4 zstd
)

add_library(roscap
  src/buffer.cpp
  src/recorder.cpp)

target_link_libraries(roscap ${catkin_LIBRARIES} ${Boost_LIBRARIES}
  # ${crypto++_LIBRARIES}
  # ${Zstd_LIBRARIES}
  # ${LZ4_LIBRARIES}
  # ${fmt_LIBRARIES}
  crypto++
  fmt
  lz4
  zstd
)

add_executable(record src/record.cpp)
target_link_libraries(record roscap)

# add_executable(play src/play.cpp)
# target_link_libraries(play roscap)

install(DIRECTORY include/${PROJECT_NAME}/
  DESTINATION ${CATKIN_PACKAGE_INCLUDE_DESTINATION}
  FILES_MATCHING PATTERN "*.h")
install(TARGETS roscap
  ARCHIVE DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
  LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
  RUNTIME DESTINATION ${CATKIN_GLOBAL_BIN_DESTINATION})
install(TARGETS record #play
  ARCHIVE DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
  LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
  RUNTIME DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION})
# catkin_install_python(PROGRAMS
#   scripts/bag2png.py
#   scripts/bagsort.py
#   scripts/fastrebag.py
#   scripts/fixbag.py
#   scripts/fixbag_batch.py
#   scripts/fix_md5sums.py
#   scripts/fix_moved_messages.py
#   scripts/fix_msg_defs.py
#   scripts/makerule.py
#   scripts/savemsg.py
#   scripts/topic_renamer.py
#   DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION})

# if(CATKIN_ENABLE_TESTING)
#   catkin_add_nosetests(test)
# endif()
