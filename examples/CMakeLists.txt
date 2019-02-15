cmake_minimum_required(VERSION 2.8)
project (crow_examples)
set(CROW_LIBS ${CMAKE_THREAD_LIBS_INIT} ${Boost_LIBRARIES})
if(OPENSSL_FOUND)
	include_directories(${OPENSSL_INCLUDE_DIR})
	set(CROW_LIBS ${CROW_LIBS} ${OPENSSL_LIBRARIES})
endif()
if(ZLIB_FOUND)
	add_definitions(-DHAVE_ZLIB)
	include_directories(${ZLIB_INCLUDE_DIR})
	set(CROW_LIBS ${CROW_LIBS} ${ZLIB_LIBRARIES})
endif()

if (MSVC)
	add_executable(example_vs example_vs.cpp)
	target_link_libraries(example_vs ${CROW_LIBS})
	add_executable(example_websocket websocket/example_ws.cpp)
	target_link_libraries(example_websocket ${CROW_LIBS})
	add_custom_command(OUTPUT ws.html
	    COMMAND ${CMAKE_COMMAND} -E
	    copy ${PROJECT_SOURCE_DIR}/websocket/templates/ws.html ${CMAKE_CURRENT_BINARY_DIR}/templates/ws.html
	    DEPENDS ${PROJECT_SOURCE_DIR}/websocket/templates/ws.html
	)
	add_custom_target(example_ws_copy ALL DEPENDS ws.html)
else ()
	add_executable(helloworld helloworld.cpp)
	target_link_libraries(helloworld ${CROW_LIBS})

if (OPENSSL_FOUND)
	add_executable(example_ssl ssl/example_ssl.cpp)
	target_link_libraries(example_ssl ${CROW_LIBS})
endif()

add_executable(example_websocket websocket/example_ws.cpp)
target_link_libraries(example_websocket ${CROW_LIBS})
add_custom_command(OUTPUT ws.html
    COMMAND ${CMAKE_COMMAND} -E
    copy ${PROJECT_SOURCE_DIR}/websocket/templates/ws.html ${CMAKE_CURRENT_BINARY_DIR}/templates/ws.html
    DEPENDS ${PROJECT_SOURCE_DIR}/websocket/templates/ws.html
)
add_custom_target(example_ws_copy ALL DEPENDS ws.html)

add_executable(example example.cpp)
#target_link_libraries(example crow)
target_link_libraries(example ${CROW_LIBS})

if (Tcmalloc_FOUND)
	target_link_libraries(example ${Tcmalloc_LIBRARIES})
endif(Tcmalloc_FOUND)

add_executable(example_with_all example_with_all.cpp)
add_dependencies(example_with_all amalgamation)
#target_link_libraries(example crow)
target_link_libraries(example_with_all ${CROW_LIBS})

add_custom_command(OUTPUT example_test.py
	COMMAND ${CMAKE_COMMAND} -E
	copy ${PROJECT_SOURCE_DIR}/example_test.py ${CMAKE_CURRENT_BINARY_DIR}/example_test.py
	DEPENDS ${PROJECT_SOURCE_DIR}/example_test.py 
)
add_custom_target(example_copy ALL DEPENDS example_test.py)

add_executable(example_chat example_chat.cpp)
target_link_libraries(example_chat ${CROW_LIBS})
add_custom_command(OUTPUT example_chat.html
	COMMAND ${CMAKE_COMMAND} -E
	copy ${PROJECT_SOURCE_DIR}/example_chat.html ${CMAKE_CURRENT_BINARY_DIR}/example_chat.html
	DEPENDS ${PROJECT_SOURCE_DIR}/example_chat.html 
)
add_custom_target(example_chat_copy ALL DEPENDS example_chat.html)

#SET( CMAKE_CXX_FLAGS  "${CMAKE_CXX_FLAGS} -g -pg" )
#SET( CMAKE_EXE_LINKER_FLAGS  "${CMAKE_EXE_LINKER_FLAGS} -g -pg" )
endif()
