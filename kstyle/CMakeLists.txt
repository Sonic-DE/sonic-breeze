################# includes #################
include_directories(
  animations
  debug
)

include_directories(${CMAKE_SOURCE_DIR}/libbreezecommon)
include_directories(${CMAKE_BINARY_DIR}/libbreezecommon)

################# configuration #################
configure_file(config-breeze.h.cmake ${CMAKE_CURRENT_BINARY_DIR}/config-breeze.h )

########### next target ###############
set(breeze_PART_SRCS
    animations/breezeanimation.cpp
    animations/breezeanimations.cpp
    animations/breezeanimationdata.cpp
    animations/breezebaseengine.cpp
    animations/breezebusyindicatordata.cpp
    animations/breezebusyindicatorengine.cpp
    animations/breezedialdata.cpp
    animations/breezedialengine.cpp
    animations/breezeenabledata.cpp
    animations/breezegenericdata.cpp
    animations/breezeheaderviewdata.cpp
    animations/breezeheaderviewengine.cpp
    animations/breezescrollbardata.cpp
    animations/breezescrollbarengine.cpp
    animations/breezespinboxengine.cpp
    animations/breezespinboxdata.cpp
    animations/breezestackedwidgetdata.cpp
    animations/breezestackedwidgetengine.cpp
    animations/breezetabbarengine.cpp
    animations/breezetabbardata.cpp
    animations/breezetoolboxengine.cpp
    animations/breezetransitiondata.cpp
    animations/breezetransitionwidget.cpp
    animations/breezewidgetstateengine.cpp
    animations/breezewidgetstatedata.cpp
    debug/breezewidgetexplorer.cpp
    breezeaddeventfilter.cpp
    breezeblurhelper.cpp
    breezeframeshadow.cpp
    breezehelper.cpp
    breezemdiwindowshadow.cpp
    breezemnemonics.cpp
    breezepropertynames.cpp
    breezeshadowhelper.cpp
    breezesplitterproxy.cpp
    breezestyle.cpp
    breezestyleplugin.cpp
    breezetileset.cpp
    breezewindowmanager.cpp
    breezetoolsareamanager.cpp
)

kconfig_add_kcfg_files(breeze_PART_SRCS ../kdecoration/breezesettings.kcfgc)
kconfig_add_kcfg_files(breeze_PART_SRCS breezestyleconfigdata.kcfgc)


add_library(breeze6 MODULE ${breeze_PART_SRCS})

ecm_qt_declare_logging_category(breeze6
    HEADER
        breeze_logging.h
    IDENTIFIER
        BREEZE
    CATEGORY_NAME
        breeze6
    DEFAULT_SEVERITY
        Warning
)

target_link_libraries(breeze6
    Qt6::Core
    Qt6::Gui
    Qt6::Widgets
)

if(HAVE_QTDBUS)
    target_link_libraries(breeze6
        Qt6::DBus
    )
endif()

if( BREEZE_HAVE_QTQUICK )
    target_link_libraries(breeze6
        Qt6::Quick
        KF6::CoreAddons
    )
endif()

target_link_libraries(breeze6
    KF6::CoreAddons
    KF6::ConfigCore
    KF6::ConfigGui
    KF6::GuiAddons
    KF6::IconThemes
    KF6::WindowSystem
    KF6::ColorScheme
)

target_link_libraries(breeze6 breezecommon6)

if(KF6FrameworkIntegration_FOUND)
    target_link_libraries(breeze6 KF6::Style)
endif()


if (WIN32)
    # As stated in https://docs.microsoft.com/en-us/cpp/c-runtime-library/math-constants M_PI only gets defined
    # when if _USE_MATH_DEFINES is defined
    target_compile_definitions(breeze6 PRIVATE _USE_MATH_DEFINES _BSD_SOURCE)
endif()


########### install files ###############
install(TARGETS breeze6 DESTINATION ${KDE_INSTALL_QTPLUGINDIR}/styles/)
install(FILES breeze.themerc  DESTINATION  ${KDE_INSTALL_DATADIR}/kstyle/themes)

########### subdirectories ###############

if(TARGET "KF6::KCMUtils")
    add_subdirectory(config)
endif()
