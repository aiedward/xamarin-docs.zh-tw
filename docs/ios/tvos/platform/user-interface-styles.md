---
title: "新的使用者介面樣式"
description: "本文章涵蓋淺色且深色 UI 佈景主題的 Apple 已新增至 tvOS 10 且 Xamarin.tvOS 應用程式中實作方式。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 8BC37683-AD9E-45CD-BE40-96965618AD1D
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: b970ff3570079c0e9fba10718ddcd75fbe857f2c
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/09/2018
---
# <a name="new-user-interface-styles"></a>新的使用者介面樣式

_本文章涵蓋淺色且深色 UI 佈景主題的 Apple 已新增至 tvOS 10 且 Xamarin.tvOS 應用程式中實作方式。_

tvOS 10 現在支援所有內建 UIKit 控制項將會自動同時深色和淺色使用者介面主題會隨著調整，依據使用者的喜好設定。 此外，開發人員可以手動調整 UI 項目，根據使用者選取的佈景主題，而且可以覆寫指定的佈景主題。


<a name="About-the-New-User-Interface-Styles" />

## <a name="about-the-new-user-interface-styles"></a>有關新的使用者介面樣式

如前所述，tvOS 10 現在支援所有內建 UIKit 控制項將會自動同時深色和淺色使用者介面主題會隨著調整，就會根據使用者的喜好設定。

使用者可以切換此佈景主題，請前往**設定** > **一般** > **外觀**和切換**Light**和**深色**:

