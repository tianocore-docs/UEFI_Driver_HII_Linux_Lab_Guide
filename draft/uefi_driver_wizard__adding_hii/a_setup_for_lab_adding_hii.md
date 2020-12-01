<!--- @file
file setup-for-lab-adding-hii

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
## Lab 1 a. Setup for Lab adding HII {#setup-for-lab-adding-hii}

**Complete** [Lab Setup](../lab_setup/README.md) to configure for building with OvmfPkg with QEMU.


**Skip** to Lab 1.b if the UEFI Driver Porting lab was completed.

1). Start with LAB 6\. on Driver porting Lab solution and **create** a folder called **MyWizardDriver** in the ~/src/edk2 workspace<br>
2). Now**, locate** and **open**: ~/SRC/LabSampleCode\MyWizardDriver<br>
3). **Copy **the following Files to ~/SRC/edk2/MyWizardDriver <br>
 - ComponentName.c               ComponentName.h                       
 - DriverBinding.h
 - HiiConfigAccess.c             HiiConfigAccess.h
 - MyWizardDriver.c              MyWizardDriver.h
 - MyWizardDriver.inf            MyWizardDriver.uni
 - MyWizardDriver.vfr            MyWizardDriverNVDataStruc.h
 - SimpleTextOutput.c            SimpleTextOutput.h
 
4). **Update** src/edk2/OvmfPkg/OvmfPkgX64.dsc and add the MyWizardDriver.inf to `[Components]` section:<br>
Hint: add to the last module in the `[Components] `section<br>

```
 MyWizardDriver/MyWizardDriver.inf{
   <LibraryClasses>    DebugLib|MdePkg/Library/UefiDebugLibConOut/UefiDebugLibConOut.inf
 }

```
5). **Save** and **close** the file

6). Open Terminal Command Prompt<br>
  `bash$ cd ~/src/edk2  `<br>







  
