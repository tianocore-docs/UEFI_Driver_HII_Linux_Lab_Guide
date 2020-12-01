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
## Lab 6\. Updating the Menu: Creating a String to Name a Saved Configuration {#6-updating-the-menu-creating-a-string-to-name-a-saved-configuration}

In this lab, you’ll create a string to name a saved configuration that will be stored into the NVRAM variable space. This lab uses the VFR term “string” to prompt the user to enter a string value. The VFR can determine the minimum and maximum number of characters of the string length with the terms “`minsize`” and “`maxsize`”. Since there is also an enable/disable switch, the VFR can use the “`grayoutif`” term again to allow or disallow changes to this field.
![](/media/image59.png)
###### Figure 6 : Menu with a string item

1.  **Update** the MyWizardDriver.vfr file 
2.  **Add** the following code to the location at approx. line 77 and before the “`resetbutton`” item (as shown below): 
```
//
// Define a string (EFI_IFR_STRING) to name the configuration in the 
// NVRAM variable
//
  grayoutif  ideqval MWD_IfrNVData.MyWizardDriverChooseToEnable == 0x0;
    string    varid    = MWD_IfrNVData.MyWizardDriverStringData,
              prompt   = STRING_TOKEN(STR_MY_STRING_PROMPT),
              help     = STRING_TOKEN(STR_MY_STRING_HELP),
              minsize  = 3,
              maxsize  = 20,
    endstring;
  endif;
```
![](/media/image60.png)
3. **Save** MyWizardDriver.vfr 
4. **Update** MyWizardDriver.uni 
5. **Add** the following code to the bottom of the file: <br>

```

#string STR_MY_STRING_PROMPT           #language en "Name of Configuration"
#string STR_MY_STRING_HELP             #language en "Enter a name for this configuration. This string can is 3 to 20 characters"

```
![](/media/image61.png)<br>
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
7. **Select **“Name of Configuration” then **Press** “Enter” <br>
**Notice **the string text pop up menu gets displayed.
![](/media/image62.png)
8. **Test** the “`minsize`” by only **typing** your choice of a two character string. Then **Press** “Enter” <br>
**Notice** the an error message validating that you need to enter at least three or more characters. 
![](/media/image63.png)
9. **Press** “Enter” to clear the message 
10. **Press** “Enter” again to re-enter pop up menu 
11. **Test **by** typing **more than “`maxsize`” of 20 characters**** Then  **Press** “Enter” . <br>
**Notice** that the Browser only allows the maximum number of 20 characters to be entered with a forced stop. There is no error message but no more characters are allowed to be typed into the pop up menu.
![](/media/image64.png)
12. **Press** “Enter” <br>
**Notice** that the “**Configuration changed**” message is displayed
![](/media/image65.png) 
13.  **Test** the `grayoutif` by selecting **“Enable My XYZ Device”** 
14. **Press** the “Spacebar” to toggle off/disable.  Then **Press** the “Spacebar” again to Enable. <br>
**Notice** that the “Select Base Address”  and “Name of Configruation” fields are  now grayed out and not selectable 
![](/media/image66.png)
15. **Press **“F10” to save.
16. **Press ** “Escape” to exit
17. **Press ** “Escape” to exit the “Device Manager”
18.  **Select** “Continue”  and then **Press ** “Enter”
19.  At the Shell Prompt, **type** `dmpstore -all` <br>
**Notice** the unicode string “`12345678901234567890`” (or the 20 character value you typed) is now stored 
because you entered those characters in the HII form menu.  This is because the file `WizardDriverNVDataStruc.h` has the data structure stored in NVRAM with the GUID define name `MWD_IfrNVData `of type `MYWIZARDDRIVER_CONFIGURATION`.  Notice that string data is the first 20 bytes in the data structure `MWD_IfrNVData.MyWizardDriverStringData`.
![](/assets/image65_66.JPG)
20. **Type** “reset” and then “Enter" at the Shell prompt <br>
![](/media/image97.png)
21. **Exit** QEMU
---

For any build issues copy the solution files from ~/Fw/LabSolutions/LessonE.6

NOTE: Delete Directory ~/src/edk2/Build/OvmfX64/DEBUG_GCC5/X64/MyWizardDriver before the Build command to build the MyWizardDriver Clean


### End of Lab 6