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
ms.openlocfilehash: a6eb3167fd0880984a74245c4653642ea3979354
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "72678834"
---
# <a name="right-to-left-localization"></a>由右至左當地語系化

[![下載範例](~/media/shared/download.png)下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/todolocalizedrtl)

_從右至左的當地語系化，會將由右至左文字方向的支援新增至 Xamarin.Forms 應用程式。_

> [!NOTE]
> 從右至左當地語系化需要 iOS 9 或更新版本，以及 Android 的 API 17 或更新版本。

文字方向即為眼睛瀏覽頁面 UI 項目的方向。 某些語言 (例如阿拉伯文和希伯來文) 需要將 UI 項目配置為從右至左的文字方向。 這可以通過[`VisualElement.FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection)設置 屬性來實現。 此屬性取得或設定 UI 元素在控制其佈局的任何父元素中流動的方向,並且應[`FlowDirection`](xref:Xamarin.Forms.FlowDirection)設定為 枚舉值之一:

- [`LeftToRight`](xref:Xamarin.Forms.FlowDirection.LeftToRight)
- [`RightToLeft`](xref:Xamarin.Forms.FlowDirection.RightToLeft)
- [`MatchParent`](xref:Xamarin.Forms.FlowDirection.MatchParent)

將[`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection)屬性設定[`RightToLeft`](xref:Xamarin.Forms.FlowDirection.RightToLeft)為 元素通常將對齊方式設置為右側,讀取順序設定為從右到左,以及控制項的佈局從右向左流動:

