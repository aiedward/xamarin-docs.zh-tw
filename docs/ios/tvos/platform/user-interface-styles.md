---
title: 在 Xamarin 中 tvOS 使用者介面樣式
description: 本文涵蓋 Apple 已新增至 tvOS 10 的 Light 和深色 UI 主題, 以及如何在 tvOS 應用程式中加以執行。
ms.prod: xamarin
ms.assetid: 8BC37683-AD9E-45CD-BE40-96965618AD1D
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: ca3580648d357d2c943274717af66ad5d825ce75
ms.sourcegitcommit: 3d21bb1a6d9b78b65aa49917b545c39d44aa3e3c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2019
ms.locfileid: "70065583"
---
# <a name="tvos-user-interface-styles-in-xamarin"></a>在 Xamarin 中 tvOS 使用者介面樣式

_本文涵蓋 Apple 已新增至 tvOS 10 的 Light 和深色 UI 主題, 以及如何在 tvOS 應用程式中加以執行。_

tvOS 10 現在支援深色和淺色使用者介面主題, 所有的內建 UIKit 控制項都會根據使用者的喜好設定自動調整。 此外, 開發人員也可以根據使用者選取的主題, 手動調整 UI 元素, 而且可以覆寫指定的主題。

<a name="About-the-New-User-Interface-Styles" />

## <a name="about-the-new-user-interface-styles"></a>關於新的使用者介面樣式

如上所述, tvOS 10 現在支援深色和淺色使用者介面主題, 所有的內建 UIKit 控制項都會根據使用者的喜好設定自動調整。

使用者可以移至 [**設定** > ] **[一般] [**  > **外觀**], 並切換 [**淺色**] 和 [**深色**] 來切換此主題:

