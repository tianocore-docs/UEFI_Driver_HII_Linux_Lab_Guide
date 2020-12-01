<!--- @file
 file Updating HII to Save Data Settings.md

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

## Lab 2\. Updating HII to Save Data Settings {#2-updating-hii-to-save-data-settings}
  
In this lab, you’ll learn how to modify and update your driver’s HII code to save the users settings into NVRAM. The UEFI Driver Wizard created the protocols for your driver to update and interface with the HII browser engine and database. The HII configuration access Protocol functions for MyWizardDriver are in the file ~src/edk2/MyWizardDriver/HiiConfigAccess.c. This next lab will install these protocols and update them to save the user data from the HII menus into NVRAM.

1. **Update** the` MyWizardDriver.c `file <BR> Your driver will need to keep track of the consumed protocols in it’s own data structure so it will need to declare local pointers to these and then store them in its own private context data structure.
2. **Add** the following local variable declarations in the function `MyWizardDriverDriverEntryPoint` Entry Point (as shown below Approx. line 185 ): 
```
EFI_HII_STRING_PROTOCOL         *HiiString;
EFI_FORM_BROWSER2_PROTOCOL      *FormBrowser2;
EFI_HII_CONFIG_ROUTING_PROTOCOL *HiiConfigRouting;
```
![](/media/image27.png)
3. **Add** the following code to locate and store consumed protocols **before** the `// Publish sample Fromset and config access`  comment (as shown below Approx. line 227): <br> The reason is to Locate the Hii Database, Hii String, Hii Form browser and config routing protocols and store their pointers into the Private context data structure for your driver to access.

```
//Begin code
 //
  // Locate Hii Database protocol
  //
  Status = gBS->LocateProtocol (&gEfiHiiDatabaseProtocolGuid, NULL, (VOID **) &HiiDatabase);
  if (EFI_ERROR (Status)) {
    return Status;
  }
  PrivateData->HiiDatabase = HiiDatabase;

  //
  // Locate HiiString protocol
  //
  Status = gBS->LocateProtocol (&gEfiHiiStringProtocolGuid, NULL, (VOID **) &HiiString);
  if (EFI_ERROR (Status)) {
    return Status;
  }
  PrivateData->HiiString = HiiString;
  
  //
  // Locate Formbrowser2 protocol
  //
  Status = gBS->LocateProtocol (&gEfiFormBrowser2ProtocolGuid, NULL, (VOID **) &FormBrowser2);
  if (EFI_ERROR (Status)) {
    return Status;
  }
  PrivateData->FormBrowser2 = FormBrowser2;
  
  //
  // Locate ConfigRouting protocol
  //
  Status = gBS->LocateProtocol (&gEfiHiiConfigRoutingProtocolGuid, NULL, (VOID **) &HiiConfigRouting);
  if (EFI_ERROR (Status)) {
    return Status;
  }
  PrivateData->HiiConfigRouting = HiiConfigRouting;

//End code
```
![](/media/image28.png)<br>

4). Since the Hii Database Protocol was located earlier in the code with the previous code insertion and is no longer necessary,** comment out **the old OpenProtocol code with the “`//`” (approx. lines 289-298, as shown below) and** add the comment **"`//`" Done above <br> 
Make sure not to comment out the second “` if (!EFI_ERROR (Status)) {`”<br>

![](/media/image29.png)<br> 
**Note**:  The earlier `LocateProtocol` code already found the pointer to the Hii Database protocol and stored it to the local pointer variable `HiiDatabase`. <br> When we added the driver-consumed protocols, we searched via `LocateProtocol` for the Hii Database pointer function. Since we did it above we’re now commenting out this code.<br>

5). **Comment out** the matching “`}`” with “`//`” to the if statement (as shown below at approx. line 310): 
![](/media/image30.png)<br>

6). **Save** MyWizardDriver.c<br>

7). **Open** ~src/edk2/MyWizardDriver/HiiConfigAccess.c. <br>  The Driver Wizard only made dummy functions for the extract, route and callback functions. In order to save the Data passed into the forms from the Hii Browser engine, you will need to port these functions to be functional.<br>

8). **Add** the following extern statements for the form GUID and the NVRam variable (as shown below) these are global to the driver module only hence the beginning lower case “m” is the standard for a global for a module : 

```
extern EFI_GUID   mMyWizardDriverFormSetGuid;
extern CHAR16     mIfrVariableName[];
```
![](/media/image31.png)<br>

