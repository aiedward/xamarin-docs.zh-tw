---
title: Xamarin 中的替代應用程式圖示
description: 本檔說明如何在 Xamarin 中使用替代的應用程式圖示。 它會討論如何將這些圖示新增至 Xamarin 專案、如何修改 plist 檔案，以及如何以程式設計方式管理應用程式的圖示。
ms.prod: xamarin
ms.assetid: 302fa818-33b9-4ea1-ab63-0b2cb312299a
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/29/2017
ms.openlocfilehash: 6ac5a6924f2b297b63a73b8b417dd68bad062a84
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91431350"
---
# <a name="alternate-app-icons-in-xamarinios"></a>Xamarin 中的替代應用程式圖示

_本文涵蓋如何在 Xamarin 中使用替代的應用程式圖示。_

Apple 已在 iOS 10.3 中新增數個增強功能，可讓應用程式管理其圖示：

- `ApplicationIconBadgeNumber` -取得或設定跳板中應用程式圖示的徽章。
- `SupportsAlternateIcons` -如果 `true` 應用程式具有替代的圖示集。
- `AlternateIconName` -傳回目前選取之替代圖示的名稱， `null` 如果使用主要圖示則為。
- `SetAlternameIconName` -使用此方法將應用程式的圖示切換至指定的替代圖示。

![當應用程式變更其圖示時的範例警示](alternate-app-icons-images/icons04.png)

<a name="Adding-Alternate-Icons"></a>

## <a name="adding-alternate-icons-to-a-xamarinios-project"></a>將替代圖示新增至 Xamarin. iOS 專案

若要允許應用程式切換至替代圖示，則必須在 [Xamarin] 應用程式專案中包含圖示影像的集合。 這些映射無法使用一般方法新增至專案 `Assets.xcassets` ，必須直接新增至 **Resources** 資料夾。

執行下列動作：

1. 選取資料夾中的必要圖示影像，選取 [全部]，並將其拖曳到**方案總管**中的 [**資源**] 資料夾中：

    ![從資料夾選取圖示影像](alternate-app-icons-images/icons00.png)

2. 出現提示時，請選取 [ **複製**]， **針對所有選取的檔案使用相同的動作** ，然後按一下 [ **確定]** 按鈕：

    ![[將檔案新增至資料夾] 對話方塊](alternate-app-icons-images/icons02.png)

3. 完成時， **Resources** 資料夾應如下所示：

    ![Resources 資料夾看起來應該像這樣](alternate-app-icons-images/icons01.png)

<a name="Modifying-the-Info.plist-File"></a>

## <a name="modifying-the-infoplist-file"></a>修改 plist 檔案

將必要的映射新增至 **Resources** 資料夾後，必須將 [CFBundleAlternateIcons](https://developer.apple.com/library/content/documentation/General/Reference/InfoPlistKeyReference/Articles/CoreFoundationKeys.html#//apple_ref/doc/uid/TP40009249-SW13) 金鑰新增至專案的 **plist** 檔案。 此索引鍵會定義新圖示的名稱，以及組成它的影像。

執行下列動作：

1. 在 [方案總管]**** 中，按兩下 [Info.plist]**** 檔案以開啟它進行編輯。
2. 切換至 [來源] 檢視。
3. 新增組合 **圖示** 索引鍵，並將 **類型** 設定為 [ **字典**]。
4. 加入索引 `CFBundleAlternateIcons` 鍵，並將 **型** 別設定為 **Dictionary**。
5. 加入索引 `AppIcon2` 鍵，並將 **型** 別設定為 **Dictionary**。 這會是新的替代應用程式圖示集的名稱。
6. 新增索引 `CFBundleIconFiles` 鍵並將 **類型** 設定為 **陣列**
7. 將新字串加入至 `CFBundleIconFiles` 每個圖示檔的陣列，以省略擴充功能和 `@2x` 、等等 `@3x` 尾碼 (範例 `100_icon`) 。 針對組成替代圖示集的每個檔案重複此步驟。
8. 將索引 `UIPrerenderedIcon` 鍵加入至 `AppIcon2` 字典、將 **類型** 設定為 **布林** 值，並將值設定為 [ **否**]。
9. 將變更儲存至檔案。

完成時，所產生的 **plist** 檔案看起來應該如下所示：

![完成的資訊 plist 檔案](alternate-app-icons-images/icons03.png)

或者，如果在文字編輯器中開啟，則如下所示：

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

<a name="Managing-the-Apps-Icon"></a>

## <a name="managing-the-apps-icon"></a>管理應用程式的圖示 

在 plist 檔案中包含的圖示影像和正確設定的 **資訊** ，開發人員可以使用新增至 ios 10.3 的許多新功能之一，來控制應用程式的圖示。

`SupportsAlternateIcons`類別的屬性 `UIApplication` 可讓開發人員查看應用程式是否支援替代圖示。 例如：

```csharp
// Can the app select a different icon?
PrimaryIconButton.Enabled = UIApplication.SharedApplication.SupportsAlternateIcons;
AlternateIconButton.Enabled = UIApplication.SharedApplication.SupportsAlternateIcons;
```

`ApplicationIconBadgeNumber`類別的屬性 `UIApplication` 可讓開發人員取得或設定跳板中應用程式圖示的目前徽章編號。 預設值為零 (0)。 例如：

```csharp
// Set the badge number to 1
UIApplication.SharedApplication.ApplicationIconBadgeNumber = 1;
```

`AlternateIconName`類別的屬性 `UIApplication` 可讓開發人員取得目前選取之替代應用程式圖示的名稱，或者， `null` 如果應用程式使用主要圖示，則會傳回它。 例如：

```csharp
// Get the name of the currently selected alternate
// icon set
var name = UIApplication.SharedApplication.AlternateIconName;

if (name != null ) {
    // Do something with the name
}
```

`SetAlternameIconName`類別的屬性 `UIApplication` 可讓開發人員變更應用程式圖示。 將圖示的名稱傳遞至選取的或 `null` 以返回主要圖示。 例如：

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

![當應用程式變更其圖示時的範例警示](alternate-app-icons-images/icons04.png)

如果使用者切換回主要圖示，將會顯示類似下列的警示：

![當應用程式變更為主要圖示時的範例警示](alternate-app-icons-images/icons05.png)

<a name="Summary"></a>

## <a name="summary"></a>摘要

本文涵蓋將替代的應用程式圖示新增至 Xamarin 專案，並在應用程式中使用它們。

## <a name="related-links"></a>相關連結

- [iOSTenThree 範例](/samples/xamarin/ios-samples/ios10-iostenthree/)