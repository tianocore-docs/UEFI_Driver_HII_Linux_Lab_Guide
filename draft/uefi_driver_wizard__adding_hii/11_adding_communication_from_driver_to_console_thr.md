<!--- @file
 file

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
## Lab 11\. Adding Communication from Driver to Console through HII {#11-adding-communication-from-driver-to-console-through-hii}

In this lab, you’ll add communication from the driver to the console through HII. More specifically, you’ll add code to retrieve a string from the HII database and print the string to the console. Then, you’ll add the string in French, change the language, and test to ensure the correct language is displayed. The reason the driver should avoid direct string text to the console without the HII support is because there is no localization for text string inside the driver’s source code. By using the HII database the strings are tokenized making localization easier.

### Examine the MdeModulePkg.Chm for HII
1. Use the .Chm reader and open  the MdeModulePkg.Chm. For HII database library functions are in  the MdeModulePkg .Chm file. Search on HiiLib.h functions 
2. Select the Index tab 
3. Type: HiiLib.h <br>
![](/media/image98.png)
**_Note_**: Notice the list of Hii function calls available. To get Strings the `HiiGetString` function can be used. 
![](/media/image99.png)

### Add changes to your Driver
1. **Update** the ~/src/edk2/OvmfPkg/OvmfPkgX64.fdf file 
2. Make your driver stand alone again. **Remove** (or comment out) the include statement in the OvmfPkgX64.fdf file: <br>
` #INF MyWizardDriver/MyWizardDriver.inf` <br>
![](/media/image100.png)
3. **Save** OvmfPkgX64.fdf 
4. **Update** the MyWizardDriver.uni file 
5. Add the following code to the top of the file at approx. line 14 as shown: <br>
  `#langdef fr-FR  "Francais"`<br>
![](/media/image101.png)
6. **Add** the following code to the end of the file: <br>


```
#string STR_LANGUAGE_TEST_STRING        #language en "Laurie's Test String"                    
				                        #language fr-FR "Chaîne de test de Laurie"

```
![](/media/image102.png)<br>


7). **Save** MyWizardDriver.uni <br>

8). **Update** the MyWizardDriver.c file <br>

9). **Add** the following local variable for `StringPtr` after “`BOOLEAN ActionFlag;`” and before “`Status = EFI_SUCCESS;`”(as shown below): <br>
  `EFI_STRING StringPtr;`<br>
![](/media/image103.png)<br>

10). Add the following code after “`FreePool (ConfigRequestHdr);`” (as shown below) to edit the driver’s entry point with a debug and print statement by making a call to the `HiiGetString` for the token to print (at approx line 364): 

```
StringPtr        = HiiGetString (HiiHandle[0], STRING_TOKEN (STR_LANGUAGE_TEST_STRING), NULL);
   DEBUG ((EFI_D_INFO,"[MyWizardDriver-Entrypoint] My String was: %s\n", StringPtr) );
   Print(L"%s\n", StringPtr );
```
![](/media/image104.png)<br>

11). **Save** the MyWizardDriver.c 


### Build and test Driver
1.  At the Terminal Command Prompt (**Cntl-Alt-T**)
```
bash$ cd ~/src/edk2
bash$ build
```
2. **Copy** the `OVMF.fd` BIOS image created from the `build` to the run-ovmf directory naming it `bios.bin` <br>
   `bash$ cp ~/src/edk2/Build/OvmfX64/DEBUG_GCC5/FV/OVMF.fd bios.bin`  <br>

3. **Invoke** Qemu <br>
```
 bash$ cd ~/run-ovmf
 bash$ . RunQemu.sh 
```
4.  **At the UEFI Shell prompt,type** fs0: 
5.  **Type** Load MyWizardDriver.efi  and then **Press** “Enter”
![](/media/image105.png) <br>
**Notice **that the string’s English version is displayed:
6. **Type** Reset  and then **Press** “Enter” 
7.  **Type** "build run" and then  **Press** “Enter” 
8. **Type** "exit" at the shell prompt 
9. **Select Language** and then **Press** “Enter” 
![](/media/image106.png)<br>

10.  **Select** “Français”  and then **Press** “Enter” <Br>
![](/media/image107.png)<br>
11. **Select** “Continuer” and then **Press** “Enter” <br>
![](/media/image108.png)
12. At the Shell Prompt, **type** Fs0: 
13. **Type** load MyWizardDriver.efi <br>
![](/media/image109.png)<br>
14.  **Type** “reset” and then  **Press** “Enter” <br>
![](/media/image97.png)
15. **Exit** QEMU
---

For any build issues copy the solution files from ~/FW/LabSolutions/LessonE.11
NOTE: Delete Directory 

~/src/edk2/Build/OvmfX64/DEBUG_GCC5/X64/MyWizardDriver before the Build command to build the MyWizardDriver Clean.
Make sure you update OvmfPkgX64.fdf.


### End Lab 11