<!--- @file
 README.md file for Lab_setup

Copyright (c) 2018, Intel Corporation. All rights reserved.<BR>

Redistribution and use in source (original document form) and 'compiled'
forms (converted to PDF, epub, HTML and other formats) with or without
modification, are permitted provided that the following conditions are met:

1) Redistributions of source code (original document form) must retain the
above copyright notice, this list of conditions and the following
disclaimer as the first lines of this file unmodified.

2) Redistributions in compiled form (transformed to other DTDs, converted to
PDF, epub, HTML and other formats) must reproduce the above copyright
notice, this list of conditions and the following disclaimer in the
documentation and/or other materials provided with the distribution.

THIS DOCUMENTATION IS PROVIDED BY TIANOCORE PROJECT "AS IS" AND ANY EXPRESS OR
IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE IMPLIED WARRANTIES OF
MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE ARE DISCLAIMED. IN NO
EVENT SHALL TIANOCORE PROJECT BE LIABLE FOR ANY DIRECT, INDIRECT, INCIDENTAL,
SPECIAL, EXEMPLARY, OR CONSEQUENTIAL DAMAGES (INCLUDING, BUT NOT LIMITED TO,
PROCUREMENT OF SUBSTITUTE GOODS OR SERVICES; LOSS OF USE, DATA, OR PROFITS;
OR BUSINESS INTERRUPTION) HOWEVER CAUSED AND ON ANY THEORY OF LIABILITY,
WHETHER IN CONTRACT, STRICT LIABILITY, OR TORT (INCLUDING NEGLIGENCE OR
OTHERWISE) ARISING IN ANY WAY OUT OF THE USE OF THIS DOCUMENTATION, EVEN IF
ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.

-->
# Lab Setup Linux for Ubuntu 16.04 {#lab-setup}


1.  Download the [UEFI Training Materials](https://github.com/Laurie0131/Lab_Material_FW) .zip (accept any security notifications) 
2. **Click** “Open”  and Extract the file to HOME which will take a few minutes <br>
Note:  It is highly important that you unzip the file correctly to this location because all the file locations in this training guide follow that format. The Default will be in` /FW/...` Directory
  - `~/Lab_Material_FW/FW `
    - Documentation 
    - DriverWizard 
    - edk2      
    - edk2Linux 
    - LabSampleCode

3.  **Install** the Ubuntu Linux tools:
```shell
sudo apt-get install build-essential uuid-dev iasl git 
sudo apt-get install gcc-5 nasm 
sudo apt-get install qemu
```
4. ** Create **a directory “src”<br>
```shell
mkdir ~src`
```
5. From the `~.../FW` folder, copy and paste folder “`~.../FW/edk2`” to `~src`
6. **Rename** or **mv** the directory “`~src/edk2/BaseTools`” to something else <br>
```shell
cd ~src/edk2`
mv BaseTools BaseToolsX`
```
7. **Extract** the file `~FW/edk2Linux/BaseTools.tar.gz`  to  `~src/edk2`<br>
```shell
cd ~src/edk2
```
8. Make the BaseTools and setup the environment <br>
```shell
make –C BaseTools
. edksetup.sh
```
9. **Open **the file Conf/target.txt<br>
```shell
gedit Conf/target.txt
```
10. Update the following:

```
ACTIVE_PLATFORM       = OvmfPkg/OvmfPkgX64.dsc
TARGET_ARCH           = X64
TOOL_CHAIN_TAG        = GCC5
```
![](/media/gedit_target.txt.JPG)
11. **Save** and Exit target.txt
12. To build OvmfPkg **Type**<br> `bash$ build`
13. The file OVMF.fd should be in the Build directory: `~/src/edk2/Build/OvmfX64/DEBUG_GCC5/FV/OVMF.fd`

<br>
#### Build errors
For build errors the Build option for GCC5 may need to be updated:
1. **Edit** ~/src/edk2/tools_def.txt
2. **Find** `DEFINE GCC44_ALL_CC_FLAGS` remove the "`-Werror`" flag because it will treat warnings as errors.<br>
`DEFINE GCC44_ALL_CC_FLAGS = -g -fshort-wchar -fno-builtin -fno-strict-aliasing -Wall -Wno-array-bounds -ffunction-sections -fdata-sections -include AutoGen.h -fno-common -DSTRING_ARRAY_NAME=$(BASE_NAME)Strings` <br>
3. **Save** tools_def.txt
4. Build again **Type**<br> `bash$ build`


<br>

### Invoke QEMU to run UEFI Shell {#invoke-qemu-to-run-uefi-shell}
 

1. **Create** a run-ovmf directory under the home directory
```
cd ~
mkdir ~run-ovmf
cd run-ovmf
```
2. **Create** a directory hda-contents to use as a hard disk image <br>
 ```shell
 mkdir hda-contents
```
3. **Copy** the `OVMF.fd` BIOS image created from the `build` to the run-ovmf directory naming it `bios.bin` <br>
```shell
cp ~/src/edk2/Build/OvmfX64/DEBUG_GCC5/FV/OVMF.fd bios.bin
```
4. Create a Linux shell script to run the QEMU from the run-ovmf directory <br>
```shell
gedit RunQemu.sh
```
 Add the following:  <br>

```shell
qemu-system-x86_64 -pflash bios.bin -hda fat:rw:hda-contents -net none -debugcon file:debug.log -global isa-debugcon.iobase=0x402
```
![](/media/geditRunQemush.png)
5. **Save** RunQemu.sh and exit
6. **Run** the RunQemu.sh Linux Shell Script: <br>
```shell
. RunQemu.sh
```
![](/media/QEMU_BootingOVMF.JPG)


### End of Lab Setup for Linux
