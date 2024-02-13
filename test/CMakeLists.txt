enable_testing()
set(TESTS_ROOT ${CMAKE_CURRENT_SOURCE_DIR} PARENT_SCOPE)
file(GLOB test_srcs CONFIGURE_DEPENDS
  "${CMAKE_CURRENT_SOURCE_DIR}/*.h"
  "${CMAKE_CURRENT_SOURCE_DIR}/*.cpp"
)

# Not the google unit test
add_executable(ng_unit_tests c_api.cpp main.cpp tests.cpp)
target_include_directories(ng_unit_tests PRIVATE
  ${ORT_HEADER_DIR}
  ${CMAKE_SOURCE_DIR}/src
)
target_link_directories(ng_unit_tests PRIVATE ${ORT_LIB_DIR})
target_link_libraries(ng_unit_tests PRIVATE
  onnxruntime-genai-static
  ${ONNXRUNTIME_LIB}
)

# google unit test
add_executable(unit_tests test.cpp)
target_include_directories(unit_tests PRIVATE
  ${ORT_HEADER_DIR}
  ${CMAKE_SOURCE_DIR}/src
)

target_link_directories(unit_tests PRIVATE ${ORT_LIB_DIR})
target_link_libraries(unit_tests PRIVATE
  onnxruntime-genai-static
  ${ONNXRUNTIME_LIB}
  GTest::gtest_main
)
if(USE_CUDA AND CMAKE_CUDA_COMPILER)
  set_target_properties(unit_tests PROPERTIES LINKER_LANGUAGE CUDA)
  target_link_libraries(unit_tests PRIVATE cublasLt cublas cudnn curand cufft cudart)
endif()

source_group("Sources" FILES ${test_srcs})
set_property(DIRECTORY ${CMAKE_SOURCE_DIR} PROPERTY VS_STARTUP_PROJECT unit_tests)
#set_property(TARGET unit_tests PROPERTY TARGET_RUNTIME_LIBRARY_DIRS ${ORT_LIB_DIR})
include(GoogleTest)
gtest_discover_tests(unit_tests)