BUNDLE_IDENTIFIER   = com.fourjs.scanner
APPNAME             = CDVScanner
DEVICE              = phone
MODE                = debug
IDENTITY='(59W7RY75ZU)'
PROVISIONING_PROFILE=/Users/leo/Library/MobileDevice/Provisioning\ Profiles/4js_wildcard

ifndef GMIDIR
GMIDIR=$(FGLDIR)
endif

#GMA settings.. GMA needs an absolute path to the plugin
#so we use some gnu make features to build it
ABSPATH=$(abspath .)
DEMODIR=$(shell  dirname $(ABSPATH))
PLUGINDIR=$(shell dirname $(DEMODIR))
PLUGIN=$(notdir $(PLUGINDIR))
GMIPLUGIN=$(PLUGIN)
#GMI
# in case you build the demo when having the plugin checked out of github 
# then we can use a relative build path for GMI
ifneq ($(wildcard ../../fgldemo),)
  GMIPLUGIN=$(CURDIR)/../..
#  FGLLDPATH=../../fgl
endif

MODULES  = $(patsubst %.4gl, %.42m, $(wildcard *.4gl))
FORMS    = $(patsubst %.per, %.42f, $(wildcard *.per))

%.42m: %.4gl
	fglcomp -M $*

%.42f: %.per
	fglform -M $*

all:: $(MODULES) $(FORMS) 

define gmibuild
	gmibuildtool --app-name=$(APPNAME) --bundle-id=$(BUNDLE_IDENTIFIER) --provisioning=$(PROVISIONING_PROFILE) --certificate=$(IDENTITY) --device=$(DEVICE) --mode=$(MODE) --build-cordova=$(GMIPLUGIN) 
endef

gmi.build: all
	$(call gmibuild,no)

gmi.install: all
	$(call gmibuild,yes)

../../../cordova-plugin-compat:
	cd ../../.. && git clone https://github.com/apache/cordova-plugin-compat && cd -

gma.build: all ../../../cordova-plugin-compat
#build the native plugin out of this repository  and install it in GMA
	cd ../../.. && gmabuildtool scaffold --install-plugins $(PLUGIN),cordova-plugin-compat && cd -
	gmabuildtool build --build-quietly --build-cordova $(PLUGIN),cordova-plugin-compat --build-app-package-name $(BUNDLE_IDENTIFIER) --build-app-name $(APPNAME) -bih gma_icons/ic_app_hdpi.png -bim gma_icons/ic_app_mdpi.png -bixh gma_icons/ic_app_xhdpi.png -bixxh gma_icons/ic_app_xxhdpi.png

#GMA_DEBUG=-d
gma.install: 
	$$ANDROID_SDK_ROOT/platform-tools/adb $(GMA_DEBUG) install -r `find . -name app-unsigned.apk`

echo::
	echo "PLUGIN=$(PLUGIN)"
	echo "GMIPLUGIN=$(GMIPLUGIN)"

clean::
	rm -f *.sch *.dbs *.42*
	rm -rf build bin distbin gma gmi/appRoot
	rm -f cordovamap.txt gmi/*.a gmi/link_flags.sh *.4pwvs *.4pwdb ../../src/ios/*.a
	rm -f ../../pluginCalls.*
