---
title: 第1章的摘要。 如何 Xamarin.Forms 融入？
description: 建立 Mobile Apps Xamarin.Forms ：第1章的摘要。 如何 Xamarin.Forms 融入？
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: F3F864FF-EE70-49D0-90D1-388889037625
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 1af4b990d388436c04a2feb0abe3d4e3a8bc6756
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93370451"
---
# <a name="summary-of-chapter-1-how-does-no-locxamarinforms-fit-in"></a>第1章的摘要。 如何 Xamarin.Forms 融入？

[![下載範例](~/media/shared/download.png) 下載範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter01)

> [!NOTE]
> 這本書是在2016的春季發行，而且自那時起尚未更新。 本書中有很多工具價值，但部分資料已過期，有些主題則不再是完全正確或完整。

程式設計中最不愉快的工作之一，就是將程式碼基底從某個平臺移植到另一個平臺，特別是當該平臺牽涉到不同的程式設計語言時。 移植程式碼來重構程式碼時，有一個誘惑，但是如果兩個平臺都必須平行維護，則這兩個程式碼基底之間的差異將讓未來的維護變得更困難。

## <a name="cross-platform-mobile-development"></a>跨平台行動裝置程式開發

以行動平臺為目標時，這個問題很常見。 目前有兩個主要的行動平臺，也就是執行 iOS 作業系統的 Apple 系列 Iphone 和 Ipad，以及在各種電話和平板電腦上執行的 Android 作業系統。 另一個重要的平臺是 Microsoft 的通用 Windows 平臺 (UWP) ，可讓單一程式以 Windows 10 為目標。

希望以這些平臺為目標的軟體廠商必須處理不同的使用者介面架構、三種不同的開發環境、三種不同的程式設計介面， &mdash; 最滑雪 &mdash; 的三種不同的程式設計語言：適用于 IPhone 和 IPad 的目標 C、適用于 Android 的 JAVA，以及適用于 Windows 的 c #。

## <a name="the-c-and-net-solution"></a>C # 和 .NET 方案

雖然目標 C、JAVA 和 c # 都是衍生自 C 程式設計語言，但卻是由非常不同的路徑所發展。 C # 是這些語言的最新版本，並以非常實用的方式成熟。 此外，c # 與稱為 .NET 的整個程式設計基礎結構密切相關，它提供數學、偵測、反映、集合、全球化、檔案 i/o、網路、安全性、執行緒、web 服務、資料處理，以及 XML 和 JSON 讀取和寫入的支援。

Xamarin 目前提供的工具可讓您使用 c # 和 .NET，以原生 Mac、iOS 和 Android Api 為目標。 這些工具稱為 Xamarin、Xamarin、和 Xamarin，統稱為 Xamarin 平臺。 這些是使用 .NET 慣用語來表達這些平臺原生 Api 的程式庫和系結。

開發人員可以使用 Xamarin 平臺，以 c # 撰寫以 Mac、iOS 或 Android 為目標的應用程式。 但是，當目標為多個平臺時，在目標平臺之間共用一些程式碼相當有意義。 這包括將程式分隔為平臺相依程式碼， (通常會涉及使用者介面) 和平臺獨立程式碼，通常只需要基底 .NET framework。 這個平臺獨立程式碼可以位於便攜類別庫 (PCL) 或共用專案，通常稱為共用資產專案或 SAP。

> [!NOTE]
> 便攜類別庫已由 .NET Standard 程式庫取代。 書籍中的所有範例程式碼都已轉換成使用 .NET standard 程式庫。

## <a name="introducing-no-locxamarinforms"></a>正在推出 Xamarin.Forms

以多個行動平臺為目標時， Xamarin.Forms 可允許更多程式碼共用。 撰寫的單一程式可將目標設為 Xamarin.Forms 下列平臺：

- 適用于 iPhone、iPad 和 iPod touch 上執行之程式的 iOS
- Android 手機和平板電腦上執行的程式
- 要設為目標的通用 Windows 平臺 Windows 10

