---
title: Xamarin.Forms 控制項範本簡介
description: Xamarin.Forms 控制項範本可以在執行階段輕鬆地設定和重新設定應用程式頁面的佈景主題。 本文提供控制項範本的簡介。
ms.prod: xamarin
ms.assetid: 8B8E2360-6531-44A3-A7C8-9A8808DE9B86
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/08/2016
ms.openlocfilehash: 70646999154297592137c6966626b318fb73897c
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2019
ms.locfileid: "70771259"
---
# <a name="introduction-to-xamarinforms-control-templates"></a>Xamarin.Forms 控制項範本簡介

_[Xamarin] 控制項範本可讓您在執行時間輕鬆地主題和重新主題應用程式頁面。本文提供控制項範本的簡介。_

控制項具有不同的屬性 (例如 `BackgroundColor` 和 `TextColor`)，可定義控制項外觀的各層面。 這些屬性可使用[樣式](~/xamarin-forms/user-interface/styles/index.md)來設定，並可在執行階段變更以實作基本佈景主題。 不過，樣式無法確保清楚區隔頁面及其內容的外觀，而且設定這類屬性可做的變更有限。

控制項範本可清楚區隔頁面及其內容的外觀，因此建立的頁面可輕鬆設定佈景主題。 例如，應用程式可能包含提供深色佈景主題和淺色佈景主題的應用程式層級控制項範本。 您可以套用其中一個控制項範本，來設定應用程式中每個 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 的佈景主題，而不需要變更每一頁所顯示的內容。 此外，控制項範本所提供的佈景主題，不僅限於變更控制項的屬性。 還可變更用來實作佈景主題的控制項。

## <a name="creating-a-controltemplate"></a>建立 ControlTemplate

[`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) 可指定頁面或檢視的外觀，並包含根配置及配置中用來實作範本的控制項。 一般而言，`ControlTemplate` 會利用 [`ContentPresenter`](xref:Xamarin.Forms.ContentPresenter) 來標示頁面或檢視所要顯示內容的出現位置。 使用 `ControlTemplate` 的頁面或檢視會接著定義由 `ContentPresenter` 顯示內容。 下圖說明內含一些控制項的頁面 `ControlTemplate`，包括以藍色方框標示的 `ContentPresenter`：

![](introduction-images/control-template.png "Control Template for a Page")

[`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) 可透過設定其 `ControlTemplate` 屬性來套用至下列類型：

- [`ContentPage`](xref:Xamarin.Forms.ContentPage)
- [`ContentView`](xref:Xamarin.Forms.ContentView)
- [`TemplatedPage`](xref:Xamarin.Forms.TemplatedPage)
- [`TemplatedView`](xref:Xamarin.Forms.TemplatedView)

建立 [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) 並將它指派給這些類型時，會以 `ControlTemplate` 中定義的外觀取代任何現有外觀。 此外，除了使用 `ControlTemplate` 屬性來設定外觀，還可以使用樣式套用控制項範本來進一步擴充佈景主題功能。

> [!NOTE]
> `TemplatedPage` 和 `TemplatedView` 類型為何？ `TemplatedPage` 是 `ContentPage` 的基底類別，也是 Xamarin.Forms 所提供的最基本頁面類型。 不同於 `ContentPage`，`TemplatedPage` 沒有 `Content` 屬性。 因此，您無法將內容直接新增至 `TemplatedPage` 執行個體。 相反地，請設定 `TemplatedPage` 執行個體的控制項範本來新增內容。 同樣地，`TemplatedView` 是 `ContentView` 的基底類別。 不同於 `ContentView`，`TemplatedView` 沒有 `Content` 屬性。 因此，您無法將內容直接新增至 `TemplatedView` 執行個體。 相反地，請設定 `TemplatedView` 執行個體的控制項範本來新增內容。

您可以在 XAML 和 C# 中建立控制項範本：

- 在 XAML 中建立的控制項範本是在指派給頁面 [`Resources`](xref:Xamarin.Forms.VisualElement.Resources) 集合或應用程式 [`Resources`](xref:Xamarin.Forms.Application.Resources) 集合 (後者更常見) 的 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) 中定義。
- 在 C# 中所建立控制項範本通常是在頁面的類別中定義，或在可全域存取的類別中定義。

選擇要在何處定義 [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) 執行個體會影響其可使用的位置：

- 在頁面層級定義的 [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) 執行個體只能套用至頁面。
- 在應用程式層級定義的 [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) 執行個體則可套用至整個應用程式的所有頁面。

檢視階層架構中較低控制項範本會優先於定義於較高位置的控制項範本。 例如，定義於頁面層級且名名為 `DarkTheme` 之 [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) 會優先於定義於應用程式層級且名稱完全相同的範本。 因此，如有控制項範本定義要套用至應用程式中每一頁的佈景主題，則應該在應用程式層級定義該範本。

## <a name="related-links"></a>相關連結

- [樣式](~/xamarin-forms/user-interface/styles/index.md)
- [ControlTemplate](xref:Xamarin.Forms.ControlTemplate)
- [ContentPresenter](xref:Xamarin.Forms.ContentPresenter)
