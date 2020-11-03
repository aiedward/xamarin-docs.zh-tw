---
title: 自動化屬性
description: 本文說明如何在應用程式中使用 AutomationProperties 類別 Xamarin.Forms ，讓螢幕讀取器可以說出頁面上的元素。
ms.prod: xamarin
ms.assetid: c0bb6893-fd26-47e7-88e5-3c333c9f786c
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/18/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: f05ca6eaaaedea69e8b6eb951d31fb7701a2f0de
ms.sourcegitcommit: 836d54779190b1bef1b43bc0c2016c9b3034bfda
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/03/2020
ms.locfileid: "93281179"
---
# <a name="automation-properties-in-no-locxamarinforms"></a>中的 Automation 屬性 Xamarin.Forms

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-accessibility)

_Xamarin.Forms 允許使用 AutomationProperties 類別中的附加屬性，在使用者介面專案上設定存取範圍值，進而設定原生協助工具值。本文說明如何使用 AutomationProperties 類別，讓螢幕讀取器可以說出頁面上的元素。_

Xamarin.Forms 允許透過下列附加屬性，在使用者介面元素上設定 automation 屬性：

- `AutomationProperties.IsInAccessibleTree` – 表示項目是否可供可存取的應用程式使用。 如需詳細資訊，請參閱 [AutomationProperties.IsInAccessibleTree](#automationpropertiesisinaccessibletree)。
- `AutomationProperties.Name` – 作為項目可朗讀識別碼的項目簡短描述。 如需詳細資訊，請參閱 [AutomationProperties.Name](#automationpropertiesname)。
- `AutomationProperties.HelpText` – 項目的較長描述，可視為與項目建立關聯的工具提示文字。 如需詳細資訊，請參閱 [AutomationProperties.HelpText](#automationpropertieshelptext)。
- `AutomationProperties.LabeledBy` – 可讓另一個項目定義目前項目的協助工具資訊。 如需詳細資訊，請參閱 [AutomationProperties.LabeledBy](#automationpropertieslabeledby)。

這些附加屬性會設定原生協助工具值，讓螢幕助讀程式可以讀出項目。 如需附加屬性的詳細資訊，請參閱[附加屬性](~/xamarin-forms/xaml/attached-properties.md)。

> [!IMPORTANT]
> 使用 `AutomationProperties`，附加屬性可能會影響 Android 上的 UI 測試執行。 [`AutomationId`](xref:Xamarin.Forms.Element.AutomationId)、 `AutomationProperties.Name` 和 `AutomationProperties.HelpText` 屬性都會設定原生 `ContentDescription` 屬性， `AutomationProperties.Name` 並將和 `AutomationProperties.HelpText` 屬性值的優先順序高於 `AutomationId` 值 (如果 `AutomationProperties.Name` 和 `AutomationProperties.HelpText` 都已設定，這些值將會串連) 。 這表示尋找 `AutomationId` 的任何測試將會失敗，如果 `AutomationProperties.Name` 或 `AutomationProperties.HelpText` 也在項目上設定的話。 在此案例中，UI 測試應該更改為尋找 `AutomationProperties.Name` 或 `AutomationProperties.HelpText` 的值，或是兩者的串連。

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
> 請注意，[ `SetValue` ] (x： Xamarin.Forms 。BindableObject (Xamarin.Forms 。BindableProperty、System.object) # A3 方法也可以用來設定 `AutomationProperties.IsInAccessibleTree` 附加屬性– `entry.SetValue(AutomationProperties.IsInAccessibleTreeProperty, true);`

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
> 請注意，[ `SetValue` ] (x： Xamarin.Forms 。BindableObject (Xamarin.Forms 。BindableProperty、System.object) # A3 方法也可以用來設定 `AutomationProperties.Name` 附加屬性– `activityIndicator.SetValue(AutomationProperties.NameProperty, "Progress indicator");`

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
> 請注意，[ `SetValue` ] (x： Xamarin.Forms 。BindableObject (Xamarin.Forms 。BindableProperty、System.object) # A3 方法也可以用來設定 `AutomationProperties.HelpText` 附加屬性– `button.SetValue(AutomationProperties.HelpTextProperty, "Tap to toggle the activity indicator");`

在某些平臺上，如果是編輯控制項（例如 [`Entry`](xref:Xamarin.Forms.Entry) ），則 `HelpText` 有時可以省略屬性，並將其取代為預留位置文字。 例如，「在此輸入您的名稱」是在 [`Entry.Placeholder`](xref:Xamarin.Forms.InputView.Placeholder) 使用者實際輸入之前，將文字放在控制項中的屬性很好的候選。

## <a name="automationpropertieslabeledby"></a>AutomationProperties.LabeledBy

`AutomationProperties.LabeledBy` 附加屬性可讓另一個項目定義目前項目的協助工具資訊。 例如，的 [`Label`](xref:Xamarin.Forms.Label) 旁邊 [`Entry`](xref:Xamarin.Forms.Entry) 可以用來描述代表的內容 `Entry` 。 執行下列工作即可用 XAML 來達成這點：

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

> [!IMPORTANT]
> `AutomationProperties.LabeledByProperty`IOS 尚未支援。

> [!NOTE]
> 請注意，[ `SetValue` ] (x： Xamarin.Forms 。BindableObject (Xamarin.Forms 。BindableProperty、System.object) # A3 方法也可以用來設定 `AutomationProperties.IsInAccessibleTree` 附加屬性– `entry.SetValue(AutomationProperties.LabeledByProperty, nameLabel);`

## <a name="accessibility-intricacies"></a>協助工具的複雜性

以下章節描述在特定控制項上，設定協助工具值的複雜性。

### <a name="navigationpage"></a>NavigationPage

在 Android 上，若要設定螢幕讀取器將在中動作列的上一頁箭號讀取的文字 [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) ，請 `AutomationProperties.Name` `AutomationProperties.HelpText` 在上設定和屬性 [`Page`](xref:Xamarin.Forms.Page) 。 但請注意，這對 OS [上一步] 按鈕沒有任何效果。

### <a name="masterdetailpage"></a>MasterDetailPage

在 iOS 和通用 Windows 平臺 (UWP) 上，若要設定螢幕讀取器將在上的切換按鈕中讀取的文字，請在上 [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) 設定的 `AutomationProperties.Name` 、和 `AutomationProperties.HelpText` 屬性 `MasterDetailPage` ，或在 `IconImageSource` 頁面的屬性上設定 `Master` 。

在 Android 上，若要設定螢幕讀取器將針對上的切換按鈕讀取的文字 [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) ，請將字串資源新增至 Android 專案：

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

在 iOS、Android 及 UWP 上， `Text` [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem) 如果 `AutomationProperties.Name` `AutomationProperties.HelpText` 未定義或值，螢幕讀取器將會讀取實例的屬性值。

在 iOS 及 UWP 上，`AutomationProperties.Name` 屬性值會取代螢幕助讀程式所朗讀的 `Text` 屬性值。

在 Android 上，`AutomationProperties.Name` 及 (或) `AutomationProperties.HelpText` 屬性值會完全取代螢幕助讀程式可同時看到及朗讀的 `Text` 屬性值。 請注意，此為小於 26 的 API 之限制。

## <a name="related-links"></a>相關連結

- [附加屬性](~/xamarin-forms/xaml/attached-properties.md)
- [Accessibility (Samples)](/samples/xamarin/xamarin-forms-samples/userinterface-accessibility) (協助工具 (範例))