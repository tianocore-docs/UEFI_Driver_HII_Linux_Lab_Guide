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
## Lab 7\. Updating the Menu: Numeric Entry {#7-updating-the-menu-numeric-entry}

In this lab, you’ll learn how to add a numeric entry to your driver menu. This lab uses the VFR term “`numeric`” that prompts the user to enter a free-form numeric value. The VFR determines the minimum and maximum values with the terms “`minimum`” and “`maximum`”. Since there is also an enable/disable switch, the VFR uses the “`suppressif`” term to display or hide this field when disabled. Also this field displays as decimal (default) or hexadecimal with the “`flags`” switch.
![](/media/image68.png)
###### Figure 7 : Menu with Numeric item entry

1. **Update** the MyWizardDriver.vfr file 
2.  **Add** the following code in the location shown below at approx. Line 90 and before the “`resetbutton`” item: 
```
//
// Define a numeric free form menu item 
//
  suppressif  ideqval MWD_IfrNVData.MyWizardDriverChooseToEnable == 0x0;
   numeric varid   = MWD_IfrNVData.MyWizardDriverHexData,                          
            prompt  = STRING_TOKEN(STR_DATA_HEX_PROMPT),
            help    = STRING_TOKEN(STR_NUMERIC_HELP),
            flags   = DISPLAY_UINT_HEX ,     // Display in HEX format (if not specified, default is in decimal format)
            minimum = 0,
```
![](/media/image69.png)
3. **Save** MyWizardDriver.vfr 
4. **Update** the MyWizardDriver.uni file 
5. **Add** the following code to the bottom of the file:

```
#string STR_DATA_HEX_PROMPT            #language en "Enter ZY Base (Hex)"
#string STR_NUMERIC_HELP               #language en "This is the help for entering a Base address in Hex. The valid range in this case is from 0 to 250."

```
6).  **Save** MyWizardDriver.uni 


#### Build and test MyWizardDriver

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
4. At the UEFI Shell prompt,type **exit**<br>
![](/media/image46.png)
5. Now at the setup front page menu,** select** “Device Manager”
6. Inside the Device Manager menu** press **the down arrow to “My Wizard Driver Sample Formset” **Press** "Enter"<br>
**Notice** the value for “Enter ZY Base(Hex)” is `022`.  Hex is the default because of the VFR field “`default = 0x22`”
![](/media/image70.png)
7. **Select** “Enter ZY Base(Hex)”  and then **Press** “Enter” 
8. **Test** by typing a “`M`” character<br>
**Notice** that only Numeric characters are allowed and also only values `00` to `0FA` Hex. When values outside the range or non-numeric characters are entered the red **“!!”** sting is displayed at the bottom of the menu. <br>
The string **“!!”** is part of the Browser engine : `MdeModulePkg\Universal\SetupBrowserDxe\SetupBrowserStr.uni`<BR>
`#string INPUT_ERROR_MESSAGE            #language en-US  "!!"`
![](/media/image71.png)

9. **Press** “Enter” again 
10. **Test** by typing a value of ‘`99`’ Hex  and then **Press** “Enter” <br>
**Notice** that the “**Configuration changed**” message is displayed.
11. **Test** the “`surpressif`” by pressing the “spacebar” to “Enable My XYZ Device” then press the “Space” bar to toggle off or “Disabled”. <br>
**Notice** the “Select Base Address” and “Name of Configuration” fields are now grayed out and not selectable and the “**Enter ZY Base(Hex)**” does not appear at all. 
![](/media/image72.png)
12. **Press** “Space bar” again to “Enable My XYZ Device” and the“**Enter ZY Base(Hex)**” is displayed again
![](/media/image73.png)
13. **Press** “F10”  to save, then “Escape” to exit
14. **Press** “Escape” to exit the “Device Manager” 
15. **Select “**Continue**”** and then **Press** "Enter”
16. At the Shell Prompt, **type** `dmpstore –all` <br>
**Notice** by `modifyingMyWizardDriverNVDataStruc.h` our data structure stored in NVRAM is named `MWD_IfrNVData` of type `MYWIZARDDRIVER_CONFIGURATION`. 
![](/assets/image74_75.JPG)
17. **Type “reset”** at the Shell prompt <br>
![](/media/image97.png)
18. **Exit** QEMU

---

For any build issues copy the solution files from ~/FW/LabSolutions/LessonE.7

NOTE: Delete Directory ~/src/edk2/Build/OvmfX64/DEBUG_GCC5/X64/MyWizardDriver before the Build command to build the MyWizardDriver Clean


### End of Lab 7