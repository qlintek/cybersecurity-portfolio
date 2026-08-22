# Incident Report: Malicious Browser Installation & Persistence Hunt

## Summary
TEchnician was contacted regarding malicious indicators on laptop. Customer complained of frequent popups and thought it was a Virus.

Technician turned PC on and quickly disconnected from internet, Customer already had internet key saved.

## Initial Findings
After Disconnected from network, Technician searched downloads and discovered a new browser had recently been installed along with suspicious extension **"Package Tracker_igkzb"** searching logs found McAfee anti-virus reported blocking Shift Browser from connecting to `downloads77-windows[.]73dkt-vwrfs[.]xyz`

Both of these naming conventions are indicative of malicious software. OSINT search indicated the downloads address is a known malicious entity. 

## Process Investigation
Technician began trying to delete Shift Browser after confirming with customer that they were unfamiliar with a Shift Browser or any package tracking extensions. however when technician attempted to uninstall and delete program error message reported that program was open. 

Technician ran a task query to identify suspicious tasks:

`tasklist /v`

Technician noted 6 instances of **shift.exe** running in the background, Technician force killed those instances with:

`taskkill /IM shift.exe /F`

## Persistence Investigation

### Startup Entries

Then to attempt to prevent persistence of malicious actor, Technician checked for startup tasks and scheduled tasks.

`wmic startup get caption,command` 

was used to identify start up tasks which revealed a suspicious shift start up and a suspicious copilot start up. Deleted those tasks with:

`reg delete "HKCU\Software\Microsoft\Windows\CurrentVersion\Run" /v <name of tasks>`

### Scheduled Tasks

then I queried scheduled tasks with:

`schtasks /query /fo LIST /v`

Which provided a difficult to read list of schedules tasks so technician piped results to a csv and exported to excel to parse and search more quickly on customer device.

`schtasks /query /fo LIST /v > <user direcotry>/Desktop/Task_Schedcule.csv`

Technician identified suspicious task labeled "ShiftLaunchTask" and then used command:

`schtasks /delete /tn "ShiftLaunchTask" /f`

to get rid of it.

### Offline Scan
Technician recovered Customer's BitLocker key and ran an offline scan, which came up clean. 

## Ticket Pause for End of Shift
Technician is out of time for Persistence hunt today and is powering down customer device to prevent any missed persistence.

## Plan for next shift

1. Run Malwarebytes
2. Run AdwCleaner
3. Reboot and check for Regeneration
    -Startup entries
    -Scheduled tasks
    -Suspicious processes
    -AppData executables
    -Browser extensions


