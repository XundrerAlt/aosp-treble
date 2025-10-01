# AOSP Treble

# Build information
## Hardware configuration
CPU: Intel Core i5-9400F  
RAM: 16 GB + 64 GB swap  
Disk: 300 GB SSD (aosp-src) + 1 TB HDD (aosp-src/out)  
Time to build: ~9 hours  

## Testing results
Test device: Redmi Note 12S  
Status: Stable for daily use  
Known issues: WebView not working  

# How to build

First clone my repository to get patches and local manifests:
```bash
git clone https://github.com/XundrerAlt/aosp-treble.git -b android-15 aosp-treble/
```

Then initialize AOSP repository:
```bash
repo init -u https://android.googlesource.com/platform/manifest -b android-15.0.0_r36 --git-lfs
```

Apply local manifests
```bash
mkdir .repo/local_manifests
cp aosp-treble/manifests/* .repo/local_manifests/
```

Start syncing:
```bash
repo sync
```

Or my method:
```bash
repo sync -c --force-sync --no-clone-bundle --no-tags -j64 || repo sync -c --force-sync --no-clone-bundle --no-tags -j4
```

Now apply patches:
```bash
bash aosp-treble/patch.sh aosp-treble/ trebledroid
bash aosp-treble/patch.sh aosp-treble/ ponces
```

Generate phh makefile
```bash
cp aosp-treble/aosp.mk device/phh/treble
cd device/phh/treble
bash generate.sh aosp
cd ../../..
```

Build treble add (optional)
```bash
cd treble_app
bash build.sh release
cp TrebleApp.apk ../vendor/hardware_overlay/TrebleApp/app.apk
cd ..
```

Setup environment
```bash
source build/envsetup.sh
#source build/core/build_id.mk - optional
```

Lunch
```bash
lunch treble_arm64_bvN-bp1a-userdebug
```

If you want to include GAPPS
```bash
lunch treble_arm64_bgN-bp1a-userdebug
```

And start building
```bash
#m systemimage -jN
m systemimage -j10
```

Your system.img in out/target/product/tdgsi_arm64_ab/system.img

### Or just paste to terminal:
```bash
git clone https://github.com/XundrerAlt/aosp-treble.git -b android-15 aosp-treble/
repo init -u https://android.googlesource.com/platform/manifest -b android-15.0.0_r36 --depth=1
mkdir .repo/local_manifests
cp aosp-treble/manifests/* .repo/local_manifests/
repo sync -c --force-sync --no-clone-bundle --no-tags -j64 || repo sync -c --force-sync --no-clone-bundle --no-tags -j4
bash aosp-treble/patch.sh aosp-treble/ trebledroid
bash aosp-treble/patch.sh aosp-treble/ ponces

cp aosp-treble/aosp.mk device/phh/treble
cd device/phh/treble
bash generate.sh aosp
cd ../../..

cd treble_app
bash build.sh release
cp TrebleApp.apk ../vendor/hardware_overlay/TrebleApp/app.apk
cd ..

source build/envsetup.sh

lunch treble_arm64_bvN-bp1a-userdebug
# lunch treble_arm64_bgN-bp1a-userdebug - if you want GAPPS
m systemimage -j10
```

# Credits
- [TrebleDroid](https://github.com/TrebleDroid) - aosp-treble/patches/trebledroid
- [phhusson](https://github.com/phhusson) - device/phh/treble
- [ponces](https://github.com/ponces) - vendor/ponces, aosp-treble/patches/ponces, vendor/gapps
- [Doze-off](https://github.com/Doze-off) - vendor/hardware_overlay (supports my device)
- my weak PC
- and everyone else
