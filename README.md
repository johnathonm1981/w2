# Optimize-Offline #

Optimize-Offline is a Windows Image (WIM) optimization script designed for Windows 10 builds RS2 to RS5 64-bit architectures.

## About Optimize-Offline ##

- Primary focus' are the removal of unnecessary bloat, privacy and security enhancements, cleaner aesthetics, increased performance and a significantly better user experience.
- Does not perform any changes to an installed or live system nor can it optimize a live system.
- Makes multiple changes to both the offline system and registry hives to enhance security, usability and privacy while also improving performance.
- Checks the health of the image both before and after the script runs to ensure the image retains a healthy status.
- Detects what System Applications were removed, and further removes any associated drivers or services associated with them.
- Adds removed System Applications' scheduled tasks to the SetupComplete script to be automatically disabled during Windows installation.
- Optional Features and Windows Packages can be removed and/or disabled by adding them to the editable field list and using their respective switches.

## Script safety ##

It is the responsibility of the end-user to be aware of what each parameter and switch does.

## About the -Registry switch ##

The -Registry switch applies an array of entries and values to the image's registry hives designed to further enhance both the security of the default image as well as its usability and aesthetics.
The script only applies those registry entries and values applicable to the image build being optimized and bypasses those that are unsupported.
A few of the switch settings:

- Completely disables Cortana without removing the default search feature.
- Disables a significant amount of telemetry, logging, tracking, monitoring and background feedback submission.
- Prevents bloatware link creation and disables a plethora of annoying default features.
- Disables Windows' annoying pop-up notifications and tips.
- Disables non-explicit application and system sensor access.
- Disables error reporting and automatic syncronization.
- Improves the default aesthetics by cleaning-up redundant or unused links.
- etc.

## About the SetupComplete.cmd script ##

The SetupComplete.cmd is a setup script that automatically runs after the OOBE component pass completes during the setup of a new Windows 10 installation. It includes the further automatic disabling of tasks for services or applications that were removed and the implementation of firewall rules to block telemetry. It also includes the automatic detection and removal of the DefaultUser0 ghost account that can often times be created.

## Script process and settings danger ##

None of the automatic processes or settings are dangerous; however one must be careful when selecting what System Applications are removed.

## About System Applications ##

System Applications are a lot like Provisioned Application Packages (Metro Apps) in respect that they are provisioned and installed during the setup of Windows. During the Windows Setup component pass, setup looks for these System Applications in the default registry and provisions them for installation only if their entries are present. By removing these entries, Windows Setup does not provision them for installation.

This method is safer than force removing the System Application using its component package because it retains the default file structure. Furthermore, the force removal of System Applications' component packages can trip the dreaded "STATUS_SXS_COMPONENT_STORE_CORRUPT" flag. This is a critical component store corruption flag that will then be detected by any servicing command and Windows Update and prevent both the servicing and updating of the Operating System. The only way to remedy and fix this error is to re-install or reset the Operating System.

*The upcoming GUI version of Optimize-Offline has full component package removal by both changing the permanency values of packages and using the DISM.API to allocate them and then remove them*.

## System Applications universally safe, and recommended, to remove ##

This can vary, depending on the end-user's final image requirements, but SecHealthUI (Windows Defender), ParentalControls, ContentDeliveryManager, MicrosoftEdge, PPIProjection, HolographicFirstRun, BioEnrollment (if no Biometrics will be used), SecureAssessmentBrowser and (optionally) XboxGameCallableUI are all safe to remove. XboxGameCallableUI should only be removed if all Xbox Provisioned Application Packages are also removed and will not be used (see below). Moreover, Cortana can also be removed; however, doing so will render the default search feature inoperable so its removal is only recommended if the end-user will be using a 3rd party search program like ClassicShell.

**ShellExperienceHost should never be removed**.

## Provisioned Application Packages (Metro Apps) removal ##

The removal of Xbox.TCUI and Xbox.IdentityProvider will prevent the Windows Store Apps Troubleshooter from working properly and likewise affect the Windows Store. It is not recommended to remove these if the Windows Store is required.

## About the Defaultuser0 ghost account ##

Any time an OEM Windows Image is modified offline, or the System Preparation, Reset and Provisioning Package deployment features are used, there is a chance this ghost account will surface.
defaultuser0 is not a real account, however, and is a bug that has been present in Windows through countless flavors and variations. It is not added to any user groups nor does it even have a profile.
Conversely, failing to remove the defaultuser0 account immediately after Windows Installation completes can lead to future headaches.  As an example, if you reset Windows with the defaultuser0 ghost account still present, upon the restart of the device, Windows will force you to log into the defaultuser0 account to continue.

Optimize-Offline remidies this issue by first setting the proper key property in the offline registry hives that enables non-elevated removal of the defaultuser0 ghost account, and second by adding a function in the SetupComplete.cmd script that queries the registry for the SID of the defaultuser0 ghost account, and then proceeding to remove the account, registry keys and profile directories automatically if that SID is found.
This ensures the defaultuser0 ghost account, if present, is always entirely removed immediately after the installation of Windows.

**A reboot is recommended after the first bootup of the optimized image in order to complete the DefaultUser0 ghost account removal**.