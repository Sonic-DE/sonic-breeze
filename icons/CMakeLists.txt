set(BREEZE_ICON_INSTALL_DIR "${KDE_INSTALL_FULL_ICONDIR}/breeze")
set(BREEZE_DARK_ICON_INSTALL_DIR "${KDE_INSTALL_FULL_ICONDIR}/breeze-dark")

install(FILES breeze/index.theme
        DESTINATION "${BREEZE_ICON_INSTALL_DIR}")
install(FILES breeze-dark/index.theme
        DESTINATION "${BREEZE_DARK_ICON_INSTALL_DIR}")

# Breeze is an overlay on Silver. Only install icons that are not available
# from the inherited Silver themes; shared and Sonic-branded icons stay in one
# place and are resolved through icon-theme inheritance.
install(DIRECTORY breeze/places breeze/preferences
        DESTINATION "${BREEZE_ICON_INSTALL_DIR}")

gtk_update_icon_cache("${BREEZE_ICON_INSTALL_DIR}")
gtk_update_icon_cache("${BREEZE_DARK_ICON_INSTALL_DIR}")
