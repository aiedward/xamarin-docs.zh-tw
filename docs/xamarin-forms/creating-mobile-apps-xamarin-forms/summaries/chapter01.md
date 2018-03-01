---
title: "第 1 的摘要。 Xamarin.Forms 納入如何？"
ms.topic: article
ms.prod: xamarin
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: c0f3313fa3c4d1075be7deeb871e303006c533e8
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="summary-of-chapter-1-how-does-xamarinforms-fit-in"></a>第 1 的摘要。 Xamarin.Forms 納入如何？

其中一個在程式設計中最不愉快的作業移植程式碼基底到另一個，從一個平台，特別是如果該平台牽涉到不同的程式設計語言。 誘惑時沒有移植程式碼，重新建構，但如果必須維護兩個平台，以平行方式，則兩個程式碼基底差異會讓未來維護更加困難。

## <a name="cross-platform-mobile-development"></a>跨平台行動開發

行動平台為目標時，常會使用此問題。 目前，有兩個主要的行動平台、 Apple Iphone 和 Ipad 執行 iOS 作業系統和 Android 的手機和平板電腦執行的作業系統系列。 另一個重要的平台是 Microsoft 的通用 Windows 平台 (UWP)，可讓單一的程式，以目標為 Windows 10 和 Windows 10 行動裝置。

想要這三個平台為目標的軟體廠商必須處理不同的使用者介面典範、 三個不同的開發環境、 三個不同的程式設計介面，和 （& s) #x 2014年，或許是大部分滑雪 & #x 2014年; 三個不同的程式語言： 適用於 iPhone 和 iPad、 android、 Java 和 C# windows OBJECTIVE-C。

## <a name="the-c-and-net-solution"></a>C# 和.NET 方案

雖然 OBJECTIVE-C、 Java 和 C# 所有衍生自 C 程式設計語言，所以它們進化以非常不同的路徑。 C# 是一種語言最新和已被成熟中非常有用的方式。 此外，C# 中是密切相關的整個的程式設計基礎結構，以呼叫可提供支援的數學、 偵錯、 反映、 集合、 全球化、 檔案 I/O、 網路、 安全性、 執行緒、 web 服務、 資料處理和 XML 的.NET 和JSON 讀取和寫入。

Xamarin 目前提供工具以原生 Mac、 iOS 和 Android 的應用程式開發介面使用 C# 和.NET 為目標。 這些工具稱為 Xamarin.Mac，Xamarin.iOS、 Xamarin.Android，統稱為 Xamarin 平台。 這些是程式庫和 express 與.NET 的語言，所以這些平台的原生 Api 的繫結。

開發人員可以使用 C# 中撰寫的應用程式，該目標 Mac、 iOS 或 Android 的 Xamarin 平台。 但為目標時多個平台，可以更合理地共用相同的目標平台之間的程式碼。 這牽涉到分成 （通常包含使用者介面） 的平台相依程式碼，以及平台獨立程式碼，通常需要基底.NET framework 中的程式。 此平台獨立程式碼可能是位於可攜式類別程式庫 (PCL) 或共用的專案，通常稱為共用資產專案或 SAP。

## <a name="introducing-xamarinforms"></a>Xamarin.Forms 簡介

當目標為多個行動平台，Xamarin.Forms 可讓更多程式碼共用。 針對 Xamarin.Forms 撰寫的單一程式可以五個不同的平台為目標：

- iOS 的 iPhone、 iPad 與 iPod touch 上執行的程式
- Android 適用於 Android 手機和平板電腦上執行的程式
- 通用 Windows 平台目標 Windows 10 和 Windows 10 行動裝置版
- Windows 8.1 的 Windows 執行階段 API
- Windows Phone 8.1 的 Windows 執行階段 API

目前的 Xamarin.Forms 方案範本不包含在 Windows 8.1 和 Windows Phone 8.1 的平台的專案範本。

Xamarin.Forms 程式大量存在於 PCL 或 SAP。 每個平台所組成的小型應用程式的虛設常式會呼叫 PCL。 Xamarin.Forms 應用程式開發介面的對應為原生控制項，每個平台上，讓每個平台會維護其特性的外觀及操作：

[![平台共用的視覺效果的三個螢幕擷取畫面](images/ch01fg03-small.png "每個平台上的 Xamarin.Forms 控制項")](images/ch01fg03-large.png "Xamarin.Forms 每個平台上的控制項")

從左到右顯示 iPhone、 Android phone 和 Windows 10 行動電話螢幕擷取畫面。 在每個畫面上，此頁面包含 Xamarin.Forms [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/)顯示文字， [ `Button` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/)來起始動作， [ `Switch` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Switch/)的選擇為開啟/關閉的值，和[ `Slider` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Slider/)指定連續範圍內的值。 這些檢視的所有四個屬性的子系[ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/)上[ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)。

也會附加到該網頁是包含數個 Xamarin.Forms 工具列[ `ToolbarItem` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ToolbarItem/)物件。 這些是顯示為 iOS 和 Android 的螢幕頂端和底部的 [Windows 10 行動裝置版] 畫面的圖示。

Xamarin.Forms 也支援 XAML 中，針對數個應用程式平台開發在 Microsoft 的可延伸的應用程式標記語言。 在 XAML 中定義如上所示的程式的所有視覺效果中所示[ **PlatformVisuals** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter01/PlatformVisuals)範例。

Xamarin.Forms 程式可以判斷哪些平台執行，並據以執行不同的程式碼。 更有力，開發人員可以撰寫自訂程式碼用於各種平台，並執行該程式碼從 Xamarin.Forms 方案，平台無關的方式。 開發人員也可以藉由撰寫每個平台的轉譯器中建立其他控制項。

Xamarin.Forms 是針對特定業務應用程式，或建立原型，或進行簡單的概念證明示範很好的解決方案，是較不適合需要向量圖形或複雜的觸控互動的應用程式。

## <a name="your-development-environment"></a>您的開發環境

您要當做目標的平台和功能的電腦上您要使用什麼取決於您的開發環境。

如果您想要目標 iOS，您必須安裝 Xcode 和 Xamarin 平台安裝的 Mac。 也支援 Android 需要安裝 Java 和所需的 Sdk。 您可以再目標 iOS 和 Android 使用 Visual Studio for mac。

安裝 Visual Studio 可讓電腦上您 iOS、 Android 和 Windows 平台為目標。 不過，目標 iOS 從 Visual Studio 仍會需要安裝 Xcode 和 Xamarin 平台安裝的 Mac。

您可以測試電腦的 USB 來連接是在實際裝置上或模擬器上的程式。

## <a name="installation"></a>安裝

之前建立，並建置 Xamarin.Forms 應用程式，您應該嘗試建立與個別建置 iOS 應用程式、 Android 應用程式和 UWP 應用程式，根據您想要的目標和開發環境的平台。

Xamarin 及 Microsoft 的 web sites 包含有關如何執行這項操作：

- [IOS 使用者入門](~/ios/get-started/index.md)
- [Android 使用者入門](~/android/get-started/index.md)
- [Windows 開發人員中心](http://dev.windows.com)

您可以一次建立並執行這些個別的平台專案，您應該不會有問題建立和執行 Xamarin.Forms 應用程式。



## <a name="related-links"></a>相關連結

- [第 1 全文檢索 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch01-Apr2016.pdf)
- [第 1 範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter01)
