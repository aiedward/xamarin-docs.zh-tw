---
title: 在 Xamarin 中 tvOS 使用者介面樣式
description: 本文涵蓋 Apple 已新增至 tvOS 10 的淺色和深色 UI 主題，以及如何在 tvOS 應用程式中加以執行。
ms.prod: xamarin
ms.assetid: 8BC37683-AD9E-45CD-BE40-96965618AD1D
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/16/2017
ms.openlocfilehash: 793a82b7bb88f3e0c0a233c8c18c2d90f741b137
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91434759"
---
# <a name="tvos-user-interface-styles-in-xamarin"></a>在 Xamarin 中 tvOS 使用者介面樣式

_本文涵蓋 Apple 已新增至 tvOS 10 的淺色和深色 UI 主題，以及如何在 tvOS 應用程式中加以執行。_

tvOS 10 現在支援深色和淺色消費者介面主題，所有的內建 UIKit 控制項都將根據使用者的喜好設定自動調整。 此外，開發人員可以根據使用者選取的主題，以手動方式調整 UI 元素，並可覆寫指定的主題。

<a name="About-the-New-User-Interface-Styles"></a>

## <a name="about-the-new-user-interface-styles"></a>關於新的消費者介面樣式

如上所述，tvOS 10 現在支援深色和淺色消費者介面主題，所有的內建 UIKit 控制項都將根據使用者的喜好設定自動調整。

使用者可以移至 [**設定**  >  **一般**  >  **外觀**]，然後在 [**淺色**] 和 [**深色**] 之間切換，來切換此主題：