[![](user-interface-styles-images/theme01.png "設定應用程式")](user-interface-styles-images/theme01.png#lightbox)

當**深色**佈景主題選取，則所有的使用者介面項目就會切換成深色背景的淺文字：

[![](user-interface-styles-images/theme02.png "深色佈景主題")](user-interface-styles-images/theme02.png#lightbox)

使用者可以選擇隨時切換佈景主題，並因此根據目前的活動，Apple TV 所在的位置或一天的時間可能會執行。

Light UI 是以預設佈景主題和任何現有的 tvOS 應用程式仍會使用淺色佈景主題中，不論使用者的喜好設定，除非 tvos 10，以善用暗色調佈景主題會修改它們。 TvOS 10 應用程式也能夠覆寫目前的佈景主題，以及常用的淺 」 或 「 暗色調佈景主題的部分或所有其 UI。

<a name="Adopting-the-Light-and-Dark-Themes" />

## <a name="adopting-the-light-and-dark-themes"></a>採用淺色與深色佈景主題

若要支援這項功能，Apple 已新增至新的 API`UITraitCollection`類別和 tvOS 應用程式必須選擇加入以支援深色的外觀 (透過設定其`Info.plist`檔案)。

若要選擇加入以淺色調和暗色調佈景主題支援，執行下列作業：

1. 在方案總管中按兩下 `Info.plist` 檔案將其開啟以進行編輯。
2. 選取**來源**檢視 （從編輯器的底部）。
3. 加入新的金鑰，並呼叫它`UIUserInterfaceStyle`: 

    [![](user-interface-styles-images/theme03.png "UIUserInterfaceStyle 金鑰")](user-interface-styles-images/theme03.png#lightbox)
4. 保持類型設定為`String`和輸入的值為`Automatic`: 

    [![](user-interface-styles-images/theme04.png "輸入自動")](user-interface-styles-images/theme04.png#lightbox)
5. 將變更儲存到檔案。

有三個可能的值為`UIUserInterfaceStyle`機碼：

- **Light** -強制 tvOS 應用程式的 UI 要一律使用淺色佈景主題。
- **深色**-強制 tvOS 應用程式的 UI 要一律使用 暗色調佈景主題。
- **自動**-根據使用者的喜好設定中設定的淺色調和暗色調佈景主題之間切換。 這是慣用的設定。

<a name="UIKit-Theme-Support" />

### <a name="uikit-theme-support"></a>UIKit 佈景主題支援

如果 tvOS 應用程式使用標準的內建`UIView`型控制項，它們會自動將回應不需要開發人員介入的 UI 佈景主題。

此外，`UILabel`和`UITextView`會自動將根據選取的 UI 佈景主題色彩變更：

- 文字會是黑色淺色佈景主題。
- 文字會白色深色的佈景主題。

如果開發人員變更文字色彩手動 （無論是在分鏡腳本或程式碼），就會負責處理根據 UI 佈景主題色彩變更。

<a name="New-Blur-Effects" />

### <a name="new-blur-effects"></a>新的柔邊效果

對於支援在淺色調和暗色調佈景主題當中 tvOS 10 應用程式中，Apple 已加入兩個新的模糊效果。 這些新的效果會自動調整柔邊根據使用者選取，如下所示的 UI 佈景主題：

- `UIBlurEffectStyleRegular` -使用光線模糊淺色佈景主題和在深色模糊深色的佈景主題。
- `UIBlurEffectStyleProminent` -使用額外淺色模糊淺色佈景主題和額外深色模糊深色的佈景主題。

<a name="Working-with-Trait-Collections" />

## <a name="working-with-trait-collections"></a>使用特性集合

新`UserInterfaceStyle`屬性`UITraitCollection`類別可以用來取得目前選取的 UI 佈景主題，並將`UIUserInterfaceStyle`列舉的下列值之一：

- **Light** -已選取 淺 UI 佈景主題。
- **深色**-已選取深色 UI 佈景主題。
- **未指定**-檢視已經不顯示螢幕上，因此目前的 UI 佈景主題是未知。

此外，特性集合會 tvOS 10 中，有下列功能：
 
- 您可以根據自訂外觀 proxy`UserInterfaceStyle`的給定`UITraitCollection`變更項目例如影像或項目根據佈景主題色彩。
- TvOS 應用程式可以藉由覆寫處理特性集合變更`TraitCollectionDidChange`方法`UIView`或`UIViewController`類別。

> [!IMPORTANT]
> **注意：** Xamarin.tvOS 早期預覽 tvos 10 未完全支援`UIUserInterfaceStyle`如`UITraitCollection`尚未。 在未來版本中，將會加入完整的支援。




<a name="Customizing-Appearance-Based-on-Theme" />

### <a name="customizing-appearance-based-on-theme"></a>自訂佈景主題的外觀

針對支援外觀 proxy 的使用者介面項目，其外觀可以調整根據 UI 佈景主題，其特性的集合。 因此，對於給定的 UI 項目中，開發人員可以指定一個淺色佈景主題色彩和暗色調佈景主題的另一種色彩。

```csharp
button.SetTitleColor (UIColor.Red, UIControlState.Normal);

// TODO - Pseudocode because this isn't currently supported in the preview bindings.
var light = new UITraitCollection(UIUserInterfaceStyle.Light);
var dark = new UITraitCollection(UIUserInterfaceStyle.Dark);

button.ForTraitCollection(light).SetTitleColor (UIColor.Red, UIControlState.Normal);
button.ForTraitCollection(dark).SetTitleColor (UIColor.White, UIControlState.Normal);
```

> [!IMPORTANT]
> **注意：**不幸的是，未完全支援 Xamarin.tvOS 預覽 tvos 10`UIUserInterfaceStyle`如`UITraitCollection`，因此這種類型的自訂尚無法使用。 在未來版本中，將會加入完整的支援。

<a name="Responding-to-Theme-Changes-Directly" />

### <a name="responding-to-theme-changes-directly"></a>直接回應佈景主題變更

在開發人員需要更深入控制 UI 項目的外觀根據選取的 UI 佈景主題，它們可以覆寫`TraitCollectionDidChange`方法`UIView`或`UIViewController`類別。

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

根據 tvOS 應用程式的設計，有些時候可能會當開發人員必須覆寫任何指定的使用者介面項目的特性集合，並讓它永遠使用特定的 UI 佈景主題。

這可以使用`SetOverrideTraitCollection`方法`UIViewController`類別。 例如: 

```csharp
// Create new trait and configure it
var trait = new UITraitCollection ();
...

// Apply new trait collection
SetOverrideTraitCollection (trait, this);
```

如需詳細資訊，請參閱[特性](~/ios/user-interface/storyboards/unified-storyboards.md)和[覆寫特性](~/ios/user-interface/storyboards/unified-storyboards.md)區段我們[簡介統一分鏡腳本](~/ios/user-interface/storyboards/unified-storyboards.md)文件。

<a name="Trait-Collections-and-Storyboards" />

### <a name="trait-collections-and-storyboards"></a>特性集合和分鏡腳本

在 tvOS 10，應用程式的分鏡腳本可以設定為回應特性集合，許多 UI 項目可以察覺淺色與深色佈景主題。 目前 Xamarin.tvOS 早期預覽 tvos 10 尚不支援這項功能在介面設計工具中，因此必須在 Xcode 的因應措施的介面產生器中編輯分鏡腳本。

若要啟用特性集合支援，執行下列作業：

1. 以滑鼠右鍵按一下中的分鏡腳本檔案**方案總管 中**選取**開啟** > **Xcode 介面產生器**: 

    [![](user-interface-styles-images/theme05.png "Xcode 介面產生器開啟")](user-interface-styles-images/theme05.png#lightbox) 
2. 若要啟用特性集合支援，請切換到**檔案 Inspector**並檢查**使用特性變化**屬性**介面產生器文件**> 一節： 

    [![](user-interface-styles-images/theme06.png "啟用特性集合支援")](user-interface-styles-images/theme06.png#lightbox)
3. 確認變更為使用特性變化： 

    [![](user-interface-styles-images/theme07.png "使用特性變化警示")](user-interface-styles-images/theme07.png#lightbox)
4. 儲存分鏡腳本檔案的變更。

編輯 tvOS 介面產生器中的分鏡腳本時，Apple 已新增下列功能：

* 開發人員可以指定不同的使用者介面項目，根據 UI 佈景主題中的變化**屬性偵測器**:
    
    * 現在有數個屬性 **+** 旁邊按一下就可加入 UI 佈景主題特定版本的： 

        [![](user-interface-styles-images/theme08.png "加入 UI 佈景主題特定版本")](user-interface-styles-images/theme08.png#lightbox) 
    
    * 開發人員可以指定新的屬性，或按一下**x**按鈕將它移除： 

        [![](user-interface-styles-images/theme09.png "指定新的屬性，或按一下 [x] 按鈕，將它移除")](user-interface-styles-images/theme09.png#lightbox)
* 開發人員可以預覽的淺 」 或 「 暗色調佈景主題的介面產生器中的 UI 設計：
    
    * 在設計介面底部可讓開發人員若要切換目前的 UI 佈景主題： 

        [![](user-interface-styles-images/theme10.png "設計介面底部")](user-interface-styles-images/theme10.png#lightbox)
        
    * 介面產生器中會顯示新的佈景主題和任何特定特性集合的調整將會顯示： 

        [![](user-interface-styles-images/theme11.png "佈景主題的介面產生器中顯示")](user-interface-styles-images/theme11.png#lightbox)

此外，tvOS 模擬器現在皆具有鍵盤快速鍵可讓開發人員在淺色調和暗色調佈景主題當中 tvOS 應用程式進行偵錯時之間快速切換。 使用**命令 Shift D**鍵盤來切換淺色調和暗色調的順序。

<a name="Summary" />

## <a name="summary"></a>總結

這篇文章已涵蓋淺色且深色 UI 佈景主題的 Apple 已新增至 tvOS 10 且 Xamarin.tvOS 應用程式中實作方式。



## <a name="related-links"></a>相關連結

- [tvOS 範例](https://developer.xamarin.com/samples/tvos/all/)
- [在 tvOS 10 最新消息](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewinTVOS/Articles/tvOS10.html#//apple_ref/doc/uid/TP40017259-SW1)
