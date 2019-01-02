---
title: 自動化屬性
description: 本文說明如何在 Xamarin.Forms 應用程式中，使用 AutomationProperties 類別，讓螢幕助讀程式可以讀出頁面上的項目。
ms.prod: xamarin
ms.assetid: c0bb6893-fd26-47e7-88e5-3c333c9f786c
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2017
ms.openlocfilehash: f59a528916d2cc5efd19ba7c35a7b4f041ecbe09
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/07/2018
ms.locfileid: "53056158"
---
# <a name="automation-properties-in-xamarinforms"></a>在 Xamarin.Forms 中的自動化屬性

[![下載範例](~/media/shared/download.png) 下載範例](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Accessibility/)

_Xamarin.Forms 可讓協助在使用者介面項目上，使用來自 AutomationProperties 類別的附加屬性設定協助工具值，然後附加屬性會設定原生的協助工具值。本文說明如何使用 AutomationProperties 類別，讓螢幕助讀程式可以讀出頁面上的項目。_

Xamarin.Forms 可透過下列附加屬性在使用者介面項目上設定自動化屬性：

- `AutomationProperties.IsInAccessibleTree` – 表示項目是否可供可存取的應用程式使用。 如需詳細資訊，請參閱 [AutomationProperties.IsInAccessibleTree](#isinaccessibletree)。
- `AutomationProperties.Name` – 作為項目可朗讀識別碼的項目簡短描述。 如需詳細資訊，請參閱 [AutomationProperties.Name](#name)。
- `AutomationProperties.HelpText` – 項目的較長描述，可視為與項目建立關聯的工具提示文字。 如需詳細資訊，請參閱 [AutomationProperties.HelpText](#helptext)。
- `AutomationProperties.LabeledBy` – 可讓另一個項目定義目前項目的協助工具資訊。 如需詳細資訊，請參閱 [AutomationProperties.LabeledBy](#labeledby)。

這些附加屬性會設定原生協助工具值，讓螢幕助讀程式可以讀出項目。 如需附加屬性的詳細資訊，請參閱[附加屬性](~/xamarin-forms/xaml/attached-properties.md)。

> [!IMPORTANT]
> 使用 `AutomationProperties`，附加屬性可能會影響 Android 上的 UI 測試執行。 [`AutomationId`](xref:Xamarin.Forms.Element.AutomationId)、`AutomationProperties.Name` 和 `AutomationProperties.HelpText` 屬性，都會設定原生 `ContentDescription` 屬性，且 `AutomationProperties.Name` 和 `AutomationProperties.HelpText` 屬性值會優先於 `AutomationId` 值 (如果 `AutomationProperties.Name` 和 `AutomationProperties.HelpText` 都已設定的話，將會串連值)。 這表示尋找 `AutomationId` 的任何測試將會失敗，如果 `AutomationProperties.Name` 或 `AutomationProperties.HelpText` 也在項目上設定的話。 在此案例中，UI 測試應該更改為尋找 `AutomationProperties.Name` 或 `AutomationProperties.HelpText` 的值，或是兩者的串連。

每個平台都有不同的螢幕助讀程式，以敘述協助工具值：

- iOS 有 VoiceOver。 如需詳細資訊，請參閱 developer.apple.com 上的 [Test Accessibility on Your Device with VoiceOver](https://developer.apple.com/library/content/technotes/TestingAccessibilityOfiOSApps/TestAccessibilityonYourDevicewithVoiceOver/TestAccessibilityonYourDevicewithVoiceOver.html) (使用 VoiceOver，在裝置上測試協助工具)。
- Android 有 TalkBack。 如需詳細資訊，請參閱 developer.android.com 上的 [Testing Your App's Accessibility](https://developer.android.com/training/accessibility/testing.html#talkback) (測試應用程式協助工具)。
- Windows 有「朗讀程式」。 如需詳細資訊，請參閱[使用朗讀程式來確認主應用程式案例](/windows/uwp/accessibility/accessibility-testing#verify-main-app-scenarios-by-using-narrator/)。

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
> 請注意，[`SetValue`](xref:Xamarin.Forms.BindableObject.SetValue(Xamarin.Forms.BindableProperty,System.Object)) 方法也可用來設定 `AutomationProperties.IsInAccessibleTree` 附加屬性 – `entry.SetValue(AutomationProperties.IsInAccessibleTreeProperty, true);`

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
> 請注意，[`SetValue`](xref:Xamarin.Forms.BindableObject.SetValue(Xamarin.Forms.BindableProperty,System.Object)) 方法也可用來設定 `AutomationProperties.Name` 附加屬性 – `activityIndicator.SetValue(AutomationProperties.NameProperty, "Progress indicator");`

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
> 請注意，[`SetValue`](xref:Xamarin.Forms.BindableObject.SetValue(Xamarin.Forms.BindableProperty,System.Object)) 方法也可用來設定 `AutomationProperties.HelpText` 附加屬性 – `button.SetValue(AutomationProperties.HelpTextProperty, "Tap to toggle the activity indicator");`

在某些平台上，對於編輯控制項，例如 [`Entry`](xref:Xamarin.Forms.Entry)，`HelpText` 屬性可以有時省略並取代為預留位置文字。 比方說，「輸入您的名稱」就是 [`Entry.Placeholder`](xref:Xamarin.Forms.Entry.Placeholder) 屬性很好的候選，它會在使用者實際輸入之前將文字放在控制項中。

<a name="labeledby" />

## <a name="automationpropertieslabeledby"></a>AutomationProperties.LabeledBy

`AutomationProperties.LabeledBy` 附加屬性可讓另一個項目定義目前項目的協助工具資訊。 例如，[`Entry`](xref:Xamarin.Forms.Entry) 旁的 [`Label`](xref:Xamarin.Forms.Label)可用來描述 `Entry` 代表什麼。 執行下列工作即可用 XAML 來達成這點：

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
> 請注意，[`SetValue`](xref:Xamarin.Forms.BindableObject.SetValue(Xamarin.Forms.BindableProperty,System.Object)) 方法也可用來設定 `AutomationProperties.IsInAccessibleTree` 附加屬性 – `entry.SetValue(AutomationProperties.LabeledByProperty, nameLabel);`

## <a name="related-links"></a>相關連結

- [附加屬性](~/xamarin-forms/xaml/attached-properties.md)
- [Accessibility (Samples)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Accessibility/) (協助工具 (範例))
