---
title: Common Education Windows Update configuration
description: Learn about common Windows Update configuration used by Education organizations in Intune.
ms.date: 5/2/2024
ms.topic: tutorial
author: yegor-a
ms.author: egorabr
ms.manager: dougeby
no-loc: [Microsoft, Windows, Autopatch, Autopilot]
---

# Windows Update

Microsoft Intune and Intune for Education can configure many Windows Update configuration settings. This article summarizes the configurations that are most commonly used for student and teacher devices.

Use Microsoft Intune or Intune for Education to manage the install of Windows 10/11 software updates from Windows Update for Business. You can configure update settings on devices and configure deferral of update installation. You can also prevent devices from installing features from new Windows versions to help keep them stable, while allowing those devices to continue installing updates for quality and security.

## Update rings for Windows 10 and later

Update ring policies are a collection of settings that configure when devices that run Windows 10 and Windows 11 updates get installed.

To learn more, see:

- [Update rings for Windows 10 and later policy in Intune](/mem/intune/protect/windows-10-update-rings)
- [The Windows Update policies you should set and why](https://techcommunity.microsoft.com/t5/windows-it-pro-blog/the-windows-update-policies-you-should-set-and-why/ba-p/3270914)
- [YouTube: Windows Update for Business Fundamentals](https://www.youtube.com/watch?v=TXwp-jLDcg0&list=PLMuDtq95SdKvpS9zPyFt9fc9HgepQxaw9&index=1)
- [YouTube: Windows Update for Business Fundamentals (Japanese)](https://youtu.be/o6_eGOyv-_g)

| **Update settings** | **Value** | **Notes** | **CSP** |
| --- | --- | --- | --- |
| **Microsoft product updates** | Allow | Don't set to Block. In order to revert the configuration, PowerShell commands have to be run on each device. | [AllowMUUpdateService](/windows/client-management/mdm/policy-csp-update#allowmuupdateservice) |
| **Windows drivers** | Allow |     | [ExcludeWUDriversInQualityUpdate](/windows/client-management/mdm/policy-csp-update#excludewudriversinqualityupdate) |
| **Quality update deferral period (days)** | 7   |     | [DeferQualityUpdatesPeriodInDays](/windows/client-management/mdm/policy-csp-update#deferqualityupdatesperiodindays) |
| **Feature update deferral period (days)** | 30  | Select 0 if using a [**Feature update policy**](#windows-update-feature-control) otherwise select 30 days. | [DeferFeatureUpdatesPeriodInDays](/windows/client-management/mdm/policy-csp-update#deferfeatureupdatesperiodindays) |
| **Upgrade Windows 10 devices to Latest Windows 11 release** | No  |     | [ProductVersion](/windows/client-management/mdm/policy-csp-update#productversion) |
| **Set feature update uninstall period** (2 - 60 days) | 14  |     | [ConfigureFeatureUpdateUninstallPeriod](/windows/client-management/mdm/policy-csp-update#configurefeatureupdateuninstallperiod) |
| **Enable pre-release builds** | Not configured |     | [ManagePreviewBuilds](/windows/client-management/mdm/policy-csp-update#managepreviewbuilds) |

| **User experience settings** | **Value** | **Notes** | **CSP** |
| --- | --- | --- | --- |
| **Automatic update behavior** | Reset to default | Auto install and restart. Updates are downloaded automatically on non-metered networks and installed during "Automatic Maintenance" when the device isn't in use and isn't running on battery power.<br><br>**Note:** If Windows Update policy is configured via the settings catalog, the value should be **Auto install and restart**. | [AllowAutoUpdate](/windows/client-management/mdm/policy-csp-update#allowautoupdate) |
| **Restart checks (EDU Restart)** | Allow | Must not be disabled in existing Windows Update Rings. This setting is no longer available when creating a new Windows Update Ring policy.  | [SetEDURestart](/windows/client-management/mdm/policy-csp-update#setedurestart) |
| **Option to pause Windows updates** | Disable |     | [SetDisablePauseUXAccess](/windows/client-management/mdm/policy-csp-update#setdisablepauseuxaccess) |
| **Option to check for Windows updates** | Disable |     | [SetDisableUXWUAccess](/windows/client-management/mdm/policy-csp-update#setdisableuxwuaccess) |
| **Change notification update level** | Turn off all notifications, excluding restart warnings |     | [UpdateNotificationLevel](/windows/client-management/mdm/policy-csp-update#updatenotificationlevel) |
| **Use deadline settings** | Allow | Only enables the setting configuration. |     |
| **Deadline for feature updates** | 7   |     | [ConfigureDeadlineForFeatureUpdates](/windows/client-management/mdm/policy-csp-update#configuredeadlineforfeatureupdates) |
| **Deadline for quality updates** | 3   |     | [ConfigureDeadlineForQualityUpdates](/windows/client-management/mdm/policy-csp-update#configuredeadlineforqualityupdates) |
| **Grace period** | 2   |     | [ConfigureDeadlineGracePeriod](/windows/client-management/mdm/policy-csp-update#configuredeadlinegraceperiod)<br><br>[ConfigureDeadlineGracePeriodForFeatureUpdates](/windows/client-management/mdm/policy-csp-update#configuredeadlinegraceperiodforfeatureupdates) |
| **Auto reboot before deadline** | Yes |     | [ConfigureDeadlineNoAutoReboot](/windows/client-management/mdm/policy-csp-update#configuredeadlinenoautoreboot) |

## Settings catalog

Settings described in this section aren't available in an Update ring policy and should be configured using a settings catalog type configuration profile.

To learn more, see [Use the settings catalog to configure settings on Windows, iOS/iPadOS, and macOS devices](/mem/intune/configuration/settings-catalog).

> [!TIP]
> When creating a settings catalog profile in the Microsoft Intune admin center, you can copy a policy name from this article and paste it into the settings picker search field to find the desired policy.

| **Name** | **Value** | **Notes** | **CSP** |
| --- | --- | --- | --- |
| **:::no-loc text="No update notifications during active hours":::** | Enabled |     | [NoUpdateNotificationsDuringActiveHours](/windows/client-management/mdm/policy-csp-update#noupdatenotificationsduringactivehours) |

## Windows Update Feature Control

Windows feature updates contain new Windows features to improve the user experience. Windows feature versions are supported differently depending on the edition. It's important to ensure that the Windows version installed remains supported so that it can receive the latest security updates and support required applications such as testing apps.

Use the support lifecycle websites for each Windows operating system version and edition:

- [Windows 10 Home, Pro, and Pro Education](/lifecycle/products/windows-10-home-and-pro);
- [Windows 10 Enterprise and Education](/lifecycle/products/windows-10-enterprise-and-education);
- [Windows 11 Home, Pro, and Pro Education](/lifecycle/products/windows-11-home-and-pro);
- [Windows 11 Enterprise and Education](/lifecycle/products/windows-11-enterprise-and-education).

There are two ways to control how and when Windows feature updates are installed on Windows.

| **Feature update control type** | **Configuration** | **Advantages** | **Disadvantages** |
| --- | --- | --- | --- |
| **Automatically keep up to date (recommended)** | Use only **Update ring policies** and set a feature update deferral to 30 or more days | Devices are always kept up to date and receive access to the latest Windows features | Users may not have had training on new features, and some apps may not be compatible with the new feature version |
| **Control feature version** | Use **feature update policies** to keep devices at a particular version of Windows | Devices are kept at a particular Windows version until the policy is changed | The policy must be reviewed and changed periodically to ensure Windows remains supported |

### Automatically keep Windows up to date

Using the **update ring** you configured earlier, set the **Feature update deferral period (days)** to **30** days. The deferral period can be increased or decreased based on the school needs.

### Control feature version

A [Feature update policy](/mem/intune/protect/windows-10-feature-updates) can be configured to set devices to a particular version of Windows. Devices running older versions of Windows will update to the specified version. Devices with newer versions of Windows won't perform any feature updates.

To set a feature update policy:

1. Sign in to the [Microsoft Intune admin center](https://go.microsoft.com/fwlink/?linkid=2109431).
2. Select **Devices** > **Windows** > **Feature updates for Windows 10 and later** > **Create profile**.
3. Under **Deployment settings**:
    - Specify a name, a description (optional), and for **Feature update to deploy**, select the version of Windows with the feature set you want, and then select **Next**.
    - Configure **Rollout options** to **As soon as possible**.
4. Under **Assignments**, choose **\+ Select groups to include** and then assign the feature updates deployment to one or more device groups. Select **Next** to continue.
5. Under **Review + create**, review the settings. When ready to save the Feature updates policy, select **Create**.

> [!WARNING]
> If you don't review the feature update version at least every 18 months, your Windows devices may no longer receive security updates. Ensure you review and update the feature version to stay supported.
