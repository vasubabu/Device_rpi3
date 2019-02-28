### Android 9 Pie configuration for Raspbery Pi 3 Model B & B+

#### Setup build environment
The build host is x86_64 platform run ubuntu-16.04 (refer: https://source.android.com/setup/build/initializing)
1. Install JDK

$ sudo apt-get update
$ sudo apt-get install openjdk-8-jdk

2. Install other tools
$ sudo apt-get install git-core gnupg flex bison gperf build-essential zip curl zlib1g-dev gcc-multilib g++-multilib libc6-dev-i386 lib32ncurses5-dev x11proto-core-dev libx11-dev lib32z-dev libgl1-mesa-dev libxml2-utils xsltproc unzip

3. Install repo command to download Android 9 Pie project source code

$ mkdir ~/bin
$ PATH=~/bin:$PATH
$ curl https://storage.googleapis.com/git-repo-downloads/repo > ~/bin/repo
$ chmod a+x ~/bin/repo

#### Download source code
1. Download The Android 9 Pie official source code Create an empty directory to hold your working files. 
If you're using MacOS, this has to be on a case-sensitive filesystem. Give it any name you like:
$ mkdir WORKING_DIRECTORY
$ cd WORKING_DIRECTORY

$ repo init -u https://android.googlesource.com/platform/manifest -b android-9.0.0_r8 

2. Download Raspbery Pi 3 device configuration files

$ mkdir -pv device/brcm
$ git clone https://github.com/vasubabu/Device_rpi3 device/brcm/rpi3

3. Add local manifest file
$ mkdir -p .repo/local_manifests
$ ln -sv ../../device/brcm/rpi3/local_manifest.xml .repo/local_manifests/


4. Downloading
$ repo sync
$ repo sync -j1 -f --force-sync


#### Apply patch to Android framework
The following patch must be applyed:

diff --git a/services/core/java/com/android/server/wm/DisplayContent.java b/services/core/java/com/android/server/wm/DisplayContent.java
index cd8fdbf..6918bc3 100644
--- a/services/core/java/com/android/server/wm/DisplayContent.java
+++ b/services/core/java/com/android/server/wm/DisplayContent.java
@@ -769,7 +769,7 @@ class DisplayContent extends WindowContainer<DisplayContent.DisplayChildWindowCo
         // appropriately arbitrary number. Eventually we would like to give SurfaceFlinger
         // layers the ability to match their parent sizes and be able to skip
         // such arbitrary size settings.
-        mSurfaceSize = Math.max(mBaseDisplayHeight, mBaseDisplayWidth) * 2;
+        mSurfaceSize = (int)(Math.max(mBaseDisplayHeight, mBaseDisplayWidth) * 1.4);
 
         final SurfaceControl.Builder b = mService.makeSurfaceBuilder(mSession)
                 .setSize(mSurfaceSize, mSurfaceSize)

#### Configure build environment

$ . build/envsetup.sh
$ lunch rpi3-eng


#### Build target

$ make -j4

It will take about 10 hour for CPU: intel I3 ; RAM: 4G ; HDD: 500GB

Then you will get following images in the out/target/product/rpi3 folder:
1. rpiboot.img
2. boot.img
3. system.img
4. vendor.img
