---
title: Xamarin.iOS 中的其他應用程式圖示
description: 本文件說明如何在 Xamarin.iOS 中使用替代的應用程式圖示。 它討論如何將這些圖示新增至 Xamarin.iOS 專案、 如何修改 Info.plist 檔案，以及如何以程式設計方式管理應用程式的圖示。
ms.prod: xamarin
ms.assetid: 302fa818-33b9-4ea1-ab63-0b2cb312299a
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/29/2017
ms.openlocfilehash: 1d37a29982454367c35bfdfad205abce0eb025af
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/05/2018
ms.locfileid: "34784106"
---
# <a name="alternate-app-icons-in-xamarinios"></a>Xamarin.iOS 中的其他應用程式圖示

_本文涵蓋 Xamarin.iOS 中使用替代的應用程式圖示。_

Apple iOS 10.3 提供可讓應用程式管理它的圖示已加入數個增強功能：

 - `ApplicationIconBadgeNumber` -取得或設定應用程式圖示的徽章 Springboard 中。
 - `SupportsAlternateIcons` -如果`true`應用程式有替代組圖示。
 - `AlternateIconName` -傳回目前選取的替代圖示的名稱或`null`如果使用主要的圖示。
 - `SetAlternameIconName` -使用這個方法指定替代的圖示切換應用程式的圖示。

![](alternate-app-icons-images/icons04.png "當應用程式變更它的圖示範例警示")

<a name="Adding-Alternate-Icons" />

## <a name="adding-alternate-icons-to-a-xamarinios-project"></a>將其他圖示加入至 Xamarin.iOS 專案

若要讓應用程式切換至替代的圖示，圖示影像的集合必須包含在 Xamarin.iOS 應用程式專案。 這些映像不能加入至專案中使用一般`Assets.xcassets`方法，必須將它們加入**資源**直接資料夾。

請執行下列動作：

1. 選取資料夾中的必要的圖示影像、 選取所有，並將它們拖曳到**資源**資料夾中的**方案總管 中**:

    ![](alternate-app-icons-images/icons00.png "選取資料夾中圖示影像")

2. 出現提示時，選取**複製**，**針對所有選取的檔案使用相同的動作**按一下**確定**按鈕：

    ![](alternate-app-icons-images/icons02.png "新增檔案到資料夾 對話方塊")

3. **資源**資料夾應該如下所示完成時：

    ![](alternate-app-icons-images/icons01.png "[資源] 資料夾應該看起來像這樣")

<a name="Modifying-the-Info.plist-File" />

## <a name="modifying-the-infoplist-file"></a>修改 Info.plist 檔案

與所需的映像新增至**資源**資料夾， [CFBundleAlternateIcons](https://developer.apple.com/library/content/documentation/General/Reference/InfoPlistKeyReference/Articles/CoreFoundationKeys.html#//apple_ref/doc/uid/TP40009249-SW13)金鑰將必須加入至專案的**Info.plist**檔案。 此機碼會定義新的圖示，然後撰寫它的映像的名稱。

請執行下列動作：

1. 在 [方案總管]中，按兩下 [Info.plist] 檔案以開啟它進行編輯。
2. 切換至**來源**檢視。
3. 新增**配套圖示**索引鍵，並保留**類型**設**字典**。
4. 新增`CFBundleAlternateIcons`碼並將設定**類型**至**字典**。
5. 新增`AppIcons2`碼並將設定**類型**至**字典**。 這會是新的替代的應用程式圖示集合的名稱。
6. 新增`CFBundleIconFiles`碼並將設定**類型**至**陣列**
7. 加入新的字串，`CFBundleIconFiles`陣列的每個圖示檔案留下任何擴充功能和`@2x`， `@3x`，等尾碼 (範例`100_icon`)。 針對組成替代圖示集中的每個檔案重複此步驟。
8. 新增`UIPrerenderedIcon`鍵`AppIcons2`字典中，設定**類型**至**布林**要和值**否**。
9. 將變更儲存到檔案。

產生**Info.plist**檔案應該看起來類似下面的完成時：

![](alternate-app-icons-images/icons03.png "已完成的 Info.plist 檔案")

或如果像開啟文字編輯器中：

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

## <a name="managing-the-apps-icon"></a>管理應用程式的圖示 

與 Xamarin.iOS 專案中包含圖示映像和**Info.plist**正確設定的檔案，開發人員可以使用 iOS 10.3 加入許多新功能的其中一個來控制應用程式的圖示。

`SupportsAlternateIcons`屬性`UIApplication`類別可讓開發人員應用程式是否支援替代的圖示。 例如: 

```csharp
// Can the app select a different icon?
PrimaryIconButton.Enabled = UIApplication.SharedApplication.SupportsAlternateIcons;
AlternateIconButton.Enabled = UIApplication.SharedApplication.SupportsAlternateIcons;
```

`ApplicationIconBadgeNumber`屬性`UIApplication`類別可讓開發人員以取得或設定目前的應用程式圖示的徽章數 Springboard 中。 預設值是零 (0)。 例如: 

```csharp
// Set the badge number to 1
UIApplication.SharedApplication.ApplicationIconBadgeNumber = 1;
```

`AlternateIconName`屬性`UIApplication`類別可讓開發人員，取得目前選取替代應用程式圖示的名稱或它傳回`null`如果應用程式正在使用主要的圖示。 例如: 

```csharp
// Get the name of the currently selected alternate
// icon set
var name = UIApplication.SharedApplication.AlternateIconName;

if (name != null ) {
    // Do something with the name
}
```

`SetAlternameIconName`屬性`UIApplication`類別可讓開發人員若要變更的應用程式圖示。 傳遞圖示可選取的名稱或`null`回到主要的圖示。 例如: 

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

當執行應用程式，且使用者選取替代的圖示時，將會出現警示，如下所示：

![](alternate-app-icons-images/icons04.png "當應用程式變更它的圖示範例警示")

如果使用者切換回主要圖示，將會出現警示，如下所示：

![](alternate-app-icons-images/icons05.png "範例時警示應用程式會變成主要的圖示")

<a name="Summary" />

## <a name="summary"></a>總結

本文探討將替代的應用程式圖示加入至 Xamarin.iOS 專案和應用程式內使用它們。



## <a name="related-links"></a>相關連結

- [iOSTenThree 範例](https://developer.xamarin.com/samples/ios/iOS10/iOSTenThree)
