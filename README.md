# pi-gen-utils
Utility scripts to make it easy to develop custom RPi images using the pi-gen system (https://github.com/RPi-Distro/pi-gen )

Pi-gen is incredibly useful as way to create images with precisely the packages and features required for a task. However it is conceptually wrong to have the custom scripts developed in the pi-gen directory structure. Firstly this causes issues as and when pi-gen itself is updated and secondly it is hard to identify the changed files and therefore what changes have been made.

With these utilities you can have your files in a single directory and have them copied into the pi-gen directory to create the image. The oroginal files are then stored in a backup directory and are restored between builds so as to keep the pi-gen directory tree clean.

In the single directory the relatively complex directory structure of pi-gen is maintained but the '/'s are replaced by '_'.

e.g. a project that creates a raspbian-lite image with different base packages might have the following files in its directory:
```
config
stage0_00-configure-apt_00-run.sh
stage2_01-sys-tweaks_00-packages
stage2_01-sys-tweaks_01-run.sh
stage2_01-sys-tweaks_files_customfile.txt
stage3_SKIP
stage4_SKIP
stage4_SKIP__IMAGES
stage5_SKIP
stage5_SKIP__IMAGES
```
these would then be copied to the pi-gen directory as follows:
```
config
stage0/00-configure-apt/00-run.sh
stage2/01-sys-tweaks/00-packages
stage2/01-sys-tweaks/01-run.sh
stage2/01-sys-tweaks/files/customfile.txt
stage3/SKIP
stage4/SKIP
stage4/SKIP__IMAGES
stage5/SKIP
stage5/SKIP__IMAGES
```
## Three scripts

There are three scripts in this repo:

 * getfile.sh
   copies a file from pi-gen to the current location removing everything before "stage" or "export" and replacing all the path '/'s with '_'s
 * set.sh 
   copy the changed files into the pi-gen directory in preparation for building a new image, also copy any files that will be overwritten into a bak subdirectory of the current directory
 * restore.sh
   copy all the files in the bak directory back to pi-gen and delete any other files that were added


## Intended usage

copy the threee scripts to /usr/local/bin

```
cd base directory
git clone https://github.com/RPi-Distro/pi-gen.git
```
install the pi-gen dependencies (and if planning to run in docker also install docker and docker-compose) 
```
mkdir myproject
cd myproject
```
create the config
copy the files acress 
```
getfile.sh ../pi-gen/stageX/0...
```
create new files with the appropriate names ( stage2_01-sys-tweaks_files_customfile.txt )

```
set.sh
cd ../pi-gen
build.sh # or build-docker.sh
cd -
restore.sh
```

