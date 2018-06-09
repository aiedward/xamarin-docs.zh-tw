---
title: 使用者介面項目上設定存取範圍值
description: 本文說明如何使用 AutomationProperties 類別，讓螢幕助讀程式可以宣告有關元素在頁面上。
ms.prod: xamarin
ms.assetid: c0bb6893-fd26-47e7-88e5-3c333c9f786c
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2017
ms.openlocfilehash: ad7b1c41f34c14a81910d5be30fd6484919e8d39
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/08/2018
ms.locfileid: "35241880"
---
# <a name="setting-accessibility-values-on-user-interface-elements"></a>使用者介面項目上設定存取範圍值

_Xamarin.Forms 可讓協助工具設定在使用者介面項目上使用來自 AutomationProperties 類別，接著設定原生的存取範圍值的附加的屬性的值。本文說明如何使用 AutomationProperties 類別，讓螢幕助讀程式可以宣告有關元素在頁面上。_

## <a name="overview"></a>總覽

Xamarin.Forms 可讓您透過下列附加屬性的使用者介面項目上設定的存取範圍值：

- `AutomationProperties.IsInAccessibleTree` – 表示項目是否可存取的應用程式使用。 如需詳細資訊，請參閱[AutomationProperties.IsInAccessibleTree](#isinaccessibletree)。
- `AutomationProperties.Name` – 簡短描述，做為項目的 speakable 識別項的項目。 如需詳細資訊，請參閱[AutomationProperties.Name](#name)。
- `AutomationProperties.HelpText` – 長描述的項目可以視為與項目相關聯的工具提示文字。 如需詳細資訊，請參閱[AutomationProperties.HelpText](#helptext)。
- `AutomationProperties.LabeledBy` – 可讓另一個項目定義目前項目的協助工具資訊。 如需詳細資訊，請參閱[AutomationProperties.LabeledBy](#labeledby)。

這些連接屬性設定原生的協助工具的值，讓螢幕助讀程式可以宣告項目相關。 如需附加屬性的詳細資訊，請參閱[附加屬性](~/xamarin-forms/xaml/attached-properties.md)。

> [!IMPORTANT]
> 使用`AutomationProperties`附加的屬性可能會影響在 Android 上的執行 UI 測試。 [ `AutomationId` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Element.AutomationId/)，`AutomationProperties.Name`和`AutomationProperties.HelpText`屬性將會同時設定原生`ContentDescription`屬性，與`AutomationProperties.Name`和`AutomationProperties.HelpText`屬性值優先於`AutomationId`值 (如果有兩個`AutomationProperties.Name`和`AutomationProperties.HelpText`設定，將串連的值)。 這表示，尋找任何測試`AutomationId`如果將會失敗`AutomationProperties.Name`或`AutomationProperties.HelpText`也會在項目上設定。 在此案例中，UI 測試應該尋找的值更改`AutomationProperties.Name`或`AutomationProperties.HelpText`，或兩者的串連。

每個平台都有不同的螢幕助讀程式錄製旁白的協助工具的值：

- iOS 的旁白。 如需詳細資訊，請參閱[旁白您裝置上測試協助工具](https://developer.apple.com/library/content/technotes/TestingAccessibilityOfiOSApps/TestAccessibilityonYourDevicewithVoiceOver/TestAccessibilityonYourDevicewithVoiceOver.html)developer.apple.com 上。
- Android 提供 TalkBack。 如需詳細資訊，請參閱[測試您的應用程式的協助工具](https://developer.android.com/training/accessibility/testing.html#talkback)developer.android.com 上。
- Windows 具有 [朗讀程式]。 如需詳細資訊，請參閱[確認主應用程式案例中使用 [朗讀程式]](/windows/uwp/accessibility/accessibility-testing#verify-main-app-scenarios-by-using-narrator/)。

不過，螢幕助讀程式的確切行為取決於軟體和它的使用者的組態。 例如，大部分的螢幕助讀程式讀取焦點時，與控制項相關聯的文字讓使用者為移動的頁面上的控制項之間設定本身。 某些螢幕助讀程式也會讀取整個應用程式使用者介面頁面出現時，可讓使用者在收到所有頁面的可用資訊的內容，才能嘗試對其進行巡覽。

螢幕助讀程式也會讀取不同協助工具的值。 在範例應用程式中：

- 將讀取旁白`Placeholder`值`Entry`，後面接著使用此控制項的指示。
- 將讀取 talkBack`Placeholder`值`Entry`，後面接著`AutomationProperties.HelpText`值，後面接著使用此控制項的指示。
- [朗讀程式] 會讀取`AutomationProperties.LabeledBy`值`Entry`，後面接著說明如何使用控制項。

此外，[朗讀程式] 將會優先處理`AutomationProperties.Name`， `AutomationProperties.LabeledBy`，然後`AutomationProperties.HelpText`。 在 Android 上，可以結合 TalkBack`AutomationProperties.Name`和`AutomationProperties.HelpText`值。 因此，建議的完整協助工具測試上執行每個平台，以確保最佳的經驗。

<a name="isinaccessibletree" />

## <a name="automationpropertiesisinaccessibletree"></a>AutomationProperties.IsInAccessibleTree

`AutomationProperties.IsInAccessibleTree`附加屬性`boolean`，決定如果元素是可存取且因此可見，螢幕助讀程式。 必須設定為`true`使用其他協助工具附加屬性。 如此即可在 XAML 中，如下所示：

```xaml
<Entry AutomationProperties.IsInAccessibleTree="true" />
```

或者，它可以設定在 C# 中，如下所示：

```csharp
var entry = new Entry();
AutomationProperties.SetIsInAccessibleTree(entry, true);
```

> [!NOTE]
> 請注意， [ `SetValue` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableObject.SetValue/p/Xamarin.Forms.BindableProperty/System.Object/)方法也可用來設定`AutomationProperties.IsInAccessibleTree`附加屬性 – `entry.SetValue(AutomationProperties.IsInAccessibleTreeProperty, true);`

<a name="name" />

## <a name="automationpropertiesname"></a>AutomationProperties.Name

`AutomationProperties.Name`附加的屬性值應為螢幕助讀程式用來宣告項目簡短、 描述性的文字字串。 這個屬性應該設定為有意義，請務必了解內容或與使用者介面互動的項目。 如此即可在 XAML 中，如下所示：

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
> 請注意， [ `SetValue` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableObject.SetValue/p/Xamarin.Forms.BindableProperty/System.Object/)方法也可用來設定`AutomationProperties.Name`附加屬性 – `activityIndicator.SetValue(AutomationProperties.NameProperty, "Progress indicator");`

<a name="helptext" />

## <a name="automationpropertieshelptext"></a>AutomationProperties.HelpText

`AutomationProperties.HelpText`附加屬性應該設定為文字，描述使用者介面項目，並可以視為工具提示文字與其相關聯的項目。 如此即可在 XAML 中，如下所示：

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
> 請注意， [ `SetValue` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableObject.SetValue/p/Xamarin.Forms.BindableProperty/System.Object/)方法也可用來設定`AutomationProperties.HelpText`附加屬性 – `button.SetValue(AutomationProperties.HelpTextProperty, "Tap to toggle the activity indicator");`

在某些平台，以編輯控制項例如[ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/)、`HelpText`屬性可以有時省略並取代預留位置文字。 例如，「 輸入您的名稱 」 是設計的候選[ `Entry.Placeholder` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Entry.Placeholder/)將文字放在之前的使用者實際輸入控制項中的屬性。

<a name="labeledby" />

## <a name="automationpropertieslabeledby"></a>AutomationProperties.LabeledBy

`AutomationProperties.LabeledBy`附加的屬性可讓另一個項目定義目前項目的協助工具資訊。 例如， [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/)旁[ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/)可以用來描述哪些`Entry`代表。 如此即可在 XAML 中，如下所示：

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
> 請注意， [ `SetValue` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableObject.SetValue/p/Xamarin.Forms.BindableProperty/System.Object/)方法也可用來設定`AutomationProperties.IsInAccessibleTree`附加屬性 – `entry.SetValue(AutomationProperties.LabeledByProperty, nameLabel);`

## <a name="summary"></a>總結

本文說明如何設定協助工具值入的使用者介面項目 Xamarin.Forms 應用程式中使用附加的屬性，從`AutomationProperties`類別。 這些連接屬性設定原生的協助工具的值，讓螢幕助讀程式可以宣告有關元素在頁面上。


## <a name="related-links"></a>相關連結

- [附加屬性](~/xamarin-forms/xaml/attached-properties.md)
- [存取範圍 （範例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Accessibility/)