9). **Locate** `MyWizardDriverHiiConfigAccessExtractConfig` and **replace** line 108, “`return EFI_NOT_FOUND`”, with the following code spread over  **Next** pages: 
![](/media/image32.png)<br>
```
//Begin code
 EFI_STATUS                       Status;
  UINTN                            BufferSize;
  MYWIZARDDRIVER_DEV			   *PrivateData;
  EFI_HII_CONFIG_ROUTING_PROTOCOL  *HiiConfigRouting;
  EFI_STRING                       ConfigRequest;
  EFI_STRING                       ConfigRequestHdr;
  UINTN                            Size;
  BOOLEAN                          AllocatedRequest;
  if (Progress == NULL || Results == NULL) {
    return EFI_INVALID_PARAMETER;
  }
  //
  // Initialize the local variables.
  //
  ConfigRequestHdr  = NULL;
  ConfigRequest     = NULL;
  Size              = 0;
  *Progress         = Request;
  AllocatedRequest  = FALSE;
  PrivateData = MYWIZARDDRIVER_DEV_FROM_THIS (This);
  HiiConfigRouting = PrivateData->HiiConfigRouting;
  //
  // Get Buffer Storage data from EFI variable.
  // Try to get the current setting from variable.
  //
  BufferSize = sizeof (MYWIZARDDRIVER_CONFIGURATION);
  Status = gRT->GetVariable (
            mIfrVariableName,
            &mMyWizardDriverFormSetGuid,
            NULL,
            &BufferSize,
            &PrivateData->Configuration
            );
if (EFI_ERROR (Status)) {
  return EFI_NOT_FOUND;
}
  if (Request == NULL) {
	    DEBUG ((DEBUG_INFO, "\n:: Inside of Extract Config and Request == Null "));
  } else { 
    ConfigRequest = Request;
  } 
    //
    // Convert buffer data to <ConfigResp> by helper function BlockToConfig()
    //
    Status = HiiConfigRouting->BlockToConfig (
                                  HiiConfigRouting,
                                  ConfigRequest,
                                  (UINT8 *) &PrivateData->Configuration,
                                  BufferSize,
                                  Results,
                                  Progress
                                  );
  //
  // Free the allocated config request string.
  //
  if (AllocatedRequest) {
    FreePool (ConfigRequest);
  }
  //
  // Set Progress string to the original request string.
  //
  if (Request == NULL) {
    *Progress = NULL;
  } else if (StrStr (Request, L"OFFSET") == NULL) {
    *Progress = Request + StrLen (Request);
  }
  return Status;
// End code
```
10).  Now **locate** `MyWizardDriverHiiConfigAccessRouteConfig` and **replace** line at approx. 228, with “`return EFI_NOT_FOUND`”, with the following code: 
![](/media/image33.png)<br>


```
//Begin code
EFI_STATUS                       Status;
  UINTN                            BufferSize;
  MYWIZARDDRIVER_DEV		       *PrivateData;
  EFI_HII_CONFIG_ROUTING_PROTOCOL  *HiiConfigRouting;

  if (Configuration == NULL || Progress == NULL) {
    return EFI_INVALID_PARAMETER;
  }

  PrivateData = MYWIZARDDRIVER_DEV_FROM_THIS (This);
  HiiConfigRouting = PrivateData->HiiConfigRouting;
  *Progress = Configuration;

  //
  // Get Buffer Storage data from EFI variable
  //
  BufferSize = sizeof (MYWIZARDDRIVER_CONFIGURATION);
  Status = gRT->GetVariable (
            mIfrVariableName,
            &mMyWizardDriverFormSetGuid,
            NULL,
            &BufferSize,
            &PrivateData->Configuration
            );
  if (EFI_ERROR (Status)) {
    return Status;
  }
  
  //
  // Convert <ConfigResp> to buffer data by helper function ConfigToBlock()
  //
  BufferSize = sizeof (MYWIZARDDRIVER_CONFIGURATION);
  Status = HiiConfigRouting->ConfigToBlock (
                               HiiConfigRouting,
                               Configuration,
                               (UINT8 *) &PrivateData->Configuration,
                               &BufferSize,
                               Progress
                               );
  if (EFI_ERROR (Status)) {
    return Status;
  }

  //
  // Store Buffer Storage back to EFI variable
  //
  Status = gRT->SetVariable(
                  mIfrVariableName,
                  &mMyWizardDriverFormSetGuid,
                  EFI_VARIABLE_NON_VOLATILE | EFI_VARIABLE_BOOTSERVICE_ACCESS,
                  sizeof (MYWIZARDDRIVER_CONFIGURATION),
                  &PrivateData->Configuration
                  );
  	    DEBUG ((DEBUG_INFO, "\n:: ROUTE CONFIG Saving the configuration to NVRAM \n"));

  return Status;


  //return EFI_NOT_FOUND;
//end code
```

