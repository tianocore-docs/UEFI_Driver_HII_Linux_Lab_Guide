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
## Lab 9\. Add code to your driver when Call Back events occur for Interactive Items {#9-add-code-to-your-driver-when-call-back-events-occur-for-interactive-items}

In this lab, you’ll update your driver to print debug statements when the HII browser engine calls back into your call back function. Every time the browser does anything with the interactive labeled fields there is a call made to your driver’s call back function. We can determine the item by the `quetionid` and what action based on the action passed to your call back function. Your call back function can then add code to special case when these transitions occur.

For this lab we will simply add Debug print statements. However, the use of adding call backs to a driver’s HII functions adds the capability of providing more manageability and flexibility for the interactions between the user, the browser engine, and your driver code. In a real driver firmware situation, it may be desired to implement more complex features and functionality based upon an item changing.

1. **Update** the HiiConfigAccess.c file 
2. **Comment** **out** the DEBUG statement with “`//`” in the `MyWizardDriverHiiConfigAccessCallback` call back function approx. line 330: <br>
`    //`<br>
![](/media/image85.png)
3. **Add** a switch case statement of the question ID’s to the “Action” switch case of `EFI_BROWSER_ACTION_CHANGING` in the call back function by **adding** a nested switch case code (as shown below at approx. line 372 ) 
```
		 switch (QuestionId) {
			 case 0x1111:
				     DEBUG ((DEBUG_INFO, "\n:: START Call back- Changing ,Question ID=0x%08x Type=0x%04x Action=0x%04x", QuestionId, Type, Action));
				 break;
			 case 0x1001:
				     DEBUG ((DEBUG_INFO, "\n:: START Call back- Changing ,Question ID=0x%08x Type=0x%04x Action=0x%04x", QuestionId, Type, Action));
				 break;
			 default:
				 Status = EFI_UNSUPPORTED;
				 break;
		 }
```
![](/media/image86.png)	
4.  **Add** another nested switch case statement of the question ID’s to the “Action” switch case of EFI_BROWSER_ACTION_CHANGED in the call back function (as show below at approx. line 388): 

```
				 switch (QuestionId) {
			 case 0x1111:
				     DEBUG ((DEBUG_INFO, "\n:: START Call back- Changed ,Question ID=0x%08x Type=0x%04x Action=0x%04x", QuestionId, Type, Action));
				 break;
			 case 0x1001:
				     DEBUG ((DEBUG_INFO, "\n:: START Call back- Changed ,Question ID=0x%08x Type=0x%04x Action=0x%04x", QuestionId, Type, Action));
				 break;
			 default:
				 Status = EFI_UNSUPPORTED;
				 break;
		 }

```
![](/media/image87.png)<br>
5. **Save** MyWizardDriver.c 

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
7.  **Observe **the QEMU Console Window or debug output <BR>
**Test:** in the your drivers menu by changing the “Name of Configuration” and the “Enter ZY Base(Hex)” fields while observing the QEMU Console Window or debug output.
8. **Observe **in the QEMU Console Window or debug output the changes that you made.
![](/media/image88.png)
**Notice: ** when changing the “**Name of Configuration**” field
![](/media/image89.png)
**Notice: **when changing the “**Enter ZY Base(Hex)**” field
![](/media/image90.png)
**Notice: **when Pressing “F10”
9. **Press** “Escape” and another "Escape" to exit the “Device Manager”
10. **Select** “Continue” and then **Press** "Enter”
11. **Type** “reset” at the Shell prompt <br>
![](/media/image97.png)<br>
12.** Exit** QEMU

---
For any build issues copy the solution files from  ~/FW/LabSolutions/LessonE.9

NOTE: Delete Directory ~/src/edk2/Build/OvmfX64/DEBUG_GCC5/X64/MyWizardDriver before the Build command to build the MyWizardDriver Clean
 

### End of Lab 9