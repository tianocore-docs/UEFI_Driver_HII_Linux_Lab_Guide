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
## Lab 3\. Updating your driver to initialize data from the VFR data to the HII Database {#3-updating-your-driver-to-initialize-data-from-the-vfr-data-to-the-hii-database}

In this lab, you’ll learn how to update your driver to initialize the data according to the defaults set in the .VFR file. Thus when the user enters your driver’s menu for the first time, the values will display the defaults according to the .VFR file settings. You will also learn the rich set of HII function calls that are part of the `MdeModulePkg` in the `HiiLib` by reviewing the “MdeModulePkg Document.chm”.

### Lab 3_a._ Add HII Library Calls to Your Driver {#a-add-hii-library-calls-to-your-driver}

For this lab you will update the following files: MyWizardDriver.inf, MyWizardDriver.h, and MyWizardDriver.c
1. **Update** the MyWizardDriver.inf file  
2. **Add** the following package (as shown below):  <br>The HII Library in the MdeModulePkg has many functions to help with Communication to/from the Hii Database and Hii forms. One function call HiiSetToDefaults will compare the default settings from the .VFR file and update the driver’s configuration buffer according to the settings in the .VFR file.        <br>

```
MdeModulePkg/MdeModulePkg.dec
```
![](/media/image38.png)
**_Note_**: For other functions from the HII Library, open the .chm file “MdeModulePkg Document.chm” and search for `HiiLib.h`. <br>

3). **Add** the following library class (as shown below): <br>
`HiiLib` <br>
![](/media/image39.png)<br>

4). **Save** MyWizardDriver.inf <br>

5). **Update** the MyWizardDriver.h file <br>

6). **Add** the following code (as shown below):                <br> `#include <Library/HiiLib.h>`<br>
![](/media/image40.png)<br>

7). **Save** MyWizardDriver.h <br>

8). **Update** the MyWizardDriver.c file <br>

9). **Add Locals:** first add 2 locals for your drivers configuration buffer and a boolean flag from the Hii Library calls <br>Add the following at Approx. Line 190. 
```
  MYWIZARDDRIVER_CONFIGURATION     *Configuration;
  BOOLEAN                          ActionFlag;
```
![](/media/image41.png)<br>

10). **Add** the following to the MyWizardDriverDriverEntryPoint entry point funtion to line 319, approximately after “`BufferSize =`” as shown below 
```
// Begin code
  
  //
  // Initialize configuration data
  //
  Configuration = &PrivateData->Configuration;
  ZeroMem (Configuration, sizeof (MYWIZARDDRIVER_CONFIGURATION));
  
  //
  // Try to read NV config EFI variable first
  //
  ConfigRequestHdr = HiiConstructConfigHdr (&mMyWizardDriverFormSetGuid, mIfrVariableName, mDriverHandle[0]);
  ASSERT (ConfigRequestHdr != NULL);
// End code
```
![](/media/image42.png)<br>

11). Modify the following lines: <br>
`@~338`: remove: “`&PrivateData->`” from the “`&PrivateData->Configuration`”<br>
`@~342`: remove line: `ZeroMem (&PrivateData->Configuration, sizeof (MYWIZARDDRIVER_CONFIGURATION));`<br>
`@~347`: remove: “`&PrivateData->`” from the “`&PrivateData->Configuration`”
![](/media/image43_1.JPG)<br>

12). **Add** the following code to the `MyWizardDriverDriverEntryPoint` entry point code at approximately line 349 before<br>
 `// Install Driver Supported EFI Version Protocol onto ImageHandle`
You’re deleting the “`}`" and replacing it with the following code (as shown below).  With this replacement we are adding an “`else`” to the “`if`” statement: <br>
Note the “`}`” on line 361 is still matching the initial if statement.  Make sure you do not have a duplicate “`}`”
```
//Begin code
    //
    // EFI variable for NV config doesn't exist, we should build this variable
    // based on default values stored in IFR
    //
    ActionFlag = HiiSetToDefaults (ConfigRequestHdr, EFI_HII_DEFAULT_CLASS_STANDARD);
    ASSERT (ActionFlag);
  } else {
    //
    // EFI variable does exist and Validate Current Setting
    //
    ActionFlag = HiiValidateSettings (ConfigRequestHdr);
    ASSERT (ActionFlag);
  }  // Match if (EFI_ERROR (Status)) 
  FreePool (ConfigRequestHdr);
// end HII
// End code
```
![](/media/image44.png)<br>