[![](user-interface-styles-images/theme01.png "設定應用程式")](user-interface-styles-images/theme01.png#lightbox)

選取**深色**主題時, 所有使用者介面元素都會在深色背景上切換為淺文字:

[![](user-interface-styles-images/theme02.png "深色主題")](user-interface-styles-images/theme02.png#lightbox)

使用者隨時都可選擇切換主題, 而且可能會根據目前的活動 (Apple TV 所在的位置或當天的時間) 來執行此動作。

Light UI 主題是預設主題, 而且任何現有的 tvOS 應用程式仍會使用淺色主題, 而不論使用者的喜好設定為何, 除非已針對 tvOS 10 進行修改, 才能利用深色主題。 TvOS 10 應用程式也能夠覆寫目前的主題, 並一律針對其部分或所有 UI 使用淺色或深色主題。

<a name="Adopting-the-Light-and-Dark-Themes" />

## <a name="adopting-the-light-and-dark-themes"></a>採用淺色與深色主題

為了支援這項功能, Apple 已將新的 API 新增`UITraitCollection`至類別, 而 tvOS 應用程式必須加入宣告以支援深色外觀 (透過其`Info.plist`檔案中的設定)。

若要加入淺色與深色主題支援, 請執行下列動作:

1. 在方案總管中按兩下 `Info.plist` 檔案將其開啟以進行編輯。
2. 選取 [**來源**] 視圖 (從編輯器的底部)。
3. 新增金鑰並呼叫它`UIUserInterfaceStyle`:

    [![](user-interface-styles-images/theme03.png "UIUserInterfaceStyle 鍵")](user-interface-styles-images/theme03.png#lightbox)
4. 將 [類型] 保留`String`設定為, 並輸入`Automatic`下列值:

    [![](user-interface-styles-images/theme04.png "輸入自動")](user-interface-styles-images/theme04.png#lightbox)
5. 將變更儲存到檔案。

索引`UIUserInterfaceStyle`鍵有三個可能的值:

- **Light** -強制 tvOS 應用程式的 UI 一律使用淺色主題。
- **深色**-強制 tvOS 應用程式的 UI 一律使用深色主題。
- **自動**-根據使用者在 [設定] 中的喜好設定, 在淺色與深色主題之間切換。 這是慣用的設定。

<a name="UIKit-Theme-Support" />

### <a name="uikit-theme-support"></a>UIKit 主題支援

如果 tvOS 應用程式使用的是以標準為基礎`UIView`的內建控制項, 則會自動回應 UI 主題, 而不需要開發人員介入。

此外, `UILabel`和`UITextView`會根據 [選取 UI 主題] 自動變更其色彩:

- 淺色主題中的文字將會是黑色。
- 深色主題中的文字將會是白色。

如果開發人員是以手動方式變更文字色彩 (在腳本或程式碼中), 他們會負責根據 UI 主題來處理色彩變更。

<a name="New-Blur-Effects" />

### <a name="new-blur-effects"></a>新的模糊效果

為了支援 tvOS 10 應用程式中的淺色和深色主題, Apple 已新增兩個新的模糊效果。 這些新效果將會根據使用者選取的 UI 主題自動調整模糊, 如下所示:

- `UIBlurEffectStyleRegular`-在淺色主題中使用淺模糊, 暗色調主題中則為暗模糊。
- `UIBlurEffectStyleProminent`-在淺色主題中使用額外的淺模糊, 暗色調主題則會有額外的模糊效果。

<a name="Working-with-Trait-Collections" />

## <a name="working-with-trait-collections"></a>使用特性集合

類別的`UserInterfaceStyle`新屬性`UIUserInterfaceStyle`可以用來取得目前選取的 UI 主題, 而且將會是下列其中一個值的列舉: `UITraitCollection`

- **Light** -已選取 light UI 主題。
- **深色**-已選取深色 UI 主題。
- **未指定**-尚未對螢幕顯示此視圖, 因此目前的 UI 主題不明。

此外, 特徵集合在 tvOS 10 中具有下列功能:

- 外觀 proxy 可以根據`UserInterfaceStyle`指定`UITraitCollection`的來自訂, 以根據主題變更影像或專案色彩之類的事物。
- TvOS 應用程式可以藉由覆寫`TraitCollectionDidChange` `UIView`或`UIViewController`類別的方法, 來處理特性集合變更。

> [!IMPORTANT]
> TvOS 10 的 tvOS 早期預覽版本`UIUserInterfaceStyle` `UITraitCollection`尚不完整支援。 在未來的版本中將會新增完整支援。




<a name="Customizing-Appearance-Based-on-Theme" />

### <a name="customizing-appearance-based-on-theme"></a>根據主題自訂外觀

對於支援外觀 proxy 的使用者介面元素, 可以根據其特性集合的 UI 主題來調整其外觀。 因此, 針對特定的 UI 專案, 開發人員可以為淺色主題指定一個色彩, 並為深色主題指定另一個色彩。

```csharp
button.SetTitleColor (UIColor.Red, UIControlState.Normal);

// TODO - Pseudocode because this isn't currently supported in the preview bindings.
var light = new UITraitCollection(UIUserInterfaceStyle.Light);
var dark = new UITraitCollection(UIUserInterfaceStyle.Dark);

button.ForTraitCollection(light).SetTitleColor (UIColor.Red, UIControlState.Normal);
button.ForTraitCollection(dark).SetTitleColor (UIColor.White, UIControlState.Normal);
```

> [!IMPORTANT]
> 可惜的是, 適用于 tvOS 10 的 tvOS Preview 並不`UIUserInterfaceStyle`完全`UITraitCollection`支援, 因此這種類型的自訂功能尚無法使用。 在未來的版本中將會新增完整支援。

<a name="Responding-to-Theme-Changes-Directly" />

### <a name="responding-to-theme-changes-directly"></a>直接回應主題變更

在開發人員中, 需要根據選取的 ui 主題, 更深入地控制 ui 元素的外觀, 他們可以覆`TraitCollectionDidChange`寫`UIView`或`UIViewController`類別的方法。

例如：

```csharp
public override void TraitCollectionDidChange (UITraitCollection previousTraitCollection)
{
    base.TraitCollectionDidChange (previousTraitCollection);

    // Take action based on the Light or Dark theme
    ...
}
```

<a name="Responding-to-Theme-Changes-Directly" />

### <a name="overriding-a-trait-collection"></a>覆寫特性集合

根據 tvOS 應用程式的設計, 開發人員有時可能需要覆寫指定使用者介面專案的特性集合, 並讓它一律使用特定的 UI 主題。

這可以在`UIViewController`類別上使用`SetOverrideTraitCollection`方法來完成。 例如：

```csharp
// Create new trait and configure it
var trait = new UITraitCollection ();
...

// Apply new trait collection
SetOverrideTraitCollection (trait, this);
```

如需詳細資訊, 請參閱整合分鏡指令檔[簡介](~/ios/user-interface/storyboards/unified-storyboards.md)的[特性](~/ios/user-interface/storyboards/unified-storyboards.md)和覆[寫特性](~/ios/user-interface/storyboards/unified-storyboards.md)章節。

<a name="Trait-Collections-and-Storyboards" />

### <a name="trait-collections-and-storyboards"></a>特性集合和分鏡腳本

在 tvOS 10 中, 應用程式的腳本可設定為回應特性集合, 而許多 UI 元素則可感知淺色與深色主題。 TvOS 10 的目前 tvOS 早期預覽不支援介面設計工具中的這項功能, 因此必須在 Xcode 的 Interface Builder 中編輯腳本, 以因應其因應措施。

若要啟用特性集合支援, 請執行下列動作:

1. 以滑鼠右鍵按一下**方案總管**中的分鏡腳本檔案, 然後選取 **開啟方式** >  **Xcode Interface Builder**:

    [![](user-interface-styles-images/theme05.png "使用 Xcode 開啟 Interface Builder")](user-interface-styles-images/theme05.png#lightbox)
2. 若要啟用特性集合支援, 請切換至 [檔案偵測**器**], 並選取 [ **Interface Builder**檔] 區段中的 [**使用特性變化**] 屬性:

    [![](user-interface-styles-images/theme06.png "啟用特性集合支援")](user-interface-styles-images/theme06.png#lightbox)
3. 確認變更以使用特性變化:

    [![](user-interface-styles-images/theme07.png "使用特徵變化警示")](user-interface-styles-images/theme07.png#lightbox)
4. 將變更儲存至分鏡腳本檔案。

在 Interface Builder 中編輯 tvOS 分鏡腳本時, Apple 已加入下列功能:

* 開發人員可以根據**屬性偵測器**中的 UI 主題, 指定不同的使用者介面元素變化:

  * 現在有數個屬性 **+** 旁邊的可以按一下以新增 UI 佈景主題特定版本：

    [![](user-interface-styles-images/theme08.png "新增 UI 主題特定版本")](user-interface-styles-images/theme08.png#lightbox)

  * 開發人員可以指定新的屬性, 或按一下 [ **x** ] 按鈕將它移除:

    [![](user-interface-styles-images/theme09.png "指定新的屬性, 或按一下 [x] 按鈕將它移除")](user-interface-styles-images/theme09.png#lightbox)
* 開發人員可以在 Interface Builder 中, 預覽淺色或深色主題中的 UI 設計:

  * Design Surface 的底部可讓開發人員切換目前的 UI 主題:

    [![](user-interface-styles-images/theme10.png "Design Surface 的底部")](user-interface-styles-images/theme10.png#lightbox)

  * 新的主題會顯示在 Interface Builder 中, 而且會顯示任何特性集合特定的調整:

    [![](user-interface-styles-images/theme11.png "顯示在 Interface Builder 中的主題")](user-interface-styles-images/theme11.png#lightbox)

此外, tvOS 模擬器現在具有鍵盤快速鍵, 可讓開發人員在 tvOS 應用程式時, 快速切換淺色和深色主題。 使用**命令-Shift-D**鍵盤順序, 在淺色與深色之間切換。

<a name="Summary" />

## <a name="summary"></a>總結

本文涵蓋 Apple 已新增至 tvOS 10 的 Light 和深色 UI 主題, 以及如何在 tvOS 應用程式中加以執行。



## <a name="related-links"></a>相關連結

- [tvOS 範例](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [TvOS 10 的新功能](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewinTVOS/Articles/tvOS10.html#//apple_ref/doc/uid/TP40017259-SW1)
