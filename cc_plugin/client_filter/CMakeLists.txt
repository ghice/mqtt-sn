
function (cc_plugin_mqttsn_client_filter)
    set (name "mqttsn_client_filter")
    
    if (NOT Qt5Core_FOUND)
        message(WARNING "Can NOT build ${name} due to missing Qt5Core library")
        return()
    endif ()
    
    if (NOT Qt5Widgets_FOUND)
        message(WARNING "Can NOT build ${name} due to missing Qt5Widgets library")
        return()
    endif ()
    
    set (meta_file "${CMAKE_CURRENT_SOURCE_DIR}/mqttsn_client_filter.json")
    set (stamp_file "${CMAKE_CURRENT_BINARY_DIR}/refresh_stamp.txt")
    
    set (refresh_plugin_header TRUE)
    if ((NOT EXISTS ${stamp_file}) OR (${meta_file} IS_NEWER_THAN ${stamp_file}))
        execute_process(
            COMMAND ${CMAKE_COMMAND} -E touch ${CMAKE_CURRENT_SOURCE_DIR}/Plugin.h)
        execute_process(
            COMMAND ${CMAKE_COMMAND} -E touch ${stamp_file})
    endif ()
    
    
    set (src
        Plugin.cpp
        Filter.cpp
        widget/PubSubWidget.cpp
        widget/SubListWidget.cpp
        widget/FilterConfigWidget.cpp
    )
    
    set (hdr
        Plugin.h
        Filter.h
        widget/PubSubWidget.h
        widget/SubListWidget.h
        widget/FilterConfigWidget.h
    )
    
    qt5_wrap_cpp(
        moc
        ${hdr}
    )
    
    qt5_wrap_ui(
        ui
        ui/PubSubWidget.ui
        ui/SubListElementWidget.ui
        ui/SubListWidget.ui
        ui/FilterConfigWidget.ui
    )
    
    qt5_add_resources(resources ${CMAKE_CURRENT_SOURCE_DIR}/ui.qrc)
    
    add_library (${name} SHARED ${src} ${moc} ${ui} ${resources})
    target_link_libraries (${name} ${DEFAULT_CLIENT_LIB_TGT} ${CC_PLUGIN_LIBRARIES})
    qt5_use_modules(${name} Core)
    
    install (
        TARGETS ${name}
        DESTINATION ${PLUGIN_INSTALL_DIR})

    if (MQTTSN_CC_PLUGIN_COPY_TO_CC_INSTALL_PATH)        
        install (
            TARGETS ${name}
            DESTINATION ${CC_PLUGIN_DIR})
     endif ()
    
endfunction()

######################################################################

find_package(Qt5Core)
find_package(Qt5Widgets)

include_directories(
    ${CMAKE_BINARY_DIR}/client/src/basic
    ${CMAKE_SOURCE_DIR}/client/include
    ${CMAKE_CURRENT_BINARY_DIR}
    ${CMAKE_CURRENT_SOURCE_DIR}
)

cc_plugin_mqttsn_client_filter ()