---
title: Introduction to Xamarin.Forms 控制項範本
description: Xamarin.Forms 控制項範本提供的功能，輕鬆地設定和重新佈景主題在執行階段的應用程式頁面。 這篇文章提供控制項範本的簡介。
ms.prod: xamarin
ms.assetid: 8B8E2360-6531-44A3-A7C8-9A8808DE9B86
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/08/2016
ms.openlocfilehash: 6b7a6c6d9c9c541e1d5e821fc2dac202e98bec62
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/12/2018
ms.locfileid: "38994421"
---
# <a name="introduction-to-xamarinforms-control-templates"></a>Introduction to Xamarin.Forms 控制項範本

_Xamarin.Forms 控制項範本提供的功能，輕鬆地設定和重新佈景主題在執行階段的應用程式頁面。這篇文章提供控制項範本的簡介。_

控制項有不同的屬性，例如`BackgroundColor`和`TextColor`，可以定義控制項的外觀的層面。 可以設定這些屬性，使用[樣式](~/xamarin-forms/user-interface/styles/index.md)，其可以在執行階段來實作基本的佈景主題變更。 不過，樣式不會維持清楚的分隔頁的外觀和其內容，而且可以藉由設定這類屬性中所做的變更是有限。

控制項範本提供清楚的分隔頁面的外觀和其內容，因此可讓建立可輕鬆地配置其佈景主題的頁面。 例如，應用程式可能包含提供暗色調佈景主題和淺色佈景主題的應用程式層級控制項範本。 每個[ `ContentPage` ](xref:Xamarin.Forms.ContentPage)應用程式中可以配置其佈景主題套用控制項範本的其中一個而不需要變更每個頁面所要顯示的內容。 此外，控制項範本所提供的佈景主題，不僅可以變更控制項的屬性。 它們也可以變更用來實作佈景主題的控制項。

## <a name="creating-a-controltemplate"></a>建立 ControlTemplate

A [ `ControlTemplate` ](xref:Xamarin.Forms.ControlTemplate)指定頁面或檢視的外觀，並包含根版面配置和版面配置，這項實作範本的控制項內。 通常`ControlTemplate`將會利用[ `ContentPresenter` ](xref:Xamarin.Forms.ContentPresenter)將標示要顯示的網頁或檢視的內容會出現的位置。 網頁或檢視來取用`ControlTemplate`接著會定義要顯示的內容`ContentPresenter`。 下圖說明`ControlTemplate`包含數個控制項，包括頁面`ContentPresenter`標示有藍色矩形：

![](introduction-images/control-template.png "網頁的控制項範本")

A [ `ControlTemplate` ](xref:Xamarin.Forms.ControlTemplate)可以套用至下列類型，藉由設定其`ControlTemplate`屬性：

- [`ContentPage`](xref:Xamarin.Forms.ContentPage)
- [`ContentView`](xref:Xamarin.Forms.ContentView)
- [`TemplatedPage`](xref:Xamarin.Forms.TemplatedPage)
- [`TemplatedView`](xref:Xamarin.Forms.TemplatedView)

當[ `ControlTemplate` ](xref:Xamarin.Forms.ControlTemplate)建立並指派至這些型別，任何現有的外觀會取代中所定義的外觀`ControlTemplate`。 此外，以及使用設定外觀`ControlTemplate`屬性、 控制項範本也可以藉由使用樣式來進一步套用展開佈景主題的能力。

> [!NOTE]
>  *何謂`TemplatedPage`和`TemplatedView`類型？* `TemplatedPage` 是基底類別`ContentPage`，並且是 Xamarin.Forms 所提供的最基本頁面類型。 不同於`ContentPage`，`TemplatedPage`沒有`Content`屬性。 因此，內容無法直接加入`TemplatedPage`執行個體。 相反地，加入所設定的控制項範本的內容`TemplatedPage`執行個體。 同樣地，`TemplatedView`是基底類別`ContentView`。 不同於`ContentView`，`TemplatedView`沒有`Content`屬性。 因此，內容無法直接加入`TemplatedView`執行個體。 相反地，加入所設定的控制項範本的內容`TemplatedView`執行個體。

在 XAML 和 C# 中，您可以建立控制項範本：

- 在 XAML 中建立的控制項範本中定義[ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) ，指派給[ `Resources` ](xref:Xamarin.Forms.VisualElement.Resources)集合頁面上，或是通常[ `Resources` ](xref:Xamarin.Forms.Application.Resources)應用程式集合。
- 在頁面的類別中，或可全域存取的類別中，通常被定義在 C# 中建立的控制項範本。

選擇要定義在何處[ `ControlTemplate` ](xref:Xamarin.Forms.ControlTemplate)執行個體可以使用它的影響：

- [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) 在頁面層級定義的執行個體只能套用至頁面。
- [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) 在應用程式層級定義的執行個體可以套用至整個應用程式的頁面。

檢視階層中較低的控制項範本的優先於設定更高版本定義。 例如， [ `ControlTemplate` ](xref:Xamarin.Forms.ControlTemplate)名為`DarkTheme`，把它定義在頁面層級將會優先於應用程式層級定義的相同具名範本。 因此，應該定義控制項範本定義要套用至應用程式中的每個頁面的佈景主題，應用程式層級。


## <a name="related-links"></a>相關連結

- [樣式](~/xamarin-forms/user-interface/styles/index.md)
- [ControlTemplate](xref:Xamarin.Forms.ControlTemplate)
- [ContentPresenter](xref:Xamarin.Forms.ContentPresenter)
