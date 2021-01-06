---
title: 從右至左當地語系化
description: 從右至左的當地語系化可將由右至左流動方向的支援新增至 Xamarin.Forms 應用程式。
ms.prod: xamarin
ms.assetid: 90E0CB16-C42A-4CC8-A70E-0C2CFB64A429
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 11/05/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 5d06b0467a5029fec6d0c92a683114b9e0d04685
ms.sourcegitcommit: 044e8d7e2e53f366942afe5084316198925f4b03
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/06/2021
ms.locfileid: "97940183"
---
# <a name="right-to-left-localization"></a>由右至左當地語系化

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/todolocalizedrtl)

_從右至左的當地語系化可將由右至左流動方向的支援新增至 Xamarin.Forms 應用程式。_

> [!NOTE]
> 從右至左當地語系化需要 iOS 9 或更新版本，以及 Android 的 API 17 或更新版本。

文字方向即為眼睛瀏覽頁面 UI 項目的方向。 某些語言 (例如阿拉伯文和希伯來文) 需要將 UI 項目配置為從右至左的文字方向。 這可以藉由設定屬性來達成 [`VisualElement.FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) 。 這個屬性會取得或設定 UI 專案在控制其版面配置的任何父項目內的流動方向，而且應該設定為其中一個 [`FlowDirection`](xref:Xamarin.Forms.FlowDirection) 列舉值：

- [`LeftToRight`](xref:Xamarin.Forms.FlowDirection.LeftToRight)
- [`RightToLeft`](xref:Xamarin.Forms.FlowDirection.RightToLeft)
- [`MatchParent`](xref:Xamarin.Forms.FlowDirection.MatchParent)

將 [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) 屬性上的屬性設定為，通常會將靠右 [`RightToLeft`](xref:Xamarin.Forms.FlowDirection.RightToLeft) 對齊、由右至左的讀取順序，以及要從右至左流動的控制項版面配置：

[![以由右至左流動方向的阿拉伯文 TodoItemPage](rtl-images/TodoItemPage-Arabic.png "以由右至左流動方向的阿拉伯文 TodoItemPage")](rtl-images/TodoItemPage-Arabic-Large.png#lightbox "以由右至左流動方向的阿拉伯文 TodoItemPage")

> [!TIP]
> 您應該只 [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) 在初始配置上設定屬性。 在執行階段變更此值時，會導致佔用大量資源的配置程序，而影響效能。

沒有父系之專案的預設 [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) 屬性值為 [`LeftToRight`](xref:Xamarin.Forms.FlowDirection.LeftToRight) ，而父元素的預設值為 `FlowDirection` [`MatchParent`](xref:Xamarin.Forms.FlowDirection.MatchParent) 。 因此，項目會從視覺化樹狀結構中的父代繼承 `FlowDirection` 屬性值，且任何項目都可以覆寫它從其父代所取得的值。

> [!TIP]
> 針對由右至左的語言當地語系化應用程式時，請 [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) 在頁面或根配置上設定屬性。 這可讓頁面上或根配置內含的所有項目適當地回應文字方向。

## <a name="respecting-device-flow-direction"></a>遵守裝置的文字方向

開發人員應該根據所選語言和地區，明確選擇要遵守的裝置文字方向；系統並不會自動選擇。 將 [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) 頁面上的屬性或根配置設定為值，即可達成此目的 `static` [`Device.FlowDirection`](xref:Xamarin.Forms.Device.FlowDirection) ：

```xaml
<ContentPage ... FlowDirection="{x:Static Device.FlowDirection}"> />
```

```csharp
this.FlowDirection = Device.FlowDirection;
```

頁面的所有子專案或根配置預設會繼承 [`Device.FlowDirection`](xref:Xamarin.Forms.Device.FlowDirection) 值。

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

![資訊： plist 支援的語言](rtl-images/ios-locales.png "資訊： plist 支援的語言")

如需詳細資訊，請參閱[在 iOS 中的當地語系化基本概念](../../../ios/app-fundamentals/localization/index.md#localization-basics-in-ios)。

您可以將 **Info.plist** 中所指定的裝置/模擬器語言和地區變更為由右至左地區設定，以測試由右至左當地語系化。

> [!WARNING]
> 請注意，當您將語言和區域變更為 iOS 上的由右至左地區設定時， [`DatePicker`](xref:Xamarin.Forms.DatePicker) 如果您未包含地區設定所需的資源，任何視圖都會擲回例外狀況。 例如，當測試具有 `DatePicker` 的阿拉伯文應用程式時，請務必選取 [iOS 組建] 窗格 [國際化] 區段中的 [中東]。

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

- [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) 按鈕位置、工具列專案位置和轉換動畫是由裝置地區設定控制，而不是由 [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) 屬性所控制。
- [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) 滑動方向不會翻轉。
- [`Image`](xref:Xamarin.Forms.Image) 視覺內容不會翻轉。
- [`WebView`](xref:Xamarin.Forms.WebView) 內容不遵守 [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) 屬性。
- 必須新增 `TextDirection` 屬性，以控制文字的對齊方式。

### <a name="ios"></a>iOS

- [`Stepper`](xref:Xamarin.Forms.Stepper) 方向由裝置的地區設定控制，而不是由屬性所控制 [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) 。
- [`EntryCell`](xref:Xamarin.Forms.EntryCell) 文字對齊由裝置的地區設定控制，而不是由屬性所控制 [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) 。
- [`ContextActions`](xref:Xamarin.Forms.Cell.ContextActions) 不反轉筆勢和對齊方式。

### <a name="android"></a>Android

- [`SearchBar`](xref:Xamarin.Forms.SearchBar) 方向由裝置的地區設定控制，而不是由屬性所控制 [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) 。
- [`ContextActions`](xref:Xamarin.Forms.Cell.ContextActions) 放置是由裝置的地區設定控制，而不是由屬性所控制 [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) 。

### <a name="uwp"></a>UWP

- [`Editor`](xref:Xamarin.Forms.Editor) 文字對齊由裝置的地區設定控制，而不是由屬性所控制 [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) 。
- [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) 子系不會繼承屬性 [`FlyoutPage`](xref:Xamarin.Forms.FlyoutPage) 。
- [`ContextActions`](xref:Xamarin.Forms.Cell.ContextActions) 文字對齊由裝置的地區設定控制，而不是由屬性所控制 [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) 。

## <a name="force-right-to-left-layout"></a>強制由右至左的版面配置

無論裝置設定為何，都可以藉由修改個別的平臺專案，來強制使用由右至左配置的 Android 應用程式。

### <a name="ios"></a>iOS

您可以藉由修改 **AppDelegate** 類別，強制將 iOS 應用程式一律使用由右至左的版面配置，如下所示：

1. 將 `IntPtr_objc_msgSend` 函數宣告為您類別中的第一行 `AppDelegate` ：

   ```csharp
   [System.Runtime.InteropServices.DllImport(ObjCRuntime.Constants.ObjectiveCLibrary, EntryPoint = "objc_msgSend")]
   internal extern static IntPtr IntPtr_objc_msgSend(IntPtr receiver, IntPtr selector, UISemanticContentAttribute arg1);
   ```

1. 從方法中呼叫函式 `IntPtr_objc_msgSend` `FinishedLaunching` ，然後從方法傳回 `FinshedLaunching` ：

   ```csharp
   bool result = base.FinishedLaunching(app, options);

   ObjCRuntime.Selector selector = new ObjCRuntime.Selector("setSemanticContentAttribute:");
   IntPtr_objc_msgSend(UIView.Appearance.Handle, selector.Handle, UISemanticContentAttribute.ForceRightToLeft);

   return result;
   ```

這種方法適用于一律需要由右至左配置的應用程式，而且會移除設定屬性的需求 [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) 。

如需此方法的詳細資訊 `IntrPtr_objc_msgSend` ，請參閱 [Xamarin 中的目標 C 選取器](~/ios/internals/objective-c-selectors.md)。

### <a name="android"></a>Android

Xamarin. Android 應用程式可以透過修改 **>mainactivity** 類別以包含下列程式碼，強制一律使用由右至左的版面配置：

```csharp
Window.DecorView.LayoutDirection = LayoutDirection.Rtl;
```

> [!NOTE]
> 這種方法需要將應用程式設定為支援由右至左的版面配置。 如需詳細資訊，請參閱 [Android platform 安裝程式](#android)。

這種方法適用于一律需要由右至左配置的應用程式，而且不需要設定 [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) 大部分控制項的屬性。 不過，某些控制項（例如 [`CollectionView`](xref:Xamarin.Forms.CollectionView) ）不 `LayoutDirection` 會遵守屬性，而且仍然需要 `FlowDirection` 設定屬性。

## <a name="right-to-left-language-support-with-xamarinuniversity"></a>Xamarin.University 的由右至左語言支援

> [!VIDEO https://youtube.com/embed/f2lQ5yw3iiU]

**Xamarin.Forms 3.0 由右至左支援影片**

## <a name="related-links"></a>相關連結

- [TodoLocalizedRTL 範例應用程式](/samples/xamarin/xamarin-forms-samples/todolocalizedrtl)
