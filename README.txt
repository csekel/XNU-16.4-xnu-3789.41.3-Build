Readme for building XNU 16.4(10.12.3) 

#Open Terminal && Run these commands in order

SDK_ROOT=`xcodebuild -version -sdk macosx Path`
CURRENT_DIR=`pwd`
sudo mkdir -p "${SDK_ROOT}/usr/local/include/kernel/os"
sudo mkdir -p "${SDK_ROOT}/usr/local/lib/kernel"
sudo cp -rf "${CURRENT_DIR}/libdispatch/firehose_buffer_private.h" "${SDK_ROOT}/usr/local/include/kernel/os/"
sudo cp -rf "${CURRENT_DIR}/libdispatch/libfirehose_kernel.a" "${SDK_ROOT}/usr/local/lib/kernel/"

# Make a Directory called "xnu_build" on your Desktop && cd to it 
 		
mkdir -p ~/Desktop/xnu_build
cd ~/Desktop/xun_build


# Curl these files from Opensource.apple.com
curl -O https://opensource.apple.com/tarballs/dtrace/dtrace-209.20.4.tar.gz && curl -O https://opensource.apple.com/tarballs/AvailabilityVersions/AvailabilityVersions-26.30.3.tar.gz && curl -O https://opensource.apple.com/tarballs/xnu/xnu-3789.41.3.tar.gz 

# Run this command to untar all downloaded files and rm the tar.gz files

 	for file in *.tar.gz; do tar -zxf $file; done && rm -f *.tar.gz

 # Building Dtrace and Avaliablilty -- copy each line by line and paste into terminal 

	cd dtrace-209.20.4
	mkdir -p obj sym dst
	xcodebuild install -target ctfconvert -target ctfdump -target ctfmerge ARCHS="x86_64" SRCROOT=$PWD OBJROOT=$PWD/obj SYMROOT=$PWD/sym DSTROOT=$PWD/dst
	sudo ditto $PWD/dst/Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain
	cd ..
	cd AvailabilityVersions-26.30.3
	mkdir -p dst
	make install SRCROOT=$PWD DSTROOT=$PWD/dst
	sudo ditto $PWD/dst/usr/local `xcrun -sdk macosx -show-sdk-path`/usr/local
	cd ..
	
# Building XNU 16.4

cd xnu-3789.41.3/
sudo make SDKROOT=macosx ARCH_CONFIGS=X86_64 KERNEL_CONFIGS=RELEASE
