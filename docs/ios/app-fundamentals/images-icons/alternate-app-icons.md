---
title: 在 Xamarin.iOS 中的其他應用程式圖示
description: 本文件說明如何在 Xamarin.iOS 中使用替代的應用程式圖示。 它討論如何將這些圖示新增至 Xamarin.iOS 專案、 如何修改 Info.plist 檔案中，以及如何以程式設計方式管理應用程式的圖示。
ms.prod: xamarin
ms.assetid: 302fa818-33b9-4ea1-ab63-0b2cb312299a
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/29/2017
ms.openlocfilehash: 3f009d84d1d38c379f65de52949c66f3e86fc654
ms.sourcegitcommit: ffb0f3dbf77b5f244b195618316bbd8964541e42
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/26/2018
ms.locfileid: "39275985"
---
# <a name="alternate-app-icons-in-xamarinios"></a>在 Xamarin.iOS 中的其他應用程式圖示

_這篇文章將說明如何在 Xamarin.iOS 中使用替代的應用程式圖示。_

Apple iOS 10.3 可讓應用程式來管理它的圖示已新增數個增強功能：

 - `ApplicationIconBadgeNumber` -取得或設定跳板系列中的應用程式圖示的徽章。
 - `SupportsAlternateIcons` -如果`true`應用程式有另一組的圖示。
 - `AlternateIconName` -傳回目前選取的替代圖示的名稱或`null`如果使用的主要圖示。
 - `SetAlternameIconName` -使用這個方法來指定替代的圖示切換應用程式的圖示。

![](alternate-app-icons-images/icons04.png "警示範例應用程式變更它的圖示時")

<a name="Adding-Alternate-Icons" />

## <a name="adding-alternate-icons-to-a-xamarinios-project"></a>將替代的圖示加入至 Xamarin.iOS 專案

若要允許的應用程式切換至替代的圖示，圖示影像的集合必須包含在 Xamarin.iOS 應用程式專案。 這些映像不能加入至專案使用典型`Assets.xcassets`方法，必須將它們加入至**資源**直接資料夾。

請執行下列動作：

1. 選取資料夾中的所需的圖示影像、 選取所有，並將它們拖曳到**資源**中的資料夾**方案總管 中**:

    ![](alternate-app-icons-images/icons00.png "選取資料夾中的圖示影像")

2. 出現提示時，選取**複製**，**針對所有選取的檔案使用相同的動作**然後按一下**確定**按鈕：

    ![](alternate-app-icons-images/icons02.png "新增檔案到資料夾 對話方塊")

3. **資源**資料夾應該看起來如下所示完成：

    ![](alternate-app-icons-images/icons01.png "[資源] 資料夾應該看起來像這樣")

<a name="Modifying-the-Info.plist-File" />

## <a name="modifying-the-infoplist-file"></a>修改 Info.plist 檔案

使用所需的影像加入至**資源**資料夾中， [CFBundleAlternateIcons](https://developer.apple.com/library/content/documentation/General/Reference/InfoPlistKeyReference/Articles/CoreFoundationKeys.html#//apple_ref/doc/uid/TP40009249-SW13)金鑰將必須新增至專案**Info.plist**檔案。 此機碼會定義 [新增] 圖示和組合的映像的名稱。

請執行下列動作：

1. 在 [方案總管]中，按兩下 [Info.plist] 檔案以開啟它進行編輯。
2. 若要切換**來源**檢視。
3. 新增**套件組合圖示**鍵，然後離開**型別**設定為**字典**。
4. 新增`CFBundleAlternateIcons`鍵，並設定**型別**要**字典**。
5. 新增`AppIcon2`鍵，並設定**型別**要**字典**。 這會是新的替代的應用程式圖示集的名稱。
6. 新增`CFBundleIconFiles`鍵，並設定**型別**到**陣列**
7. 加入新的字串，以供`CFBundleIconFiles`陣列的每個圖示檔案留下任何延伸模組和`@2x`， `@3x`，等尾碼 (範例`100_icon`)。 針對每個替代的圖示集，使您的檔案重複此步驟。
8. 新增`UIPrerenderedIcon`機碼`AppIcon2`字典中，設定**型別**來**布林**要和值**否**。
9. 將變更儲存到檔案。

產生**Info.plist**檔案應該如下所示完成：

![](alternate-app-icons-images/icons03.png "已完成的 Info.plist 檔案")

或類似時，顯示在文字編輯器中開啟。:

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

與 Xamarin.iOS 專案中包含的圖示映像並**Info.plist**正確設定的檔案，開發人員可以使用其中一項新增至 iOS 10.3 的許多新功能來控制應用程式的圖示。

`SupportsAlternateIcons`屬性`UIApplication`類別可讓開發人員應用程式是否支援替代的圖示。 例如: 

```csharp
// Can the app select a different icon?
PrimaryIconButton.Enabled = UIApplication.SharedApplication.SupportsAlternateIcons;
AlternateIconButton.Enabled = UIApplication.SharedApplication.SupportsAlternateIcons;
```

`ApplicationIconBadgeNumber`屬性`UIApplication`類別可讓開發人員取得或設定目前的應用程式圖示徽章數在跳板系列。 預設值是零 (0)。 例如: 

```csharp
// Set the badge number to 1
UIApplication.SharedApplication.ApplicationIconBadgeNumber = 1;
```

`AlternateIconName`的屬性`UIApplication`類別可讓開發人員目前所選取替代的應用程式圖示的名稱，或傳回`null`如果應用程式使用的主要圖示。 例如: 

```csharp
// Get the name of the currently selected alternate
// icon set
var name = UIApplication.SharedApplication.AlternateIconName;

if (name != null ) {
    // Do something with the name
}
```

`SetAlternameIconName`屬性`UIApplication`類別可讓開發人員變更應用程式圖示。 傳遞圖示，以選取的名稱或`null`返回主要圖示。 例如: 

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

當應用程式執行時，使用者選取替代的圖示時，將會出現警示，如下所示：

![](alternate-app-icons-images/icons04.png "警示範例應用程式變更它的圖示時")

如果使用者切換回到主要的圖示，將會顯示警示，如下所示：

![](alternate-app-icons-images/icons05.png "警示範例應用程式變更為 [主要] 圖示")

<a name="Summary" />

## <a name="summary"></a>總結

本文探討將替代的應用程式圖示新增至 Xamarin.iOS 專案和應用程式內使用它們。



## <a name="related-links"></a>相關連結

- [iOSTenThree 範例](https://developer.xamarin.com/samples/ios/iOS10/iOSTenThree)
