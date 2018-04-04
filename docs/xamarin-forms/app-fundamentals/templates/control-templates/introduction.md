---
title: 簡介
description: Xamarin.Forms 控制項範本提供輕鬆佈景主題和 re 佈景主題的功能在執行階段應用程式頁面。 本文章提供控制項範本的簡介。
ms.prod: xamarin
ms.assetid: 8B8E2360-6531-44A3-A7C8-9A8808DE9B86
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/08/2016
ms.openlocfilehash: 744419cbc457ffb6dab6b46d690151c08ca35d42
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="introduction"></a>簡介

_Xamarin.Forms 控制項範本提供輕鬆佈景主題和 re 佈景主題的功能在執行階段應用程式頁面。本文章提供控制項範本的簡介。_

控制項有不同的屬性，例如`BackgroundColor`和`TextColor`，可定義控制項的外觀。 這些屬性可以使用設定[樣式](~/xamarin-forms/user-interface/styles/index.md)，這可以在執行階段來實作基本的佈景主題變更。 不過，樣式不會保留清楚分隔頁面的外觀和其內容，而且可以設定這類屬性所做的變更會受到限制。

控制項範本提供清楚分隔頁面的外觀和其內容，因此允許建立可以輕鬆地設定主題的網頁。 例如，應用程式可能包含暗色調佈景主題和淺色佈景主題所提供的應用程式層級的控制項範本。 每個[ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)應用程式中可以佈景主題套用控制項樣板的其中一個不需要變更每個頁面所顯示的內容。 此外，在控制項範本所提供的佈景主題當中不限於變更控制項的屬性。 它們也可以變更用來實作佈景主題的控制項。

## <a name="creating-a-controltemplate"></a>建立 ControlTemplate

A [ `ControlTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ControlTemplate/)指定外觀的頁面或檢視中，且包含根版面配置和版面配置，實作範本的控制項內。 一般而言，`ControlTemplate`會利用[ `ContentPresenter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPresenter/)將標示要顯示檢視的頁面內容的顯示位置。 使用檢視的頁面`ControlTemplate`然後會定義要顯示的內容`ContentPresenter`。 下圖說明`ControlTemplate`頁面包含的控制項，包括數字`ContentPresenter`藍色矩形標記：

![](introduction-images/control-template.png "頁面控制項範本")

A [ `ControlTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ControlTemplate/)可以藉由設定套用至下列型別及其`ControlTemplate`屬性：

- [`ContentPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)
- [`ContentView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentView/)
- [`TemplatedPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.TemplatedPage/)
- [`TemplatedView`](https://developer.xamarin.com/api/type/Xamarin.Forms.TemplatedView/)

當[ `ControlTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ControlTemplate/)建立並指派至這些型別，任何現有的外觀會取代中定義的外觀`ControlTemplate`。 此外，以及藉由設定外觀`ControlTemplate`屬性，也可以藉由使用樣式來進一步套用範本控制展開佈景主題的能力。

> [!NOTE]
>  *什麼是`TemplatedPage`和`TemplatedView`類型？* `TemplatedPage` 是基底類別`ContentPage`，Xamarin.Forms 所提供的最基本的頁面類型。 不同於`ContentPage`，`TemplatedPage`沒有`Content`屬性。 因此，內容無法直接加入`TemplatedPage`執行個體。 相反地，加入所設定的控制項範本的內容`TemplatedPage`執行個體。 同樣地，`TemplatedView`是基底類別`ContentView`。 不同於`ContentView`，`TemplatedView`沒有`Content`屬性。 因此，內容無法直接加入`TemplatedView`執行個體。 相反地，加入所設定的控制項範本的內容`TemplatedView`執行個體。

以 XAML 和 C# 中，您可以建立控制項樣板：

- 在 XAML 中建立的控制項範本中定義[ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/) ，指派給[ `Resources` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Resources/)集合頁面上，或是通常[ `Resources` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Application.Resources/)應用程式集合。
- 在頁面的類別中，或可全域存取的類別中，通常被定義在 C# 中建立的控制項範本。

選擇要定義在何處[ `ControlTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ControlTemplate/)執行個體可以使用它的影響：

- [`ControlTemplate`](https://developer.xamarin.com/api/type/Xamarin.Forms.ControlTemplate/) 在頁面層級定義的執行個體只能套用至頁面。
- [`ControlTemplate`](https://developer.xamarin.com/api/type/Xamarin.Forms.ControlTemplate/) 在應用程式層級定義的執行個體可以套用至整個應用程式的頁面。

控制項範本的檢視階層架構中較低的優先順序高於定義更高版本上。 例如， [ `ControlTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ControlTemplate/)名為`DarkTheme`在定義的頁面層級將會優先於應用程式層級定義的相同具名範本。 因此，定義要套用至應用程式中的每個頁面的佈景主題的控制項範本應該定義在應用程式層級。


## <a name="related-links"></a>相關連結

- [樣式](~/xamarin-forms/user-interface/styles/index.md)
- [ControlTemplate](https://developer.xamarin.com/api/type/Xamarin.Forms.ControlTemplate/)
- [ContentPresenter](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPresenter/)
