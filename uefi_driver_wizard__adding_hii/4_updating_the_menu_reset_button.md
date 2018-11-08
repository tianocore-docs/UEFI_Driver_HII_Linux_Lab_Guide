<!--- @file
 file

Copyright (c) 2018, Intel Corporation. All rights reserved.<BR>

Redistribution and use in source (original document form) and 'compiled'
forms (converted to PDF, epub, HTML and other formats) with or without
modification, are permitted provided that the following conditions are met:

1). Redistributions of source code (original document form) must retain the
above copyright notice, this list of conditions and the following
disclaimer as the first lines of this file unmodified.

2). Redistributions in compiled form (transformed to other DTDs, converted to
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
## Lab 4\. Updating the Menu: Reset Button {#4-updating-the-menu-reset-button}

In this lab, you’ll learn how to add a resetbutton to your driver’s form menu. It’s time to add more configuration fields to your menu, enabling users to modify more fields now that you’ve built a driver that 1) saves data from forms into NVRAM 2) updates data from the .VFR forms and 3) builds into the platform drivers.

The next set of labs will update .VFR, `MyWizardDriver.vfr`, and UNI `MyWizardDriver.uni` string files to incrementally add a reset button (Lab 4), pop-up box (Lab 5), string name (Lab 6), and numeric hex value (Lab 7) to your driver’s form menu:

1). **Update** the MyWizardDriver.vfr file 
2). **Add** the following code (as shown below after the “GUID” definition Apprx. Line 29): `MyStandardDefault`,

```

    prompt =    STRING_TOKEN(STR_STANDARD_DEFAULT_PROMPT),
    attribute = 0x0000;        // Default ID: 0000 standard default

```
![](/media/image48.png)<br>
3). **Add** the folowing code before the “`endform`” (as shown below Approx. Line 55): 

```
   resetbutton
      defaultstore = MyStandardDefault,
      prompt   = STRING_TOKEN(STR_STANDARD_DEFAULT_PROMPT_RESET),
      help     = STRING_TOKEN(STR_STANDARD_DEFAULT_HELP),
    endresetbutton;

```
![](/media/image49.png)<br>
4). **Save** MyWizardDriver.vfr <br>

5). **Update** the MyWizardDriver.uni file <br>

6). **Add** the following strings at the end of the file to support the “`STR_`“ referenced added in the .vfr file: <br>


```
#string STR_STANDARD_DEFAULT_PROMPT    #language en "Standard Default"
#string STR_STANDARD_DEFAULT_PROMPT_RESET    #language en "Reset to Standard Default"
#string STR_STANDARD_DEFAULT_HELP      #language en "This will reset all the Questions to their standard default value"

```
7). **Save** MyWizardDriver.uni 



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
5. Now at the setup front page menu,** select **“Device Manager”
6. **Press** “Enter” to enter “Device Manager”
7. Inside the Device Manager menu** press **the down arrow to “My Wizard Driver Sample Formset” **Press** "Enter"
8. **Access** the My Wizard Driver menu and **notice** the item “Reset to Standard Default” 
![](/media/image50.png)
9. **Press Down Arrow to “**Reset to Standard Default” 
10. **Press** “Enter" to select 
11. **Notice** the “**Configuration changed**” message at the bottom of the menu 
12. To Exit **Press** “Escape” then “Y” 
13. To Exit the “Device Manager” Page: **Press** “Escape” 
14. **Press** Up Arrow to “Continue” <br> **Observe:** Notice that since this change requires a reset, the shell will exit out completely. 
![](/media/image51.png)
15. **Press** “Enter” to  reset <br>
16.  **Exit** QEMU
---

For any build issues copy the solution files from ~/FW/LabSolutions/LessonE.4


NOTE: Delete Directory ~/src/edk2/Build/OvmfX64/DEBUG_GCC5/X64/MyWizardDriver before the Build command to build the MyWizardDriver Clean 


### End of Lab 4