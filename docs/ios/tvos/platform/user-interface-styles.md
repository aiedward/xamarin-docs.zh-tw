---
title: tvOS 在 Xamarin 中的使用者介面樣式
description: 本文章涵蓋光源和深色 UI 佈景主題，Apple 已加入 tvOS 10，以及如何實作它們的 Xamarin.tvOS 應用程式中。
ms.prod: xamarin
ms.assetid: 8BC37683-AD9E-45CD-BE40-96965618AD1D
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 2536ca5d3bff3f5b7962bc4fcf58b31a130fd03c
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61270829"
---
# <a name="tvos-user-interface-styles-in-xamarin"></a>tvOS 在 Xamarin 中的使用者介面樣式

_本文章涵蓋光源和深色 UI 佈景主題，Apple 已加入 tvOS 10，以及如何實作它們的 Xamarin.tvOS 應用程式中。_

tvOS 10 現在支援暗色調 」 和 「 Light 使用者介面佈景主題，所有的內建 UIKit 控制項將會自動調整，根據使用者的喜好設定。 此外，開發人員可以手動調整使用者所選取的佈景主題為基礎的 UI 元素，而且可以覆寫指定的佈景主題。

<a name="About-the-New-User-Interface-Styles" />

## <a name="about-the-new-user-interface-styles"></a>有關新的使用者介面樣式

如上所述，tvOS 10 現在支援暗色調 」 和 「 Light 使用者介面佈景主題，所有的內建 UIKit 控制項將會自動調整，就會根據使用者的喜好設定。

使用者可以移至切換此佈景主題**設定** > **一般** > **外觀**以及切換**光線**並**深色**:

