## Forging MVT-Tool Results - Proof of Concept

I am demonstrating yet another way to get false positive results using the MVT-Tool.  Amnesty International and The Citizen Lab continually claim to find Pegasus malicious processes are only found on iPhones that have been infected with the NSO Group spyware. 

This proof of concept demonstrates a  lack of knowledge as it pertains to iOS mobile forensics, iOS development, and basic iOS mobile device framework querying. 

### Setting Up

I performed the following to achieve the forgery.

1. Downloaded the Appium iOS Test App https://github.com/appium/ios-test-app
2. Load the test app into Xcode
3. I changed the Display Name to Phone Diagnostics and named the Bundle Identifier Diagnosticd. Diagnosticd is one of the malicious indicators of compromise found in the STIX2 file created by Amnesty International 
![Forging MVT-Tool Results](https://i.postimg.cc/63FGFdLQ/Screen-Shot-2022-11-27-at-11-15-10-PM.png)
4. This is an example of what the app looks like on the device![Forging MVT-Tool Results - App installed](https://i.postimg.cc/MG83JQ5d/Diagnosticsd.png) 
5. `idevicebackup2 encryption on 123`
6. `idevicebackup2 backup --full .`
7. `mkdir decrypt`
8. `mkdir results`
9. `mvt-ios decrypt-backup -d decrypt/ -p 123 0137152d6c6b1fe5cc8af13f34f123e080128445/`
10. `mvt-ios check-backup -o results/ decrypt/`

## Results
### Watch the video here
[![# False Positive Pegasus Spyware Forging With MVT-Tool](https://i.postimg.cc/zBBYdBk0/forging-pegasus-youtube.jpg)](https://www.youtube.com/watch?v=3KHmJVO547Y")

`WARNING  [mvt.ios.cli] The analysis of the backup produced 2 detections!`

`WARNING  [mvt.ios.modules.mixed.locationd] Found a known suspicious process name "Diagnosticd" matching indicators from "Pegasus"`
`WARNING  [mvt.ios.modules.mixed.locationd] Found a suspicious process name in LocationD entry Diagnosticd`
`WARNING  [mvt.ios.modules.mixed.osanalytics_addaily] Found a known suspicious process name "Diagnosticd" matching indicators from "Pegasus"`

> MVT-Tool is only looking for a text name, there is no logic or reasoning behind the software that is supposed to detect Pegasus Spyware.

A user can download a legitimate app from the Apple App Store called Bluetooth Files for example, and the bundle identifier could be called bluetoothfs. 

bluetoothfs is a malicious indicator Amnesty International has cleared to be Pegasus, but in this example it is a normal iOS application the user has downloaded. 

Amnesty and Citizen Lab work off of device backups and don't need physical phones, according to Director of The Citizen Lab Ron Deibert. 

The results generated by the MVT-Tool do not show The CFBundleDisplayName of the "Malicious Process"

The application is only looking for the "BundleId"

This is an example output from the results file generated by MVT-Tool
`{
        "BundleId": "Diagnosticd",
        "SupportedAuthorizationMask": 1,
        "Executable": "/private/var/containers/Bundle/Application/F0DD3918-FD87-4733-A89A-7B32A0B13C2C/Dignosticd.app/Dignosticd",
        "Registered": "/private/var/containers/Bundle/Application/F0DD3918-FD87-4733-A89A-7B32A0B13C2C/Dignosticd.app/Dignosticd",
        "package": "Diagnosticd",
        "matched_indicator": {
            "value": "Diagnosticd",
            "type": "processes",
            "name": "Pegasus",
            "stix2_file_name": "raw.githubusercontent.com_AmnestyTech_investigations_master_2021-07-18_nso_pegasus.stix2"
        }
        `

If you run the following command with the physical iPhone you will see that Diagnosticd is the identifier for a legitimate iPhone App.

`ideviceinstaller --list-apps`
`CFBundleIdentifier, CFBundleVersion, CFBundleDisplayName`
`Diagnosticd, "1.0", "Phone Diagnostics"`



## Conclusion

1. Without physical devices a proper iOS mobile forensics examination and analysis cannot be performed
2.  iOS backups can be manipulated easily, and MVT-Tool does not hash their backups to prevent tampering
3. Legitimate Apple App store applications can be mistaken as Pegasus processes because MVT-Tool disregards the Application Name
