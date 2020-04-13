---
title: 第 1 章摘要。 Xamarin.Forms 如何適應？
description: 使用 Xamarin.表單創建行動應用程式:第 1 章摘要。 Xamarin.Forms 如何適應？
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: F3F864FF-EE70-49D0-90D1-388889037625
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: 6dfa473bdfb4c1dd88ca833dbf5011a0bbdec42a
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "73032889"
---
# <a name="summary-of-chapter-1-how-does-xamarinforms-fit-in"></a>第 1 章摘要。 Xamarin.Forms 如何適應？

[![下載範例](~/media/shared/download.png)下載範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter01)

> [!NOTE]
> 本頁的註釋指示 Xamarin.Forms 與本書中介紹的材料有分歧的領域。

程式設計中最令人不快的工作之一是從一個平臺移植到另一個平台的代碼庫,特別是如果該平臺涉及不同的程式設計語言。 移植代碼來重構代碼時也會有一種誘惑,但如果兩個平台必須並行維護,那麼兩個代碼庫之間的差異將使將來的維護更加困難。

## <a name="cross-platform-mobile-development"></a>跨平台行動裝置程式開發

在定位移動平臺時,此問題很常見。 目前,有兩個主要的移動平臺,蘋果的iPhone和iPad系列運行iOS操作系統,和Android操作系統運行在各種手機和平板電腦上。 另一個重要的平臺是微軟的通用Windows平臺(UWP),它允許單個程序同時針對Windows 10。

希望針對這些平臺的軟體供應商必須處理不同的使用者介面模式、三種不同的開發環境、三種不同的程式設計介面,&mdash;或許還有&mdash;三種不同的程式設計語言:iPhone和iPad的Objective-C、用於Android的Java和適用於Windows的C#。

## <a name="the-c-and-net-solution"></a>C# 與 .NET 解決方案

儘管目標 C、JAVA 和 C# 都源自 C 程式設計語言,但它們是由非常不同的路徑演變而來的。 C# 是這些語言中最新的,並且一直以非常有用的方式成熟。 此外,C# 與稱為 .NET 的整個程式設計基礎結構密切相關,它支援數學、調試、反射、集合、全球化、檔 I/O、網路、安全、線程、Web 服務、數據處理以及 XML 和 JSON 讀取和寫入。

Xamarin 目前提供的工具使用 C# 和 .NET 定位本機 Mac、iOS 和 Android API。 這些工具稱為 Xamarin.Mac、Xamarin.iOS 和 Xamarin.Android,統稱為 Xamarin 平臺。 這些是使用 .NET 習慣用表示這些平臺的本機 API 的庫和綁定。

開發人員可以使用 Xamarin 平臺在 C# 中編寫面向 Mac、iOS 或 Android 的應用程式。 但是,當定位到多個平臺時,在目標平台之間共用一些代碼是很有意義的。 這涉及到將程式分離到與平台相關的代碼(通常涉及使用者介面)和獨立於平臺的代碼,這通常只需要基 .NET 框架。 此獨立於平臺的代碼可以駐留在便攜式類庫 (PCL) 中,也可以駐留在通常稱為共用資產專案或 SAP 的共享專案中。

> [!NOTE]
> 便攜式類庫已被 .NET 標準庫替換。 書中的所有範例代碼已轉換為使用 .NET 標準庫。

## <a name="introducing-xamarinforms"></a>介紹 Xamarin.表單

當定位多個移動平臺時,Xamarin.Forms 允許更多的代碼共用。 為 Xamarin.Forms 撰寫的單個程式可以針對以下平臺:

- iOS 用於在 iPhone、iPad 和 iPod 觸控上執行的程式
- 適用於在 Android 手機和平板電腦上運行的程式的 Android
- Windows 10 的通用 Windows 平台

