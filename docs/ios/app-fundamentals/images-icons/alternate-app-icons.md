---
title: Xamarin 中的替代應用程式圖示
description: 本檔說明如何在 Xamarin 中使用替代的應用程式圖示。 它討論如何將這些圖示新增至 Xamarin 專案、如何修改 plist 檔案, 以及如何以程式設計方式管理應用程式的圖示。
ms.prod: xamarin
ms.assetid: 302fa818-33b9-4ea1-ab63-0b2cb312299a
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/29/2017
ms.openlocfilehash: 43ef420f569f7dbca6140798f798f3a128d9c706
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2019
ms.locfileid: "68653427"
---
# <a name="alternate-app-icons-in-xamarinios"></a>Xamarin 中的替代應用程式圖示

_本文說明如何在 Xamarin 中使用替代的應用程式圖示。_

Apple 已將數個增強功能新增至 iOS 10.3, 讓應用程式可以管理其圖示:

- `ApplicationIconBadgeNumber`-取得或設定跳板中應用程式圖示的徽章。
- `SupportsAlternateIcons`-如果`true`應用程式有替代的圖示集。
- `AlternateIconName`-傳回目前選取的替代圖示或`null`使用主要圖示的名稱。
- `SetAlternameIconName`-使用此方法可將應用程式的圖示切換至指定的替代圖示。

![](alternate-app-icons-images/icons04.png "應用程式變更其圖示時的範例警示")

<a name="Adding-Alternate-Icons" />

## <a name="adding-alternate-icons-to-a-xamarinios-project"></a>將替代圖示新增至 Xamarin iOS 專案

若要允許應用程式切換至替代圖示, 必須將圖示影像的集合包含在 Xamarin iOS 應用程式專案中。 這些映射無法使用一般`Assets.xcassets`方法加入至專案, 必須直接新增至**Resources**資料夾。

請執行下列動作：

1. 選取資料夾中的必要圖示影像, 選取 全部, 並將其拖曳至 **方案總管**中的 **資源** 資料夾:

    ![](alternate-app-icons-images/icons00.png "選取資料夾中的圖示影像")

2. 出現提示時, 選取 [**複製**],**對所有選取的檔案使用相同的動作**, 然後按一下 [**確定]** 按鈕:

    ![](alternate-app-icons-images/icons02.png "[將檔案新增至資料夾] 對話方塊")

3. 完成時, **Resources**資料夾應如下所示:

    ![](alternate-app-icons-images/icons01.png "Resources 資料夾看起來應該像這樣")

<a name="Modifying-the-Info.plist-File" />

## <a name="modifying-the-infoplist-file"></a>修改 plist 檔案

將必要的映射新增至**Resources**資料夾之後, 必須將[CFBundleAlternateIcons](https://developer.apple.com/library/content/documentation/General/Reference/InfoPlistKeyReference/Articles/CoreFoundationKeys.html#//apple_ref/doc/uid/TP40009249-SW13)索引鍵新增至專案的**plist**檔案。 此機碼會定義新圖示的名稱, 以及構成它的影像。

請執行下列動作：

1. 在 [方案總管]  中，按兩下 [Info.plist]  檔案以開啟它進行編輯。
2. 切換至 [**來源**] 視圖。
3. 新增配套**圖示**金鑰, 並將**類型**設定為 [**字典**]。
4. 新增金鑰, 並將**類型**設定為 [字典]。 `CFBundleAlternateIcons`
5. 新增金鑰, 並將**類型**設定為 [字典]。 `AppIcon2` 這會是新的替代應用程式圖示集的名稱。
6. 加入索引  鍵, 並將類型設定為**陣列** `CFBundleIconFiles`
7. 針對每個圖示檔, `CFBundleIconFiles`將新字串新增至陣列, 以省略副檔名`@2x`以及、 `@3x`等等尾碼 (範例`100_icon`)。 針對組成替代圖示集的每個檔案重複此步驟。
8. `AppIcon2`將索引鍵加入字典中,將類型設定為布林值,並將值設為[否]。`UIPrerenderedIcon`
9. 將變更儲存到檔案。

完成時, 產生的**plist**檔案看起來應該如下所示:

![](alternate-app-icons-images/icons03.png "已完成的 plist 檔案")

或者, 如果在文字編輯器中開啟, 就像這樣:

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

使用包含在**plist**檔案中的圖示影像正確設定後, 開發人員可以使用新增至 ios 10.3 的眾多新功能之一來控制應用程式的圖示。

`UIApplication`類別的屬性可讓開發人員查看應用程式是否支援其他圖示。 `SupportsAlternateIcons` 例如：

```csharp
// Can the app select a different icon?
PrimaryIconButton.Enabled = UIApplication.SharedApplication.SupportsAlternateIcons;
AlternateIconButton.Enabled = UIApplication.SharedApplication.SupportsAlternateIcons;
```

`UIApplication`類別`ApplicationIconBadgeNumber`的屬性可讓開發人員取得或設定跳板中應用程式圖示的目前徽章編號。 預設值為零 (0)。 例如：

```csharp
// Set the badge number to 1
UIApplication.SharedApplication.ApplicationIconBadgeNumber = 1;
```

`UIApplication`類別的`null`屬性可讓開發人員取得目前選取之替代應用程式圖示的名稱, 或如果應用程式使用主要圖示, 則會傳回。 `AlternateIconName` 例如：

```csharp
// Get the name of the currently selected alternate
// icon set
var name = UIApplication.SharedApplication.AlternateIconName;

if (name != null ) {
    // Do something with the name
}
```

`UIApplication`類別的屬性可讓開發人員變更應用程式圖示。 `SetAlternameIconName` 將圖示的名稱傳遞至 select 或`null` , 以回到主要圖示。 例如：

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

當應用程式執行且使用者選取替代圖示時, 會顯示類似下列的警示:

![](alternate-app-icons-images/icons04.png "應用程式變更其圖示時的範例警示")

如果使用者切換回主要圖示, 將會顯示如下的警示:

![](alternate-app-icons-images/icons05.png "當應用程式變更為主要圖示時的範例警示")

<a name="Summary" />

## <a name="summary"></a>總結

本文涵蓋將替代應用程式圖示新增至 Xamarin iOS 專案, 並在應用程式內使用它們。



## <a name="related-links"></a>相關連結

- [iOSTenThree 範例](https://docs.microsoft.com/samples/xamarin/ios-samples/ios10-iostenthree/)
