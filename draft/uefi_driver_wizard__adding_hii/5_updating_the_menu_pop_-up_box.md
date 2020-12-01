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
## Lab 5\. Updating the Menu: Pop-up Box {#5-updating-the-menu-pop-up-box}

In this lab, you’ll learn how to add a _pop-up box_ to your driver’s form menu by using the “**oneof**” VFR term. We will also only update the MyWizardDriver.vfr and MyWizardDriver.uni files.
![](/media/image52.png)
###### Figure 5 My Wizard Driver with a pop-up box

**Background Information **
The VFR term “**oneof**” will declare a pop-up menu. The user then selects one field that will dictate the value stored in the NVRAM variable.  Looking at Figure 5 above, there are three values:

| Value | Display | String token |
| --- | --- | --- |
| 0 | 500 Hex | STR\_ONE\_OF\_TEXT3 |
| 1 | 480 Hex | STR\_ONE\_OF\_TEXT2 |
| 2 | 400 Hex | STR\_ONE\_OF\_TEXT1 |

For this lab you will add code to give your driver menu a pop-up menu item by defining a “**oneof**” item.  Also, if the device is “**disabled**”, then use the VFR term “**grayoutif**” statement so that the pop-up menu is not accessible and cannot be changed.  The browser engine will use the configuration variable `MWD_IfrNVData.MyWizardDriverChooseToEnable` with a value of  `0x0` to determine if the device is enabled or disabled


1. **Update** the MyWizardDriver.vfr file 
2.  **Add** the following code before the “`resetbutton`” statement (approximately line 53) 
```
// Begin code
    //
    // Define oneof (EFI_IFR_ONE_OF)
    //
   grayoutif  ideqval MWD_IfrNVData.MyWizardDriverChooseToEnable == 0x0;

    oneof name = MyOneOf2,                                // Define reference name for Question
      varid   = MWD_IfrNVData.MyWizardDriverBaseAddress, 
      // Use "DataStructure.Member" to reference Buffer Storage
      prompt  = STRING_TOKEN(STR_ONE_OF_PROMPT),
      help    = STRING_TOKEN(STR_ONE_OF_HELP),
      //
      // Define an option (EFI_IFR_ONE_OF_OPTION)
      //
      option text = STRING_TOKEN(STR_ONE_OF_TEXT3), value = 0x0, flags = 0;
      option text = STRING_TOKEN(STR_ONE_OF_TEXT2), value = 0x1, flags = 0;
      //
      // DEFAULT indicate this option will be marked with  
      //  EFI_IFR_OPTION_DEFAULT
      //
      option text = STRING_TOKEN(STR_ONE_OF_TEXT1), value = 0x2, 
          flags = DEFAULT;
    endoneof;
   endif;
// end code
```
![](/media/image53.png)
3. **Save** the MyWizardDriver.vfr file 
4. **Update** the MyWizardDriver.uni file 
5. **Add** the following code to the end of the file (as shown below):

```c
// begin code
#string STR_ONE_OF_PROMPT              #language en "Select Base Address"

#string STR_ONE_OF_HELP                #language en "Select a Base address of 400, 480 or 500 Hex.  Values 0,1 or 2(default) is stored in the NVRAM Data" 

#string STR_ONE_OF_TEXT1               #language en "400 Hex"
#string STR_ONE_OF_TEXT2               #language en "480 Hex"
#string STR_ONE_OF_TEXT3               #language en "500 Hex"

// end code
```
![](/media/image54.png)<br>

6). **Save** MyWizardDriver.uni 


#### Build and test MyWizardDriver

1.  At the Terminal Command Prompt (**Cntl-Alt-T**)
```
bash$ cd ~/src/edk2
bash$ build
```
2. **Copy** the `OVMF.fd` BIOS image created from the `build` to the run-ovmf directory naming it `bios.bin` <br>
   `bash$ cp ~/src/edk2/Build/OvmfX64/DEBUG_GCC5/FV/OVMF.fd bios.bin`  <br>

3. **Invoke** Qemu <br>
 `bash$ cd ~/run-ovmf`<br>
 `bash$ . RunQemu.sh `<br>

4. At the UEFI Shell prompt,type **exit**<br>
![](/media/image46.png)
5. Now at the setup front page menu,** select** “Device Manager”
6. Inside the Device Manager menu** press **the down arrow to “My Wizard Driver Sample Formset” **Press** "Enter"
7. **Down Arrow** to “Select Base Address”
![](/media/image55.png)<br>
**Notice** the Pop up menu 
8. **Select** “480 Hex” then **press** "Enter"<br>
**Observe**: the “**Configuration changed**” message at the bottom
![](/media/image56.png)
9. **Test** the “`grayoutif`” by selecting “Enable My XYZ Device” then **press** the “Space” bar to toggle off or “Disabled”.<br>
**Notice** the “Select Base Address” is now grayed out and not Selectable. 
![](/media/image57.png) 
10. **Press** “Space” again to Enable
11. To Exit **Press ** “Escape” then “Y” or “F10” then “Escape”
12. To Exit the “Device Manager” Page: **Press** “Escape”
13. **Press** Up Arrow to “Continue”
14. At the Shell Prompt **type**: "`dmpstore -all`"
![](/assets/image58-59.JPG)
15. Observe file MyWizardDriverNVDataStruc.h and the NVRAM `MWD_IfrNVData` variable. <br>
By updating MyWizardDriverNVDataStruc.h, our data structure stored in NVRAM is named `MWD_IfrNVData` of type `MYWIZARDDRIVER_CONFIGURATION`.<BR>
**Notice** that the base address byte is the next to the last byte in the data structure `MWD_IfrNVData.MyWizardDriverBaseAddress` where `02 == 400H`, `01 == 480H`, and `00 == 500H` <br>
Notice the NVRAM Variable with the value of `480H` will have a true value of `01`.
16. **Type** “reset” at the Shell prompt <br>
![](/media/image97.png)
17. **Exit** QEMU

---
For any build issues copy the solution files from ~/Fw/LabSolutions/LessonE.5

NOTE: Delete Directory ~/src/edk2/Build/OvmfX64/DEBUG_GCC5/X64/MyWizardDriver before the Build command to build the MyWizardDriver Clean


#### End of Lab 5