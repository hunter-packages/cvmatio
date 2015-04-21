# build the project
set(BUILD_EXECUTABLE False)
if(BUILD_EXECUTABLE)
    set(SRC_FILES MatlabIO.cpp main.cpp)
    add_executable(${PROJECT_NAME} ${SRC_FILES})
else()
    set(SRC_FILES MatlabIO.cpp)
    add_library(${PROJECT_NAME} SHARED ${SRC_FILES})
endif()

#link
target_link_libraries(${PROJECT_NAME}   ${Boost_LIBRARIES} 
                                        ${OpenCV_LIBS} 
                                        ${ZLIB_LIBRARIES}
)

set(CVMATIO_PUBLIC_HEADERS 
  ../include/EFStream.hpp
  ../include/MatlabIO.hpp
  ../include/MatlabIOContainer.hpp
  ../include/typetraits.hpp
)

message("CVMATIO_PUBLIC_HEADERS ${CVMATIO_PUBLIC_HEADERS}")

set_target_properties(${PROJECT_NAME} PROPERTIES PUBLIC_HEADER "${CVMATIO_PUBLIC_HEADERS}")

# install
install(TARGETS ${PROJECT_NAME} RUNTIME DESTINATION bin
                                LIBRARY DESTINATION lib
				ARCHIVE DESTINATION lib
				PUBLIC_HEADER DESTINATION include/cvmatio
)
