bin_PROGRAMS = app01
app01_SOURCES = app01.c

package:
	sudo checkinstall --install=no --pkgname $(bin_PROGRAMS)

clean-local:
	sudo checkinstall uninstall $(bin_PROGRAMS)
