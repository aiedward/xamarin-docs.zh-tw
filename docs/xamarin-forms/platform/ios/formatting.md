---
title: 新增 iOS 特定格式
description: 本文說明如何設定 iOS 特有的外觀，而不需要使用 Xamarin.Forms 自訂轉譯器。
ms.prod: xamarin
ms.assetid: CE50E207-D092-4D88-8439-1B51F178E7ED
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/29/2016
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 4381b1fbe7b38c2695bce645903d599bdb792fc2
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/22/2020
ms.locfileid: "86937432"
---
# <a name="adding-ios-specific-formatting"></a>新增 iOS 特定格式

設定 iOS 特定格式的其中一種方式是建立控制項的[自訂](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)轉譯器，並設定每個平臺的平臺特定樣式和色彩。

控制 Xamarin.Forms iOS 應用程式外觀方式的其他選項包括：

- 設定[ **Info. plist**中的顯示選項](#customizing-infoplist)
- 透過[ `UIAppearance` API](#uiappearance-api)設定控制項樣式

以下將討論這些替代方案。

## <a name="customizing-infoplist"></a>自訂資訊。 plist

**Plist**檔案可讓您設定 iOS 應用程式轉譯的某些層面，例如如何（以及是否）顯示狀態列。

例如， [Todo 範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/todo)會使用下列程式碼來設定所有平臺上的導覽列色彩和文字色彩：

```csharp
var nav = new NavigationPage (new TodoListPage ());
nav.BarBackgroundColor = Color.FromHex("91CA47");
nav.BarTextColor = Color.White;
```

結果會顯示在下方的畫面程式碼片段中。 請注意，狀態列專案是黑色的（無法在中設定， Xamarin.Forms 因為它是平臺特定功能）。

![iOS 主題](theme-images/status-default-sml.png)

在理想的情況下，狀態列也會是可直接在 iOS 專案中完成的白色。 將下列專案新增至**plist** ，以強制狀態列為白色：

![iOS Info. plist 專案](theme-images/info-plist.png)

或直接編輯對應的**plist**檔案，以包含：

```xml
<key>UIStatusBarStyle</key>
<string>UIStatusBarStyleLightContent</string>
<key>UIViewControllerBasedStatusBarAppearance</key>
<false/>
```

現在，當應用程式執行時，導覽列會是綠色且其文字為白色（由於 Xamarin.Forms 格式） *，而且*狀態列文字也會因為 iOS 專屬設定而呈白色：

![iOS 主題](theme-images/status-white-sml.png)

## <a name="uiappearance-api"></a>UIAppearance API

[ `UIAppearance` API](~/ios/user-interface/ios-ui/introduction-to-the-appearance-api.md)可用來設定許多 iOS 控制項的視覺屬性，*而不需要*建立[自訂](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)轉譯器。

將一行程式碼新增至**AppDelegate.cs** `FinishedLaunching` 方法，可以使用其屬性來為指定類型的所有控制項建立樣式 `Appearance` 。 下列程式碼包含兩個範例-全域設定索引標籤列和切換控制項的樣式：

IOS 專案中的**AppDelegate.cs**

```csharp
public override bool FinishedLaunching (UIApplication app, NSDictionary options)
{
  // tab bar
    UITabBar.Appearance.SelectedImageTintColor = UIColor.FromRGB(0x91, 0xCA, 0x47); // green
  // switch
    UISwitch.Appearance.OnTintColor = UIColor.FromRGB(0x91, 0xCA, 0x47); // green
    // required Xamarin.Forms code
    Forms.Init ();
    LoadApplication (new App ());
    return base.FinishedLaunching (app, options);
}
```

### <a name="uitabbar"></a>UITabBar

根據預設，在中，選取的索引標籤列圖示[`TabbedPage`](~/xamarin-forms/app-fundamentals/navigation/tabbed-page.md)
會是藍色：

![TabbedPage 中的預設 iOS 索引標籤欄圖示](theme-images/tabbar-default.png)

若要變更此行為，請設定 `UITabBar.Appearance` 屬性：

```csharp
UITabBar.Appearance.SelectedImageTintColor = UIColor.FromRGB(0x91, 0xCA, 0x47); // green
```

這會使選取的索引標籤變成綠色：

![TabbedPage 中的綠色 iOS 索引標籤欄圖示](theme-images/tabbar-custom.png)

使用此 API 可讓您自訂下列各項的外觀Xamarin.Forms
`TabbedPage`在 iOS 上使用極少的程式碼。 如需使用自訂轉譯器來設定索引標籤之特定字型的詳細資訊，請參閱[自訂](https://github.com/xamarin/recipes/tree/master/Recipes/xamarin-forms/iOS/customize-tabs)索引標籤配方。

### <a name="uiswitch"></a>UISwitch

`Switch`控制項是另一個可以輕鬆地設計樣式的範例：

```csharp
UISwitch.Appearance.OnTintColor = UIColor.FromRGB(0x91, 0xCA, 0x47); // green
```

這兩個螢幕擷取畫面會顯示 `UISwitch` 左側的預設控制項，以及 `Appearance` [Todo 範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/todo)右側的自訂版本（設定）：

![預設 UISwitch 色彩](theme-images/switch-default.png) ![自訂的 UISwitch 色彩](theme-images/switch-custom.png)

### <a name="other-controls"></a>其他控制項

許多 iOS 使用者介面控制項都可以使用[ `UIAppearance` API](~/ios/user-interface/ios-ui/introduction-to-the-appearance-api.md)來設定其預設色彩和其他屬性。

## <a name="related-links"></a>相關連結

- [UIAppearance](~/ios/user-interface/ios-ui/introduction-to-the-appearance-api.md)
- [自訂索引標籤](https://github.com/xamarin/recipes/tree/master/Recipes/xamarin-forms/iOS/customize-tabs)
