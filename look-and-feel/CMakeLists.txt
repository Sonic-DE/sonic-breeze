# Keep the established package IDs used by Plasma and existing user settings.
# The trailing slash copies each source package's contents into the explicitly
# named destination rather than installing the source directory name.
install(DIRECTORY org.kde.breeze/
        DESTINATION ${KDE_INSTALL_DATADIR}/plasma/look-and-feel/org.kde.breeze.desktop
        PATTERN "Messages.sh" EXCLUDE)
install(DIRECTORY org.kde.breezedark/
        DESTINATION ${KDE_INSTALL_DATADIR}/plasma/look-and-feel/org.kde.breezedark.desktop
        PATTERN "Messages.sh" EXCLUDE)
install(DIRECTORY org.kde.breezetwilight/
        DESTINATION ${KDE_INSTALL_DATADIR}/plasma/look-and-feel/org.kde.breezetwilight.desktop
        PATTERN "Messages.sh" EXCLUDE)
