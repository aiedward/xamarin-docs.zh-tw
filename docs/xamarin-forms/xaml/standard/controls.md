---
title: "XAML 標準 （預覽） 控制項"
description: "如何開始瀏覽 Xamarin.Forms 中的 XAML 標準預覽"
ms.topic: article
ms.prod: xamarin
ms.assetid: 287E6631-D1C5-46C5-8905-AB53D34E365D
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 11/15/2017
ms.openlocfilehash: b044cb849f9a8e591a8db5907211a55f77d6e45f
ms.sourcegitcommit: 8e722d72c5d1384889f70adb26c5675544897b1f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/15/2018
---
# <a name="xaml-standard-preview-controls"></a>XAML 標準 （預覽） 控制項

![預覽](~/media/shared/preview.png)

此頁面會列出在預覽中，連同其對等的 Xamarin.Forms 控制項可用的 XAML 標準控制項。

另外還有控制項中 XAML 標準具有新的屬性和列舉型別名稱的清單。

## <a name="controls"></a>控制項

|Xamarin.Forms|XAML 標準|
|--- |--- |
|Frame|Border|
|選擇器|ComboBox|
|ActivityIndicator|ProgressRing|
|StackLayout|StackPanel|
|ThisAddIn|TextBlock|
|進入|TextBox|
|參數|ToggleSwitch|
|ContentView|UserControl|


## <a name="properties-and-enumerations"></a>屬性和列舉型別

|Xamarin.FormsControls 與更新的屬性|Xamarin.FormsProperty 或列舉|XAML StandardEquivalent|
|--- |--- |--- |
|按鈕、 項目、 標籤、 日期選擇器、 編輯器、 SearchBar、 Timeupdown|TextColor|前景|
|VisualElement|BackgroundColor|背景 *|
|選擇器 按鈕|BorderColor, OutlineColor|BorderBrush|
|按鈕|BorderWidth|BorderThickness|
|進度列|進度|值|
|按鈕、 項目、 標籤、 編輯器、 SearchBar、 範圍、 字型|FontAttributesBold、 斜體、 無|FontStyleItalic, Normal|
|按鈕、 項目、 標籤、 編輯器、 SearchBar、 範圍、 字型|FontAttributes|FontWeights * 粗體、 一般|
|InputView|KeyboardDefault、 Url、 數目、 電話、 文字、 交談，傳送電子郵件|InputScopeNameValue * 預設值、 Url、 數字、 TelephoneNumber、 文字、 聊天室、 EmailNameOrAddress|
|StackPanel|StackOrientation|方向 *|

> [!IMPORTANT]
> 項目以標示 * 會在目前的預覽版本中不完整

## <a name="related-links"></a>相關連結

- [預覽 NuGet](https://aka.ms/xf-xamlstandard-nuget)
