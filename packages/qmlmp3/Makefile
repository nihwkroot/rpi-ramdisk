include ../toolchain.mk

PREFIX := /opt/qt

REPOS := qmlmp3

all: qmlmp3.tar.gz

qmlmp3.tar.gz: qmlmp3.service $(shell for repo in $(REPOS); do echo $$repo.timestamp; done )
	rm -rf --one-file-system $(STAGE)
	mkdir -p $(STAGE)/etc/systemd/system
	mkdir -p $(STAGE)/opt/qt/bin

	cd qmlmp3 && ../../qt/qt-host/bin/qmake
	$(MAKE) -C qmlmp3
	cp qmlmp3/qmlmp3 $(STAGE)/opt/qt/bin/

	cp qmlmp3.service $(STAGE)/etc/systemd/system/

	tar -C $(STAGE) -czf qmlmp3.tar.gz .

include ../../include/timestamp.mk

install: qmlmp3.tar.gz
	tar -xf qmlmp3.tar.gz -C $(DESTDIR)
	$(MYCHROOT) $(DESTDIR) /bin/systemctl reenable qmlmp3.service

clean:
	rm -rf --one-file-system $(STAGE) qmlmp3.tar.gz

.PHONY: clean install