11). Lastly**, locate** MyWizardDriverHiiConfigAccessCallback and **replace **at approx.**** line 326, “**return EFI_UNSUPPORTED**;”, with the following code: 
![](/media/image34.png)<br>

```
//Begin code
  MYWIZARDDRIVER_DEV      *PrivateData;
  EFI_STATUS                      Status;
  EFI_FORM_ID                     FormId;

    DEBUG ((DEBUG_INFO, "\n:: START Call back ,Question ID=0x%08x Type=0x%04x Action=0x%04x", QuestionId, Type, Action));



  if (((Value == NULL) && (Action != EFI_BROWSER_ACTION_FORM_OPEN) && (Action != EFI_BROWSER_ACTION_FORM_CLOSE))||
    (ActionRequest == NULL)) {
    return EFI_INVALID_PARAMETER;
  }


  FormId = 0;
  Status = EFI_SUCCESS;
  PrivateData = MYWIZARDDRIVER_DEV_FROM_THIS (This);

  return Status;
//end code
```

12). **Save** HiiConfigAccess.c 

#### Build and test MyWizardDriver

1.  At the Terminal Command Prompt (**Cntl-Alt-T**)
```
bash$ cd ~/src/edk2
bash$ build
```
2. **Copy**  MyWizardDriver.efi  to hda-contents<br>	  
 `bash$ cd ~/run-ovmf/hda-contents`<br>
 `bash$ cp ~/src/edk2/Build/OvmfX64/DEBUG_GCC5/X64/MyWizardDriver.efi .` <br>
3. **Invoke** Qemu <br>
 `bash$ cd ~/run-ovmf`<br>
 `bash$ . RunQemu.sh `<br>
4.  **At the UEFI Shell prompt,type** fs0: 
5.  **Type** Load MyWizardDriver.efi 
6.  **Press** “Enter” 
![](/media/image17.png)
7.  **Type** exit 
8.  Now at the setup front page menu,** select **“Device Manager”
![](/media/image18.png)
9. **Press** “Enter”  to enter **“Device Manager”**
10. **Inside the Device Manager menu** press **the down to “My Wizard Driver Sample Formset”**** **Press** "Enter"
![](/media/image19.png)
**Press** "Enter"
![](/media/image20.png)
**_Note_**: Once you hit “Enter”, notice that your form is now displayed with a choice to enable your Device. Also notice the titles and help strings that are in the .UNI file you edited. 
11. Test by **Press** the space bar to Enable and Disable the “Enable My XYZ Device” to change its value from**:** `[X]` to `[ ]` <br> **Note**: Notice the “`Configuration changed`” message at the menu bottom. 
12. **Press** “F10” 
13. **Press **“Escape” to exit
14. **Press **“Escape” to exit the “Device Manager” Page
15. **Press **Up Arrow to “Continue”
![](/media/image24.png)
16. **Press** “Enter” 
17.  At the Shell Prompt type **`Shell> dmpstore -all`** <BR> **Notice** that enable is selected and saved in NVRam as the value of 0x00:
![](/media/image36-37.JPG)
Because our data structure in` MyWizardDriverNVDataStruc.h` is stored in NVRAM with the variable name `MWD_IfrNVData` of type `MYWIZARDDRIVER_CONFIGURATION`, we can see the changes from our menu accessing through our HII forms.<br> Notice that the enable/disable byte is the last byte in data structure `MWD_IfrNVData.MyWizardDriverChooseToEnable` where `00 == disable` and `01 == enable`.
18. **Type** Reset  <br>

![](/media/image97.png)
19. **Exit** Qemu

---

For any build issues copy the solution files from  ~/Fw/LabSolutions/LessonE.2

NOTE: Del Directory ~/src/edk2/Build/OvmfX64/DEBUG_GCC5/X64/MyWizardDriver before the Build command to build the MyWizardDriver Clean


### End of Lab 2