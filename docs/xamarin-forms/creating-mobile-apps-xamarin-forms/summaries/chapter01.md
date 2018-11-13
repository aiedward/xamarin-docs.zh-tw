---
title: 第 1 章的摘要。 Xamarin.Forms 如何適應？
description: 使用 Xamarin.Forms 建立行動應用程式： 第 1 章的摘要。 Xamarin.Forms 如何適應？
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: F3F864FF-EE70-49D0-90D1-388889037625
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: dfe2d81673804e2f77694a794ef82931eb88357d
ms.sourcegitcommit: 03dfb4a2c20ad68515875b415e7d84ee9b0a8cb8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/12/2018
ms.locfileid: "51563910"
---
# <a name="summary-of-chapter-1-how-does-xamarinforms-fit-in"></a>第 1 章的摘要。 Xamarin.Forms 如何適應？

> [!NOTE] 
> 在此頁面上的附註表示其中 Xamarin.Forms 有分歧活頁簿中所呈現的題材的區域。

其中一個程式設計中最不愉快的作業移植程式碼基底到另一個平台，特別是當該平台牽涉到不同的程式設計語言。 當移植重新建構，程式碼時，會有嘗試，但如果必須以平行方式維持兩種平台，然後兩個程式碼基底之間的差異將使未來的維護更加困難。

## <a name="cross-platform-mobile-development"></a>跨平台行動裝置程式開發

以行動平台為目標時，常會使用此問題。 目前，有兩個主要的行動平台、 Iphone 和 Ipad 執行 iOS 作業系統和各種不同的手機和平板電腦執行 Android 作業系統的 Apple 系列。 另一個重要的平台是 Microsoft 的通用 Windows 平台 (UWP)，可讓單一的程式，以 Windows 10 和 Windows 10 行動裝置為目標。

想要以下列三個平台為目標的軟體廠商必須處理不同的使用者介面思維，三個不同的開發環境，三個不同的程式設計介面，並&mdash;或許是最姿&mdash;三種不同的程式設計語言： OBJECTIVE-C iPhone 與 iPad，適用於 Android、 Java 和 C# 的 Windows。

## <a name="the-c-and-net-solution"></a>C# 和.NET 方案

Objective C、 Java 和 C# 全部衍生自 C 程式設計語言，雖然它們已演變以非常不同的路徑。 C# 是一種語言最新並具有已加強非常有用的方式。 此外，C# 是密切相關整個程式設計基礎結構呼叫.NET 中，可提供支援的數學、 偵錯、 反映、 集合、 全球化、 檔案 I/O、 網路、 安全性、 執行緒、 web 服務、 資料處理和 XML 和讀取和寫入的 JSON。

Xamarin 目前會提供原生 Mac、 iOS 和 Android Api 使用 C# 和.NET 為目標的工具。 這些工具稱為 Xamarin.Mac，Xamarin.iOS、 Xamarin.Android，統稱為 Xamarin 平台。 這些是程式庫和 express.NET 慣用語與這些平台的原生 Api 的繫結。

開發人員可以使用 C# 中撰寫的應用程式，該目標 Mac、 iOS 或 Android 的 Xamarin 平台。 但當以多個平台為目標，是很合理地共用相同的目標平台之間的程式碼。 這牽涉到分成 （通常包含使用者介面） 的平台相關程式碼，以及平台獨立程式碼中，這通常需要的基底.NET framework 中的程式。 此平台獨立程式碼可以是位於可攜式類別庫 (PCL) 或共用的專案，通常稱為共用資產專案或 SAP。

> [!NOTE] 
> .NET Standard 程式庫已取代的可攜式類別庫。 活頁簿中的所有範例程式碼已經都轉換成使用.NET 標準程式庫。

## <a name="introducing-xamarinforms"></a>Xamarin.Forms 簡介

當多個行動平台為目標，則 Xamarin.Forms 可讓更多的程式碼共用。 適用於 Xamarin.Forms 撰寫的單一程式可以以五個不同的平台為目標：

- iOS 的 iPhone、 iPad 及 iPod touch 上執行的程式
- 在 Android 手機和平板電腦上執行之程式的 android
- 通用 Windows 平台目標 Windows 10 和 Windows 10 行動裝置
- Windows 8.1 的 Windows 執行階段 API
- Windows Phone 8.1 的 Windows 執行階段 API

