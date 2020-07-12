cmake_minimum_required(VERSION 3.14)
project(untitled1)

set(CMAKE_CXX_STANDARD 14)

#MACRO(HEADER_DIRECTORIES return_list)
#    FILE(GLOB_RECURSE new_list *.h *.hpp)
#    SET(dir_list "")
#    FOREACH(file_path ${new_list})
#        GET_FILENAME_COMPONENT(dir_path ${file_path} PATH)
#        SET(dir_list ${dir_list} ${dir_path})
#    ENDFOREACH()
#    LIST(REMOVE_DUPLICATES dir_list)
#    SET(${return_list} ${dir_list})
#ENDMACRO()
#
#HEADER_DIRECTORIES(header_list)
#include_directories(${header_list})
function(include_sub_directories_recursively root_dir ignore_dirs)
    if (NOT IS_DIRECTORY ${root_dir})               # 当前路径是一个目录吗，是的话就加入到包含目录
        return()
    endif()
    GET_FILENAME_COMPONENT(dirname ${root_dir} NAME)
    list(FIND ignore_dirs ${dirname} index)

    if (${index} GREATER -1)
        message("dir " ${dirname} " is excluded")
        return()
    endif()

    #        if (${root_dir} MATCHES "include")
    message("include dir: " ${root_dir})
    include_directories(${root_dir})
    #        endif()

    file(GLOB ALL_SUB RELATIVE ${root_dir} ${root_dir}/*) # 获得当前目录下的所有文件，让如ALL_SUB列表中
    foreach(sub ${ALL_SUB})
        if (IS_DIRECTORY ${root_dir}/${sub})
            include_sub_directories_recursively(${root_dir}/${sub} "${ignore_dirs}") # 对子目录递归调用，包含
        endif()
    endforeach()
endfunction()


set(ignore_dirs ".vscode" ".idea" "cmake-build-debug")
include_sub_directories_recursively(${CMAKE_SOURCE_DIR} "${ignore_dirs}")

file(GLOB_RECURSE SOURCES ./**.cpp ./**.cc)
add_executable(untitled1 ${SOURCES})