> [!NOTE]
> Xamarin.Forms 不再支援 Windows 8.1、Windows Phone 8.1 或 Windows 10 移動版,但 Xamarin.Forms 應用程式確實在 Windows 10 桌面上運行。 此外,[還有對](~/xamarin-forms/platform/other/mac.md)Mac、WPF、GTK+ 和[Tizen](~/xamarin-forms/platform/other/tizen.md)平台[WPF](~/xamarin-forms/platform/other/wpf.md)[GTK#](~/xamarin-forms/platform/other/gtk.md)的預覽支援。

Xamarin.Forms 程式的大部分存在於庫或 SAP 中。 每個平臺都由一個小型應用程式存根組成,用於調用此共享代碼。

Xamarin.Forms API 映射到每個平臺上的本機控制項,以便每個平臺保持其特性外觀:

[![平台視覺物件共用的三重螢幕截圖](images/ch01fg03-small.png "Xamarin.每個平臺上的表單控制元件")](images/ch01fg03-large.png#lightbox "Xamarin.每個平臺上的表單控制元件")

從左到右的螢幕截圖顯示了 iPhone 和 Android 手機:

在每個螢幕上,頁面包含用於顯示文本[`Label`](xref:Xamarin.Forms.Label)的 Xamarin. 窗體、用於啟動[`Button`](xref:Xamarin.Forms.Button)操作的窗[`Switch`](xref:Xamarin.Forms.Switch)體、 用於選擇開/關值的表單以及用於[`Slider`](xref:Xamarin.Forms.Slider)在連續範圍內 指定值的表單。 這四種觀點都是[`StackLayout`](xref:Xamarin.Forms.StackLayout)a[`ContentPage`](xref:Xamarin.Forms.ContentPage)上的子 視圖。

附加到頁面的還有 Xamarin.Forms 工具列,該工具列[`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem)由多個 物件組成。 這些圖示在 iOS 和 Android 螢幕的頂部以及 Windows 10 移動螢幕的底部可見。

Xamarin.Forms 還支援 XAML,這是 Microsoft 為多個應用程式平台開發的可擴展應用程式標記語言。 如[**平台視覺示例**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter01/PlatformVisuals)所示,上述程式的所有視覺物件均以 XAML 形式定義。

Xamarin.Forms 程式可以確定它在什麼平台上運行,並相應地執行不同的代碼。 更有力地,開發人員可以為各種平台編寫自定義代碼,並基於獨立於平臺的方式從 Xamarin.Forms 程式運行該代碼。 開發人員還可以通過為每個平台編寫呈現器來創建其他控制項。

雖然 Xamarin.Forms 是業務線應用程式、原型設計或快速概念驗證演示的良好解決方案,但對於需要向量圖形或複雜觸摸交互的應用程式而言,它不太理想。

## <a name="your-development-environment"></a>您的開發環境

您的開發環境取決於要定位哪些平臺以及要使用的計算機。

如果要定位 iOS,則需要安裝帶有 Xcode 和 Xamarin 平臺的 Mac。 支援 Android 還需要安裝 Java 和所需的 SDK。 然後,您可以使用 Mac 的可視化工作室定位 iOS 和 Android。

安裝可視化工作室允許在 PC 上定位 iOS、Android 和所有 Windows 平臺。 但是,從 Visual Studio 定位 iOS 仍然需要安裝帶有 Xcode 和 Xamarin 平臺的 Mac。

您可以在透過 USB 連接到電腦的實際裝置上或模擬器上測試程式。

## <a name="installation"></a>安裝

在創建和建構 Xamarin.Forms 應用程式之前,應嘗試根據要定位的平臺和開發環境分別建立和建構 iOS 應用程式、Android 應用程式和 UWP 應用程式。

Xamarin 和 Microsoft 網站包含有關如何執行此操作的資訊:

- [開始使用 iOS](~/ios/get-started/index.md)
- [開始使用 Android](~/android/get-started/index.md)
- [Windows 開發人員中心](https://dev.windows.com)

為這些單獨的平台創建和運行專案後,創建和運行 Xamarin.Forms 應用程式時,應該沒有問題。

## <a name="related-links"></a>相關連結

- [第一章 全文(PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch01-Apr2016.pdf)
- [第 1 章範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter01)
