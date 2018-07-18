---
title: Xamarin.Forms 佈景主題
description: 本文介紹 Xamarin.Forms 佈景主題，以定義特定的標準檢視的視覺外觀。
ms.prod: xamarin
ms.assetid: 3DFB7C55-69F6-4980-A501-588719143482
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/01/2017
ms.openlocfilehash: 0f49eeba072d6aeb7ead40d5d56d4af9e9bf5e27
ms.sourcegitcommit: 632955f8cdb80712abd8dcc30e046cb9c435b922
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2018
ms.locfileid: "38814703"
---
# <a name="xamarinforms-themes"></a>Xamarin.Forms 佈景主題

![](~/media/shared/preview.png "此 API 目前為預覽狀態")

Xamarin.Forms 佈景主題發表於發展 2016年，並可讓客戶試用並提供意見以預覽的形式。

佈景主題新增至 Xamarin.Forms 應用程式包括**Xamarin.Forms.Theme.Base** Nuget 套件，再加上另一種套件，定義特定的佈景主題 （例如。 Xamarin.Forms.Theme.Light) 或其他定義本機佈景主題，應用程式。

請參閱[淺色佈景主題](light.md)並[暗色調佈景主題](dark.md)如需有關如何將它們新增至應用程式，或查看頁面[範例自訂佈景主題](custom.md)。

**重要事項︰** 您也應該遵循的步驟[佈景主題 （如下所示） 的組件載入](#loadtheme)一些未定案程式碼加入至 iOS`AppDelegate`和 Android `MainActivity`。 這會改善未來的預覽版。


## <a name="control-appearance"></a>控制項外觀

[Light](light.md)並[深色](dark.md)這兩個主題定義特定的視覺外觀，標準控制項的。 當您新增至應用程式的資源字典的佈景主題時，會變更標準控制項的外觀。

下列 XAML 標記會顯示一些常見的控制項：

```xaml
<StackLayout Padding="40">
    <Label Text="Regular label" />
    <Entry Placeholder="type here" />
    <Button Text="OK" />
    <BoxView Color="Yellow" />
    <Switch />
</StackLayout>
```

這些螢幕擷取畫面顯示使用這些控制項：

* 套用的佈景主題
* 淺色佈景主題 （只有細微差異讓任何佈景主題）
* 暗色調佈景主題

![](images/standard-none-sml.png "不使用佈景主題的控制項") ![](images/standard-light-sml.png "淺色佈景主題的控制項") ![](images/standard-dark-sml.png "暗色調佈景主題的控制項")

<a name="styleclass" />

## <a name="styleclass"></a>StyleClass

`StyleClass`屬性可讓根據佈景主題所提供的定義變更檢視的外觀。

[Light](light.md)並[深色](dark.md)這兩個主題定義三個不同的外觀，如`BoxView`: `HorizontalRule`， `Circle`，和`Rounded`。 此標記範例示範三個不同`BoxView`與套用不同的樣式類別：

```xaml
<StackLayout Padding="40">
    <BoxView StyleClass="HorizontalRule" />
    <BoxView StyleClass="Circle" />
    <BoxView StyleClass="Rounded" />
</StackLayout>
```

這會轉譯與光線和深色，如下所示：

![](images/boxview-light-sml.png "淺色佈景主題 StyleClass 與 BoxView") ![](images/boxview-dark-sml.png "與暗色調佈景主題 StyleClass BoxView")

<a name="builtin" />

## <a name="built-in-classes"></a>內建類別

除了自動設定樣式的通用控制項光源和深色佈景主題目前支援下列類別可以藉由設定套用`StyleClass`在這些控制項上：

**BoxView**

* HorizontalRule
* 圓形
* 圓角

**影像**

* 圓形
* 圓角
* 縮圖

**Button**

* 預設
* 主要
* 成功
* 資訊
* 警告
* 危險
* 連結
* 小型
* 大型

**Label**

* 頁首
* 子標頭
* 本文
* 連結
* 反向


## <a name="troubleshooting"></a>疑難排解

<a name="loadtheme" />

### <a name="could-not-load-file-or-assembly-xamarinformsthemelight-or-one-of-its-dependencies"></a>無法載入檔案或組件 'Xamarin.Forms.Theme.Light' 或其中一個相依性

在預覽版本中，佈景主題可能無法在執行階段載入。 新增下面顯示相關的專案。 若要修正此錯誤的程式碼。

**iOS**

在  **AppDelegate.cs**新增下列行之後 `LoadApplication`

```csharp
var x = typeof(Xamarin.Forms.Themes.DarkThemeResources);
x = typeof(Xamarin.Forms.Themes.LightThemeResources);
x = typeof(Xamarin.Forms.Themes.iOS.UnderlineEffect);
```

**Android**

在  **MainActivity.cs**新增下列行之後 `LoadApplication`

```csharp
var x = typeof(Xamarin.Forms.Themes.DarkThemeResources);
x = typeof(Xamarin.Forms.Themes.LightThemeResources);
x = typeof(Xamarin.Forms.Themes.Android.UnderlineEffect);
```


## <a name="related-links"></a>相關連結

- [ThemesDemo 範例](https://github.com/xamarin/xamarin-forms-samples/tree/master/Themes/ThemesDemo)
