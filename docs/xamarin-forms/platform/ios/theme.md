---
title: 新增 iOS 特有格式
description: 這篇文章說明如何設定 iOS 特有外觀，而不使用 Xamarin.Forms 自訂轉譯器。
ms.prod: xamarin
ms.assetid: CE50E207-D092-4D88-8439-1B51F178E7ED
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/29/2016
ms.openlocfilehash: 3b8a440617dedfbe23f869e865b3cedae21d6c5b
ms.sourcegitcommit: b56b3f906d2c05a3f1be219ef41be8b79e519b8e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/25/2018
ms.locfileid: "39241373"
---
# <a name="adding-ios-specific-formatting"></a>新增 iOS 特有格式

一種方式來設定 iOS 專屬格式是建立[自訂轉譯器](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)如控制項和設定平台專屬樣式，以及每個平台的色彩。

其他選項來控制包含 Xamarin.Forms iOS 應用程式的外觀的方式：

* 設定顯示選項[ **Info.plist**](#info-plist)
* 設定控制項樣式透過[ `UIAppearance` API](#uiappearance)

下面會討論這些替代方案。

<a name="info-plist"/>

## <a name="customizing-infoplist"></a>自訂的 Info.plist

**Info.plist**檔案可讓您設定 iOS 應用程式的轉譯，例如如何 （以及是否） 會顯示 [狀態] 列的某些層面。

例如，[待辦事項範例](https://developer.xamarin.com/samples/xamarin-forms/Todo/)使用下列程式碼來設定所有平台上的導覽列的色彩和文字色彩：

```csharp
var nav = new NavigationPage (new TodoListPage ());
nav.BarBackgroundColor = Color.FromHex("91CA47");
nav.BarTextColor = Color.White;
```

結果如下列螢幕程式碼片段所示。 請注意，[狀態] 列項目是黑色 (這 nelze nastavit v sekci Xamarin.Forms 因為它是平台專屬功能)。

![](theme-images/status-default-sml.png "iOS 佈景主題")

在理想情況下的 [狀態] 列也會是白色-我們可以直接在 iOS 專案中完成的項目。 下列項目新增至**Info.plist**強制是白色的 [狀態] 列：

![](theme-images/info-plist.png "iOS Info.plist 項目")

或編輯對應**Info.plist**檔案直接以納入：

```xml
<key>UIStatusBarStyle</key>
<string>UIStatusBarStyleLightContent</string>
<key>UIViewControllerBasedStatusBarAppearance</key>
<false/>
```

現在執行應用程式時，瀏覽列是綠色，而它的文字是白色 （因為 Xamarin.Forms 格式設定）*和*狀態列文字也是白色感謝 iOS 特有組態：

![](theme-images/status-white-sml.png "iOS 佈景主題")

<a name="uiappearance"/>

## <a name="uiappearance-api"></a>UIAppearance API

[ `UIAppearance` API](~/ios/user-interface/ios-ui/introduction-to-the-appearance-api.md)可用來設定 iOS 的許多控制項上的視覺屬性*而不需要*不必建立[自訂轉譯器](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)。

新增程式碼，以單行**AppDelegate.cs** `FinishedLaunching`方法可以設定指定的型別使用的所有控制項的都樣式其`Appearance`屬性。 下列程式碼包含兩個範例為全域設定樣式的索引標籤列，然後切換控制項：

**AppDelegate.cs** iOS 專案中

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

根據預設，在選取的索引標籤列圖示[ `TabbedPage` ](~/xamarin-forms/app-fundamentals/navigation/tabbed-page.md)是藍色：

![](theme-images/tabbar-default.png "預設 iOS TabbedPage 索引標籤列圖示")

若要變更此行為，請設定`UITabBar.Appearance`屬性：

```csharp
UITabBar.Appearance.SelectedImageTintColor = UIColor.FromRGB(0x91, 0xCA, 0x47); // green
```

這會導致是綠色的 [選取] 索引標籤：

![](theme-images/tabbar-custom.png "綠色 iOS TabbedPage 索引標籤列圖示")

使用此 API 可讓您自訂的 Xamarin.Forms 外觀`TabbedPage`極少的程式碼在 iOS 上。 請參閱[自訂索引標籤配方](https://github.com/xamarin/recipes/tree/master/Recipes/xamarin-forms/iOS/customize-tabs)如需詳細資訊，設定特定的字型索引標籤中使用自訂轉譯器。

### <a name="uiswitch"></a>UISwitch

`Switch`控制項是可輕鬆地自訂樣式的另一個範例：

```csharp
UISwitch.Appearance.OnTintColor = UIColor.FromRGB(0x91, 0xCA, 0x47); // green
```

這些兩個螢幕擷取畫面顯示預設值`UISwitch`在左方和自訂的版本控制 (設定`Appearance`) 在右邊[待辦事項範例](https://developer.xamarin.com/samples/xamarin-forms/Todo/):

![](theme-images/switch-default.png "預設色彩 UISwitch") ![](theme-images/switch-custom.png "自訂 UISwitch 色彩")

### <a name="other-controls"></a>其他控制項

其預設色彩和其他設定使用的屬性，可以有許多的 iOS 使用者介面控制項[ `UIAppearance` API](~/ios/user-interface/ios-ui/introduction-to-the-appearance-api.md)。



## <a name="related-links"></a>相關連結

- [UIAppearance](~/ios/user-interface/ios-ui/introduction-to-the-appearance-api.md)
- [自訂索引標籤](https://github.com/xamarin/recipes/tree/master/Recipes/xamarin-forms/iOS/customize-tabs)