[![設定應用程式](user-interface-styles-images/theme01.png)](user-interface-styles-images/theme01.png#lightbox)

選取 **深色** 主題時，所有消費者介面元素都會在深色背景切換至淺文字：

[![深色主題](user-interface-styles-images/theme02.png)](user-interface-styles-images/theme02.png#lightbox)

使用者隨時都可以選擇切換主題，並根據目前的活動（Apple TV 所在位置或一天中的時間）來進行。

Light UI 主題是預設主題，無論使用者的喜好設定為何，任何現有的 tvOS apps 仍會使用淺色主題，除非已針對 tvOS 10 進行修改，以利用深色主題。 TvOS 10 應用程式也可以覆寫目前的主題，並一律針對部分或所有的 UI 使用淺色或深色主題。

<a name="Adopting-the-Light-and-Dark-Themes"></a>

## <a name="adopting-the-light-and-dark-themes"></a>採用淺色和深色主題

為了支援這項功能，Apple 已將新的 API 新增至 `UITraitCollection` 類別，而 tvOS 應用程式必須透過其檔案) 中的設定，選擇支援深色外觀 (`Info.plist` 。

若要加入淺色和深色主題支援，請執行下列動作：

1. 在方案總管**** 中按兩下 `Info.plist` 檔案將其開啟以進行編輯。
2. 從編輯器) 底部選取 [ **來源** 視圖 (]。
3. 新增金鑰並加以呼叫 `UIUserInterfaceStyle` ：

    [![UIUserInterfaceStyle 鍵](user-interface-styles-images/theme03.png)](user-interface-styles-images/theme03.png#lightbox)
4. 將類型設定為 `String` ，並輸入 `Automatic` 下列值：

    [![輸入自動](user-interface-styles-images/theme04.png)](user-interface-styles-images/theme04.png#lightbox)
5. 將變更儲存至檔案。

機碼有三個可能的值 `UIUserInterfaceStyle` ：

- **Light** -強制 tvOS 應用程式的 UI 一律使用淺色主題。
- **深色** -強制 tvOS 應用程式的 UI 一律使用深色主題。
- 根據使用者在 [設定] 中的喜好設定，**自動**切換淺色和深色主題。 這是慣用的設定。

<a name="UIKit-Theme-Support"></a>

### <a name="uikit-theme-support"></a>UIKit 主題支援

如果 tvOS 應用程式使用的是標準的內建 `UIView` 控制項，則會自動回應 UI 主題，而不需要任何開發人員介入。

此外， `UILabel` 也 `UITextView` 會根據選取的 UI 主題自動變更其色彩：

- 淺色主題中的文字會是黑色。
- 深色主題中的文字將會是白色。

如果開發人員在腳本或程式碼) 中手動變更文字色彩 (則會負責根據 UI 主題來處理色彩變更。

<a name="New-Blur-Effects"></a>

### <a name="new-blur-effects"></a>新的模糊效果

為了支援 tvOS 10 應用程式中的淺色和深色主題，Apple 已新增兩個新的模糊效果。 這些新效果會根據使用者選取的 UI 主題自動調整模糊，如下所示：

- `UIBlurEffectStyleRegular` -在淺色主題中使用淺模糊和深色主題中的深模糊。
- `UIBlurEffectStyleProminent` -在淺色主題中使用額外的模糊模糊，深色主題中會出現額外深的模糊。

<a name="Working-with-Trait-Collections"></a>

## <a name="working-with-trait-collections"></a>使用特性集合

您 `UserInterfaceStyle` `UITraitCollection` 可以使用類別的新屬性來取得目前選取的 UI 主題，並且將會是 `UIUserInterfaceStyle` 下列其中一個值的列舉：

- **Light** -已選取 light UI 主題。
- **深色** -已選取深色 UI 主題。
- **未指定** -尚未對螢幕顯示視圖，因此目前的 UI 主題未知。

此外，特徵集合在 tvOS 10 中具有下列功能：

- 您可以根據指定之的來自訂外觀 proxy， `UserInterfaceStyle` `UITraitCollection` 以根據主題變更影像或專案色彩等專案。
- TvOS 應用程式可以藉由覆寫或類別的方法，來處理特徵集合變更 `TraitCollectionDidChange` `UIView` `UIViewController` 。

> [!IMPORTANT]
> 適用于 tvOS 10 的 Xamarin tvOS 早期預覽尚未完全支援 `UIUserInterfaceStyle` `UITraitCollection` 。 在未來的版本中將會新增完整的支援。

<a name="Customizing-Appearance-Based-on-Theme"></a>

### <a name="customizing-appearance-based-on-theme"></a>根據主題自訂外觀

針對支援外觀 proxy 的消費者介面專案，其外觀可以根據其特性集合的 UI 主題進行調整。 因此，針對指定的 UI 專案，開發人員可以為淺色主題指定一種色彩，並為深色主題指定另一種色彩。

```csharp
button.SetTitleColor (UIColor.Red, UIControlState.Normal);

// TODO - Pseudocode because this isn't currently supported in the preview bindings.
var light = new UITraitCollection(UIUserInterfaceStyle.Light);
var dark = new UITraitCollection(UIUserInterfaceStyle.Dark);

button.ForTraitCollection(light).SetTitleColor (UIColor.Red, UIControlState.Normal);
button.ForTraitCollection(dark).SetTitleColor (UIColor.White, UIControlState.Normal);
```

> [!IMPORTANT]
> 可惜的是，適用于 tvOS 10 的 tvOS Preview 並未完全 `UIUserInterfaceStyle` 支援 `UITraitCollection` ，因此這種類型的自訂尚未提供。 在未來的版本中將會新增完整的支援。

<a name="Responding-to-Theme-Changes-Directly"></a>

### <a name="responding-to-theme-changes-directly"></a>直接回應主題變更

開發人員需要根據所選的 UI 主題，更深入地控制 UI 元素的外觀，而且可以覆寫 `TraitCollectionDidChange` 或類別的方法 `UIView` `UIViewController` 。

例如：

```csharp
public override void TraitCollectionDidChange (UITraitCollection previousTraitCollection)
{
    base.TraitCollectionDidChange (previousTraitCollection);

    // Take action based on the Light or Dark theme
    ...
}
```

<a name="Responding-to-Theme-Changes-Directly"></a>

### <a name="overriding-a-trait-collection"></a>覆寫特徵集合

根據 tvOS 應用程式的設計，有時開發人員可能需要覆寫指定消費者介面專案的特性集合，而且一定要使用特定的 UI 主題。

您可以使用類別上的方法來完成此操作 `SetOverrideTraitCollection` `UIViewController` 。 例如：

```csharp
// Create new trait and configure it
var trait = new UITraitCollection ();
...

// Apply new trait collection
SetOverrideTraitCollection (trait, this);
```

如需詳細資訊，請參閱整合分鏡指令檔[簡介](~/ios/user-interface/storyboards/unified-storyboards.md)的[特性](~/ios/user-interface/storyboards/unified-storyboards.md)和覆[寫特性](~/ios/user-interface/storyboards/unified-storyboards.md)章節。

<a name="Trait-Collections-and-Storyboards"></a>

### <a name="trait-collections-and-storyboards"></a>特性集合和分鏡腳本

在 tvOS 10 中，應用程式的分鏡腳本可以設定為回應特性集合，而且許多 UI 元素都可感知淺色和深色主題。 目前的 tvOS tvOS 10 早期預覽版本尚不支援介面設計師中的這項功能，因此，您必須在 Xcode 的 Interface Builder 中編輯腳本，以作為因應措施。

若要啟用特性集合支援，請執行下列動作：

1. 以滑鼠右鍵按一下**方案總管**中的分鏡腳本檔案，然後選取 [**開啟方式**  >  **Xcode Interface Builder**：

    [![以 Xcode Interface Builder 開啟](user-interface-styles-images/theme05.png)](user-interface-styles-images/theme05.png#lightbox)
2. 若要啟用特性集合支援，請切換至檔案偵測器，並檢查**Interface Builder** **檔**區段中的 [**使用特性變化**] 屬性：

    [![啟用特徵集合支援](user-interface-styles-images/theme06.png)](user-interface-styles-images/theme06.png#lightbox)
3. 確認變更為使用特徵變化：

    [![使用特徵變化警示](user-interface-styles-images/theme07.png)](user-interface-styles-images/theme07.png#lightbox)
4. 將變更儲存至分鏡腳本檔案。

在 Interface Builder 中編輯 tvOS 分鏡腳本時，Apple 已新增下列功能：

- 開發人員可以根據 **屬性偵測器**中的 UI 主題，來指定不同的消費者介面元素變化：

  - 現在有幾個屬性 **+** 可以按一下，以新增 UI 主題特定版本：

    [![新增 UI 主題的特定版本](user-interface-styles-images/theme08.png)](user-interface-styles-images/theme08.png#lightbox)

  - 開發人員可以指定新的屬性，或按一下 [ **x** ] 按鈕將它移除：

    [![指定新的屬性，或按一下 [x] 按鈕將它移除](user-interface-styles-images/theme09.png)](user-interface-styles-images/theme09.png#lightbox)
- 開發人員可以從 Interface Builder 內的淺色或深色主題中預覽 UI 設計：

  - Design Surface 的底部可讓開發人員切換目前的 UI 主題：

    [![Design Surface 的底部](user-interface-styles-images/theme10.png)](user-interface-styles-images/theme10.png#lightbox)

  - 新的主題會顯示在 Interface Builder 中，並顯示任何特性集合特定的調整：

    [![顯示在 Interface Builder 中的主題](user-interface-styles-images/theme11.png)](user-interface-styles-images/theme11.png#lightbox)

此外，tvOS 模擬器現在具有鍵盤快速鍵，可讓開發人員在 tvOS 應用程式的偵錯工具時，快速切換淺色和深色主題。 使用 **命令-Shift-D** 鍵盤順序，在淺色與深色之間切換。

<a name="Summary"></a>

## <a name="summary"></a>摘要

本文涵蓋了 Apple 已新增至 tvOS 10 的 Light 和深色 UI 主題，以及如何在 tvOS 應用程式中加以執行。

## <a name="related-links"></a>相關連結

- [tvOS 範例](/samples/browse/?products=xamarin&term=Xamarin.iOS%2btvOS)
- [TvOS 10 的新功能](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewinTVOS/Articles/tvOS10.html#//apple_ref/doc/uid/TP40017259-SW1)