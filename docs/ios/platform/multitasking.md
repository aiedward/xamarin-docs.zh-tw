---
title: Xamarin 中 iPad 的多工
description: iOS 9 支援使用投影片或分割視圖，同時執行兩個應用程式。 它也支援影片播放圖片。
ms.prod: xamarin
ms.assetid: 0F2266D7-21FF-404D-A148-0CFDE76B12AA
ms.technology: xamarin-ios
ms.custom: xamu-video
author: davidortinau
ms.author: daortin
ms.date: 03/20/2017
ms.openlocfilehash: 76ce375e611ddc8ee21d76e7947601319bf9dba8
ms.sourcegitcommit: e27e29c14b783263e063baaa65d4eecb8dd31f57
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/21/2021
ms.locfileid: "98628939"
---
# <a name="multitasking-for-ipad-in-xamarinios"></a>Xamarin 中 iPad 的多工

_iOS 9 支援使用投影片或分割視圖，同時執行兩個應用程式。它也支援影片播放圖片。_

![分割畫面範例](multitasking-images/about02-sml.png) ![圖片圖片範例](multitasking-images/about03-sml.png)

iOS 9 針對在特定 iPad 硬體上同時執行兩個應用程式，增加多工支援。 透過下列功能可支援 iPad 的多工：

