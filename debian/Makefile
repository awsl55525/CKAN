.PHONY: clean test repo

DESTDIR:=../_build/deb/fakeroot
EXESRC:=../_build/repack/Release/ckan.exe
EXEDEST:=$(DESTDIR)/usr/lib/ckan/ckan.exe
SCRIPTSRC:=ckan
SCRIPTDEST:=$(DESTDIR)/usr/bin/ckan
CONTROLSRC:=control.in
CONTROLDEST:=$(DESTDIR)/DEBIAN/control
CHANGELOGSRC:=../CHANGELOG.md
CHANGELOGDEST:=$(DESTDIR)/usr/share/doc/ckan/changelog.gz
DEBCHANGESRC:=changelog
DEBCHANGEDEST:=$(DESTDIR)/usr/share/doc/ckan/changelog.Debian.gz
MANSRC:=ckan.1
MANDEST:=$(DESTDIR)/usr/share/man/man1/ckan.1.gz
ICONSRC:=ckan.ico
ICONDEST:=$(DESTDIR)/usr/share/icons/ckan.ico
COPYRIGHTSRC:=copyright
COPYRIGHTDEST:=$(DESTDIR)/usr/share/doc/ckan/copyright
DESKTOPSRC:=ckan.desktop
DESKTOPDEST:=$(DESTDIR)/usr/share/applications/ckan.desktop
CONSOLEUIDESKTOPSRC:=ckan-consoleui.desktop
CONSOLEUIDESKTOPDEST:=$(DESTDIR)/usr/share/applications/ckan-consoleui.desktop
VERSION:=$(shell egrep '^\s*\#\#\s+v.*$$' $(CHANGELOGSRC) | head -1 | sed -e 's/^\s*\#\#\s\+v//' )
DEB:=../_build/deb/ckan_$(VERSION)_all.deb

# Files for /deb/ (the .deb file)
APT_REPO_ROOT:=../_build/deb/apt-repo-root
REPODEB:=$(APT_REPO_ROOT)/pool/ckan_$(VERSION)_all.deb

# Files for /deb/dists/{stable,nightly}/
# (Release, main/binary-all/Release, main/binary-all/Packages.gz,
#  actual path to be determined by yml files)
APT_REPO_DIST:=../_build/deb/apt-repo-dist
RELEASESRC:=Release.in
DISTRELEASEDEST:=$(APT_REPO_DIST)/Release
ARCHRELEASEDEST:=$(APT_REPO_DIST)/main/binary-all/Release
PACKAGES:=$(APT_REPO_DIST)/main/binary-all/Packages.gz

repo: $(REPODEB) $(DISTRELEASEDEST) $(ARCHRELEASEDEST) $(PACKAGES)

$(REPODEB): $(DEB)
	umask 0022 && mkdir -p $(shell dirname $@)
	umask 0022 && cp $< $@

$(DISTRELEASEDEST): $(RELEASESRC)
	umask 0022 && mkdir -p $(shell dirname $@)
	umask 0022 && sed -e 's/@VERSION@/$(VERSION)/' $< > $@

$(ARCHRELEASEDEST): $(RELEASESRC)
	umask 0022 && mkdir -p $(shell dirname $@)
	umask 0022 && sed -e 's/@VERSION@/$(VERSION)/' $< > $@

$(PACKAGES): $(REPODEB)
	umask 0022 && mkdir -p $(shell dirname $@)
	umask 0022 && (cd $(APT_REPO_ROOT) && dpkg-scanpackages -m pool) | gzip -c > $@

$(DEB): $(EXEDEST) $(SCRIPTDEST) $(CONTROLDEST) $(CHANGELOGDEST) $(DEBCHANGEDEST) $(MANDEST) $(ICONDEST) $(COPYRIGHTDEST) $(DESKTOPDEST) $(CONSOLEUIDESKTOPDEST)
	umask 0022 && mkdir -p $(shell dirname $@)
	fakeroot dpkg-deb --build $(DESTDIR)/ $@

$(SCRIPTDEST): $(SCRIPTSRC)
	umask 0022 && mkdir -p $(shell dirname $@)
	umask 0022 && cp $< $@

$(EXEDEST): $(EXESRC)
	umask 0022 && mkdir -p $(shell dirname $@)
	umask 0022 && cp $< $@

$(EXESRC):
	cd .. && ./build --configuration=Release

$(CONTROLDEST): $(CONTROLSRC) $(CHANGELOGSRC)
	umask 0022 && mkdir -p $(shell dirname $@)
	umask 0022 && sed -e 's/@VERSION@/$(VERSION)/' $< > $@

$(CHANGELOGDEST): $(CHANGELOGSRC)
	umask 0022 && mkdir -p $(shell dirname $@)
	umask 0022 && gzip --best -n -c $< > $@

$(DEBCHANGEDEST): $(DEBCHANGESRC)
	umask 0022 && mkdir -p $(shell dirname $@)
	umask 0022 && gzip --best -n -c $< > $@

$(MANDEST): $(MANSRC)
	umask 0022 && mkdir -p $(shell dirname $@)
	umask 0022 && gzip --best -n -c $< > $@

$(ICONDEST): $(ICONSRC)
	umask 0022 && mkdir -p $(shell dirname $@)
	umask 0022 && cp $< $@

$(COPYRIGHTDEST): $(COPYRIGHTSRC)
	umask 0022 && mkdir -p $(shell dirname $@)
	umask 0022 && cp $< $@

$(DESKTOPDEST): $(DESKTOPSRC)
	umask 0022 && mkdir -p $(shell dirname $@)
	umask 0022 && cp $< $@

$(CONSOLEUIDESKTOPDEST): $(CONSOLEUIDESKTOPSRC)
	umask 0022 && mkdir -p $(shell dirname $@)
	umask 0022 && cp $< $@

clean:
	rm -rf ../_build/deb

test: $(DEB)
	lintian $<
