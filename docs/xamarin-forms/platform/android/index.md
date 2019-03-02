---
title: Android 平台功能
description: 這篇文章說明如何將 Android 特有的功能新增至 Xamarin.Forms 應用程式。
ms.prod: xamarin
ms.assetid: E24168F3-0138-4814-86EA-B467F6B8A545
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/07/2018
---

# <a name="android-platform-features"></a>Android 平台功能

開發適用於 Android 的 Xamarin.Forms 應用程式需要 Visual Studio。 [需求頁面](~/get-started/requirements.md)包含必要條件的詳細資訊。

## <a name="platform-specifics"></a>平台特性

平台特性可讓您使用的功能只可在特定的平台，而不需要實作自訂轉譯器或影響。

下列平台特有的功能被供 Xamarin.Forms 檢視、 頁面和 Android 上的版面配置：

- 控制疊置順序的視覺項目來判斷繪製順序。 如需詳細資訊，請參閱 < [VisualElement 提高權限，在 Android 上](visualelement-elevation.md)。
- 停用上支援的舊版的色彩模式[ `VisualElement` ](xref:Xamarin.Forms.VisualElement)。 如需詳細資訊，請參閱 < [VisualElement 舊版色彩模式，在 Android 上](legacy-color-mode.md)。

在 Android 上的 Xamarin.Forms 檢視被提供下列平台特有的功能：

- 使用預設填補和 Android 的按鈕的陰影值。 如需詳細資訊，請參閱 < [ 按鈕的邊框距離和 Android 上的陰影](button-padding-shadow.md)。
- 設定輸入的法編輯器選項的螢幕小鍵盤[ `Entry` ](xref:Xamarin.Forms.Entry)。 如需詳細資訊，請參閱 <<c0> [ 在 Android 上的項目輸入法編輯器選項](entry-ime-options.md)。
- 在啟用下拉式陰影`ImageButton`。 如需詳細資訊，請參閱 < [ImageButton 在 Android 上卸除 Shadows](imagebutton-drop-shadow.md)。
- 啟用快速捲動[ `ListView` ](xref:Xamarin.Forms.ListView)如需詳細資訊，請參閱[ListView 快速捲動在 Android 上](listview-fast-scrolling.md)。
- 控制是否[ `WebView` ](xref:Xamarin.Forms.WebView)可以顯示混合的內容。 如需詳細資訊，請參閱 < [WebView 混合內容在 Android 上](webview-mixed-content.md)。

下列平台特有的功能被供在 Android 上的 Xamarin.Forms 頁面：

- 設定導覽列的高度[ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage)。 如需詳細資訊，請參閱 < [NavigationPage 在 Android 上的列高度](navigationpage-bar-height.md)。
- 瀏覽頁面時，停用轉換動畫[ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage)。 如需詳細資訊，請參閱 < [TabbedPage 頁面轉換動畫在 Android 上](tabbedpage-transition-animations.md)。
- 啟用頁面間撥動[ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage)。 如需詳細資訊，請參閱 < [TabbedPage 撥動在 Android 上的頁面](tabbedpage-page-swiping.md)。
- 設定工具列位置和色彩[ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage)。 如需詳細資訊，請參閱 < [TabbedPage 工具列位置和 Android 上的色彩](tabbedpage-toolbar-placement-color.md)。

下列平台特有的功能提供適用於 Xamarin.Forms [ `Application` ](xref:Xamarin.Forms.Application)在 Android 上的類別：

- 設定螢幕小鍵盤的作業模式。 如需詳細資訊，請參閱 <<c0> [ 軟在 Android 上的鍵盤輸入模式](soft-keyboard-input-mode.md)。
- 停用[ `Disappearing` ](xref:Xamarin.Forms.Page.Appearing)並[ `Appearing` ](xref:Xamarin.Forms.Page.Appearing)頁面生命週期事件上暫停和繼續分別使用 AppCompat 的應用程式。 如需詳細資訊，請參閱 <<c0> [ 在 Android 上的網頁生命週期事件](page-lifecycle-events.md)。

## <a name="platform-support"></a>平台支援

一開始，預設 Xamarin.Forms Android 專案會使用舊樣式的 Android 5.0 之前是很常見的控制項轉譯。 使用範本建置應用程式有`FormsApplicationActivity`作為其主要活動的基底類別。

## <a name="material-design-via-appcompat"></a>透過 AppCompat 材料設計

Xamarin.Forms Android 專案現在使用`FormsAppCompatActivity`作為其主要活動的基底類別。 這個類別會使用**AppCompat**實作 Material Design 佈景主題的 Android 所提供的功能。

若要新增至您的 Xamarin.Forms Android 專案的 Material Design 佈景主題，請遵循[AppCompat 的安裝指示支援](appcompat-material-design.md)

以下是**Todo**預設值的範例`FormsApplicationActivity`:

[![](images/before-appcompat-sml.png "待辦事項範例應用程式，而不需要 AppCompat")](images/before-appcompat.png#lightbox "沒有 AppCompat 的待辦事項範例應用程式")

這是相同的程式碼使用專案升級之後`FormsAppCompatActivity`（及新增額外的佈景主題的資訊）：

[![](images/post-appcompat-sml.png "AppCompat 和佈景主題的待辦事項範例應用程式")](images/post-appcompat.png#lightbox "AppCompat 和佈景主題的待辦事項範例應用程式")

> [!NOTE]
> 使用時`FormsAppCompatActivity`，則[基底類別的一些 Android 的自訂轉譯器](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md)會不同。

## <a name="related-links"></a>相關連結

- [新增材料設計支援](appcompat-material-design.md)
