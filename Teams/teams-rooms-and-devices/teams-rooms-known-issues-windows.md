---
title: Known issues with Teams Rooms on Windows
description: Provides a list of known issues for Teams Rooms on Windows.
ms.author: luche
author: helenclu
ms.reviewer: sohailta, garyanselme, czawideh
manager: dcscontentpm
audience: ITPro
ms.topic: troubleshooting
f1.keywords: 
  - NOCSH
localization_priority: Normal
ms.collection: 
  - M365-collaboration
ms.custom: 
  - sap:Msft Teams Rooms Windows (MTRW)
  - seo-marvel-apr2020
  - CSSTroubleshoot
  - CI 121996
  - CI 160661
  - CI 164223
appliesto: 
  - Microsoft Teams
search.appverid: 
  - MET150
ms.date: 07/11/2024
---
# Known issues with Teams Rooms on Windows

<!-- If you get word that one of these issues no longer applies, contact meerak@microsoft.com to EoL the corresponding KB.  -->

## Software issues

| Issue  |  Description | Workaround |
| --- | --- | --- |
|Teams Rooms app out of date|The Teams Rooms console displays the **system config out of date** error.|To fix this issue, [use the Microsoft Teams Rooms recovery tool](/MicrosoftTeams/rooms/recovery-tool).|
|Can't access Windows Settings from the desktop right-click menu.|On Teams Rooms devices that are running app version 3.1.98.0 or later versions, any user account that has the Teams Rooms app installed can access Windows Settings only by using the Start menu. If you try to access Windows Settings by using another method, the Teams Rooms app will open instead.|Use one of the following workarounds to access Windows Settings:<ul><li>Select the **Settings** option from the **Start** menu.</li><li>Uninstall the Teams Rooms app for the affected user. Locate the app on the **Start** menu, right-click it and select **Uninstall**.</li></ul>|
|Teams Rooms device loses internet connectivity after installing a feature update for Windows| A Teams Rooms device that is connected to a network, which requires a proxy can't connect to the internet after installing a feature update for Windows, including an upgrade to Windows 11.<br/><br/>This issue occurs if the proxy setting for internet access on the affected device is configured for the LocalSystem instead of a system-wide proxy. The Windows feature update deletes the LocalSystem settings. This behavior is by design.|If your device has proxy settings set for the LocalSystem user, transfer those settings to the [system-wide proxy settings](/windows/client-management/mdm/networkproxy-csp) that are set and maintained through Mobile Device Management (MDM). Alternatively, you can use the [Set-WinInetProxy](https://www.powershellgallery.com/packages/WinInetProxy/0.1.0) PowerShell cmdlet to configure the system-wide proxy settings.</br></br>When you use system-wide proxy settings, they won't be affected when you install Windows feature updates on the device in future.|
|Test service plans display in Teams Rooms Pro and Teams Rooms Basic licenses|IT Admins who assign user licenses in the Microsoft Teams admin center or in Microsoft Entra ID see the following test service plans in Teams Rooms Pro and Teams Rooms Basic licenses:<ul><li>Teams Rooms Test 1</li><li>Teams Rooms Test 2</li></ul> | No workaround is required. These service plans don't have any capabilities and can be ignored. They'll continue to display for the lifetime of the two SKUs.|
|Teams Rooms device console display shows only a **Refresh** button.| On devices that are running version 4.17.x.x of the Teams Rooms for Windows app, you might see a black screen with a **Refresh** button on the console intermittently after the system has been idle for some time.|Select the **Refresh** button to restore the console display. </br></br> If you see the black screen both in the front-of-room display and on the console, select the **Refresh** button on the console display to restore both affected displays.|
|Mismatch between time and calendar display on Teams Rooms device and Front of Room display|If a Teams Rooms device on Windows is idle for more than 2 hours, the time and calendar display on the device freezes. As a result, there's a mismatch between the time and calendar information in the Front of Room display and on the console. This issue occurs intermittently.|To fix the issue, upgrade the Teams Rooms app to version 4.19.57.0.|
|Xbox application window appears on a Teams Rooms device.|In a Teams Rooms on Windows device, when you select **Room Controls** on the console, an Xbox app window appears. In some cases, this window is followed by a pop-up window that displays the following warning message:<br/><br/>In some cases, this window is followed by a pop-up window that displays the following warning message:<br/><br/><i>Gaming Services required</i><br/><br/>The Xbox app is included as part of Windows but should not start automatically.|If you see the warning message, use the following steps to work around the issue:<ol><li>In the Teams Rooms device console, select **More** > **Settings**.</li><li>Select **Windows Settings**.</li><li>Sign in as an administrator.</li><li>Search for and select **Microsoft Store**.</li><li>Select **Library** in the navigation menu.</li><li>Select **Get updates**.</li><li>After all the applications, including the Xbox app finish updating, restart the Teams Rooms device.</li></ol><br/><br/>If you don't see a warning message, use the following steps to prevent the Xbox app from starting automatically:<ol><li>Select **Room Controls** on the device console.</li><li>In the Xbox app window that appears, select **Sign in** > **Settings** in the menu.</li><li>Select **General**.</li><li>Uncheck the **Automatically launch app at startup option**.</li><li>Close the Xbox app window.</li></ol>|

