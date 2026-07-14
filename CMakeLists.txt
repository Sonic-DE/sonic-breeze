cmake_minimum_required(VERSION 3.16)
project(breeze)
set(PROJECT_VERSION "6.7.4")

set(KF6_MIN_VERSION "6.26.0")
set(KDE_COMPILERSETTINGS_LEVEL "5.82")
set(QT_MIN_VERSION "6.10.0")

include(GenerateExportHeader)
include(WriteBasicConfigVersionFile)
include(FeatureSummary)

find_package(ECM ${KF6_MIN_VERSION} REQUIRED NO_MODULE)
set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} ${ECM_MODULE_PATH} ${CMAKE_SOURCE_DIR}/cmake ${CMAKE_SOURCE_DIR}/gtk/cmake)

set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_EXPORT_COMPILE_COMMANDS ON)

option(BUILD_TESTING "Build the testing tree." ON)
if(BUILD_TESTING)
    enable_testing()
endif()

include(CMakePackageConfigHelpers)
include(ECMInstallIcons)
include(ECMQtDeclareLoggingCategory)
include(KDECompilerSettings NO_POLICY_SCOPE)
include(KDEClangFormat)
include(KDEGitCommitHooks)

include(GtkUpdateIconCache)

find_package(Sass REQUIRED)
find_package(PythonCairo REQUIRED)


option(BUILD_CURSOR "Build Breeze Cursor" ON)
option(BUILD_WITH_QTQUICK "Build with Qt Quick features enabled" ON)

set(QT_NO_CREATE_VERSIONLESS_TARGETS ON)
set(QT_NO_CREATE_VERSIONLESS_FUNCTIONS ON)

include(KDEInstallDirs6)
include(KDECMakeSettings)

if(UNIX AND NOT APPLE AND NOT ANDROID)
    find_package(Qt6 ${QT_MIN_VERSION} CONFIG REQUIRED DBus)
    set(HAVE_QTDBUS ${Qt6DBus_FOUND})

    option(WITH_DECORATIONS "Build Breeze window decorations for KWin" ON)
    option(WITH_WALLPAPERS "Install Breeze default wallpapers" ON)

    find_package(KF6FrameworkIntegration ${KF6_MIN_VERSION} CONFIG)
    set_package_properties(KF6FrameworkIntegration PROPERTIES
        DESCRIPTION "KF6 Framework Integration"
        URL "https://projects.kde.org/projects/frameworks/frameworkintegration"
        TYPE OPTIONAL
        PURPOSE "Required to use KStyle convenience functionalities in style")

    find_package(KF6KCMUtils ${KF6_MIN_VERSION} REQUIRED)
    set_package_properties(KF6KCMUtils PROPERTIES
        TYPE REQUIRED
        DESCRIPTION "Helps create configuration modules"
        PURPOSE "KCMUtils used for the configuration modules or the decoration and Qt Style")
else()
    set(WITH_DECORATIONS OFF)
    set(WITH_WALLPAPERS OFF)
endif()

find_package(Qt6 ${QT_MIN_VERSION} REQUIRED CONFIG COMPONENTS Widgets Core Svg)

find_package(KF6 ${KF6_MIN_VERSION} REQUIRED COMPONENTS
    CoreAddons
    ColorScheme
    Config
    GuiAddons
    I18n
    IconThemes
    WindowSystem)

if(BUILD_WITH_QTQUICK)
    find_package(Qt6 ${QT_MIN_VERSION} REQUIRED COMPONENTS Quick)
endif()

set(BREEZE_HAVE_KSTYLE ${KF6FrameworkIntegration_FOUND})
set(BREEZE_HAVE_QTQUICK ${Qt6Quick_FOUND})

add_subdirectory(kstyle)
add_subdirectory(libbreezecommon)
add_subdirectory(misc)
add_subdirectory(desktoptheme)
add_subdirectory(look-and-feel)
add_subdirectory(gtk)

if(WITH_DECORATIONS)
    find_package(KDecoration3 REQUIRED)
    add_subdirectory(kdecoration)
endif()
if(WITH_WALLPAPERS)
    add_subdirectory(wallpapers)
endif()

ki18n_install(po)

include(ECMSetupVersion)
ecm_setup_version(${PROJECT_VERSION} VARIABLE_PREFIX BREEZE
    PACKAGE_VERSION_FILE "${CMAKE_CURRENT_BINARY_DIR}/SonicBreezeConfigVersion.cmake")
set(CMAKECONFIG_INSTALL_DIR "${KDE_INSTALL_CMAKEPACKAGEDIR}/SonicBreeze")

configure_package_config_file("${CMAKE_CURRENT_SOURCE_DIR}/BreezeConfig.cmake.in"
                              "${CMAKE_CURRENT_BINARY_DIR}/SonicBreezeConfig.cmake"
                              PATH_VARS KDE_INSTALL_FULL_DATADIR
                              INSTALL_DESTINATION ${CMAKECONFIG_INSTALL_DIR})
install(FILES "${CMAKE_CURRENT_BINARY_DIR}/SonicBreezeConfig.cmake"
              "${CMAKE_CURRENT_BINARY_DIR}/SonicBreezeConfigVersion.cmake"
        DESTINATION "${CMAKECONFIG_INSTALL_DIR}"
        COMPONENT Devel)

unset(QUERY_EXECUTABLE CACHE)

add_subdirectory(colors)
if(BUILD_CURSOR)
    add_subdirectory(cursors)
endif()

# add clang-format target for all our real source files
file(GLOB_RECURSE ALL_CLANG_FORMAT_SOURCE_FILES *.cpp *.h)
list(FILTER ALL_CLANG_FORMAT_SOURCE_FILES EXCLUDE REGEX ".*\\.ccls-cache/.*")
kde_clang_format(${ALL_CLANG_FORMAT_SOURCE_FILES})
kde_configure_git_pre_commit_hook(CHECKS CLANG_FORMAT)

feature_summary(WHAT ALL FATAL_ON_MISSING_REQUIRED_PACKAGES)
