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
## Lab 8\. Updating your Driver for Interactive Call Backs {#8-updating-your-driver-for-interactive-call-backs}

In this lab, you’ll update your driver for interactive call backs. Call backs are a way to communicate changes the user is making in “real time” where your driver needs to intervene as the changes are made and before the user exits the current menu being displayed. These would be exception cases that the driver could interrupt the normal browser engine process.

To add call backs, the file `HiiConfigAccess.c `of your driver will be updated in the function `MyWizardDriverHiiConfigAccessCallback`. This function is called whenever any VFR items have a flag for `INTERACTIVE` set. So far, the previous labs did not have any call back items.

We can see this because there was a “Debug” call made in the `MyWizardDriverHiiConfigAccessCallback` function that never gets called:

- `HiiConfigAccess.c` (line 331)

```
DEBUG ((DEBUG_INFO, "\n:: START Call back ,Question ID=0x%08x Type=0x%04x Action=0x%04x", QuestionId, Type, Action));

```

### Lab 8_a._ Add the Case statements to the Call back routine {#a-add-the-case-statements-to-the-call-back-routine}

1. **Update** the HiiConfigAccess.c file 
2. **Add** the following code before `return status`; to include a “`case`” statement in the call back routine for the “action” passed. |
Add the following code at approx. line 343 before: <br> `return status;` 
```
//Begin Code
switch (Action) { // Start switch and passed param Action
  case EFI_BROWSER_ACTION_FORM_OPEN:  // 3
    {
    }
    break;

  case EFI_BROWSER_ACTION_FORM_CLOSE: // 4
    {
    }
    break;

  case EFI_BROWSER_ACTION_RETRIEVE: // 2
    {
    }
    break;
//End Code
```
![](/media/image75.png)
3. **Save** HiiConfigAccess.c 

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
5. Now at the setup front page menu,** select** “Device Manager”
6. Inside the Device Manager menu** press **the down arrow to “My Wizard Driver Sample Formset” **Press** "Enter"
7.  **Notice** that there are **No Debug messages for Call back** to the QEMU Console. 
8. **Press** “Escape”  and another "Escape" to exit the “Device Manager” 
9. **Select** “Continue” and then **Press** "Enter”
10. **Type** “reset” at the Shell prompt <br>
![](/media/image25.png)
11. **Exit** QEMU


### Lab 8_b._ Update the Menu for Interactive items {#b-update-the-menu-for-interactive-items}

1. **Update** the MyWizardDriver.vfr file 
2. Now, you’ll add the flag characteristic `INTERACTIVE` to the string item’s flags by using keyword `INTERACTIVE` and `questionid`. **Add** the following code in the location shown below: <br>Approx. line 83 and line 86<br>
 `questionid = 0x1001,` <br>
 `flags = INTERACTIVE,` <br>
![](/media/image78.png)
3. Include the numeric item by adding the following code in the location shown below, Approx. line 97 and line 100 <br>
` questionid = 0x1111,` <br>
` | INTERACTIVE ,` <br>
![](/media/image79.png)
4. **Save** MyWizardDriver.vfr 

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
5. Now at the setup front page menu,** select** “Device Manager”
6. Inside the Device Manager menu** press **the down arrow to “My Wizard Driver Sample Formset” **Press** "Enter"
7. Take a moment and ** review ** the QEMU Console  window
8. In the emulation window,** click **on “Name of Configuration” and “Enter ZY Base(Hex)”
9. **Notice ** the following in the QEMU Console  window: <br>
Every time the browser does anything with the interactive labeled fields there is a call made to your driver’s call back function.  We can determine which item by the `quetionid` and what action by the Action passed to your call back function.  Your call back function can then add code to special case when these transitions occur.
<br>
**Entering Form**
![](/media/image80.png)
**Changing a Value for Question ID 0x1111**
![](/media/image81.png)
**Changing a Value for Question ID 0x1001**
![](/assets/image82-84.JPG)
10. **Press** “Escape”  and another "Escape" to exit the “Device Manager” 
11. **Select** “Continue” and then **Press** "Enter”
12. **Type** “reset” at the Shell prompt <br>
![](/media/image97.png)
13. **Exit** QEMU


---

For any build issues copy the solution files from ~/FW/LabSolutions/LessonE.8

NOTE: Delete Directory ~/src/edk2/Build/OvmfX64/DEBUG_GCC5/X64/MyWizardDriver before the Build command to build the MyWizardDriver Clean


### End of Lab 8