> [!NOTE]
> Xamarin.Forms 不再支援 Windows 8.1、Windows Phone 8.1 或 Windows 10 行動裝置版，但 Xamarin.Forms 應用程式會在 Windows 10 桌面上執行。 另外還有針對 [Mac](~/xamarin-forms/platform/other/mac.md)、 [WPF](~/xamarin-forms/platform/other/wpf.md)、 [GTK #](~/xamarin-forms/platform/other/gtk.md)和 [Tizen](~/xamarin-forms/platform/other/tizen.md) 平臺的預覽支援。

大部分的 Xamarin.Forms 程式都存在於程式庫或 SAP 中。 每個平臺都是由呼叫此共用程式碼的小型應用程式存根所組成。

Xamarin.FormsApi 會對應至每個平臺上的原生控制項，讓每個平臺都能維持其特性的外觀和風格：

[![平臺視覺效果共用的三重螢幕擷取畫面](images/ch01fg03-small.png "：：：非 loc (Xamarin. Forms) ：：：每個平臺上的控制項")](images/ch01fg03-large.png#lightbox "：：：非 loc (Xamarin. Forms) ：：：每個平臺上的控制項")

從左至右的螢幕擷取畫面會顯示 iPhone 和 Android 手機：

在每個畫面上，頁面都包含 Xamarin.Forms [`Label`](xref:Xamarin.Forms.Label) 用於顯示文字的、 [`Button`](xref:Xamarin.Forms.Button) 起始動作的、 [`Switch`](xref:Xamarin.Forms.Switch) 用於選擇開啟/關閉值的，以及用 [`Slider`](xref:Xamarin.Forms.Slider) 來指定連續範圍內值的。 這四個視圖都是中的子系 [`StackLayout`](xref:Xamarin.Forms.StackLayout) [`ContentPage`](xref:Xamarin.Forms.ContentPage) 。

同時附加至頁面是由數 Xamarin.Forms 個物件所組成的工具列 [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem) 。 這些圖示會顯示為 iOS 和 Android 畫面頂端的圖示，以及位於 Windows 10 行動裝置版畫面底部的圖示。

Xamarin.Forms 也支援 XAML，這是在 Microsoft 為數個應用程式平臺開發的 Extensible Application Markup Language。 上述程式的所有視覺效果都是在 XAML 中定義，如 [**PlatformVisuals**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter01/PlatformVisuals) 範例中所示範。

Xamarin.Forms程式可以判斷執行的平臺，並據此執行不同的程式碼。 更強大且的是，開發人員可以撰寫各種平臺的自訂程式碼，並 Xamarin.Forms 以與平臺無關的方式從程式執行該程式碼。 開發人員也可以撰寫每個平臺的轉譯器，以建立其他控制項。

雖然這 Xamarin.Forms 是適用于企業營運應用程式或原型設計，或是進行快速概念證明示範的絕佳解決方案，但較不適合需要向量圖形或複雜觸控互動的應用程式。

## <a name="your-development-environment"></a>您的開發環境

您的開發環境取決於您想要設為目標的平臺，以及您想要使用的機器。

如果您想要以 iOS 為目標，您將需要已安裝 Xcode 和 Xamarin platform 的 Mac。 支援 Android 也需要安裝 JAVA 和必要的 Sdk。 然後，您可以使用 Visual Studio for Mac 以 iOS 和 Android 為目標。

安裝 Visual Studio 可讓您在電腦上以 iOS、Android 和所有 Windows 平臺為目標。 不過，以 Visual Studio 的 iOS 為目標時，仍需要已安裝 Xcode 和 Xamarin platform 的 Mac。

您可以在透過 USB 連接到電腦或模擬器上的實際裝置上測試程式。

## <a name="installation"></a>安裝

建立和建立 Xamarin.Forms 應用程式之前，您應該嘗試根據您想要作為目標的平臺和開發環境，分別建立和建立 iOS 應用程式、Android 應用程式和 UWP 應用程式。

Xamarin 和 Microsoft 網站包含有關如何執行此作業的資訊：

- [開始使用 iOS](~/ios/get-started/index.md)
- [開始使用 Android](~/android/get-started/index.md)
- [Windows 開發人員中心](https://dev.windows.com)

一旦您可以建立並執行這些個別平臺的專案，您就應該不會在建立和執行 Xamarin.Forms 應用程式時遇到問題。

## <a name="related-links"></a>相關連結

- [第1章完整文字 (PDF) ](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch01-Apr2016.pdf)
- [第1章範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter01)