### Issues with the new Teams Rooms app

| Issue  |  Description | Workaround |
| --- | --- | --- |
|New features in the new Teams Rooms app aren't available.|You won't be able to use the new Teams Rooms app unless you install WebView2 runtime version 110.0.1587.63 or higher for all users on your device.|To fix the issue, update to version 5.0.230.0 of the Teams Rooms app.|
|Screen sharing from a video teleconferencing device to a Teams Rooms for Windows device doesn't work.|On a Teams Rooms for Windows device that's assigned a Microsoft Teams Pro license and uses SIP and H.323 dialing, the screen shared from a video teleconferencing device might not display after you upgrade to the new Teams Rooms app.<br/><br/>This issue occurs when a screen sharing session is stopped and then restarted.|To fix the issue, update to version 5.0.230.0 of the Teams Rooms app.|
|Text in QR code displays outside the box.|In the home screen on Teams Rooms on Windows devices that use Germany as the locale, the text in the QR code box to join a meeting overflows the boundary of the box.<br/><br/>This issue is purely visual and doesn't impact the functionality of the QR code.|No workaround required.|
|Home screen display is blank.|On a Teams Rooms for Windows device, when you upgrade the Teams Rooms app from version 4.19.xxx.0 to 5.0.111.0, you might see a blank home screen on the display and a small, black app window on the console.<br/><br/>However, both the Microsoft Teams admin center and the Pro Management Portal identify the device as healthy.<br/><br/>This issue occurs in Teams Rooms devices that are assigned a legacy Teams Rooms Premium license. Those devices have a **Custom** theme setting defined by default.|To fix the issue, update to version 5.0.230.0 of the Teams Rooms app.|
|The display from an external video source is blurry.|You have a Teams Rooms on Windows device that is connected to an external video source by an HDMI. When the device is not in a meeting, the video display from the external source is blurry on the home screen of the device.<br/><br/>This issue occurs after you upgrade the Teams Rooms on Windows app on the device to version 5.0.111.0.|To fix the issue, update to version 5.0.230.0 of the Teams Rooms app.|
|Teams Rooms app doesn't sign in.|In Teams Rooms for Windows devices that are running version 5.0 111.0 of the Teams Rooms app, the assigned Autopilot profile is detected but Autologin fails.|To fix the issue, update to version 5.0.230.0 of the Teams Rooms app.|
|Teams Rooms on Windows device displays error message about a missing license.|After you upgrade the Teams Rooms for Windows app on your device to version 5.0.111.0, you see the following error message:<br/><br/><i>No valid Teams Rooms license found</i><br/><br/>This issue occurs for the following licenses of the Teams Rooms app:<ul><li>MEETING_ROOM_NOAUDIOCONF</li><li>MEETING_ROOM_AR_USGOV_GCCHIGH</li></ul><br/><br/>These licenses are missing the Teams Rooms Standard service plan.|To work around the issue, add the following information to the SkypeSettings.xml file to turn off the Teams Rooms app upgrade to version 5.0.111.0:<br/><br/>\<SkypeSettings><br/>\<EnableTeams21>false\</EnableTeams21><br/>\</SkypeSettings>|
|QR code to join meetings reappears after being disabled.|You have disabled the QR code that is displayed on the device console and in Front of Room displays to join Teams meetings.<br/><br/>However, the QR code reappears when the device restarts, or after a user either joins or leaves a meeting on the device.|Restart the device so that it picks up a new configuration that fixes the issue.|
|Volume change indicator not working|When you change the volume of a Teams Rooms device by using the volume controls that are on the device or on an external audio device, the change isn't displayed in the volume setting of the Teams Rooms device.<br/><br/>The change to the volume settings takes effect but the visual display doesn't reflect the change.|No workaround is required because the functionality is working.|

