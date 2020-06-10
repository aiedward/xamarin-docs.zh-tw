---
title： "第1章的摘要。 如何 Xamarin.Forms 融入？」
描述：「使用下列方式建立 Mobile Apps Xamarin.Forms ：第1章的摘要。 如何 Xamarin.Forms 融入？」
assetid： F3F864FF-EE70-49D0-90D1-388889037625 author： davidbritch ms-chap： dabritch ms. date：07/19/2018 否-loc： [ Xamarin.Forms ，]，請執行下列動作 Xamarin.Essentials
---

# <a name="summary-of-chapter-1-how-does-xamarinforms-fit-in"></a>第1章的摘要。 如何 Xamarin.Forms 融入？

[![下載範例 ](~/media/shared/download.png) 下載範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter01)

> [!NOTE]
> 此頁面上的附注指出 Xamarin.Forms 從書籍中所呈現之材質分歧的區域。

程式設計中最不令人不快的作業之一，就是從一個平臺移植程式碼基底，特別是當該平臺牽涉到不同的程式設計語言時。 移植程式碼以重構它時，有一個誘惑，但如果兩個平臺都必須平行維護，則這兩個程式碼基底之間的差異會使未來的維護變得更棘手。

## <a name="cross-platform-mobile-development"></a>跨平台行動裝置程式開發

以行動平臺為目標時，此問題很常見。 目前有兩個主要的行動平臺： Apple 系列的 Iphone 和 Ipad 執行 iOS 作業系統，以及在各種手機和平板電腦上執行的 Android 作業系統。 另一個重要的平臺是 Microsoft 的通用 Windows 平臺（UWP），可讓單一程式以 Windows 10 為目標。

想要以這些平臺為目標的軟體廠商必須處理不同的使用者介面架構、三種不同的開發環境、三種不同的程式設計介面，而且 &mdash; 可能大部分滑雪 &mdash; 三種不同的程式設計語言：適用于 IPhone 和 IPad 的目標-C、適用于 Android 的 JAVA，以及適用于 Windows 的 c #。

## <a name="the-c-and-net-solution"></a>C # 和 .NET 方案

雖然目標-C、JAVA 和 c # 都是衍生自 C 程式設計語言，但它們已經由非常不同的路徑進化。 C # 是這些語言的最新版本，並以非常有用的方式成熟。 此外，c # 與稱為 .NET 的整個程式設計基礎結構密切相關，它提供數學、偵錯工具、反映、集合、全球化、檔案 i/o、網路功能、安全性、執行緒、web 服務、資料處理，以及 XML 和 JSON 讀取和寫入的支援。

Xamarin 目前提供使用 c # 和 .NET 以原生 Mac、iOS 和 Android Api 為目標的工具。 這些工具稱為「Xamarin」、「Xamarin」和「xamarin」，統稱為 Xamarin 平臺。 這些程式庫和系結可透過 .NET 慣用語來表達這些平臺的原生 Api。

開發人員可以使用 Xamarin 平臺，以 c # 撰寫以 Mac、iOS 或 Android 為目標的應用程式。 但是當以多個平臺為目標時，在目標平臺之間共用一些程式碼會很有意義。 這牽涉到將程式分成平臺相依的程式碼（通常牽涉到使用者介面），以及與平臺無關的程式碼，這通常只需要基底 .NET framework。 這個與平臺無關的程式碼可以位於可移植的類別庫（PCL）或共用的專案，通常稱為共用的資產專案或 SAP。

> [!NOTE]
> 可移植的類別庫已由 .NET Standard 程式庫取代。 本書中的所有範例程式碼都已轉換成使用 .NET standard 程式庫。

## <a name="introducing-xamarinforms"></a>正在推出Xamarin.Forms

以多個行動平臺為目標時， Xamarin.Forms 可讓更多的程式碼共用。 針對撰寫的單一程式 Xamarin.Forms 可將目標設為下列平臺：

- iOS，適用于 iPhone、iPad 和 iPod touch 上執行的程式
- Android，適用于在 Android 手機和平板電腦上執行的程式
- 以 Windows 10 為目標的通用 Windows 平臺

