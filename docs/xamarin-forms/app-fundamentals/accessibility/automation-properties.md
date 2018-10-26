---
title: 自動化屬性
description: 這篇文章說明如何在 Xamarin.Forms 應用程式中，使用 AutomationProperties 類別，讓螢幕助讀程式頁面上，可以讀出項目的詳細。
ms.prod: xamarin
ms.assetid: c0bb6893-fd26-47e7-88e5-3c333c9f786c
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2017
ms.openlocfilehash: c720b9f38d2a34155face10b75f5f054f3313711
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50131139"
---
# <a name="automation-properties-in-xamarinforms"></a>在 Xamarin.Forms 中的自動化屬性

_Xamarin.Forms 可讓協助工具要在使用者介面項目設定以使用來自 AutomationProperties 類別，然後依序設定原生的協助工具的值的附加的屬性的值。這篇文章會說明如何使用 AutomationProperties 類別，讓螢幕助讀程式頁面上，可以讀出項目的詳細。_

Xamarin.Forms 可讓自動化屬性，以透過下列連接屬性的使用者介面項目上設定：

- `AutomationProperties.IsInAccessibleTree` – 表示項目是否可供存取的應用程式。 如需詳細資訊，請參閱 < [AutomationProperties.IsInAccessibleTree](#isinaccessibletree)。
- `AutomationProperties.Name` – 做為元素的 speakable 識別碼元素的簡短描述。 如需詳細資訊，請參閱 < [AutomationProperties.Name](#name)。
- `AutomationProperties.HelpText` – 更長的描述，可以視為項目相關聯的工具提示文字的項目。 如需詳細資訊，請參閱 < [AutomationProperties.HelpText](#helptext)。
- `AutomationProperties.LabeledBy` – 可讓另一個項目定義目前項目的協助工具資訊。 如需詳細資訊，請參閱 < [AutomationProperties.LabeledBy](#labeledby)。

讓螢幕助讀程式可以唸出項目相關，這些附加屬性設定原生的協助工具的值。 如需有關附加屬性的詳細資訊，請參閱[附加屬性](~/xamarin-forms/xaml/attached-properties.md)。

> [!IMPORTANT]
> 使用`AutomationProperties`附加的屬性可能會影響在 Android 上的 UI 測試執行。 [ `AutomationId` ](xref:Xamarin.Forms.Element.AutomationId)，`AutomationProperties.Name`並`AutomationProperties.HelpText`屬性會同時設定原生`ContentDescription`屬性，與`AutomationProperties.Name`和`AutomationProperties.HelpText`屬性值優先於`AutomationId`值 (如果有兩個`AutomationProperties.Name`和`AutomationProperties.HelpText`設定，將串連的值)。 這表示任何測試尋求`AutomationId`將會失敗，如果`AutomationProperties.Name`或`AutomationProperties.HelpText`也會設定項目上。 在此案例中，UI 測試應該要尋找的值更改`AutomationProperties.Name`或`AutomationProperties.HelpText`，或兩者的串連。

每個平台都有不同的螢幕助讀員旁白的協助工具的值：

- iOS 的 VoiceOver。 如需詳細資訊，請參閱 <<c0> [ 測試協助工具，您在裝置上使用 VoiceOver](https://developer.apple.com/library/content/technotes/TestingAccessibilityOfiOSApps/TestAccessibilityonYourDevicewithVoiceOver/TestAccessibilityonYourDevicewithVoiceOver.html) developer.apple.com 上。
- Android 提供 TalkBack。 如需詳細資訊，請參閱 <<c0> [ 測試您的應用程式的協助工具](https://developer.android.com/training/accessibility/testing.html#talkback)developer.android.com 上。
- Windows 具有 [朗讀程式]。 如需詳細資訊，請參閱 <<c0> [ 使用 [朗讀程式] 來確認主應用程式案例](/windows/uwp/accessibility/accessibility-testing#verify-main-app-scenarios-by-using-narrator/)。

不過，螢幕助讀程式的確切行為取決於軟體和它的使用者的組態。 例如，大部分的螢幕助讀程式朗讀焦點時，與控制項相關聯的文字讓使用者能夠設定本身，在頁面上控制項之間移動。 某些螢幕助讀程式也會讀取整個應用程式使用者介面頁面出現時，這可讓使用者收到所有頁面的可用資訊的內容，才能嘗試對其進行巡覽。

螢幕助讀程式也會讀取不同的存取範圍值。 在範例應用程式中：

- VoiceOver 會讀取`Placeholder`的值`Entry`，後面接著使用此控制項的指示。
- 將讀取 talkBack`Placeholder`的值`Entry`，後面接著`AutomationProperties.HelpText`值，後面接著使用此控制項的指示。
- 朗讀程式會讀取`AutomationProperties.LabeledBy`的值`Entry`，後面接著說明如何使用控制項。

此外，[朗讀程式] 會設定優先權`AutomationProperties.Name`， `AutomationProperties.LabeledBy`，然後`AutomationProperties.HelpText`。 在 Android 上，可以結合 TalkBack`AutomationProperties.Name`和`AutomationProperties.HelpText`值。 因此，建議，徹底的協助工具測試上執行每個平台，以確保獲得最佳的體驗。

<a name="isinaccessibletree" />

## <a name="automationpropertiesisinaccessibletree"></a>AutomationProperties.IsInAccessibleTree

`AutomationProperties.IsInAccessibleTree`附加的屬性是`boolean`，決定如果項目可供存取，並因此可見，螢幕助讀程式。 它必須設定為`true`使用其他協助工具; 附加屬性。 這可在 XAML，如下所示：

```xaml
<Entry AutomationProperties.IsInAccessibleTree="true" />
```

或者，它可以設定在 C# 中，如下所示：

```csharp
var entry = new Entry();
AutomationProperties.SetIsInAccessibleTree(entry, true);
```

> [!NOTE]
> 請注意， [ `SetValue` ](xref:Xamarin.Forms.BindableObject.SetValue(Xamarin.Forms.BindableProperty,System.Object))方法也可用來設定`AutomationProperties.IsInAccessibleTree`附加屬性： `entry.SetValue(AutomationProperties.IsInAccessibleTreeProperty, true);`

<a name="name" />

## <a name="automationpropertiesname"></a>AutomationProperties.Name

`AutomationProperties.Name`附加的屬性值應該是簡短、 描述性的文字字串，螢幕助讀程式會使用來宣告項目。 這個屬性應該設定為有意義，請務必了解內容，或與使用者介面互動的項目。 這可在 XAML，如下所示：

```xaml
<ActivityIndicator AutomationProperties.IsInAccessibleTree="true"
                   AutomationProperties.Name="Progress indicator" />
```

或者，它可以設定在 C# 中，如下所示：

```csharp
var activityIndicator = new ActivityIndicator();
AutomationProperties.SetIsInAccessibleTree(activityIndicator, true);
AutomationProperties.SetName(activityIndicator, "Progress indicator");
```

> [!NOTE]
> 請注意， [ `SetValue` ](xref:Xamarin.Forms.BindableObject.SetValue(Xamarin.Forms.BindableProperty,System.Object))方法也可用來設定`AutomationProperties.Name`附加屬性： `activityIndicator.SetValue(AutomationProperties.NameProperty, "Progress indicator");`

<a name="helptext" />

## <a name="automationpropertieshelptext"></a>AutomationProperties.HelpText

`AutomationProperties.HelpText`附加屬性應設為文字，描述使用者介面項目，並可以視為工具提示文字關聯的項目。 這可在 XAML，如下所示：

```xaml
<Button Text="Toggle ActivityIndicator"
        AutomationProperties.IsInAccessibleTree="true"
        AutomationProperties.HelpText="Tap to toggle the activity indicator" />
```

或者，它可以設定在 C# 中，如下所示：

```csharp
var button = new Button { Text = "Toggle ActivityIndicator" };
AutomationProperties.SetIsInAccessibleTree(button, true);
AutomationProperties.SetHelpText(button, "Tap to toggle the activity indicator");
```

> [!NOTE]
> 請注意， [ `SetValue` ](xref:Xamarin.Forms.BindableObject.SetValue(Xamarin.Forms.BindableProperty,System.Object))方法也可用來設定`AutomationProperties.HelpText`附加屬性： `button.SetValue(AutomationProperties.HelpTextProperty, "Tap to toggle the activity indicator");`

在某些平台，以編輯控制這類[ `Entry` ](xref:Xamarin.Forms.Entry)，則`HelpText`屬性可以有時省略並取代預留位置文字。 比方說，「 輸入您的名稱 」 是很好的候選[ `Entry.Placeholder` ](xref:Xamarin.Forms.Entry.Placeholder)將文字放置之前使用者的實際輸入控制項中的屬性。

<a name="labeledby" />

## <a name="automationpropertieslabeledby"></a>AutomationProperties.LabeledBy

`AutomationProperties.LabeledBy`附加的屬性可讓另一個項目定義目前項目的協助工具資訊。 例如， [ `Label` ](xref:Xamarin.Forms.Label)旁[ `Entry` ](xref:Xamarin.Forms.Entry)可用來描述項目的`Entry`表示。 這可在 XAML，如下所示：

```xaml
<Label x:Name="label" Text="Enter your name: " />
<Entry AutomationProperties.IsInAccessibleTree="true"
       AutomationProperties.LabeledBy="{x:Reference label}" />
```

或者，它可以設定在 C# 中，如下所示：

```csharp
var nameLabel = new Label { Text = "Enter your name: " };
var entry = new Entry();
AutomationProperties.SetIsInAccessibleTree(entry, true);
AutomationProperties.SetLabeledBy(entry, nameLabel);
```

> [!NOTE]
> 請注意， [ `SetValue` ](xref:Xamarin.Forms.BindableObject.SetValue(Xamarin.Forms.BindableProperty,System.Object))方法也可用來設定`AutomationProperties.IsInAccessibleTree`附加屬性： `entry.SetValue(AutomationProperties.LabeledByProperty, nameLabel);`

## <a name="related-links"></a>相關連結

- [附加屬性](~/xamarin-forms/xaml/attached-properties.md)
- [協助工具 （範例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Accessibility/)
