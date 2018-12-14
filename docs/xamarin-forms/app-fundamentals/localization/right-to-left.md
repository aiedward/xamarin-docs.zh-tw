---
title: 從右至左當地語系化
description: 從右至左的當地語系化，會將由右至左文字方向的支援新增至 Xamarin.Forms 應用程式。
ms.prod: xamarin
ms.assetid: 90E0CB16-C42A-4CC8-A70E-0C2CFB64A429
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 05/07/2018
ms.openlocfilehash: 67b0d90290b18c7a5b55c5e3496b54970a8cfc38
ms.sourcegitcommit: 6be6374664cd96a7d924c2e0c37aeec4adf8be13
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/13/2018
ms.locfileid: "51617601"
---
# <a name="right-to-left-localization"></a>由右至左當地語系化

_從右至左的當地語系化，會將由右至左文字方向的支援新增至 Xamarin.Forms 應用程式。_

> [!NOTE]
> 從右至左當地語系化需要 iOS 9 或更新版本，以及 Android 的 API 17 或更新版本。

文字方向即為眼睛瀏覽頁面 UI 項目的方向。 某些語言 (例如阿拉伯文和希伯來文) 需要將 UI 項目配置為從右至左的文字方向。 這可以透過設定 [`VisualElement.FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) 屬性來完成。 此屬性會取得或設定 UI 項目在任何父項目中的文字方向，以控制其配置，且應該設為其中一個 [`FlowDirection`](xref:Xamarin.Forms.FlowDirection) 列舉值：

- [`LeftToRight`](xref:Xamarin.Forms.FlowDirection.LeftToRight)
- [`RightToLeft`](xref:Xamarin.Forms.FlowDirection.RightToLeft)
- [`MatchParent`](xref:Xamarin.Forms.FlowDirection.MatchParent)

將項目上的 [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) 屬性設為 [`RightToLeft`](xref:Xamarin.Forms.FlowDirection.RightToLeft) 時，一般會設定為靠右對齊、由右至左的讀取順序，以及從右至左文字方向的控制項配置：

[![從右至左文字方向的阿拉伯文 TodoItemPage](rtl-images/TodoItemPage-Arabic.png "從右至左文字方向的阿拉伯文 TodoItemPage")](rtl-images/TodoItemPage-Arabic-Large.png#lightbox "從右至左文字方向的阿拉伯文 TodoItemPage")

> [!TIP]
> 您應該只在初始配置上設定 [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) 屬性。 在執行階段變更此值時，會導致佔用大量資源的配置程序，而影響效能。

不具父代的項目其預設 [`FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) 屬性值為 [`LeftToRight`](xref:Xamarin.Forms.FlowDirection.LeftToRight)，而具父代的項目其預設 `FlowDirection` 為 [`MatchParent`](xref:Xamarin.Forms.FlowDirection.MatchParent)。 因此，項目會從視覺化樹狀結構中的父代繼承 `FlowDirection` 屬性值，且任何項目都可以覆寫它從其父代所取得的值。

> [!TIP]
> 針對從右至左的語言當地語系化應用程式時，請在頁面或根配置上設定 [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) 屬性。 這可讓頁面上或根配置內含的所有項目適當地回應文字方向。

## <a name="respecting-device-flow-direction"></a>遵守裝置的文字方向

開發人員應該根據所選語言和地區，明確選擇要遵守的裝置文字方向；系統並不會自動選擇。 您可以在頁面上或根配置上將 [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) 屬性設為 `static` [`Device.FlowDirection`](xref:Xamarin.Forms.Device.FlowDirection) 值，來完成上述作業：

```xaml
<ContentPage ... FlowDirection="{x:Static Device.FlowDirection}"> />
```

```csharp
this.FlowDirection = Device.FlowDirection;
```

如此一來，頁面或根配置的所有子項目都預設會繼承 [`Device.FlowDirection`](xref:Xamarin.Forms.Device.FlowDirection) 值。

## <a name="platform-setup"></a>平台設定

特定平台設定需要啟用由右至左的地區設定。

### <a name="ios"></a>iOS

您應將必要的由右至左地區設定作為支援語言新增至 **Info.plist** 中 `CFBundleLocalizations` 索引鍵的陣列項目。 下列範例顯示阿拉伯文已新增到 `CFBundleLocalizations` 索引鍵的陣列：

```xml
<key>CFBundleLocalizations</key>
<array>
    <string>en</string>
    <string>ar</string>
</array>
```

![Info.plist 支援的語言](rtl-images/ios-locales.png "Info.plist 支援的語言")

