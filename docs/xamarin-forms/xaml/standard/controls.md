---
title: XAML 標準 （預覽） 控制項
description: 這篇文章將探討在 Xamarin.Forms 中提供的 XAML 標準控制項。
ms.prod: xamarin
ms.assetid: 287E6631-D1C5-46C5-8905-AB53D34E365D
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 11/15/2017
ms.openlocfilehash: 1b01d0773f0c2150db575875b770957eb6452f41
ms.sourcegitcommit: 632955f8cdb80712abd8dcc30e046cb9c435b922
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2018
ms.locfileid: "38843939"
---
# <a name="xaml-standard-preview-controls"></a>XAML 標準 （預覽） 控制項

![預覽](~/media/shared/preview.png)

此頁面列出可供預覽，以及其對等的 Xamarin.Forms 控制項的 XAML 標準控制項。

另外還有一份 XAML Standard 中有新的屬性和列舉型別名稱的控制項。

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

|Xamarin.Forms 控制項已更新的屬性|Xamarin.Forms 屬性或列舉|XAML 標準對等項目|
|--- |--- |--- |
|按鈕、 項目、 標籤、 日期選擇器、 編輯器、 SearchBar、 TimePicker|TextColor|前景|
|VisualElement|BackgroundColor|背景 *|
|選擇器中按鈕|框線色彩 OutlineColor|BorderBrush|
|按鈕|BorderWidth|BorderThickness|
|進度列|進度|值|
|按鈕、 項目、 標籤、 編輯器、 SearchBar、 範圍、 字型|FontAttributesBold、 斜體、 無|FontStyleItalic 一般|
|按鈕、 項目、 標籤、 編輯器、 SearchBar、 範圍、 字型|FontAttributes|FontWeights * 粗體、 一般|
|InputView|KeyboardDefault、 Url、 數目、 電話、 文字、 聊天、 電子郵件|InputScopeNameValue * 預設、 Url、 數字、 TelephoneNumber、 文字、 聊天、 EmailNameOrAddress|
|StackPanel|StackOrientation|方向 *|

> [!IMPORTANT]
> 項目以標示 * 會在目前的預覽版本中不完整

## <a name="related-links"></a>相關連結

- [NuGet 預覽](https://aka.ms/xf-xamlstandard-nuget)
