---
title: 在 iOS 上的 ListView 群組標頭樣式
description: 平台特性可讓您使用的功能只可在特定的平台，而不需要實作自訂轉譯器或影響。 這篇文章說明如何使用 iOS 平台特定的控制捲動期間是否 float ListView 標頭資料格。
ms.prod: xamarin
ms.assetid: 099B2C7F-727F-4BCF-903B-87E728108C24
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: 58b3787b71cff9b78f1c6b577be6c320367f1cee
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60952140"
---
# <a name="listview-group-header-style-on-ios"></a>在 iOS 上的 ListView 群組標頭樣式

[![下載範例](~/media/shared/download.png)下載範例](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)

此 iOS 平台特定的控制項是否[ `ListView` ](xref:Xamarin.Forms.ListView)捲動期間浮點數的標頭資料格。 它由在 XAML 中設定`ListView.GroupHeaderStyle`的值可繫結屬性`GroupHeaderStyle`列舉型別：

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout Margin="20">
        <ListView ... ios:ListView.GroupHeaderStyle="Grouped">
            ...
        </ListView>
    </StackLayout>
</ContentPage>
```

或者，它可以取用從 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

listView.On<iOS>().SetGroupHeaderStyle(GroupHeaderStyle.Grouped);
```

`ListView.On<iOS>`方法可讓您指定這個平台專屬只會在 iOS 上執行。 `ListView.SetGroupHeaderStyle`方法，請在[ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)命名空間，是用來控制[ `ListView` ](xref:Xamarin.Forms.ListView)捲動期間浮點數的標頭資料格。 `GroupHeaderStyle`列舉提供兩個可能值：

- `Plain` – 表示標頭資料格 float 何時[ `ListView` ](xref:Xamarin.Forms.ListView)是捲動 （預設值）。
- `Grouped` – 表示標頭資料格執行不浮動的時機[ `ListView` ](xref:Xamarin.Forms.ListView)捲動。

颾魤 ㄛ`ListView.GetGroupHeaderStyle`方法可以用來傳回`GroupHeaderStyle`套用至[ `ListView` ](xref:Xamarin.Forms.ListView)。

結果是，指定`GroupHeaderStyle`值套用至[ `ListView` ](xref:Xamarin.Forms.ListView)，這會控制捲動期間是否 float 標頭資料格：

[![浮點數和非浮動 ListView 標頭資料格，在 iOS 上的螢幕擷取畫面](listview-group-header-style-images/group-header-styles.png "浮點數和非浮動的標頭資料格的 ListView")](listview-group-header-style-images/group-header-styles-large.png#lightbox "浮點數和非浮動的標頭資料格的 ListView")

## <a name="related-links"></a>相關連結

- [PlatformSpecifics （範例）](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
