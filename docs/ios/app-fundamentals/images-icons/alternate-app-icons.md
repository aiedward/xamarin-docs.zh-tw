---
title: Alternate App Icons in Xamarin.iOS
description: This document describes how to use alternate app icons in Xamarin.iOS. It discusses how to add these icons to a Xamarin.iOS project, how to modify the Info.plist file, and how to manage the app's icon programmatically.
ms.prod: xamarin
ms.assetid: 302fa818-33b9-4ea1-ab63-0b2cb312299a
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/29/2017
ms.openlocfilehash: ed31f1dca3f823ccd0374b4fcbac1bbd9e80e022
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73004309"
---
# <a name="alternate-app-icons-in-xamarinios"></a>Alternate App Icons in Xamarin.iOS

_This article covers using alternate app icons in Xamarin.iOS._

Apple has added several enhancements to iOS 10.3 that allow an app to manage its icon:

- `ApplicationIconBadgeNumber` - Gets or sets the badge of the app icon in the Springboard.
- `SupportsAlternateIcons` - If `true` the app has an alternate set of icons.
- `AlternateIconName` - Returns the name of the alternate icon currently selected or `null` if using the primary icon.
- `SetAlternameIconName` - Use this method to switch the app's icon to the given alternate icon.

![](alternate-app-icons-images/icons04.png "A sample alert when an app changes its icon")

<a name="Adding-Alternate-Icons" />

## <a name="adding-alternate-icons-to-a-xamarinios-project"></a>Adding Alternate Icons to a Xamarin.iOS Project

To allow an app to switch to an alternate icon, a collection of icon images will need to be included in the Xamarin.iOS app project. These images cannot be added to the project using the typical `Assets.xcassets` method, they must be added to the **Resources** folder directly.

請執行下列動作：

1. Select the required icon images in a folder, select all and drag them to the **Resources** folder in the **Solution Explorer**:

    ![](alternate-app-icons-images/icons00.png "Select the icons images from a folder")

2. When prompted, select **Copy**, **Use the same action for all selected files** and click the **OK** button:

    ![](alternate-app-icons-images/icons02.png "The Add File to Folder dialog box")

3. The **Resources** folder should look like the following when completed:

    ![](alternate-app-icons-images/icons01.png "The Resources folder should look like this")

<a name="Modifying-the-Info.plist-File" />

## <a name="modifying-the-infoplist-file"></a>Modifying the Info.plist File

With the required images added to the **Resources** folder, the [CFBundleAlternateIcons](https://developer.apple.com/library/content/documentation/General/Reference/InfoPlistKeyReference/Articles/CoreFoundationKeys.html#//apple_ref/doc/uid/TP40009249-SW13) key will need to be added to the project's **Info.plist** file. This key will define the name of the new icon and the images that compose it.

請執行下列動作：

1. 在 [方案總管]中，按兩下 [Info.plist] 檔案以開啟它進行編輯。
2. Switch to the **Source** view.
3. Add a **Bundle icons** key and leave the **Type** set to **Dictionary**.
4. Add a `CFBundleAlternateIcons` key and set the **Type** to **Dictionary**.
5. Add a `AppIcon2` key and set the **Type** to **Dictionary**. This will be the name of the new alternate app icon set.
6. Add a `CFBundleIconFiles` key and set the **Type** to **Array**
7. Add a new string to the `CFBundleIconFiles` array for each icon file leaving out the extension and the `@2x`, `@3x`, etc. suffixes (example `100_icon`). Repeat this step for every file that makes up the alternate icon set.
8. Add a `UIPrerenderedIcon` key to the `AppIcon2` dictionary, set the **Type** to **Boolean** and the value to **No**.
9. 將變更儲存到檔案。

The resulting **Info.plist** file should look like the following when completed:

![](alternate-app-icons-images/icons03.png "The completed Info.plist file")

Or like this if opened in a text editor:

```xml
<key>CFBundleIcons</key>
<dict>
    <key>CFBundleAlternateIcons</key>
    <dict>
        <key>AppIcon2</key>
        <dict>
            <key>CFBundleIconFiles</key>
            <array>
                <string>100_icon</string>
                <string>114_icon</string>
                <string>120_icon</string>
                <string>144_icon</string>
                <string>152_icon</string>
                <string>167_icon</string>
                <string>180_icon</string>
                <string>29_icon</string>
                <string>40_icon</string>
                <string>50_icon</string>
                <string>512_icon</string>
                <string>57_icon</string>
                <string>58_icon</string>
                <string>72_icon</string>
                <string>76_icon</string>
                <string>80_icon</string>
                <string>87_icon</string>
            </array>
            <key>UIPrerenderedIcon</key>
            <false/>
        </dict>
    </dict>
</dict>
```

<a name="Managing-the-Apps-Icon" />

## <a name="managing-the-apps-icon"></a>Managing the App's Icon 

With the icon images included in the Xamarin.iOS project and the **Info.plist** file correctly configured, the developer can use one of many new features added to iOS 10.3 to control the app's icon.

The `SupportsAlternateIcons` property of the `UIApplication` class allows the developer to see if an app supports alternate icons. 例如:

```csharp
// Can the app select a different icon?
PrimaryIconButton.Enabled = UIApplication.SharedApplication.SupportsAlternateIcons;
AlternateIconButton.Enabled = UIApplication.SharedApplication.SupportsAlternateIcons;
```

The `ApplicationIconBadgeNumber` property of the `UIApplication` class allows the developer to get or set the current badge number of the app icon in the Springboard. 預設值為零 (0)。 例如:

```csharp
// Set the badge number to 1
UIApplication.SharedApplication.ApplicationIconBadgeNumber = 1;
```

The `AlternateIconName` property of the `UIApplication` class allows the developer to get the name of the currently selected alternate app icon or it returns `null` if the app is using the Primary Icon. 例如:

```csharp
// Get the name of the currently selected alternate
// icon set
var name = UIApplication.SharedApplication.AlternateIconName;

if (name != null ) {
    // Do something with the name
}
```

`UIApplication` 類別的 `SetAlternameIconName` 屬性可讓開發人員變更應用程式圖示。 將圖示的名稱傳遞至 select 或 `null`，以回到主要圖示。 例如:

```csharp
partial void UsePrimaryIcon (Foundation.NSObject sender)
{
    UIApplication.SharedApplication.SetAlternateIconName (null, (err) => {
        Console.WriteLine ("Set Primary Icon: {0}", err);
    });
}

partial void UseAlternateIcon (Foundation.NSObject sender)
{
    UIApplication.SharedApplication.SetAlternateIconName ("AppIcon2", (err) => {
        Console.WriteLine ("Set Alternate Icon: {0}", err);
    });
}
```

當應用程式執行且使用者選取替代圖示時，會顯示類似下列的警示：

![](alternate-app-icons-images/icons04.png "A sample alert when an app changes its icon")

如果使用者切換回主要圖示，將會顯示如下的警示：

![](alternate-app-icons-images/icons05.png "A sample alert when an app changes to the primary icon")

<a name="Summary" />

## <a name="summary"></a>總結

本文涵蓋將替代應用程式圖示新增至 Xamarin iOS 專案，並在應用程式內使用它們。

## <a name="related-links"></a>相關連結

- [iOSTenThree 範例](https://docs.microsoft.com/samples/xamarin/ios-samples/ios10-iostenthree/)
