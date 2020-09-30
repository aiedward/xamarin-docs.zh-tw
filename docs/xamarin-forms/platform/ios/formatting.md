---
title: 新增 iOS 特定格式
description: 本文說明如何在不使用自訂轉譯器的情況下設定 iOS 特定外觀 Xamarin.Forms 。
ms.prod: xamarin
ms.assetid: CE50E207-D092-4D88-8439-1B51F178E7ED
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/29/2016
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: f447a89ca4b4f21554a75ec52c5771ee9f9d35fd
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91562986"
---
# <a name="adding-ios-specific-formatting"></a>新增 iOS 特定格式

設定 iOS 特定格式的其中一種方式是建立控制項的 [自訂](~/xamarin-forms/app-fundamentals/custom-renderer/index.md) 轉譯器，並設定每個平臺的平臺特定樣式和色彩。

控制 Xamarin.Forms iOS 應用程式外觀的其他選項包括：

- 在[ **Info. plist**中設定顯示選項](#customizing-infoplist)
- 透過[ `UIAppearance` API](#uiappearance-api)設定控制項樣式

以下將討論這些替代方案。

## <a name="customizing-infoplist"></a>自訂資訊. plist

**Plist**檔案可讓您設定 iOS 應用程式轉譯的某些層面，例如 (以及是否顯示) 狀態列。

例如， [Todo 範例](/samples/xamarin/xamarin-forms-samples/todo) 會使用下列程式碼，在所有平臺上設定導覽列的色彩和文字色彩：

```csharp
var nav = new NavigationPage (new TodoListPage ());
nav.BarBackgroundColor = Color.FromHex("91CA47");
nav.BarTextColor = Color.White;
```

結果會顯示在下方的畫面程式碼片段中。 請注意，狀態列專案是黑色 (這無法在中設定 Xamarin.Forms ，因為它是) 的平臺特定功能。

![iOS 主題](theme-images/status-default-sml.png)

在理想的情況下，狀態列也是可以直接在 iOS 專案中完成的專案。 將下列專案新增至 **plist** ，以強制狀態列成為白色：

![iOS 資訊. plist 專案](theme-images/info-plist.png)

或直接編輯對應的 **plist** 檔案，以包含：

```xml
<key>UIStatusBarStyle</key>
<string>UIStatusBarStyleLightContent</string>
<key>UIViewControllerBasedStatusBarAppearance</key>
<false/>
```

現在，當應用程式執行時，流覽列為綠色，而其文字為白色 (因為 Xamarin.Forms 格式化) *，而* 狀態列文字也是白色，因為這是 iOS 特定的設定：

![iOS 主題](theme-images/status-white-sml.png)

## <a name="uiappearance-api"></a>UIAppearance API

[ `UIAppearance` API](~/ios/user-interface/ios-ui/introduction-to-the-appearance-api.md)可以用來設定許多 iOS 控制項的視覺屬性，*而不*需要建立[自訂](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)轉譯器。

在 **AppDelegate.cs**方法中加入一行程式碼， `FinishedLaunching` 可以使用其屬性來建立指定型別之所有控制項的樣式 `Appearance` 。 下列程式碼包含兩個範例-全域設定索引標籤列和切換控制項的樣式：

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

依預設，在中選取的索引標籤列圖示 [`TabbedPage`](~/xamarin-forms/app-fundamentals/navigation/tabbed-page.md)
會是藍色：

![TabbedPage 中的預設 iOS 索引標籤列圖示](theme-images/tabbar-default.png)

若要變更此行為，請設定 `UITabBar.Appearance` 屬性：

```csharp
UITabBar.Appearance.SelectedImageTintColor = UIColor.FromRGB(0x91, 0xCA, 0x47); // green
```

這會使選取的索引標籤變成綠色：

![TabbedPage 中的綠色 iOS 定位線圖示](theme-images/tabbar-custom.png)

使用此 API 可讓您自訂 Xamarin.Forms
`TabbedPage` 在 iOS 上使用極少程式碼。 如需使用自訂轉譯器來設定索引標籤的特定字型的詳細資訊，請參閱 [自訂索引標籤 [] 配方](https://github.com/xamarin/recipes/tree/master/Recipes/xamarin-forms/iOS/customize-tabs) 。

### <a name="uiswitch"></a>UISwitch

`Switch`控制項是可以輕鬆設計樣式的另一個範例：

```csharp
UISwitch.Appearance.OnTintColor = UIColor.FromRGB(0x91, 0xCA, 0x47); // green
```

這兩個螢幕擷取畫面會顯示 `UISwitch` 左側的預設控制項，以及在 `Appearance` [Todo 範例](/samples/xamarin/xamarin-forms-samples/todo)右邊的自訂版本 (設定) ：

![預設 UISwitch 色彩](theme-images/switch-default.png) ![自訂的 UISwitch 色彩](theme-images/switch-custom.png)

### <a name="other-controls"></a>其他控制項

許多 iOS 使用者介面控制項都可以使用[ `UIAppearance` API](~/ios/user-interface/ios-ui/introduction-to-the-appearance-api.md)來設定其預設色彩和其他屬性。

## <a name="related-links"></a>相關連結

- [UIAppearance](~/ios/user-interface/ios-ui/introduction-to-the-appearance-api.md)
- [自訂索引標籤](https://github.com/xamarin/recipes/tree/master/Recipes/xamarin-forms/iOS/customize-tabs)