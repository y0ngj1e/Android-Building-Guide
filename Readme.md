- Pixel Experience 13 rom and lmi (Xiaomi Poco F2 Pro) are used as an example in this guide
- As Pixel Experience is based off LineageOS, we have to use LOS based device and vendor trees. using AOSP based trees might have more errors upon compilation of the rom. 
- Have at least 300GB free space and 16GB RAM (with 20GB swap) for the build to succeed.

# 1) Set up your build environment in Ubuntu

```bash
# get superuser access.
sudo su

# install JDK 
add-apt-repository ppa:openjdk-r/ppa

# update all packages.
apt-get update

# install Java packages.
apt-get install git-core gnupg flex bison build-essential zip curl zlib1g-dev gcc-multilib g++-multilib libc6-dev-i386 lib32ncurses5-dev x11proto-core-dev libx11-dev lib32z1-dev libgl1-mesa-dev libxml2-utils xsltproc unzip fontconfig

# become a normal user.
exit

# creating a bin folder and setting up AkhilNarang Script.
mkdir ~/bin
PATH=~/bin:$PATH
cd ~/bin
curl http://commondatastorage.googleapis.com/git-repo-downloads/repo > ~/bin/repo
chmod a+x ~/bin/repo
git clone https://github.com/akhilnarang/scripts.git scripts
cd scripts
bash setup/android_build_env.sh

# now go back to parent directory (i just reopen terminal)
```


# 2) Enter your github details and download the rom source
```bash
# Connect your Github account.
git config --global user.name "FIRST_NAME LAST_NAME" 
git config --global user.email "YOUR EMAIL"

# we need to make a folder for the ROM first (Eg PE)
mkdir PE

# Go to ROM folder.
cd PE

# Now we need to initialize Pixel Experience Source (Eg 13 plus)
repo init -u https://github.com/PixelExperience/manifest -b thirteen-plus

# As an alternative you can do a shallow clone to reduce size of the source..
# A Shallow Clone only clones the source with the last commit history specified
repo init --depth=1 -u git://github.com/PixelExperience/manifest -b thirteen-plus

# And finally download the ROM source
repo sync
```

# 3) Download the necessary device trees
```bash
# Now open up file explorer and enable show hidden files if you haven't already

# Go to PE > .repo and create a new folder named "local_manifests"

# Download the file "lmi.xml" from this repo and place it inside this folder
# (you can customise this file to whichever trees you need for your device)
# (Minimally device, vendor and kernel trees are required)

# Sync the source again to download the device trees
repo sync
```
(For building new roms based on another rom's tree)
- After downloading the trees, you will need to do a basic bringup if you are building a rom based on another rom's tree. This step is not needed for my trees as it's already correctly named for PE.
- You will need to edit AndroidProducts.mk and romname_codename.mk in device/brand/codename directory
- Eg for bringing up DU ROM based on Lineage tree:
https://github.com/AtlanPrime/decommonised_device_xiaomi_miatoll/commit/7077b40c32f45f730bbf3604375b5a8a7a1dc39b

# 4) Build the rom!
```bash
# in the PE directory type
source build/envsetup.sh

# lunch it (romname_codename-userdebug depends on your AndroidProducts.mk name)
lunch aosp_lmi-userdebug

# build it
make -j2

# the make command above is for 16GB RAM peasant builders
# if you have plentiful amount of ram (>32GB) you can use this command instead to speed up build process
mka bacon
```

Your freshly built rom zip will be at romname/out/target/product/devicename 👍