[![](user-interface-styles-images/theme01.png "設定應用程式")](user-interface-styles-images/theme01.png#lightbox)

當**深色**佈景主題選取，則所有的使用者介面項目會切換為 深色背景的淺文字：

[![](user-interface-styles-images/theme02.png "深色佈景主題")](user-interface-styles-images/theme02.png#lightbox)

使用者可以選擇隨時切換佈景主題，並根據目前的活動，Apple TV 所在的位置或一天的時間可能會執行。

Light UI 佈景主題的預設佈景主題，而任何現有的 tvOS 應用程式仍會使用淺色佈景主題，不論使用者的喜好設定，除非 tvOS 10，以善用暗色調佈景主題的修改。 TvOS 10 應用程式也能夠覆寫目前的佈景主題，並一律針對部分或所有其 UI 中使用的淺 」 或 「 暗色調佈景主題。

<a name="Adopting-the-Light-and-Dark-Themes" />

## <a name="adopting-the-light-and-dark-themes"></a>採用淺色與深色佈景主題

若要支援這項功能，Apple 已新增一個新的 API，可`UITraitCollection`類別和 tvOS 應用程式必須 opt-in 以支援黑暗的外觀 (透過中設定其`Info.plist`檔案)。

若要選擇淺色調和暗色調佈景主題支援，執行下列作業：

1. 在方案總管中按兩下 `Info.plist` 檔案將其開啟以進行編輯。
2. 選取 **來源**檢視 （從編輯器的底部）。
3. 加入新的金鑰，並呼叫它`UIUserInterfaceStyle`: 

    [![](user-interface-styles-images/theme03.png "UIUserInterfaceStyle 金鑰")](user-interface-styles-images/theme03.png#lightbox)
4. 保持設為類型`String`並輸入值為`Automatic`: 

    [![](user-interface-styles-images/theme04.png "輸入自動")](user-interface-styles-images/theme04.png#lightbox)
5. 將變更儲存到檔案。

有三個可能的值，如`UIUserInterfaceStyle`機碼：

- **Light** -強制一定要使用淺色佈景主題的 tvOS 應用程式的 UI。
- **深色**-強制一定要使用深色佈景主題的 tvOS 應用程式的 UI。
- **自動**-淺色調和暗色調佈景主題之間切換會根據使用者的喜好設定。 這是慣用的設定。

<a name="UIKit-Theme-Support" />

### <a name="uikit-theme-support"></a>UIKit 佈景主題支援

如果 tvOS 應用程式使用標準的內建`UIView`型控制項，它們會自動將回應 UI 佈景主題，而不需任何開發人員介入。

此外，`UILabel`和`UITextView`會自動將變更根據選取的 UI 佈景主題色彩：

- 文字會是淺色佈景主題的黑色。
- 文字會靠深色的佈景主題的白色。

如果開發人員會手動變更文字色彩 （無論是在分鏡腳本或程式碼），他們會負責處理 UI 佈景主題為基礎的色彩變更。

<a name="New-Blur-Effects" />

### <a name="new-blur-effects"></a>新的柔邊效果

TvOS 10 應用程式中支援的淺色調和暗色調佈景主題，Apple 已新增兩個新的模糊效果。 這些新的效果會自動調整柔邊的使用者，如下所示選取 UI 佈景主題為基礎：

- `UIBlurEffectStyleRegular` -使用淺色佈景主題 」 和 「 黑暗中的光線模糊模糊深色的佈景主題。
- `UIBlurEffectStyleProminent` -使用淺色佈景主題的 extra-light 模糊和深色佈景主題的額外深色柔邊。

<a name="Working-with-Trait-Collections" />

## <a name="working-with-trait-collections"></a>使用特性集合

新`UserInterfaceStyle`的屬性`UITraitCollection`類別可用來取得目前選取的 UI 佈景主題，因此`UIUserInterfaceStyle`列舉的下列值之一：

- **Light** -已選取 Light UI 佈景主題。
- **深色**-已選取深色 UI 佈景主題。
- **未指定**-檢視已經不顯示螢幕，讓目前的 UI 佈景主題為未知。

此外，特性集合會 tvOS 10 中，有下列功能：
 
- 您可以根據自訂外觀 proxy`UserInterfaceStyle`的指定`UITraitCollection`變更映像等項目或項目色彩佈景主題為基礎。
- TvOS 應用程式可以藉由覆寫處理特性集合變更`TraitCollectionDidChange`方法`UIView`或`UIViewController`類別。

> [!IMPORTANT]
> TvOS 10 Xamarin.tvOS 早期預覽並不完全支援`UIUserInterfaceStyle`針對`UITraitCollection`尚未。 將在未來版本中加入完整的支援。




<a name="Customizing-Appearance-Based-on-Theme" />

### <a name="customizing-appearance-based-on-theme"></a>自訂佈景主題為基礎的外觀

對於支援外觀 proxy 的使用者介面項目，其外觀可以加以調整 UI 佈景主題，其特性的集合。 因此，對於給定的 UI 項目中，開發人員可以指定一個淺色佈景主題色彩和深色佈景主題的另一種色彩。

```csharp
button.SetTitleColor (UIColor.Red, UIControlState.Normal);

// TODO - Pseudocode because this isn't currently supported in the preview bindings.
var light = new UITraitCollection(UIUserInterfaceStyle.Light);
var dark = new UITraitCollection(UIUserInterfaceStyle.Dark);

button.ForTraitCollection(light).SetTitleColor (UIColor.Red, UIControlState.Normal);
button.ForTraitCollection(dark).SetTitleColor (UIColor.White, UIControlState.Normal);
```

> [!IMPORTANT]
> 不幸的是，並不完全支援 tvOS 10 Xamarin.tvOS 預覽`UIUserInterfaceStyle`針對`UITraitCollection`，因此這種類型的自訂尚無法使用。 將在未來版本中加入完整的支援。

<a name="Responding-to-Theme-Changes-Directly" />

### <a name="responding-to-theme-changes-directly"></a>直接回應佈景主題變更

在開發人員都需要選取 UI 佈景主題為基礎的 UI 項目的外觀更深入的控制權，他們可以覆寫`TraitCollectionDidChange`方法`UIView`或`UIViewController`類別。

例如: 

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

根據 tvOS 應用程式的設計，有可能的有時候開發人員需要覆寫指定的使用者介面項目的特性集合，並讓它一律使用特定的 UI 佈景主題。

這可以使用`SetOverrideTraitCollection`方法`UIViewController`類別。 例如：

```csharp
// Create new trait and configure it
var trait = new UITraitCollection ();
...

// Apply new trait collection
SetOverrideTraitCollection (trait, this);
```

如需詳細資訊，請參閱[Traits](~/ios/user-interface/storyboards/unified-storyboards.md)並[覆寫特性](~/ios/user-interface/storyboards/unified-storyboards.md)的章節我們[統一的分鏡腳本簡介](~/ios/user-interface/storyboards/unified-storyboards.md)文件。

<a name="Trait-Collections-and-Storyboards" />

### <a name="trait-collections-and-storyboards"></a>特性集合和分鏡腳本

TvOS 10，在應用程式的分鏡腳本可以設定為回應特性集合，以及許多 UI 項目可以察覺淺色調和暗色調佈景主題。 TvOS 10 的目前的 Xamarin.tvOS 早期預覽尚不支援這項功能在介面設計工具中，因此將分鏡腳本必須在因應措施的 Xcode 的 Interface Builder 中編輯。

若要啟用特性集合支援，請執行下列作業：

1. 中的分鏡腳本檔案上按一下滑鼠右鍵**方案總管**，然後選取**開啟** > **Xcode Interface Builder**: 

    [![](user-interface-styles-images/theme05.png "Xcode 介面產生器開啟")](user-interface-styles-images/theme05.png#lightbox) 
2. 若要啟用特性集合支援，請切換到**檔案偵測器**並查看**使用特徵變化**中的屬性**介面產生器文件**區段： 

    [![](user-interface-styles-images/theme06.png "啟用特性集合支援")](user-interface-styles-images/theme06.png#lightbox)
3. 確認使用特性變化的變更： 

    [![](user-interface-styles-images/theme07.png "使用特徵變化警示")](user-interface-styles-images/theme07.png#lightbox)
4. 分鏡腳本檔案中儲存的變更。

編輯 tvOS 介面產生器中的分鏡腳本時，Apple 已新增下列功能：

* 開發人員可以指定使用者介面項目中的 UI 佈景主題為基礎的不同變化**屬性偵測器**:
    
    * 現在有數個屬性 **+** 旁邊的可以按一下以新增 UI 佈景主題特定版本： 

        [![](user-interface-styles-images/theme08.png "新增 UI 佈景主題特定版本")](user-interface-styles-images/theme08.png#lightbox) 
    
    * 開發人員可以指定新的屬性，或按一下**x**  按鈕，將其移除： 

        [![](user-interface-styles-images/theme09.png "指定新的屬性，或按一下 [x] 按鈕，將它移除")](user-interface-styles-images/theme09.png#lightbox)
* 開發人員可以在預覽中的淺 」 或 「 暗色調佈景主題與 Interface Builder 中的 UI 設計：
    
    * 在設計介面底部可讓開發人員若要切換目前的 UI 佈景主題： 

        [![](user-interface-styles-images/theme10.png "設計介面底部")](user-interface-styles-images/theme10.png#lightbox)
        
    * 新的佈景主題會顯示在 介面產生器，而且會顯示任何特性集合特定調整： 

        [![](user-interface-styles-images/theme11.png "在介面產生器中顯示佈景主題")](user-interface-styles-images/theme11.png#lightbox)

此外，tvOS 模擬器現在已可讓開發人員快速在淺色調和暗色調佈景主題當中的 tvOS 應用程式進行偵錯時切換鍵盤快速鍵。 使用**命令 Shift D**鍵盤來切換淺色調和暗色調的順序。

<a name="Summary" />

## <a name="summary"></a>總結

這篇文章已涵蓋光源和深色 UI 佈景主題，Apple 已加入 tvOS 10，以及如何實作它們的 Xamarin.tvOS 應用程式中。



## <a name="related-links"></a>相關連結

- [tvOS 範例](https://developer.xamarin.com/samples/tvos/all/)
- [TvOS 10 中最新消息](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewinTVOS/Articles/tvOS10.html#//apple_ref/doc/uid/TP40017259-SW1)
