add_library(CPPTEMPLATE_SRC OBJECT
   lib.cpp
)

if(COVERAGE)
    target_link_libraries(CPPTEMPLATE_SRC coverage_config)
endif()