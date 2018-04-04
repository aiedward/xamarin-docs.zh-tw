---
title: 加入 iOS 專屬格式
ms.prod: xamarin
ms.assetid: CE50E207-D092-4D88-8439-1B51F178E7ED
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/29/2016
ms.openlocfilehash: 280ca523d3e3b4f5037d626cc5fd0bd5b31d0e8b
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="adding-ios-specific-formatting"></a>加入 iOS 專屬格式

一種方式來設定 iOS 專屬格式是建立[自訂轉譯器](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)控制項並設定特定平台的樣式和色彩每個平台。

其他選項來控制包含 Xamarin.Forms iOS 應用程式的外觀的方式：

* 設定顯示選項在[ **Info.plist**](#info-plist)
* 設定透過控制項樣式[`UIAppearance`應用程式開發介面](#uiappearance)

以下討論這些替代方案。

<a name="info-plist"/>

## <a name="customizing-infoplist"></a>自訂 Info.plist

**Info.plist**檔案可讓您設定 iOS 應用程式的轉譯，例如如何 （以及是否） 會顯示 [狀態] 列的某些層面。

例如， [Todo 範例](https://developer.xamarin.com/samples/xamarin-forms/Todo/)設定所有平台上的導覽列色彩和文字色彩是使用下列程式碼：

```csharp
var nav = new NavigationPage (new TodoListPage ());
nav.BarBackgroundColor = Color.FromHex("91CA47");
nav.BarTextColor = Color.White;
```

結果如以下螢幕程式碼片段所示。 請注意，[狀態] 列項目是黑色 （這不能設定 Xamarin.Forms 中因為它是特定平台功能）。

![](theme-images/status-default-sml.png "iOS 設定主題")

在理想情況下 [狀態] 列也會顯示為白色-我們可以直接在 iOS 專案中完成的項目。 新增下列項目至**Info.plist**強制是白色的 [狀態] 列：

![](theme-images/info-plist.png "iOS Info.plist 項目")

或編輯對應**Info.plist**直接包含的檔案：

```xml
<key>UIStatusBarStyle</key>
<string>UIStatusBarStyleLightContent</string>
<key>UIViewControllerBasedStatusBarAppearance</key>
<false/>
```

現在執行應用程式時，瀏覽列是綠色，其文字是白色的 （因為 Xamarin.Forms 格式化）*和*狀態列文字也是白色感謝您為 iOS 專屬設定：

![](theme-images/status-white-sml.png "iOS 設定主題")

<a name="uiappearance"/>

## <a name="uiappearance-api"></a>UIAppearance 應用程式開發介面

[ `UIAppearance` API](~/ios/user-interface/ios-ui/introduction-to-the-appearance-api.md)可用來設定許多 iOS 控制項上的視覺屬性*沒有*不必建立[自訂轉譯器](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)。

加入程式碼，以單行**d** `FinishedLaunching`方法可以設定指定的型別使用的所有控制項的都樣式其`Appearance`屬性。 下列程式碼包含兩個範例-全域設定樣式 索引標籤列，然後切換控制項：

**D** iOS 專案中

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

根據預設，在選取的索引標籤列圖示[ `TabbedPage` ](~/xamarin-forms/app-fundamentals/navigation/tabbed-page.md)會是藍色：

![](theme-images/tabbar-default.png "預設 iOS TabbedPage 索引標籤列圖示")

若要變更此行為，請設定`UITabBar.Appearance`屬性：

```csharp
UITabBar.Appearance.SelectedImageTintColor = UIColor.FromRGB(0x91, 0xCA, 0x47); // green
```

這會導致選取的索引標籤會顯示為綠色：

![](theme-images/tabbar-custom.png "綠色 iOS TabbedPage 索引標籤列圖示")

使用此 API 可讓您自訂的 Xamarin.Forms 外觀`TabbedPage`iOS 以非常少的程式碼上。 請參閱[自訂索引標籤配方](https://developer.xamarin.com/recipes/cross-platform/xamarin-forms/ios/customize-tabs/)如需有關使用自訂的轉譯器來設定特定索引標籤的字型。

### <a name="uiswitch"></a>UISwitch

`Switch`控制項就可以輕鬆地設定樣式的另一個範例：

```csharp
UISwitch.Appearance.OnTintColor = UIColor.FromRGB(0x91, 0xCA, 0x47); // green
```

這些兩個螢幕擷取畫面顯示預設`UISwitch`在左窗格和自訂的版本控制 (設定`Appearance`) 右側[Todo 範例](https://developer.xamarin.com/samples/xamarin-forms/Todo/):

![](theme-images/switch-default.png "預設 UISwitch 色彩") ![ ](theme-images/switch-custom.png "自訂 UISwitch 色彩")

### <a name="other-controls"></a>其他控制項

其預設色彩和使用設定其他屬性，可以有許多 iOS 使用者介面控制項[ `UIAppearance` API](~/ios/user-interface/ios-ui/introduction-to-the-appearance-api.md)。



## <a name="related-links"></a>相關連結

- [UIAppearance](~/ios/user-interface/ios-ui/introduction-to-the-appearance-api.md)
- [自訂索引標籤](https://developer.xamarin.com/recipes/cross-platform/xamarin-forms/ios/customize-tabs/)
