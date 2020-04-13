---
title: 自動化屬性
description: 本文說明如何在 Xamarin.Forms 應用程式中，使用 AutomationProperties 類別，讓螢幕助讀程式可以讀出頁面上的項目。
ms.prod: xamarin
ms.assetid: c0bb6893-fd26-47e7-88e5-3c333c9f786c
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/18/2018
ms.openlocfilehash: 12c6229c1922f0bd4a4d25ca796bcb46141a326c
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "77131131"
---
# <a name="automation-properties-in-xamarinforms"></a>在 Xamarin.Forms 中的自動化屬性

[![下載範例](~/media/shared/download.png)下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-accessibility)

_Xamarin.Forms 允許透過使用自動化屬性類的附加屬性在使用者介面元素上設置輔助功能值,這些屬性反過來設置本機輔助功能值。本文介紹如何使用自動化屬性類,以便螢幕閱讀器可以談論頁面上的元素。_

Xamarin.Forms 可透過下列附加屬性在使用者介面項目上設定自動化屬性：

- `AutomationProperties.IsInAccessibleTree` – 表示項目是否可供可存取的應用程式使用。 如需詳細資訊，請參閱 [AutomationProperties.IsInAccessibleTree](#isinaccessibletree)。
- `AutomationProperties.Name` – 作為項目可朗讀識別碼的項目簡短描述。 如需詳細資訊，請參閱 [AutomationProperties.Name](#name)。
- `AutomationProperties.HelpText` – 項目的較長描述，可視為與項目建立關聯的工具提示文字。 如需詳細資訊，請參閱 [AutomationProperties.HelpText](#helptext)。
- `AutomationProperties.LabeledBy` – 可讓另一個項目定義目前項目的協助工具資訊。 如需詳細資訊，請參閱 [AutomationProperties.LabeledBy](#labeledby)。

這些附加屬性會設定原生協助工具值，讓螢幕助讀程式可以讀出項目。 如需附加屬性的詳細資訊，請參閱[附加屬性](~/xamarin-forms/xaml/attached-properties.md)。

> [!IMPORTANT]
> 使用 `AutomationProperties`，附加屬性可能會影響 Android 上的 UI 測試執行。 `AutomationProperties.Name`和[`AutomationId`](xref:Xamarin.Forms.Element.AutomationId)`AutomationProperties.Name``AutomationProperties.HelpText``AutomationId``AutomationProperties.Name``AutomationProperties.HelpText`屬性都將設置本機屬性,和 屬性值優先於值(如果同時設置, 則將串聯這些值)。`AutomationProperties.HelpText``ContentDescription` 這表示尋找 `AutomationId` 的任何測試將會失敗，如果 `AutomationProperties.Name` 或 `AutomationProperties.HelpText` 也在項目上設定的話。 在此案例中，UI 測試應該更改為尋找 `AutomationProperties.Name` 或 `AutomationProperties.HelpText` 的值，或是兩者的串連。

每個平台都有不同的螢幕助讀程式，以敘述協助工具值：

- iOS 有 VoiceOver。 如需詳細資訊，請參閱 developer.apple.com 上的 [Test Accessibility on Your Device with VoiceOver](https://developer.apple.com/library/content/technotes/TestingAccessibilityOfiOSApps/TestAccessibilityonYourDevicewithVoiceOver/TestAccessibilityonYourDevicewithVoiceOver.html) (使用 VoiceOver，在裝置上測試協助工具)。
- Android 有 TalkBack。 如需詳細資訊，請參閱 developer.android.com 上的 [Testing Your App's Accessibility](https://developer.android.com/training/accessibility/testing.html#talkback) (測試應用程式協助工具)。
- Windows 有「朗讀程式」。 如需詳細資訊，請參閱[使用朗讀程式來確認主應用程式案例](/windows/uwp/accessibility/accessibility-testing#verify-main-app-scenarios-by-using-narrator)。

不過，螢幕助讀程式的確切行為取決於軟體和其使用者組態。 例如，大部分的螢幕助讀程式獲得焦點時，會讀出與控制項建立關聯的文字，讓使用者能在頁面上控制項之間移動時定位自己。 某些螢幕助讀程式也會在頁面出現時讀出整個應用程式使用者介面，這可讓使用者能在嘗試巡覽頁面之前，就收到頁面的所有可用資訊內容。

螢幕助讀程式也會讀出不同的協助工具值。 在範例應用程式中：

- VoiceOver 會讀出 `Entry` 的 `Placeholder` 值，後面接著使用控制項的指示。
- TalkBack 會讀出 `Entry` 的 `Placeholder` 值、後面接著 `AutomationProperties.HelpText` 值，然後接著使用控制項的指示。
- 朗讀程式會讀出 `Entry` 的 `AutomationProperties.LabeledBy` 值，後面接著使用控制項的指示。

此外，朗讀程式會依序優先處理 `AutomationProperties.Name`、`AutomationProperties.LabeledBy`，然後處理 `AutomationProperties.HelpText`。 在 Android 上，TalkBack 可以結合 `AutomationProperties.Name` 和 `AutomationProperties.HelpText` 值。 因此，建議在每個平台上進行徹底的協助工具測試，以確保獲得最佳的體驗。

<a name="isinaccessibletree" />

## <a name="automationpropertiesisinaccessibletree"></a>AutomationProperties.IsInAccessibleTree

`AutomationProperties.IsInAccessibleTree` 附加屬性是 `boolean`，它決定項目是否可供螢幕助讀程式存取，並因此可見。 它必須設定為 `true` 才能使用其他協助工具附加屬性。 執行下列工作即可用 XAML 來達成這點：

```xaml
<Entry AutomationProperties.IsInAccessibleTree="true" />
```

或者，可以在 C# 中設定，如下所示：

```csharp
var entry = new Entry();
AutomationProperties.SetIsInAccessibleTree(entry, true);
```

> [!NOTE]
> 請注意,[`SetValue`](xref:Xamarin.Forms.BindableObject.SetValue(Xamarin.Forms.BindableProperty,System.Object))該方法還可用於`AutomationProperties.IsInAccessibleTree`設定 附加屬性 |`entry.SetValue(AutomationProperties.IsInAccessibleTreeProperty, true);`

<a name="name" />

## <a name="automationpropertiesname"></a>AutomationProperties.Name

`AutomationProperties.Name` 附加屬性值應該是簡短的描述性文字字串，螢幕助讀程式會用它來宣告項目。 對於了解內容或與使用者介面互動而言具有重要意義的項目，應該設定這個屬性。 執行下列工作即可用 XAML 來達成這點：

```xaml
<ActivityIndicator AutomationProperties.IsInAccessibleTree="true"
                   AutomationProperties.Name="Progress indicator" />
```

或者，可以在 C# 中設定，如下所示：

```csharp
var activityIndicator = new ActivityIndicator();
AutomationProperties.SetIsInAccessibleTree(activityIndicator, true);
AutomationProperties.SetName(activityIndicator, "Progress indicator");
```

> [!NOTE]
> 請注意,[`SetValue`](xref:Xamarin.Forms.BindableObject.SetValue(Xamarin.Forms.BindableProperty,System.Object))該方法還可用於`AutomationProperties.Name`設定 附加屬性 |`activityIndicator.SetValue(AutomationProperties.NameProperty, "Progress indicator");`

<a name="helptext" />

## <a name="automationpropertieshelptext"></a>AutomationProperties.HelpText

`AutomationProperties.HelpText` 附加屬性應設為描述使用者介面項目的文字，且可以視為與項目建立關聯的工具提示文字。 執行下列工作即可用 XAML 來達成這點：

```xaml
<Button Text="Toggle ActivityIndicator"
        AutomationProperties.IsInAccessibleTree="true"
        AutomationProperties.HelpText="Tap to toggle the activity indicator" />
```

或者，可以在 C# 中設定，如下所示：

```csharp
var button = new Button { Text = "Toggle ActivityIndicator" };
AutomationProperties.SetIsInAccessibleTree(button, true);
AutomationProperties.SetHelpText(button, "Tap to toggle the activity indicator");
```

> [!NOTE]
> 請注意,[`SetValue`](xref:Xamarin.Forms.BindableObject.SetValue(Xamarin.Forms.BindableProperty,System.Object))該方法還可用於`AutomationProperties.HelpText`設定 附加屬性 |`button.SetValue(AutomationProperties.HelpTextProperty, "Tap to toggle the activity indicator");`

在某些平臺上,對於編輯控制項(如,[`Entry`](xref:Xamarin.Forms.Entry)有時可以省`HelpText`略 該屬性並將其替換為占位符文本)。 例如,「在此處輸入您的姓名」是屬性的一個很好的候選項,該[`Entry.Placeholder`](xref:Xamarin.Forms.InputView.Placeholder)屬性在使用者的實際輸入之前將文本置於控制項中。

<a name="labeledby" />

## <a name="automationpropertieslabeledby"></a>AutomationProperties.LabeledBy

`AutomationProperties.LabeledBy` 附加屬性可讓另一個項目定義目前項目的協助工具資訊。 例如,可以使用[`Label`](xref:Xamarin.Forms.Label)旁邊的[`Entry`](xref:Xamarin.Forms.Entry)一個用於描述`Entry`表示 的內容。 執行下列工作即可用 XAML 來達成這點：

```xaml
<Label x:Name="label" Text="Enter your name: " />
<Entry AutomationProperties.IsInAccessibleTree="true"
       AutomationProperties.LabeledBy="{x:Reference label}" />
```

或者，可以在 C# 中設定，如下所示：

```csharp
var nameLabel = new Label { Text = "Enter your name: " };
var entry = new Entry();
AutomationProperties.SetIsInAccessibleTree(entry, true);
AutomationProperties.SetLabeledBy(entry, nameLabel);
```

> [!NOTE]
> 請注意,[`SetValue`](xref:Xamarin.Forms.BindableObject.SetValue(Xamarin.Forms.BindableProperty,System.Object))該方法還可用於`AutomationProperties.IsInAccessibleTree`設定 附加屬性 |`entry.SetValue(AutomationProperties.LabeledByProperty, nameLabel);`

## <a name="accessibility-intricacies"></a>協助工具的複雜性

以下章節描述在特定控制項上，設定協助工具值的複雜性。

### <a name="navigationpage"></a>NavigationPage

在 Android 上,要設定螢幕閱讀[`NavigationPage`](xref:Xamarin.Forms.NavigationPage)器將在 操作列中讀取的後退箭頭的文本,`AutomationProperties.Name``AutomationProperties.HelpText`[`Page`](xref:Xamarin.Forms.Page)請設置 和 屬性。 但請注意，這對 OS [上一步] 按鈕沒有任何效果。

### <a name="masterdetailpage"></a>MasterDetailPage

在 iOS 與通用 Windows 平台 (UWP) 上,要設定[`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage)螢幕閱讀器將讀取的`AutomationProperties.Name`文字`AutomationProperties.HelpText`,`MasterDetailPage`以在上`IconImageSource`設定的`Master`屬性, 或在頁面上的屬性上設定 。

在 Android 上,要設定螢幕閱讀器將讀取[`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage)的文字 ,請向 Android 專案加入字串資源:

```xml
<resources>
    <string name="app_name">Xamarin Forms Control Gallery</string>
    <string name="btnMDPAutomationID_open">Open Side Menu message</string>
    <string name="btnMDPAutomationID_close">Close Side Menu message</string>
</resources>
```

然後將 `Master` 頁面 `IconImageSource` 屬性的 `AutomationId` 屬性，設為適當的字串：

```csharp
var master = new ContentPage { ... };
master.IconImageSource.AutomationId = "btnMDPAutomationID";
```

### <a name="toolbaritem"></a>ToolbarItem

在 iOS、Android 和 UWP`Text`上,螢幕[`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem)閱讀器將讀取 實例`AutomationProperties.Name`的`AutomationProperties.HelpText`屬性值,前提是 未定義或定義值。

在 iOS 及 UWP 上，`AutomationProperties.Name` 屬性值會取代螢幕助讀程式所朗讀的 `Text` 屬性值。

在 Android 上，`AutomationProperties.Name` 及 (或) `AutomationProperties.HelpText` 屬性值會完全取代螢幕助讀程式可同時看到及朗讀的 `Text` 屬性值。 請注意，此為小於 26 的 API 之限制。

## <a name="related-links"></a>相關連結

- [附加屬性](~/xamarin-forms/xaml/attached-properties.md)
- [Accessibility (Samples)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-accessibility) (協助工具 (範例))
