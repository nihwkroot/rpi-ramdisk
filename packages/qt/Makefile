include ../toolchain.mk

PREFIX := /opt/qt
HOSTPREFIX := $(BASEDIR)/qt-host

REPOS := qtbase qtwebengine qtwebkit qtdeclarative qtwebsockets qtwebview qtwebglplugin qtxmlpatterns
SUBREPOS := qtwebglplugin qtwebview qtdeclarative qtxmlpatterns qtwebsockets qtwebkit qtwebengine

ifeq ("$(VERBOSE_QT_BUILD)","1")
REDIRECT :=
else
REDIRECT := > /dev/null
endif

all: qt.tar.gz

qt.tar.gz: sysroot.part $(shell for repo in $(REPOS); do echo $$repo.timestamp; done )
	#rm -rf --one-file-system $(STAGE) $(HOSTPREFIX)
	#for repo in $(REPOS); do git -C $$repo clean -dfxq; done

	cd qtbase && ./configure -release -opengl es2 -device linux-rasp-pi2-g++ \
		-qpa eglfs -no-libinput -no-linuxfb -no-xcb -no-kms -no-gbm \
		-no-gtk -no-widgets -no-compile-examples -no-sql-tds \
		-device-option CROSS_COMPILE=$(TOOLCHAIN) -sysroot $(SYSROOT) \
		-opensource -confirm-license -make libs -strip -optimize-size \
		-prefix $(PREFIX) -extprefix $(STAGE)/$(PREFIX) -hostprefix $(HOSTPREFIX) $(REDIRECT)

	$(MAKE) -C qtbase $(REDIRECT)
	$(MAKE) -C qtbase install $(REDIRECT)
	cd $(BASEDIR)
	for repo in $(SUBREPOS); do \
		cd $$repo && git submodule update --init \
		../qt-host/bin/qmake $(REDIRECT); \
		cd $(BASEDIR); \
		$(MAKE) -C $$repo $(REDIRECT); \
		$(MAKE) -C $$repo install $(REDIRECT); \
	done

	mkdir -p $(STAGE)/etc/ld.so.conf.d
	echo $(PREFIX)/lib > $(STAGE)/etc/ld.so.conf.d/opt-qt.conf

	tar -C $(STAGE) --exclude=.$(PREFIX)/doc --exclude=.$(PREFIX)/include \
		--exclude=.$(PREFIX)/lib/cmake --exclude=.$(PREFIX)/lib/pkgconfig \
		-czf qt.tar.gz .

include ../../include/timestamp.mk

pull:
	git submodule update --remote .

install: qt.tar.gz
	tar -xf qt.tar.gz -C $(DESTDIR)

clean:
	for repo in $(REPOS); do git -C $$repo clean -dfxq; done
	rm -rf --one-file-system $(STAGE) $(HOSTPREFIX) *.timestamp qt.tar.gz

.PHONY: clean install
