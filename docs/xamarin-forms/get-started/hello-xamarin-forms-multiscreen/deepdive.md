---
title: Xamarin.Forms 多重畫面深度剖析
description: 本文介紹 Xamarin.Forms 應用程式中的頁面巡覽和資料繫結，並示範其在多重畫面跨平台應用程式中的用法。
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: e4faa36c-6600-48c0-94c4-b4431103a4
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/02/2016
ms.openlocfilehash: 1c7edff3c71b9d7530b2acf21acaa06149156d43
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/11/2018
ms.locfileid: "35242231"
---
# <a name="xamarinforms-multiscreen-deep-dive"></a>Xamarin.Forms 多重畫面深度剖析

在 [Xamarin.Forms 多重畫面快速入門](~/xamarin-forms/get-started/hello-xamarin-forms-multiscreen/quickstart.md)中，Phoneword 應用程式已延伸為包含可追蹤應用程式通話記錄的另一個畫面。 本文會檢閱已經建立的項目，以了解 Xamarin.Forms 應用程式中的頁面巡覽和資料繫結。

## <a name="navigation"></a>巡覽

Xamarin.Forms 提供內建的巡覽模型，可管理一疊頁面的巡覽和使用者體驗。 此模型會實作一疊後進先出 (LIFO) 的 `Page` 物件。 若要從一頁移動到另一頁，應用程式要將新頁面推送至這個堆疊。 若要返回到上一頁，應用程式將會從堆疊快顯目前的頁面。

Xamarin.Forms 的 [`NavigationPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) 類別可管理 [`Page`](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/) 物件的堆疊。 `NavigationPage` 類別也會將巡覽列新增到顯示標題，以及返回上一頁之平台相應 [上一頁]<span class="uiitem"></span> 按鈕的頁面頂端。 下列程式碼範例示範如何在應用程式的第一頁周圍包裝 `NavigationPage`：

```csharp
public App ()
{
    ...
    MainPage = new NavigationPage (new MainPage ());
}
```

所有 [`ContentPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) 執行個體都有一個可公開方法的 [`Navigation`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Navigation/) 屬性，以修改頁面堆疊。 只有在應用程式包含 [`NavigationPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) 時，才會叫用這些方法。 若要導覽至 `CallHistoryPage`，必須叫用 [`PushAsync`](https://developer.xamarin.com/api/member/Xamarin.Forms.NavigationPage.PushAsync/p/Xamarin.Forms.Page/) 方法，如下列程式碼範例所示：

```csharp
async void OnCallHistory(object sender, EventArgs e)
{
    await Navigation.PushAsync (new CallHistoryPage ());
}
```

這會使新 `CallHistoryPage` 物件推送到巡覽堆疊上。 若要以程式設計的方式返回到原始頁面，`CallHistoryPage` 物件必須叫用 [`PopAsync`](https://developer.xamarin.com/api/member/Xamarin.Forms.NavigationPage.PopAsync()/) 方法，如下列程式碼範例所示：

```csharp
await Navigation.PopAsync();
```

不過，在 Phoneword 應用程式中不需要這個程式碼，因為 [`NavigationPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) 類別會將巡覽列新增至包含返回上一頁之平台相應 [上一頁]<span class="uiitem"></span> 按鈕的頁面頂端。

## <a name="data-binding"></a>資料繫結

資料繫結用來簡化 Xamarin.Forms 應用程式顯示其資料以及與之互動的方式。 它會在使用者介面與基礎應用程式之間建立連線。 [`BindableObject`](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableObject/) 類別包含許多支援資料繫結的基礎結構。

資料繫結會定義兩個物件之間的關聯性。 「來源」物件將會提供資料。 「目標」物件將會取用 (而且通常會顯示) 來源物件中的資料。 在 Phoneword 應用程式中，繫結目標是可顯示電話號碼的 [`ListView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) 控制項，而 `PhoneNumbers` 集合則是繫結來源。

系統會在 `App` 類別中宣告並初始化 `PhoneNumbers` 集合，如下列程式碼範例所示：

```csharp
public partial class App : Application
{
   public static List<string> PhoneNumbers { get; set; }

   public App ()
   {
     PhoneNumbers = new List<string>();
     ...
   }
   ...
}
```

系統會在 `CallHistoryPage` 類別中宣告並初始化 [`ListView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) 執行個體，如下列程式碼範例所示：

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage ...
             xmlns:local="clr-namespace:Phoneword;assembly=Phoneword"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             ...>
    ...
    <ContentPage.Content>
       ...
       <ListView ItemsSource="{x:Static local:App.PhoneNumbers}" />
       ...
    </ContentPage.Content>
</ContentPage>
```

在此範例中，[`ListView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) 控制項將會顯示 [`ItemsSource`](https://developer.xamarin.com/api/property/Xamarin.Forms.ItemsView.ItemsSource/) 屬性所繫結之資料的 `IEnumerable` 集合。 資料的集合可以是任何類型的物件，但是根據預設，`ListView` 將使用每個項目的 `ToString` 方法顯示該項目。 [`x:Static`](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.StaticExtension/) 標記延伸用來指出 `ItemsSource` 屬性將繫結至可能位於 `local` 命名空間之 `App` 類別的靜態 `PhoneNumbers` 屬性。

如需有關資料繫結的詳細資訊，請參閱[資料繫結基本概念](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)。 如需有關 XAML 標記延伸的詳細資訊，請參閱 [XAML 標記延伸](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)。

## <a name="additional-concepts-introduced-in-phoneword"></a>Phoneword 中導入的其他概念

[`ListView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) 負責在螢幕上顯示項目的集合。 `ListView` 中的每個項目都包含在單一資料格中。 如需有關使用 `ListView` 控制項的詳細資訊，請參閱 [ListView](~/xamarin-forms/user-interface/listview/index.md)。

## <a name="summary"></a>總結

本文介紹了 Xamarin.Forms 應用程式中的頁面巡覽和資料繫結，並示範了其在多重畫面跨平台應用程式中的用法。