> [!NOTE]
> Xamarin.Forms不再支援 Windows 8.1、Windows Phone 8.1 或 Windows 10 行動裝置版，但 Xamarin.Forms 應用程式會在 Windows 10 桌面上執行。 另外還有適用于[Mac](~/xamarin-forms/platform/other/mac.md)、 [WPF](~/xamarin-forms/platform/other/wpf.md)、 [GTK #](~/xamarin-forms/platform/other/gtk.md)和[Tizen](~/xamarin-forms/platform/other/tizen.md)平臺的預覽支援。

大部分的 Xamarin.Forms 程式都存在於程式庫或 SAP 中。 每個平臺都包含一個小型的應用程式存根，其會呼叫此共用程式碼。

Xamarin.FormsApi 會對應到每個平臺上的原生控制項，讓每個平臺都能維持其特性的外觀與風格：

[![平臺視覺效果共用的三向螢幕擷取畫面](images/ch01fg03-small.png "[!OP.每個平臺上的無 LOC （Xamarin）控制項")](images/ch01fg03-large.png#lightbox "[!OP.每個平臺上的無 LOC （Xamarin）控制項")

從左至右的螢幕擷取畫面會顯示 iPhone 和 Android 手機：

在每個畫面上，頁面包含 Xamarin.Forms [`Label`](xref:Xamarin.Forms.Label) 用於顯示文字的、用於 [`Button`](xref:Xamarin.Forms.Button) 起始動作的、用於 [`Switch`](xref:Xamarin.Forms.Switch) 選擇開啟/關閉值的，以及用 [`Slider`](xref:Xamarin.Forms.Slider) 來指定連續範圍內的值。 這四個視圖都是上的子系 [`StackLayout`](xref:Xamarin.Forms.StackLayout) [`ContentPage`](xref:Xamarin.Forms.ContentPage) 。

同時附加至頁面是由數 Xamarin.Forms 個物件所組成的工具列 [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem) 。 在 iOS 和 Android 畫面頂端和 Windows 10 行動裝置畫面底部，這些圖示會顯示為圖示。

Xamarin.Forms也支援 XAML，這是在 Microsoft 針對數個應用程式平臺開發的 Extensible Application Markup Language。 上述程式的所有視覺效果都是在 XAML 中定義，如[**PlatformVisuals**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter01/PlatformVisuals)範例中所示。

Xamarin.Forms程式可以判斷其執行所在的平臺，並據此執行不同的程式碼。 更強大且的是，開發人員可以撰寫各種平臺的自訂程式碼，並 Xamarin.Forms 以與平臺無關的方式從程式執行該程式碼。 開發人員也可以藉由撰寫每個平臺的轉譯器來建立其他控制項。

雖然 Xamarin.Forms 是適用于企業營運應用程式或原型設計的絕佳解決方案，或是快速的概念證明示範，但較不適合需要向量圖形或複雜觸控互動的應用程式。

## <a name="your-development-environment"></a>您的開發環境

您的開發環境取決於您想要做為目標的平臺，以及您想要使用的機器。

如果您想要以 iOS 為目標，您將需要已安裝 Xcode 和 Xamarin 平臺的 Mac。 同時支援 Android 也需要安裝 JAVA 和必要的 Sdk。 然後，您可以使用 Visual Studio for Mac 以 iOS 和 Android 為目標。

安裝 Visual Studio 可讓您在電腦上以 iOS、Android 和所有 Windows 平臺為目標。 不過，以 Visual Studio 為目標的 iOS 仍然需要安裝具有 Xcode 和 Xamarin 平臺的 Mac。

您可以在透過 USB 連接到電腦的實際裝置上，或在模擬器上測試程式。

## <a name="installation"></a>安裝

建立和建立 Xamarin.Forms 應用程式之前，您應該嘗試分別建立和建立 iOS 應用程式、Android 應用程式和 UWP 應用程式，視您想要作為目標的平臺和您的開發環境而定。

Xamarin 和 Microsoft 網站包含如何執行此動作的資訊：

- [開始使用 iOS](~/ios/get-started/index.md)
- [開始使用 Android](~/android/get-started/index.md)
- [Windows 開發人員中心](https://dev.windows.com)

一旦您可以為這些個別平臺建立及執行專案，您應該不會在建立和執行應用程式時遇到任何問題 Xamarin.Forms 。

## <a name="related-links"></a>相關連結

- [第1章全文檢索（PDF）](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch01-Apr2016.pdf)
- [第1章範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter01)