> [!NOTE] 
> Xamarin.Forms 也不再支援 Windows 8.1、 Windows Phone 8.1 或 Windows 10 行動裝置，但 Xamarin.Forms 應用程式執行 Windows 10 桌面上。 另外還有提供預覽支援[Mac](~/xamarin-forms/platform/mac.md)， [WPF](~/xamarin-forms/platform/wpf.md)， [GTK #](~/xamarin-forms/platform/gtk.md)，以及[Tizen](/xamarin-forms/platform/tizen.md)平台。

大部分的 Xamarin.Forms 程式存在於文件庫 」 或 「 SAP。 每個平台所組成的小型應用程式虛設常式會呼叫此共用的程式碼。 

Xamarin.Forms Api 對應至每個平台上的原生控制項，讓每個平台會維持其特性的外觀及操作：

[![三重的螢幕擷取畫面的平台共用的視覺效果](images/ch01fg03-small.png "每個平台上的 Xamarin.Forms 控制項")](images/ch01fg03-large.png#lightbox "Xamarin.Forms 控制項在每個平台上")

從左到右顯示在 iPhone、 Android 手機和 Windows 10 行動電話螢幕擷取畫面。 

> [!NOTE] 
> Xamarin.Forms 不再支援 Windows 10 行動裝置。

在每個畫面上，此頁面包含 Xamarin.Forms [ `Label` ](xref:Xamarin.Forms.Label)來顯示文字[ `Button` ](xref:Xamarin.Forms.Button)用來起始動作， [ `Switch` ](xref:Xamarin.Forms.Switch)的選擇開啟/關閉的值，以及[ `Slider` ](xref:Xamarin.Forms.Slider)來指定連續的範圍內的值。 這些檢視的所有四個屬性的子系[ `StackLayout` ](xref:Xamarin.Forms.StackLayout)上[ `ContentPage` ](xref:Xamarin.Forms.ContentPage)。

也會附加到該網頁是包含數個 Xamarin.Forms 工具列[ `ToolbarItem` ](xref:Xamarin.Forms.ToolbarItem)物件。 這些是顯示為 iOS 和 Android 的畫面頂端和底部的 [Windows 10 行動裝置] 畫面的圖示。

Xamarin.Forms 也支援 XAML、 Extensible Application Markup Language 在 Microsoft 開發的數個應用程式平台。 如上所示的程式的所有視覺效果會定義在 XAML 中所示[ **PlatformVisuals** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter01/PlatformVisuals)範例。

Xamarin.Forms 程式可以判斷何種平台執行，並據以執行不同的程式碼。 更有力，開發人員可以撰寫自訂程式碼適用於各種平台，並從 Xamarin.Forms 方案中執行該程式碼平台無關的方式。 開發人員也可以藉由撰寫每個平台的轉譯器中建立額外的控制項。

很好的解決方案，針對特定業務應用程式，或建立原型，或進行快速的概念證明示範 Xamarin.Forms 時，就比較不適合需要向量圖形或複雜的觸控式互動的應用程式。

## <a name="your-development-environment"></a>您的開發環境

您的開發環境，取決於您想要為目標的平台和功能的電腦上您想要使用。

如果您想要目標 iOS，您將需要 Mac 以使用 Xcode 和 Xamarin 平台安裝。 也支援 Android 需要安裝 Java 和所需的 Sdk。 IOS 和 Android 的 Visual Studio for mac 中使用，就可以鎖定

安裝 Visual Studio 可讓電腦上您 iOS、 Android 和 Windows 平台為目標。 不過，以從 Visual Studio iOS 為目標仍會需要 Mac 以使用 Xcode 和 Xamarin 平台安裝。

您可以測試連接的 USB 至電腦，可能是在實際裝置上或模擬器上的程式。

## <a name="installation"></a>安裝

再建立及建置 Xamarin.Forms 應用程式，您應該嘗試建立和個別建置 iOS 應用程式、 Android 應用程式和 UWP 應用程式，視您想要的目標和您的開發環境的平台而定。

Xamarin 與 Microsoft web sites 包含有關如何執行這項操作：

- [開始使用 iOS](~/ios/get-started/index.md)
- [Android 使用者入門](~/android/get-started/index.md)
- [Windows 開發人員中心](http://dev.windows.com)

您可以建立一次，並執行這些個別的平台的專案，您應該會有任何問題建立並執行 Xamarin.Forms 應用程式。

## <a name="related-links"></a>相關連結

- [第 1 章全文檢索 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch01-Apr2016.pdf)
- [第 1 章範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter01)