- [**滑**](#Slide-Over) 出-可讓使用者在 [滑出] 面板中暫時執行第二個 iOS 應用程式， (在畫面的右側或左邊，根據語言方向) ，其涵蓋大約25% 的主要應用程式目前正在執行。 投影片只適用于 iPad Pro、iPad Air、iPad Air 2、iPad 迷你2、iPad 迷你3或 iPad 迷你4。
- [**分割視圖**](#Split-View) -在支援的 ipad 硬體上 (ipad Air 2、ipad 迷你4和 ipad Pro 僅) ，使用者可以挑選第二個應用程式，並與目前執行中的應用程式並存執行，以進行分割螢幕模式。 使用者可以控制每個應用程式所佔用主畫面的百分比。
- [**圖片**](#Picture-in-Picture) -針對播放影片內容的應用程式，現在可以在可移動且可調整大小的視窗中播放影片，以浮動在目前正在 iOS 裝置上執行的其他應用程式。 使用者可以完全掌控此視窗的大小和位置。 圖片中的圖片僅適用于 iPad Pro、iPad Air、iPad Air 2、iPad 迷你2、iPad 迷你3或 iPad 迷你4。

在 [您的應用程式中支援多工](#Supporting-Multitasking-in-your-App)處理時，有一些需要考慮的事項，包括：

- [螢幕大小和方向](#Screen-Size-Considerations)
- [自訂硬體鍵盤快速鍵](#Custom-Hardware-Keyboard-Shortcuts)
- [資源管理](#Resource-Management-Considerations)

應用程式開發人員也可以選擇不進行 [多工](#Opting-Out-of-Multitasking)處理，包括 [停用 PIP 影片播放](#Disabling-PIP-Video-Playback)。

本文將涵蓋必要步驟，以確保您的 Xamarin iOS 應用程式能在多工環境中正確執行，或如何選擇不適合您應用程式的多工處理。

> [!VIDEO https://youtube.com/embed/GctYAozoLr8]

**適用于 iPad 的多工影片**

<a name="Multitasking-QuickStart"></a>

## <a name="multitasking-quickstart"></a>多工快速入門

若要支援 **滑動** 或 **分割視圖** ，您的應用程式必須執行下列動作：

- 針對 iOS 9 (或更高的) 建立。
- 將分鏡腳本用於其啟動畫面 (而非影像資產) 。
- 使用具有自動調整和大小類別的分鏡腳本來作為其 UI。
- 支援所有4個 iOS 裝置方向 (直向、右上角、橫向 & 橫向) 。

<a name="Multitasking"></a>

## <a name="about-multitasking-for-ipad"></a>關於 iPad 的多工

iOS 9 在 iPad 上提供新的多工功能 _，並引進_ 了 _投影片_、_分割視圖_ (iPad Air 2、iPad 迷你4和 iPad Pro，只) 和圖片。 我們將在下列各節中進一步瞭解這些功能。

<a name="Slide-Over"></a>

### <a name="slide-over"></a>滑過

[滑過] 功能可讓使用者挑選第二個應用程式，並將它顯示在小型滑動面板中，以提供快速互動。 [滑出] 面板是暫時性的，而且會在使用者再次回到使用主應用程式時關閉。

[![滑過面板](multitasking-images/about01.png)](multitasking-images/about01.png#lightbox)

要記住的重點是，使用者決定哪些應用程式會並存執行，而開發人員無法控制此進程。 因此，您需要執行幾項操作，以確保您的 Xamarin iOS 應用程式能在滑出的面板中正確執行：

- **使用 [自動調整] 和 [大小] 類別** —因為您的 Xamarin iOS 應用程式現在可以在滑出的側邊面板中執行，所以您無法再依賴裝置、螢幕大小或其方向來配置您的 UI。 為了確保您的應用程式能夠正確地調整其介面，您必須使用自動調整和大小類別。 如需詳細資訊，請參閱整合分鏡指令檔的 [簡介](~/ios/user-interface/storyboards/unified-storyboards.md) 。
- **有效率地使用資源** —因為您的應用程式現在可以與另一個正在執行的應用程式共用系統，所以您的應用程式很重要地使用系統資源。 當記憶體變成稀疏時，系統會自動終止耗用最多記憶體的應用程式。 如需詳細資訊，請參閱 Apple 的 [IOS 應用程式能源效益指南](https://developer.apple.com/library/prerelease/ios/documentation/Performance/Conceptual/EnergyGuide-iOS/index.html#//apple_ref/doc/uid/TP40015243) 。

投影片只適用于 iPad Pro、iPad Air、iPad Air 2、iPad 迷你2、iPad 迷你3或 iPad 迷你4。 若要深入瞭解如何準備您的應用程式以進行滑動，請參閱 Apple [採用 iPad 檔的多工增強功能](https://developer.apple.com/library/prerelease/ios/documentation/WindowsViews/Conceptual/AdoptingMultitaskingOniPad/index.html#//apple_ref/doc/uid/TP40015145) 。

<a name="Split-View"></a>

### <a name="split-view"></a>分割檢視

在支援的 iPad 硬體上 (iPad Air 2、iPad 迷你4和 iPad Pro 僅) ，使用者可以挑選第二個應用程式，並與目前正在執行的應用程式並排執行，以進行分割螢幕模式。 使用者可以藉由拖曳螢幕上的分隔線來控制每個應用程式所占的主畫面百分比。

[![分割視圖](multitasking-images/about02.png)](multitasking-images/about02.png#lightbox)

如同滑過，使用者會決定哪些應用程式會並存執行，而開發人員無法控制此進程。 因此，「分割」視圖會在「Xamarin iOS 應用程式」上放置類似的需求：

- **使用自動篩選和大小類別** —因為您的 Xamarin iOS 應用程式現在可以在使用者指定大小的分割螢幕模式中執行，所以您不再依賴裝置、螢幕大小或其方向來配置 UI。 為了確保您的應用程式能夠正確地調整其介面，您必須使用自動調整和大小類別。 如需詳細資訊，請參閱整合分鏡指令檔的 [簡介](~/ios/user-interface/storyboards/unified-storyboards.md) 。
- **有效率地使用資源** —因為您的應用程式現在可以與另一個正在執行的應用程式共用系統，所以您的應用程式很重要地使用系統資源。 當記憶體變成稀疏時，系統會自動終止耗用最多記憶體的應用程式。 如需詳細資訊，請參閱 Apple 的 [IOS 應用程式能源效益指南](https://developer.apple.com/library/prerelease/ios/documentation/Performance/Conceptual/EnergyGuide-iOS/index.html#//apple_ref/doc/uid/TP40015243) 。

若要深入瞭解如何準備應用程式以進行分割，請參閱 Apple [採用 iPad 檔的多工增強功能](https://developer.apple.com/library/prerelease/ios/documentation/WindowsViews/Conceptual/AdoptingMultitaskingOniPad/index.html#//apple_ref/doc/uid/TP40015145) 。

<a name="Picture-in-Picture"></a>

### <a name="picture-in-picture"></a>圖片中的圖片

圖片功能的新圖片 (也稱為 _PIP_) 可讓使用者在小型的浮動視窗中觀看影片，讓使用者可以在其他執行中應用程式的螢幕上的任何位置進行放置。

[![圖片浮動視窗中的範例圖片](multitasking-images/about03.png)](multitasking-images/about03.png#lightbox)

如同滑過和分割視圖，使用者可以完全掌控觀賞圖片模式圖片的影片。 如果您應用程式的主要功能是觀看影片，則需要進行一些修改，才能在 PIP 模式中正確運作。 否則，就不需要進行任何變更，就能支援 PIP。

若要讓您的應用程式在使用者的要求中顯示 PIP 影片，您必須使用 _AVKit_ 或 _AV 基礎 api_。 Media Player framework 已在 iOS 9 中進行過折舊，且不支援 PIP。

圖片中的圖片僅適用于 iPad Pro、iPad Air、iPad Air 2、iPad 迷你2、iPad 迷你3或 iPad 迷你4。 如需詳細資訊，請參閱我們的 [PictureInPicture 範例應用程式](/samples/browse/?products=xamarin&term=Xamarin.iOS%2biOS9) 和 Apple 的 [圖片快速入門](https://developer.apple.com/library/prerelease/ios/documentation/WindowsViews/Conceptual/AdoptingMultitaskingOniPad/QuickStartForPictureInPicture.html#//apple_ref/doc/uid/TP40015145-CH14) 檔。

<a name="Supporting-Multitasking-in-your-App"></a>

## <a name="supporting-multitasking-in-your-app"></a>在您的應用程式中支援多工

針對任何現有的 Xamarin iOS 應用程式，只要您的應用程式已遵循 Apple 的設計指南和 iOS 8 的最佳作法，就可以支援多工處理。 這表示應用程式應該針對其消費者介面配置使用具有自動調整和大小類別的分鏡腳本 (如需詳細資訊，請參閱我們的整合分鏡腳本 [簡介](~/ios/user-interface/storyboards/unified-storyboards.md)) 。

針對這些應用程式，您幾乎不需要進行任何變更，就能支援多工處理，並在其內正常運作。 如果您的應用程式 UI 是使用其他方法建立，例如在 c # 程式碼中直接定位和調整大小的 UI 元素，或者它依賴特定的裝置螢幕大小或方向，則需要經過大量修改才能正確支援 iOS 9 多工。

若要在任何新的 Xamarin iOS 應用程式上支援 iOS 9 多工處理，請再次針對所有應用程式的消費者介面版面配置使用具有自動調整和大小類別的分鏡腳本，並執行下列各節中的指示。

<a name="Screen-Size-Considerations"></a>

### <a name="screen-size-and-orientation-considerations"></a>螢幕大小和方向考慮

在 iOS 9 之前，您可以根據特定裝置螢幕大小和方向來設計您的應用程式。 由於應用程式現在可以在 [滑出] 面板或 [分割視圖] 模式中執行，因此它可以在 iPad 上的 compact 或一般水準大小類別中找到自己的執行狀態，而不論裝置的實體方向或螢幕大小。

[![螢幕大小和方向考慮](multitasking-images/sizeclasses01.png)](multitasking-images/sizeclasses01.png#lightbox)

在 iPad 上，全螢幕應用程式具有一般水準和垂直大小的類別。 所有 Iphone 但 iPhone 6 Plus 和 iPhone 6s Plus 都有各種方向的精簡型類別。 在橫向模式中，iPhone 6 Plus 和 iPhone 6s Plus 具有一般的水準大小類別和精簡型垂直大小類別， (與 iPad 迷你) 很類似。

在支援滑過和分割視圖的 Ipad 上，您最後可以使用下列組合：

| **Orientation** | **主要應用程式** | **次要應用程式** |
|--- |--- |--- |
| **直向** |75% 的螢幕<br />水準壓縮<br />一般垂直|25% 的螢幕<br />水準壓縮<br />一般垂直|
| **橫向** |75% 的螢幕<br />一般水準<br />一般垂直|25% 的螢幕<br />水準壓縮<br />一般垂直|
| **橫向** |50% 的螢幕<br />水準壓縮<br />一般垂直|50% 的螢幕<br />水準壓縮<br />一般垂直|

在範例 [MuliTask](/samples/xamarin/ios-samples/ios9-multitask) 應用程式中，如果以橫向模式在 iPad 上以全螢幕執行，則會同時顯示清單和詳細資料檢視：

[![同時顯示清單和詳細資料檢視](multitasking-images/sizeclasses03.png)](multitasking-images/sizeclasses03.png#lightbox)

如果相同的應用程式是在滑過的面板中執行，則會將其配置為 Compact 水準大小類別，並只顯示清單：

[![只有當裝置水準時所顯示的清單](multitasking-images/sizeclasses04.png)](multitasking-images/sizeclasses04.png#lightbox)

為了確保您的應用程式在這些情況下正確運作，您應該採用特性集合以及大小類別，並符合 `IUIContentContainer` 和 `IUITraitEnvironment` 介面。 如需詳細資訊，請參閱 Apple 的 [UITraitCollection 類別參考](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UITraitCollection_ClassReference/index.html#//apple_ref/doc/uid/TP40014202) ，以及我們的統一分鏡腳本指南 [簡介](~/ios/user-interface/storyboards/unified-storyboards.md) 。

此外，您不能再依賴 [裝置] 畫面界限來定義應用程式的可見區域，您將必須改為使用應用程式的視窗界限。 由於視窗界限完全受到使用者的控制，因此您無法以程式設計方式調整它們，或防止使用者變更這些界限。

最後，您的應用程式必須使用分鏡腳本檔案來呈現其啟動畫面，而不是使用一組 **.png** 影像檔案，並支援所有四個介面方向 (直向、向下、向左和向右) ，以在 [滑下] 面板或 [分割視圖] 模式中執行。

<a name="Custom-Hardware-Keyboard-Shortcuts"></a>

### <a name="custom-hardware-keyboard-shortcuts"></a>自訂硬體鍵盤快速鍵

在 iPad 上執行的 iOS 9 中，Apple 已擴充對硬體鍵盤的支援。 Ipad 一律包含透過藍牙的基本外部鍵盤支援，以及某些鍵盤製造商建立的鍵盤，其中包含硬式有線 iOS 特定的金鑰。

現在，有了 iOS 9 之後，應用程式就可以建立自己的自訂鍵盤快速鍵。 此外，還有一些基本鍵盤快速鍵可供使用，例如 **命令-C** (複製) 、 **命令 X** (剪下) 、 **命令-V** (貼上) 和 **命令轉換-H** (home) ，而不會特別寫入應用程式來回應它們。

**命令** 索引標籤會顯示應用程式切換器，可讓使用者在應用程式之間快速切換，就像 Mac 作業系統一樣：

[![應用程式切換器](multitasking-images/keyboard01.png)](multitasking-images/keyboard01.png#lightbox)

如果 iOS 9 應用程式包含鍵盤快速鍵，則使用者可以按住 **命令**、 **Option** 或 **Control** 鍵，以在快顯視窗中顯示它們：

[![螢幕擷取畫面：顯示應用程式的鍵盤快速鍵。](multitasking-images/keyboard02.png)](multitasking-images/keyboard02.png#lightbox)

#### <a name="defining-custom-keyboard-shortcuts"></a>定義自訂鍵盤快速鍵

如果我們將下列程式碼新增至應用程式中的 View 或 View 控制器，當該視圖或控制器可見時，即可使用自訂鍵盤快速鍵：

```csharp
#region Custom Keyboard Shortcut
public override bool CanBecomeFirstResponder {
    get { return true; }
}

public override UIKeyCommand[] KeyCommands {
    get {

        var keyCommand = UIKeyCommand.Create (new NSString("n"), UIKeyModifierFlags.Command, new Selector ("NewEntry"), new NSString("New Entry"));
        return new UIKeyCommand[]{ keyCommand };
    }
}

[Export("NewEntry")]
public void NewEntry() {

    // Add a new entry
    ...

}
#endregion
```

首先，我們會覆寫 `CanBecomeFirstResponder` 屬性並返回， `true` 讓 View 或 view 控制器可以接收鍵盤輸入。 

接下來，我們會覆寫 `KeyCommands` 屬性，並 `UIKeyCommand` 為 **命令-N** 擊鍵建立新的。 當擊鍵啟動時，我們 `NewEntry` 會呼叫方法 (使用 `Export` 命令) 來執行要求的動作，以公開給 iOS 9。

如果我們在附加硬體鍵盤的 iPad 上執行此應用程式，且使用者輸入 **命令-N**，則會將新專案新增至清單。 如果使用者按住 **命令** 鍵，則會顯示快捷方式清單：

[![螢幕擷取畫面：顯示應用程式的新專案快捷方式。](multitasking-images/keyboard03.png)](multitasking-images/keyboard03.png#lightbox)

請參閱範例執行 [程式](/samples/xamarin/ios-samples/ios9-multitask) 範例，以取得範例執行。

<a name="Resource-Management-Considerations"></a>

### <a name="resource-management-considerations"></a>資源管理考慮

即使是已使用 iOS 8 設計指南和最佳作法的應用程式，有效率的資源管理還是可能仍會發生問題。 在 iOS 9 中，應用程式不再具有記憶體、CPU 或其他系統資源的獨佔用途。

如此一來，您就必須微調您的 Xamarin iOS 應用程式，以有效使用系統資源，或在低記憶體的情況下，將其視為終止。 這同樣適用于選擇不多多工的應用程式，因為第二個應用程式可能仍會在滑過的面板或圖片視窗中執行，而需要額外的資源，或導致重新整理率低於每秒60個畫面格。

請考慮下列使用者動作及其含意：

- 透過 **面板在投影片中輸入文字**-即使您的應用程式沒有文字輸入，系統鍵盤現在也可以透過其 UI 來顯示。 因此，應用程式可能需要回應鍵盤顯示通知 (例如顯示和隱藏鍵盤) 。
- **在 [滑過] 面板中執行第二個應用程式** -新的應用程式現在會在前景中執行，並與現有的應用程式競爭系統資源（例如記憶體和 CPU 週期）。
- **在 PIP 視窗中播放影片** -不只可以涵蓋應用程式介面的一部分，但是啟動影片的應用程式仍在背景執行，並耗用 CPU 和記憶體資源。

為了確保您的應用程式有效率地使用資源，您應該執行下列作業：

- 使用檢測功能來 **分析應用程式**，以檢查記憶體流失、明確 CPU 使用量，以及應用程式可能封鎖主執行緒的區域。
- **回應狀態轉換方法** -在您的 **AppDelegate.cs** 檔覆寫中，以及回應狀態變更方法（例如應用程式在背景中輸入或返回）。 釋放任何下一頁資產，例如影像、資料或視圖，以及 view controller。
- **並行測試與記憶體密集型應用程式** -使用隨需記憶體的應用程式，在實體 iOS 硬體上使用滑出和分割（例如在衛星視圖) 模式中 (的地圖），並測試這兩個應用程式保持回應且未損毀。

如需有關資源管理的詳細資訊，請參閱 Apple 的 [IOS 應用程式能源效益指南](https://developer.apple.com/library/prerelease/ios/documentation/Performance/Conceptual/EnergyGuide-iOS/index.html#//apple_ref/doc/uid/TP40015243) 。

<a name="Opting-Out-of-Multitasking"></a>

## <a name="opting-out-of-multitasking"></a>退出多工

雖然 Apple 建議所有 iOS 9 應用程式都支援多工處理，但應用程式可能會有很明確的原因，例如需要全螢幕才能正常運作的遊戲或相機應用程式。

若要讓您的 Xamarin iOS 應用程式選擇不是在 [滑出] 面板或 [分割視圖] 模式中執行，請編輯專案的 **plist** 檔案，然後勾選 [ **需要全螢幕**]：

[![退出多工](multitasking-images/fullscreen01.png)](multitasking-images/fullscreen01.png#lightbox)

> [!IMPORTANT]
> 雖然退出多工處理可防止您的應用程式在 [滑出] 或 [分割] 中執行，但它並不會防止另一個應用程式在 [滑出] 或 [圖片影片] 中執行，使其無法與您的應用程式一起顯示。

<a name="Disabling-PIP-Video-Playback"></a>

### <a name="disabling-pip-video-playback"></a>停用 PIP 影片播放

在大多數情況下，您的應用程式應該允許使用者播放它在圖片浮動視窗中顯示的任何影片內容。 不過，在某些情況下可能不需要這麼做，例如遊戲剪下場景影片。

若退出宣告 PIP 影片播放，請在您的應用程式中執行下列動作：

- 如果您使用 `AVPlayerViewController` 來顯示影片，請將屬性設定 `AllowsPictureInPicturePlayback` 為 `false` 。
- 如果您使用 `AVPlayerLayer` 來顯示影片，請不要具現化 `AVPictureInPictureController` 。
- 如果您使用 `WKWebView` 來顯示影片，請將屬性設定 `AllowsPictureInPictureMediaPlayback` 為 `false` 。

<a name="Summary"></a>

## <a name="summary"></a>[摘要]

本文涵蓋了確保 Xamarin iOS 應用程式能在 iOS 9 的新多工 Ipad 功能中執行並正確運作所需的步驟。 此外，它涵蓋了針對不適合的應用程式退出宣告多工處理。

## <a name="related-links"></a>相關連結

- [iOS 9 範例](/samples/browse/?products=xamarin&term=Xamarin.iOS%2biOS9)
- [ (的) 範例 ](/samples/xamarin/ios-samples/ios9-multitask)
- [整合分鏡腳本簡介](~/ios/user-interface/storyboards/unified-storyboards.md)
- [適用于開發人員的 iOS 9](https://developer.apple.com/ios/pre-release/)
- [iOS 9。0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [在 iPad 上採用多工增強功能](https://developer.apple.com/library/prerelease/ios/documentation/WindowsViews/Conceptual/AdoptingMultitaskingOniPad/index.html#//apple_ref/doc/uid/TP40015145)
- [Blog 文章](https://blog.xamarin.com/using-auto-layouts-for-ios-9-splitview/)