13).  **Save** the MyWizardDriver.c file 

#### Build and test MyWizardDriver

1. At the Terminal Command Prompt (**Cntl-Alt-T**)
```
bash$ cd ~/src/edk2
bash$ build
```
2. **Copy** MyWizardDriver.efi to hda-contents<br>
`bash$ cd ~/run-ovmf/hda-contents`<br>
`bash$ cp ~/src/edk2/Build/OvmfX64/DEBUG_GCC5/X64/MyWizardDriver.efi .` <br>
3. **Invoke** Qemu <br>
`bash$ cd ~/run-ovmf`<br>
`bash$ . RunQemu.sh `<br>
4.  **At the UEFI Shell prompt,type** fs0: 
5.  **Type** Load MyWizardDriver.efi and then **Press** “Enter” 
![](/media/image17.png)
6.  **Type** exit 
7.  Now at the setup front page menu,** select **“Device Manager”
![](/media/image18.png)
8. **Press** “Enter”  to enter **“Device Manager”**
9. Inside the Device Manager menu** press **the down to **“My Wizard Driver Sample Formset” **
![](/media/image19.png)
10. **Press** “Enter”
![](/media/image20.png) 
11. **Press** “Escape” to exit
10. **Press** “Escape” to Exit the “Device Manager” Page
11. **Press** Up Arrow to “Continue” and then **Press** “Enter” 
![](/media/image24.png)
12. **Type** "`Reset`"  <br>
![](/media/image97.png)
13. **Exit** QEMU
<br> 

For any build issues copy the solution files from ~/FW/LbSolutions/LessonE.3
NOTE: Del Directory ~/src/edk2/Build/OvmfX64/DEBUG_GCC5/X64/MyWizardDriver before the Build command to build the MyWizardDriver Clean

<br><br><br>
<br><br><br>






### Lab 3_b._ Add your Driver to the platform  {#b-add-your-driver-to-the-platform}

As of now, your driver needs to be soft loaded each time from the shell prompt.  In this lab, you’ll update the platform .FDF file to force your driver to load as part of the platform UEFI driver.  

1. **Open** to update:  ~/src/edk2/OvmfPkg/OvmfPkgX64.Fdf 
2. **Add** the following code (as shown below before “`!if $(BUILD_NEW_SHELL) == TRUE`” ):<br>
 `INF MyWizardDriver/MyWizardDriver.inf`  <Br>
3. **Save** ~/src/edk2/OvmfPkg/OvmfPkgX64.Fdf 




#### Build and test MyWizardDriver

1.  At the Terminal Command Prompt (**Cntl-Alt-T**)
```
bash$ cd ~/src/edk2
bash$ build
```
3. **Copy** the **Note:**`OVMF.fd` BIOS image created from the `build` to the run-ovmf directory naming it `bios.bin` <br>
   `bash$ cp ~/src/edk2/Build/OvmfX64/DEBUG_GCC5/FV/OVMF.fd bios.bin`  <br>
3. **Invoke** Qemu <br>
 `bash$ cd ~/run-ovmf`<br>
 `bash$ . RunQemu.sh `<br>
4.  At the UEFI Shell prompt,**type** "`Exit`" <br>
![](/media/image46.png)

5.  Now at the setup front page menu,** select **“Device Manager”
![](/media/image18.png)
6. **Press** “Enter”  to enter **“Device Manager”**
7. Inside the Device Manager menu ** Notice** that the My Wizard Driver Sample Formset is added without having to issue the “Load” command from the shell prompt.
![](/media/image47.png)
8. **Press **“Escape” to exit the “Device Manager” Page
9. **Press **Up Arrow to “Continue”
![](/media/image24.png)
10. **Press** “Enter” 
11. **Type** "`Reset`"<br> 
![](/media/image97.png)
12. **Exit** QEMU
 

---

For any build issues copy the solution files from ~/FW/LbSolutions/LessonE.3

NOTE: Del Directory ~/src/edk2/Build/OvmfX64/DEBUG_GCC5/X64/MyWizardDriver before the Build command to build the MyWizardDriver Clean


#### End of Lab 3
