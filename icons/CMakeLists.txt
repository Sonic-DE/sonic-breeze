set(BREEZE_ICON_INSTALL_DIR "${KDE_INSTALL_FULL_ICONDIR}/breeze")
set(BREEZE_DARK_ICON_INSTALL_DIR "${KDE_INSTALL_FULL_ICONDIR}/breeze-dark")

install(FILES breeze/index.theme
        DESTINATION "${BREEZE_ICON_INSTALL_DIR}")
install(FILES breeze-dark/index.theme
        DESTINATION "${BREEZE_DARK_ICON_INSTALL_DIR}")

# Breeze provides a full pre-sonic icon set. Sonic branding icons (start-here-sonicde*,
# folder-activities*) are deliberately excluded so they resolve through
# Inherits=silver and apply to all child themes.
install(DIRECTORY breeze/
        DESTINATION "${BREEZE_ICON_INSTALL_DIR}"
        PATTERN "index.theme" EXCLUDE)
install(DIRECTORY breeze/
        DESTINATION "${BREEZE_DARK_ICON_INSTALL_DIR}"
        PATTERN "index.theme" EXCLUDE)

gtk_update_icon_cache("${BREEZE_ICON_INSTALL_DIR}")
gtk_update_icon_cache("${BREEZE_DARK_ICON_INSTALL_DIR}")