## Hardware issues

| Issue  |  Description | Workaround |
| --- | --- | --- |
|During a Coordinated meeting, when the meeting volume is changed by using a room remote, the speaker on a Surface Hub or Teams Rooms device turns on.|For a trusted device such as a Surface Hub or Teams Rooms device that is set up to automatically join a Coordinated meeting when the primary device joins, the speaker turns on when a room remote is used to change the meeting volume. This issue occurs even though the audio settings on the device are turned off, and whether they're enabled or disabled.|Turn off proximity join and room remote capabilities on the trusted devices that automatically join a Coordinated meeting.|
|When setting up a new Teams Rooms device, the display becomes unresponsive after you initiate the setup process.|The Out of Box Experience (OOBE) for a new Teams Rooms for Windows device freezes in the following scenarios:<ul><li>On a device that uses an OTP, when you select the **Get Started** button, the UI becomes unresponsive and the button becomes disabled.</li><li>On a device that's enrolled in Windows Autopilot, the setup process doesn't proceed beyond the opening screen.</li></ul><br/>This issue occurs on devices that have version 4.19.82.0 of the Teams Rooms app. The cause of the issue might be one of the following reasons:<ul><li>The device is not [certified](/microsoftteams/rooms/certified-hardware?tabs=Windows) for use with Teams Rooms on Windows.</li><li>The HDMI ingest module on the certified device is not recognized.</li></ul>|Devices that are not certified for use with Teams Rooms on Windows are not supported.</br></br>If your device is certified for use with Teams Rooms on Windows, unplug the device and plug it in again to retry the recognition process for the HDMI ingest module.</br></br>If the issue still persists, contact the OEM.<br/><br/>**Note**: This issue is fixed in Teams Rooms devices that have version 5.0.111.0 of the Teams Rooms app.|

## Limitations

- Teams Rooms doesn't support High-Bandwidth Digital Content Protection (HDCP) input. Using HDCP input might cause issues that affect High-Definition Multimedia Interface (HDMI) ingest functionality, such as video and audio. To avoid these issues, make sure that the HDCP options are turned off for switches that are connected to Teams Rooms.
- When you use the Call app on a Teams Rooms device to dial the toll number or conference ID for a Teams meeting, the conference bridge triggers multiple call flows. Because the Teams Rooms app is designed to support only one active call at a time, the call fails.

  Instead of using the Call app, join the meeting by using the **Join with an ID** option and entering the meeting ID.
- If you're in a call with another user who is on a Teams Rooms device, you can't transfer the call. This is because the Teams Rooms app is designed to support only one call at a time.

## Support for third-party Teams devices

For issues with Teams devices offered by third-party providers, contact their individual sites for support:

- Logitech: [Logitech Support](https://support.logi.com/hc/)
- Crestron: [Crestron Support](https://support.crestron.com/)
- Poly: [Poly Support](https://www.poly.com/us/en/support)

[!INCLUDE [Third-party disclaimer](../../includes/third-party-contact-disclaimer.md)]