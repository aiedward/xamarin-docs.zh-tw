---
title: "主題"
ms.topic: article
ms.prod: xamarin
ms.assetid: 3DFB7C55-69F6-4980-A501-588719143482
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/01/2017
ms.openlocfilehash: a62d6c0cb9b6c41ebf3f2a6e4bd350f9ace986f6
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/28/2018
---
# <a name="themes"></a>主題

![](~/media/shared/preview.png "這個 API 目前處於預覽狀態")

Xamarin.Forms 佈景主題在 Evolve 2016 所宣佈，而且可做為客戶再試一次，以及提供意見反應的預覽。

佈景主題新增到 Xamarin.Forms 應用程式包括**Xamarin.Forms.Theme.Base** Nuget 封裝，再加上定義特定的佈景主題 （例如其他封裝 Xamarin.Forms.Theme.Light) 或其他定義本機佈景主題，應用程式。

請參閱[淺色佈景主題](light.md)和[暗色調佈景主題](dark.md)指示如何將它們加入至應用程式，或簽出的頁面[範例自訂佈景主題](custom.md)。

**重要事項：**您也應該遵循的步驟[載入佈景主題 （下方） 的組件](#loadtheme)的未定案程式碼加入至 iOS`AppDelegate`和 Android `MainActivity`。 這將在未來的預覽版本中改進。


## <a name="control-appearance"></a>控制項外觀

[Light](light.md)和[深色](dark.md)這兩個主題定義適用於標準控制特定的視覺外觀。 一旦您將主題加入應用程式的資源字典時，將會變更的標準控制項的外觀。

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

這些螢幕擷取畫面顯示以這些控制項：

* 沒有套用的佈景主題
* 淺色佈景主題 （只有只有些微的差異有無佈景主題）
* 暗色調佈景主題

![](images/standard-none-sml.png "不使用佈景主題的控制項") ![ ](images/standard-light-sml.png "淺色佈景主題的控制項") ![ ](images/standard-dark-sml.png "暗色調佈景主題的控制項")

<a name="styleclass" />

## <a name="styleclass"></a>StyleClass

`StyleClass`屬性可讓根據佈景主題所提供的定義變更檢視的外觀。

[Light](light.md)和[深色](dark.md)這兩個主題定義三個不同的外觀，如`BoxView`: `HorizontalRule`， `Circle`，和`Rounded`。 此標記會顯示三個不同`BoxView`與套用不同的樣式類別：

```xaml
<StackLayout Padding="40">
    <BoxView StyleClass="HorizontalRule" />
    <BoxView StyleClass="Circle" />
    <BoxView StyleClass="Rounded" />
</StackLayout>
```

這會轉譯淺色與深色，如下所示：

![](images/boxview-light-sml.png "淺色佈景主題 StyleClass 與 BoxView") ![ ](images/boxview-dark-sml.png "與暗色調佈景主題 StyleClass BoxView")

<a name="builtin" />

## <a name="built-in-classes"></a>內建類別

除了自動設定樣式的通用控制項淺色與深色的佈景主題目前支援下列類別可以藉由設定套用`StyleClass`這些控制項上：

**BoxView**

* HorizontalRule
* 圓形
* 捨入

**影像**

* 圓形
* 捨入
* 縮圖

**Button**

* 預設
* 主要
* 成功
* 資訊
* 警告
* 危險
* 連結
* 小
* 大型

**Label**

* 頁首
* 則
* 本文
* 連結
* 反向


## <a name="troubleshooting"></a>疑難排解

<a name="loadtheme"/>

### <a name="could-not-load-file-or-assembly-xamarinformsthemelight-or-one-of-its-dependencies"></a>無法載入檔案或組件 'Xamarin.Forms.Theme.Light' 或其中一個相依性

在預覽版本中，可能無法在執行階段載入佈景主題。 加入程式碼如下所示到相關的專案以修正這個錯誤。

**iOS**

在**d**新增以下行列之後 `LoadApplication`

```csharp
var x = typeof(Xamarin.Forms.Themes.DarkThemeResources);
x = typeof(Xamarin.Forms.Themes.LightThemeResources);
x = typeof(Xamarin.Forms.Themes.iOS.UnderlineEffect);
```

**Android**

在**Weatherapp**新增以下行列之後 `LoadApplication`

```csharp
var x = typeof(Xamarin.Forms.Themes.DarkThemeResources);
x = typeof(Xamarin.Forms.Themes.LightThemeResources);
x = typeof(Xamarin.Forms.Themes.Android.UnderlineEffect);
```


## <a name="related-links"></a>相關連結

- [ThemesDemo 範例](https://github.com/xamarin/xamarin-forms-samples/tree/master/Themes/ThemesDemo)