如需詳細資訊，請參閱[在 iOS 中的當地語系化基本概念](https://docs.microsoft.com/xamarin/ios/app-fundamentals/localization/#localization-basics-in-ios)。

您可以將 **Info.plist** 中所指定的裝置/模擬器語言和地區變更為由右至左地區設定，以測試由右至左當地語系化。

> [!WARNING]
> 請注意，在 iOS 上將語言和地區變更為由右至左地區設定時，如果您未包含地區設定所需的資源，則任何 [`DatePicker`](xref:Xamarin.Forms.DatePicker) 檢視都會擲回例外狀況。 例如，當測試具有 `DatePicker` 的阿拉伯文應用程式時，請務必選取 [iOS 組建] 窗格 [國際化] 區段中的 [中東]。

### <a name="android"></a>Android

您應更新應用程式的 **AndroidManifest.xml** 檔案，以讓 `<uses-sdk>` 節點將 `android:minSdkVersion` 屬性設為 17，且 `<application>` 節點將 `android:supportsRtl` 屬性設為 `true`：

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest ... >
    <uses-sdk android:minSdkVersion="17" ... />
    <application ... android:supportsRtl="true">
    </application>
</manifest>
```

您可以啟用 [設定] > [開發人員選項] 中的 [Force RTL layout direction] \(強制 RTL 配置方向\)，將裝置/模擬器語言和地區變更為使用由右至左語言，以測試由右至左當地語系化。

### <a name="universal-windows-platform-uwp"></a>通用 Windows 平台 (UWP)

您應在 **Package.appxmanifest** 檔案的 `<Resources>` 節點中指定所需的語言資源。 下列範例顯示阿拉伯文已新增到 `<Resources>` 節點：

```xml
<Resources>
    <Resource Language="x-generate"/>
    <Resource Language="en" />
    <Resource Language="ar" />
</Resources>
```

此外，UWP 要求必須在 .NET Standard 程式庫中明確定義應用程式的預設文化特性。 您可以將 `AssemblyInfo.cs` 或其他類別中的 `NeutralResourcesLanguage` 屬性設為預設文化特性，以完成上述作業：

```csharp
using System.Resources;

[assembly: NeutralResourcesLanguage("en")]
```

您可以將裝置語言和地區變更為適當的由右至左地區設定，以測試由右至左當地語系化。

## <a name="limitations"></a>限制

Xamarin.Forms 由右至左當地語系化目前有一些限制：

- [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) 按鈕位置、工具列項目位置及轉換動畫均由裝置的地區設定控制，而非由 [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) 屬性控制。
- [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) 撥動方向無法翻轉。
- [`Image`](xref:Xamarin.Forms.Image) 視覺內容無法翻轉。
- [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert(System.String,System.String,System.String)) 和 [`DisplayActionSheet`](xref:Xamarin.Forms.Page.DisplayActionSheet(System.String,System.String,System.String,System.String[])) 方向由裝置的地區設定控制，而非由 [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) 屬性控制。
- [`WebView`](xref:Xamarin.Forms.WebView) 內容不會遵守 [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) 屬性。
- 必須新增 `TextDirection` 屬性，以控制文字的對齊方式。

### <a name="ios"></a>iOS

- [`Stepper`](xref:Xamarin.Forms.Stepper) 方向由裝置的地區設定控制，而非由 [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) 屬性控制。
- [`EntryCell`](xref:Xamarin.Forms.EntryCell) 文字對齊方式由裝置的地區設定控制，而非由 [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) 屬性控制。
- [`ContextActions`](xref:Xamarin.Forms.Cell.ContextActions) 手勢和對齊方式無法反轉。

### <a name="android"></a>Android

- [`SearchBar`](xref:Xamarin.Forms.SearchBar) 方向由裝置的地區設定控制，而非由 [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) 屬性控制。
- [`ContextActions`](xref:Xamarin.Forms.Cell.ContextActions) 位置由裝置的地區設定控制，而非由 [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) 屬性控制。

### <a name="uwp"></a>UWP

- [`Editor`](xref:Xamarin.Forms.Editor) 文字對齊方式由裝置的地區設定控制，而非由 [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) 屬性控制。
- [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) 子系不會繼承 [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) 屬性。
- [`ContextActions`](xref:Xamarin.Forms.Cell.ContextActions) 文字對齊方式由裝置的地區設定控制，而非由 [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) 屬性控制。

## <a name="right-to-left-language-support-with-xamarinuniversity"></a>Xamarin.University 的由右至左語言支援

> [!VIDEO https://youtube.com/embed/f2lQ5yw3iiU]

**Xamarin.Forms 3.0 由右至左支援由 [Xamarin University](https://university.xamarin.com/) 提供**

## <a name="related-links"></a>相關連結

- [TodoLocalizedRTL 範例應用程式](https://developer.xamarin.com/samples/xamarin-forms/TodoLocalizedRTL/)