[![阿拉伯文中的 TodoItemPage,從右至左流動方向](rtl-images/TodoItemPage-Arabic.png "阿拉伯文中的 TodoItemPage,從右至左流動方向")](rtl-images/TodoItemPage-Arabic-Large.png#lightbox "阿拉伯文中的 TodoItemPage,從右至左流動方向")

> [!TIP]
> 應僅在初始佈局上[`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection)設置屬性。 在執行階段變更此值時，會導致佔用大量資源的配置程序，而影響效能。

沒有父[`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection)元素的元素的預設屬性值[`LeftToRight`](xref:Xamarin.Forms.FlowDirection.LeftToRight)為 ,而具有`FlowDirection`父元素的元素的預設[`MatchParent`](xref:Xamarin.Forms.FlowDirection.MatchParent)值為 。 因此，項目會從視覺化樹狀結構中的父代繼承 `FlowDirection` 屬性值，且任何項目都可以覆寫它從其父代所取得的值。

> [!TIP]
> 為從右向左語言本地化應用時,在[`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection)頁面或根佈局上設置該屬性。 這可讓頁面上或根配置內含的所有項目適當地回應文字方向。

## <a name="respecting-device-flow-direction"></a>遵守裝置的文字方向

開發人員應該根據所選語言和地區，明確選擇要遵守的裝置文字方向；系統並不會自動選擇。 可以通過[`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection)將 頁面上的屬性或根佈局`static`[`Device.FlowDirection`](xref:Xamarin.Forms.Device.FlowDirection)設定為 值來實現:

```xaml
<ContentPage ... FlowDirection="{x:Static Device.FlowDirection}"> />
```

```csharp
this.FlowDirection = Device.FlowDirection;
```

默認情況下,頁面或根佈局的所有子元素將繼承該[`Device.FlowDirection`](xref:Xamarin.Forms.Device.FlowDirection)值。

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
> 請注意,在 iOS 上將語言和區域更改為從右到左區域設置時,如果不[`DatePicker`](xref:Xamarin.Forms.DatePicker)包含區域設置所需的資源,則任何視圖都會引發異常。 例如，當測試具有 `DatePicker` 的阿拉伯文應用程式時，請務必選取 [iOS 組建]**** 窗格 [國際化]**** 區段中的 [中東]****。

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

您可以啟用 [設定] > [開發人員選項]**** 中的 [Force RTL layout direction] \(強制 RTL 配置方向\)****，將裝置/模擬器語言和地區變更為使用由右至左語言，以測試由右至左當地語系化。

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

- [`NavigationPage`](xref:Xamarin.Forms.NavigationPage)按鈕位置、工具列專案位置和過渡動畫由裝置區域設定控制,而不是屬性[`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection)。
- [`CarouselPage`](xref:Xamarin.Forms.CarouselPage)滑動方向不會翻轉。
- [`Image`](xref:Xamarin.Forms.Image)視覺內容不會翻轉。
- [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert(System.String,System.String,System.String))和[`DisplayActionSheet`](xref:Xamarin.Forms.Page.DisplayActionSheet(System.String,System.String,System.String,System.String[]))方向由設備區域設置控制,[`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection)而不是屬性。
- [`WebView`](xref:Xamarin.Forms.WebView)內容不尊重[`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection)屬性。
- 必須新增 `TextDirection` 屬性，以控制文字的對齊方式。

### <a name="ios"></a>iOS

- [`Stepper`](xref:Xamarin.Forms.Stepper)方向由設備區域設置控制,[`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection)而不是屬性。
- [`EntryCell`](xref:Xamarin.Forms.EntryCell)文字對齊由設備區域設置控制,[`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection)而不是屬性。
- [`ContextActions`](xref:Xamarin.Forms.Cell.ContextActions)手勢和對齊方式不會反轉。

### <a name="android"></a>Android

- [`SearchBar`](xref:Xamarin.Forms.SearchBar)方向由設備區域設置控制,[`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection)而不是屬性。
- [`ContextActions`](xref:Xamarin.Forms.Cell.ContextActions)放置由設備區域設置控制,[`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection)而不是屬性控制。

### <a name="uwp"></a>UWP

- [`Editor`](xref:Xamarin.Forms.Editor)文字對齊由設備區域設置控制,[`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection)而不是屬性。
- [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection)財產不由[`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage)子級繼承。
- [`ContextActions`](xref:Xamarin.Forms.Cell.ContextActions)文字對齊由設備區域設置控制,[`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection)而不是屬性。

## <a name="force-right-to-left-layout"></a>強制從右向左佈局

Xamarin.iOS 和 Xamarin.Android 應用程式可以強制通過修改相應的平台項目,始終使用從右到左的佈局,而不管設備設置如何。

### <a name="ios"></a>iOS

Xamarin.iOS 應用程式可以強制始終使用從右到左的佈局,修改**AppDelegate**類,如下所示:

1. 將`IntPtr_objc_msgSend`函數宣告`AppDelegate`為 類別的第一行:

   ```csharp
   [System.Runtime.InteropServices.DllImport(ObjCRuntime.Constants.ObjectiveCLibrary, EntryPoint = "objc_msgSend")]
   internal extern static IntPtr IntPtr_objc_msgSend(IntPtr receiver, IntPtr selector, UISemanticContentAttribute arg1);
   ```

1. 從`IntPtr_objc_msgSend``FinishedLaunching`方法呼叫 函數,`FinshedLaunching`然後從 方法返回:

   ```csharp
   bool result = base.FinishedLaunching(app, options);

   ObjCRuntime.Selector selector = new ObjCRuntime.Selector("setSemanticContentAttribute:");
   IntPtr_objc_msgSend(UIView.Appearance.Handle, selector.Handle, UISemanticContentAttribute.ForceRightToLeft);

   return result;
   ```

此方法對於始終需要從右向左佈局並刪除設置[`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection)屬性的要求的應用程式很有用。

有關該方法`IntrPtr_objc_msgSend`的詳細資訊,請參閱[Xamarin.iOS 中的目標 C 選擇器](~/ios/internals/objective-c-selectors.md)。

### <a name="android"></a>Android

Xamarin.Android 應用程式可以通過修改**MainActivity**類來包括以下行,從而強制始終使用從右到左的佈局:

```csharp
Window.DecorView.LayoutDirection = LayoutDirection.Rtl;
```

此方法對於始終需要從右向左佈局並刪除設置[`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection)屬性的要求的應用程式很有用。

## <a name="right-to-left-language-support-with-xamarinuniversity"></a>Xamarin.University 的由右至左語言支援

> [!VIDEO https://youtube.com/embed/f2lQ5yw3iiU]

**Xamarin.Forms 3.0 由右至左支援影片**

## <a name="related-links"></a>相關連結

- [TodoLocalizedRTL 範例應用程式](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/todolocalizedrtl)
