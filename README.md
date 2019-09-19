# Start-Menu-Layout
Windows 10 Dynamic Start Menu Script. This adds custom groups to the Windows 10 start menu and can be updated on the fly for users.
Credit for the original script goes to Kevin Johnston: https://github.com/kmjohnston/PowerShell/tree/master/StartMenuLayout

#Instructions
Save the menu.JSON in some place that workstations can access, I use domain.local\netlogon\StartMenu
Customize the menu.json as needed for the number of groups you want and the names of the applications. You can get the exact names by running Get-Apps-And-IDs.ps1 on a machine and examining the output json file.

#Setup a new GPO for the StartMenu
Setup a policy to define a the Start Layout under User Configuration -> Administrative Templates -> Start Menu and Taskbar -> Start Layout
I define the path as c:\ProgramData\StartMenu
Set Create-Start-Menu-Layout-XML.ps1 as alogon script for users (could also do at startup on the computer level)
Set Get-Apps-And-IDs.ps1 as a logoff script for users
Set a file copy policy to copy the menu.json file from the domain.local\netlogon\StartMenu to C:\ProgramData\StartMenu. It's set to replace only if newer.

#Other Info
The Create script will compare at login the generated layout file and existing file if no items have been updated it will not replace the layout.xml defined in the gpo "Start Layout" setting. Ever time the time stamp on the Layout XML is updated Windows will reapply the menu and in doing so will shrink it to be exactly 1 group wide. By not applying a new file unless there is a change will prevent the menu from shrinking ever login and should be a rare occurence.

I do define that this is only a PartialStartLayout. If someone was so inclined to make a switch on the script to define it as enforced you are welcome to submit. Being a Partial Layout allows users to make their own groups and customize those as they desire.

#Issues
I have found that on occasion Windows will stop applying a new layout file. This appears to occur when testing and making frequent changes to the menu.json and the resulting layout.xml. If the menu appears to stop changing open the registry and remove any of keys with tile in the name found here: Computer\HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\CloudStore\Store\Cache\DefaultAccount NOTE: Deleting the keys will blow away the users customizations. I'm sure if someone looked closer they could figure out the exact one. It hasent been an issue for me. Possibly this exact key: HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\CloudStore\Store\Cache\DefaultAccount\$de${randomstring}$start.tilegrid$windows.data.curatedtilecollection.tilecollection
