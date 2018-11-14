---
title: 從右至左的當地語系化
description: 從右至左當地語系化會新增到 Xamarin.Forms 應用程式的支援由右至左的流向。
ms.prod: xamarin
ms.assetid: 90E0CB16-C42A-4CC8-A70E-0C2CFB64A429
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 05/07/2018
ms.openlocfilehash: 67b0d90290b18c7a5b55c5e3496b54970a8cfc38
ms.sourcegitcommit: 6be6374664cd96a7d924c2e0c37aeec4adf8be13
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/13/2018
ms.locfileid: "51617601"
---
# <a name="right-to-left-localization"></a>從右至左的當地語系化

_從右至左當地語系化會新增到 Xamarin.Forms 應用程式的支援由右至左的流向。_

> [!NOTE]
> 從右至左當地語系化需要 iOS 9 或更高版本，以及 API 17 或更高版本，在 Android 上使用。

資料流程方向是所在頁面的 UI 項目會掃描出的方向。 某些語言中的，如阿拉伯文和希伯來文，需要的 UI 項目會配置在由右至左的流向。 這可以藉由設定來達成[ `VisualElement.FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection)屬性。 此屬性會取得或設定，控制其配置，並應該設為其中任何父項目中的 UI 項目流程中的方向[ `FlowDirection` ](xref:Xamarin.Forms.FlowDirection)列舉值：

- [`LeftToRight`](xref:Xamarin.Forms.FlowDirection.LeftToRight)
- [`RightToLeft`](xref:Xamarin.Forms.FlowDirection.RightToLeft)
- [`MatchParent`](xref:Xamarin.Forms.FlowDirection.MatchParent)

設定[ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection)屬性設[ `RightToLeft` ](xref:Xamarin.Forms.FlowDirection.RightToLeft)項目上一般設定對齊右、 從右至左的讀取順序和控制項的版面配置從從右至左：

[![以阿拉伯數字與由右至左的流向 TodoItemPage](rtl-images/TodoItemPage-Arabic.png "TodoItemPage 以阿拉伯數字與由右至左的流向")](rtl-images/TodoItemPage-Arabic-Large.png#lightbox "TodoItemPage 以阿拉伯數字與由右至左的流向")

> [!TIP]
> 您應該只設定[ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection)初始版面配置上的屬性。 變更此值在執行階段會導致耗費資源的版面配置程序，將會影響效能。

預設值[ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection)沒有父元素的屬性值是[ `LeftToRight` ](xref:Xamarin.Forms.FlowDirection.LeftToRight)，而預設`FlowDirection`具有父代的項目是[ `MatchParent`](xref:Xamarin.Forms.FlowDirection.MatchParent). 因此，項目繼承`FlowDirection`從視覺化樹狀結構中，且任何項目在其父代的屬性值可以覆寫它從其父代所取得的值。

> [!TIP]
> 當地語系化時由右至左語言的應用程式，設定[ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection)頁面或根的版面配置上的屬性。 這會導致所有頁面上或根版面配置，適當地流向回應中包含的項目。

## <a name="respecting-device-flow-direction"></a>關於裝置流程方向

關於裝置的文字方向會根據選取的語言和地區是明確的開發人員的選擇，並不會自動。 它可藉由設定[ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection)屬性頁面上或根配置到`static` [ `Device.FlowDirection` ](xref:Xamarin.Forms.Device.FlowDirection)值：

```xaml
<ContentPage ... FlowDirection="{x:Static Device.FlowDirection}"> />
```

```csharp
this.FlowDirection = Device.FlowDirection;
```

所有子項目的頁面或根版面配置，將預設然後都繼承[ `Device.FlowDirection` ](xref:Xamarin.Forms.Device.FlowDirection)值。

## <a name="platform-setup"></a>平台安裝程式

特定平台安裝程式，才能啟用從右至左的地區設定。

### <a name="ios"></a>iOS

需要由右至左的地區設定應做為支援的語言新增至陣列項目，如`CFBundleLocalizations`中的索引鍵**Info.plist**。 下列範例示範具有已新增到陣列中的阿拉伯文`CFBundleLocalizations`機碼：

```xml
<key>CFBundleLocalizations</key>
<array>
    <string>en</string>
    <string>ar</string>
</array>
```

![支援的 Info.plist 語言](rtl-images/ios-locales.png "Info.plist 支援的語言")

如需詳細資訊，請參閱 <<c0> [ 當地語系化基本概念，在 iOS 中](https://docs.microsoft.com/xamarin/ios/app-fundamentals/localization/#localization-basics-in-ios)。

可以變更由右至左的地區設定中所指定的語言和地區裝置/模擬器上的測試從右至左當地語系化**Info.plist**。

> [!WARNING]
> 請注意，當任何由右至左地區設定，在 iOS 上，變更語言和地區[ `DatePicker` ](xref:Xamarin.Forms.DatePicker)檢視將會擲回例外狀況，如果您不包含地區設定所需的資源。 例如，當測試應用程式，以具有阿拉伯數字`DatePicker`，確定**mideast**中選取**國際化**一節**iOS 組建**窗格。

### <a name="android"></a>Android

應用程式的**AndroidManifest.xml**應該更新檔案以便`<uses-sdk>`節點集`android:minSdkVersion`17，此屬性並`<application>`節點集`android:supportsRtl`屬性設定為`true`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest ... >
    <uses-sdk android:minSdkVersion="17" ... />
    <application ... android:supportsRtl="true">
    </application>
</manifest>
```

藉由變更裝置/模擬器使用由右至左的語言，或啟用可以測試從右至左當地語系化**強制從右至左的版面配置方向**中**設定 > 開發人員選項**。

### <a name="universal-windows-platform-uwp"></a>通用 Windows 平台 (UWP)

應指定所需的語言資源`<Resources>`的節點**Package.appxmanifest**檔案。 下列範例顯示已加入的阿拉伯文`<Resources>`節點：

```xml
<Resources>
    <Resource Language="x-generate"/>
    <Resource Language="en" />
    <Resource Language="ar" />
</Resources>
```

此外，UWP 需要應用程式的預設文化特性明確定義.NET Standard 程式庫中。 這可藉由設定`NeutralResourcesLanguage`屬性中`AssemblyInfo.cs`，或在另一個類別中，為預設文化特性：

```csharp
using System.Resources;

[assembly: NeutralResourcesLanguage("en")]
```

從右至左當地語系化再經過變更的語言和地區，在裝置上的適當由右至左的地區設定。

## <a name="limitations"></a>限制

Xamarin.Forms 由右至左當地語系化目前有一些限制：

- [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) 按鈕位置 工具列項目位置，並且過場動畫受到裝置的地區設定，而非[ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection)屬性。
- [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) 撥動方向不會翻轉。
- [`Image`](xref:Xamarin.Forms.Image) 將視覺內容不會翻轉。
- [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert(System.String,System.String,System.String)) 並[ `DisplayActionSheet` ](xref:Xamarin.Forms.Page.DisplayActionSheet(System.String,System.String,System.String,System.String[]))方向由裝置的地區設定，而非[ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection)屬性。
- [`WebView`](xref:Xamarin.Forms.WebView) 內容不會遵守[ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection)屬性。
- A`TextDirection`屬性必須要加入，以控制文字的對齊方式。

### <a name="ios"></a>iOS

- [`Stepper`](xref:Xamarin.Forms.Stepper) 方向由裝置的地區設定，而非[ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection)屬性。
- [`EntryCell`](xref:Xamarin.Forms.EntryCell) 文字對齊方式會受到裝置的地區設定，而非[ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection)屬性。
- [`ContextActions`](xref:Xamarin.Forms.Cell.ContextActions) 筆勢和對齊方式不會反轉。

### <a name="android"></a>Android

- [`SearchBar`](xref:Xamarin.Forms.SearchBar) 方向由裝置的地區設定，而非[ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection)屬性。
- [`ContextActions`](xref:Xamarin.Forms.Cell.ContextActions) 放置會受到裝置的地區設定，而非[ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection)屬性。

### <a name="uwp"></a>UWP

- [`Editor`](xref:Xamarin.Forms.Editor) 文字對齊方式會受到裝置的地區設定，而非[ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection)屬性。
- [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) 屬性不會繼承[ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage)子系。
- [`ContextActions`](xref:Xamarin.Forms.Cell.ContextActions) 文字對齊方式會受到裝置的地區設定，而非[ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection)屬性。

## <a name="right-to-left-language-support-with-xamarinuniversity"></a>由右至左的語言支援與 Xamarin.University

> [!VIDEO https://youtube.com/embed/f2lQ5yw3iiU]

**Xamarin.Forms 3.0 從右至左支援，藉由[Xamarin University](https://university.xamarin.com/)**

## <a name="related-links"></a>相關連結

- [TodoLocalizedRTL 範例應用程式](https://developer.xamarin.com/samples/xamarin-forms/TodoLocalizedRTL/)
