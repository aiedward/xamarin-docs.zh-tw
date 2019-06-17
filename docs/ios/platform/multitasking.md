---
title: 適用於 iPad 的 Xamarin.iOS 的多工
description: iOS 9 支援在相同的時間，透過使用投影片或分割檢視所執行的兩個應用程式。 它也支援的視訊播放圖片的圖片。
ms.prod: xamarin
ms.assetid: 0F2266D7-21FF-404D-A148-0CFDE76B12AA
ms.technology: xamarin-ios
ms.custom: xamu-video
author: lobrien
ms.author: laobri
ms.date: 03/20/2017
ms.openlocfilehash: 6a9c66720aeb97c5b894cbea42c6cc0c5285c67d
ms.sourcegitcommit: bf18425f97b48661ab6b775195eac76b356eeba0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/01/2019
ms.locfileid: "64978619"
---
# <a name="multitasking-for-ipad-in-xamarinios"></a>適用於 iPad 的 Xamarin.iOS 的多工

_iOS 9 支援在相同的時間，透過使用投影片或分割檢視所執行的兩個應用程式。它也支援的視訊播放圖片的圖片。_

![](multitasking-images/about02-sml.png "分割畫面範例") ![](multitasking-images/about03-sml.png "圖片中圖片的範例")

iOS 9 新增多工作業支援同時執行兩個應用程式，在特定的 iPad 的硬體上。 適用於 iPad 的多工支援透過下列功能：

- [**透過投影片**](#Slide-Over) -可讓使用者暫時面板 （無論是在 [依語言方向] 畫面的左右端），它涵蓋了大約 25%的目前執行的主要應用程式出一張投影片中執行的第二個的 iOS 應用程式。 投影片上是僅適用於 iPad Pro，iPad Air、 iPad 空氣 2、 iPad Mini 2、 iPad Mini 3 或 iPad Mini 4。
- [**分割檢視**](#Split-View) -支援的 iPad 的硬體上 (iPad 空氣 2，iPad Mini 4 和 iPad 僅限 Pro)，使用者可以挑選的第二個應用程式，並執行它並排顯示目前正在執行的應用程式，在分割畫面模式。 使用者可以控制主畫面中，每個應用程式所佔的百分比。
- [**在圖中的圖片**](#Picture-in-Picture) -播放視訊內容，現在，影片會播放可移動且可調整大小的視窗會漂浮在目前在 iOS 裝置上執行的其他應用程式中的應用程式。 使用者可以完全控制的大小和位置，此視窗。 圖片中的圖片是僅適用於 iPad Pro，iPad Air、 iPad 空氣 2、 iPad Mini 2、 iPad Mini 3 或 iPad Mini 4。

有幾個時應該考慮的事項[應用程式中支援多工作業](#Supporting-Multitasking-in-your-App)，包括：

- [螢幕大小及方向](#Screen-Size-Considerations)
- [自訂硬體鍵盤快速鍵](#Custom-Hardware-Keyboard-Shortcuts)
- [資源管理](#Resource-Management-Considerations)

身為應用程式開發人員也可以[退出多工作業](#Opting-Out-of-Multitasking)，包括[停用 PIP 視訊播放](#Disabling-PIP-Video-Playback)。

本文將介紹以確保您的 Xamarin.iOS 應用程式能夠正確執行多工作業環境，或如何退出多工作業，如果不是適合您的應用程式所需的步驟。

> [!VIDEO https://youtube.com/embed/GctYAozoLr8]

**適用於 iPad 的視訊的多工**


<a name="Multitasking-QuickStart" />

## <a name="multitasking-quickstart"></a>多工的快速入門

若要支援**投影片透過**或**分割檢視**您的應用程式必須執行下列動作：

 - 在建置 iOS 9 （或更新版本）。
 - 用於其啟動畫面上的分鏡腳本 （和不影像資產）。
 - 使用自動版面配置和大小類別將分鏡腳本用於其 UI。
 - 支援所有 4 個 iOS 裝置方向 （直向、 上下顛倒的直向、 橫向左和橫向右邊）。

<a name="Multitasking" />

## <a name="about-multitasking-for-ipad"></a>關於適用於 iPad 的多工

iOS 9 提供新推出的 iPad 上的多工能力_投影片透過_，_分割檢視_(iPad 空氣 2，iPad Mini 4 和僅限 iPad Pro) 及_Picture in Picture_。 我們在下列各節將進一步了解這些功能。

<a name="Slide-Over" />

### <a name="slide-over"></a>透過投影片

透過 [投影片] 功能可讓使用者選擇第二個應用程式，並顯示在一個小型的滑動面板，以提供快速的互動。 投影片透過面板是暫時性的和使用者會回到一次使用主要的應用程式時將會關閉。

[![](multitasking-images/about01.png "透過 [滑動] 面板")](multitasking-images/about01.png#lightbox)

請記住的重點是，使用者會決定將執行的兩個應用程式，並排顯示，而且開發人員有沒有控制這個程序。 如此一來，有您要如何確保您的 Xamarin.iOS 應用程式能夠正確執行投影片透過面板中的幾件事：

- **使用自動版面配置和大小類別**— 因為 Xamarin.iOS 應用程式現在可以執行在投影片外側邊窗格中，您可以不再依賴裝置、 螢幕大小或其方向來配置您的 UI。 若要確保您的應用程式會正確調整它的介面，您必須使用自動版面配置和大小類別。 如需詳細資訊，請參閱我們[統一的分鏡腳本簡介](~/ios/user-interface/storyboards/unified-storyboards.md)文件。
- **資源有效率地使用**— 您的應用程式可以現在系統與共用另一個執行中應用程式，因為很重要，您的應用程式可有效率地使用系統資源。 疏鬆記憶體時，系統會自動終止應用程式會耗用最多的記憶體。 請參閱 Apple [iOS 應用程式的能源效率指南](https://developer.apple.com/library/prerelease/ios/documentation/Performance/Conceptual/EnergyGuide-iOS/index.html#//apple_ref/doc/uid/TP40015243)如需詳細資訊。

投影片上是僅適用於 iPad Pro，iPad Air、 iPad 空氣 2、 iPad Mini 2、 iPad Mini 3 或 iPad Mini 4。 若要深入了解投影片上準備您的應用程式，請參閱 Apple[採用在 iPad 上的多工增強功能](https://developer.apple.com/library/prerelease/ios/documentation/WindowsViews/Conceptual/AdoptingMultitaskingOniPad/index.html#//apple_ref/doc/uid/TP40015145)文件。

<a name="Split-View" />

### <a name="split-view"></a>分割檢視

支援的 iPad 的硬體上 (iPad 空氣 2，iPad Mini 4 和 iPad 僅限 Pro)，使用者可以選擇的第二個應用程式，並執行它並排顯示目前正在執行的應用程式，在分割畫面模式。 使用者可以控制每個應用程式佔用拖曳的主畫面的百分比螢幕上分割線。

[![](multitasking-images/about02.png "分割檢視")](multitasking-images/about02.png#lightbox)

投影片上，例如使用者決定哪些兩個應用程式將會執行並排顯示，且同樣地，開發人員無法控制這個程序。 如此一來，分割檢視會將類似的需求置於 Xamarin.iOS 應用程式：

- **使用自動版面配置和大小類別**— 因為 Xamarin.iOS 應用程式現在可以在使用者的指定大小的分割畫面模式中執行，您可以不再依賴裝置、 螢幕大小或其方向來配置您的 UI。 若要確保您的應用程式會正確調整它的介面，您必須使用自動版面配置和大小類別。 如需詳細資訊，請參閱我們[統一的分鏡腳本簡介](~/ios/user-interface/storyboards/unified-storyboards.md)文件。
- **資源有效率地使用**— 您的應用程式可以現在系統與共用另一個執行中應用程式，因為很重要，您的應用程式可有效率地使用系統資源。 疏鬆記憶體時，系統會自動終止應用程式會耗用最多的記憶體。 請參閱 Apple [iOS 應用程式的能源效率指南](https://developer.apple.com/library/prerelease/ios/documentation/Performance/Conceptual/EnergyGuide-iOS/index.html#//apple_ref/doc/uid/TP40015243)如需詳細資訊。

若要深入了解您的應用程式正在準備分割檢視，請參閱 Apple[採用在 iPad 上的多工增強功能](https://developer.apple.com/library/prerelease/ios/documentation/WindowsViews/Conceptual/AdoptingMultitaskingOniPad/index.html#//apple_ref/doc/uid/TP40015145)文件。

<a name="Picture-in-Picture" />

### <a name="picture-in-picture"></a>Picture in Picture

新的圖片，圖片功能中 (也稱為_PIP_) 可讓使用者觀看的影片的小型的浮動視窗中，使用者可以在上方的其他執行的應用程式 畫面上任何位置定位。

[![](multitasking-images/about03.png "範例在圖片浮動視窗中的圖片")](multitasking-images/about03.png#lightbox)

為與投影片上分割檢視中，使用者可以完全控制觀賞圖片模式中的圖片中的影片。 如果您的應用程式的 main 函式若要觀看影片時，它會需要一些修改，以便在 PIP 模式中正確運作。 否則，任何變更，不才能支援 PIP。

若要在使用者的要求顯示 PIP 視訊應用程式中，您必須使用其中一個_AVKit_或_AV Foundation Api_。 Media Player framework 有已在 iOS 9 中停用，且不支援 PIP。

圖片中的圖片是僅適用於 iPad Pro，iPad Air、 iPad 空氣 2、 iPad Mini 2、 iPad Mini 3 或 iPad Mini 4。 如需詳細資訊，請參閱我們[PictureInPicture 範例應用程式](https://developer.xamarin.com/samples/ios/iOS9/)與 Apple[圖片快速入門中的圖片](https://developer.apple.com/library/prerelease/ios/documentation/WindowsViews/Conceptual/AdoptingMultitaskingOniPad/QuickStartForPictureInPicture.html#//apple_ref/doc/uid/TP40015145-CH14)文件。

<a name="Supporting-Multitasking-in-your-App" />

## <a name="supporting-multitasking-in-your-app"></a>在您的應用程式中支援多工作業

任何現有的 Xamarin.iOS 應用程式中，支援多工是透明的工作，只要您的應用程式已遵循 Apple 的設計指南和 iOS 8 的最佳作法。 這表示，應用程式應該使用分鏡腳本與自動版面配置和大小類別為其使用者介面版面配置 (請參閱我們[統一的分鏡腳本簡介](~/ios/user-interface/storyboards/unified-storyboards.md)如需詳細資訊)。

這些應用程式，幾乎不需要變更，才能為支援多工作業，而且也在它的行為。 如果您的應用程式的 UI，則使用其他方法，例如直接定位和調整大小的 UI 項目中的建立C#程式碼，或是如果它依賴特定裝置的螢幕大小或方向，則需要大幅的修改才能正確支援 iOS 9 多工作業。

若要在任何新的 Xamarin.iOS 應用程式支援 iOS 9 多工作業，一次對所有的應用程式的使用者介面版面配置，使用自動版面配置和大小類別使用分鏡腳本，並實作下列各節中的指示。

<a name="Screen-Size-Considerations" />

### <a name="screen-size-and-orientation-considerations"></a>螢幕大小和方向的考量

IOS 9 之前，您可以設計您的應用程式，針對特定裝置的螢幕大小和方向。 因為滑出面板中，或在分割檢視模式中，現在可以執行應用程式，它可以尋找本身在 compact 或定期的水平大小類別 iPad 上執行，無論裝置的實體方向或螢幕大小為何。

[![](multitasking-images/sizeclasses01.png "螢幕大小和方向的考量")](multitasking-images/sizeclasses01.png#lightbox)

在 iPad 上的全螢幕應用程式會有一般的水平和垂直大小類別。 所有 Iphone 但 iPhone 6 Plus 和 iPhone 6s 此外，有精簡的大小類別以任一方向的兩個方向。 IPhone 6 Plus 和 iPhone 6s Plus 以橫向模式有一個規則的水平大小類別和 Compact 的垂直大小類別 （很像 iPad Mini）。

在支援透過 投影片和分割檢視的 Ipad，您可以得到下列組合：

| **方向** | **主要的應用程式** | **次要的應用程式** |
|--- |--- |--- |
| **直向** |75%的畫面<br />Compact 的水平<br />一般的垂直|25%的畫面<br />Compact 的水平<br />一般的垂直|
| **Landscape** |75%的畫面<br />一般的水平<br />一般的垂直|25%的畫面<br />Compact 的水平<br />一般的垂直|
| **Landscape** |50%的畫面<br />Compact 的水平<br />一般的垂直|50%的畫面<br />Compact 的水平<br />一般的垂直|

在範例中[MuliTask](https://developer.xamarin.com/samples/monotouch/ios9/MultiTask/)應用程式，如果它以橫向模式 iPad 上執行全螢幕，它會顯示清單和詳細資料檢視，在相同的時間：

[![](multitasking-images/sizeclasses03.png "清單和同時呈現的詳細資料檢視")](multitasking-images/sizeclasses03.png#lightbox)

如果投影片透過面板中執行相同的應用程式時，它會做為精簡的水平大小類別配置，並顯示只有清單：

[![](multitasking-images/sizeclasses04.png "只顯示水平裝置時的清單")](multitasking-images/sizeclasses04.png#lightbox)

若要確保您的應用程式在這些情況下正確運作，您應該採用特性以及大小類別的集合，並符合`IUIContentContainer`和`IUITraitEnvironment`介面。 請參閱 Apple [UITraitCollection 類別參考](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UITraitCollection_ClassReference/index.html#//apple_ref/doc/uid/TP40014202)和我們[統一的分鏡腳本簡介](~/ios/user-interface/storyboards/unified-storyboards.md)指南以取得詳細的資訊。

此外，您可以不再依賴裝置螢幕界限來定義應用程式的可見區域，您必須改為使用您的應用程式視窗界限。 由於視窗界限完全控制的使用者，無法以程式設計方式調整它們，或防止使用者變更這些界限。

最後，您的應用程式必須使用分鏡腳本檔案來呈現而不是使用一組其 [啟動] 畫面 **.png**影像檔，並支援所有的四個介面方向 （直向上下顛倒的直向、 橫向和橫向 Right）若要被視為在投影片透過面板中，或在分割檢視模式中執行。

<a name="Custom-Hardware-Keyboard-Shortcuts" />

### <a name="custom-hardware-keyboard-shortcuts"></a>自訂硬體鍵盤快速鍵

在 iOS 9 iPad 上執行，Apple 已擴充的支援的硬體鍵盤。 Ipad 一律有包含基本的外接式鍵盤支援，透過藍芽和某些鍵盤製造商建立鍵盤包含內嵌式 iOS 專屬的金鑰。

現在，含 iOS 9，應用程式可以建立自己自訂的鍵盤快速鍵。 此外，某些基本的鍵盤快速鍵也使用類似**命令-C** （複製），**命令 X** （剪下），**命令-V** （貼上） 和**命令 Shift H** （首頁）、 未被特別撰寫的回應給他們的應用程式。

**命令索引標籤**可讓使用者從鍵盤，很像 Mac OS 的應用程式之間快速切換應用程式切換器會顯示：

[![](multitasking-images/keyboard01.png "應用程式切換器")](multitasking-images/keyboard01.png#lightbox)

IOS 9 應用程式包含了鍵盤快速鍵，如果使用者按住**命令**，**選項**或是**控制**它們顯示在快顯視窗中的索引鍵：

[![](multitasking-images/keyboard02.png "鍵盤快速鍵快顯視窗")](multitasking-images/keyboard02.png#lightbox)

#### <a name="defining-custom-keyboard-shortcuts"></a>定義自訂鍵盤快速鍵

如果我們將下列程式碼加入檢視或檢視控制器在我們的應用程式，該檢視或控制站時顯示，將可使用自訂的鍵盤快速鍵：

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

首先，我們會覆寫`CanBecomeFirstResponder`屬性，並傳回`true`讓檢視或檢視控制器可以接收鍵盤輸入。 

接下來，我們會覆寫`KeyCommands`屬性，並建立新`UIKeyCommand`for**命令 N**按鍵輸入。 啟動按鍵輸入時，我們會呼叫`NewEntry`方法 (我們公開於 iOS 9 使用`Export`命令) 執行要求的動作。

如果我們在 iPad 上執行此應用程式連接的硬體鍵盤與使用者輸入**命令 N**，新的項目會新增至清單。 如果使用者按住**命令**索引鍵、 快速鍵的清單隨即出現：

[![](multitasking-images/keyboard03.png "鍵盤快速鍵快顯視窗")](multitasking-images/keyboard03.png#lightbox)

此範例，請參閱[MultiTask 應用程式](https://developer.xamarin.com/samples/monotouch/ios9/MultiTask/)如需範例實作。

<a name="Resource-Management-Considerations" />

### <a name="resource-management-considerations"></a>資源管理考量

對於已經使用 iOS 8 的設計指南和最佳作法的應用程式，甚至是有效的資源管理仍然可能發生問題。 在 iOS 9 中，應用程式不再需要獨佔使用的記憶體、 CPU 或其他系統資源。

如此一來，您必須修改以調整您的 Xamarin.iOS 應用程式有效地使用系統資源，或它發生低記憶體情況下的終止。 這是同樣的退出多工作業的應用程式，因為第二個應用程式可能仍然以投影片透過 [控制台] 或圖片在圖片視窗需要額外的資源，或造成重新整理的頻率低於每秒 60 畫面格數。

請考慮下列的使用者動作和及其含意：

- **透過 [滑動] 面板中輸入文字**-即使您的應用程式的任何文字輸入，系統鍵盤現在顯示透過其 UI。 如此一來，應用程式可能需要回應 （例如顯示和隱藏鍵盤） 的鍵盤顯示通知。
- **執行投影片透過面板中的第二個應用程式**-新的應用程式現在在前景執行並與現有的應用程式，例如記憶體和 CPU 循環的系統資源競爭。
- **播放視訊，以在 PIP 視窗**-不只可以此視窗會涵蓋您的應用程式介面的一部分，但應用程式啟動影片仍在背景執行並耗用 CPU 和記憶體資源。

若要確保您的應用程式會有效率地使用資源，您應該執行下列作業：

- **分析應用程式使用 Instruments** -檢查是否有記憶體流失的問題，明確的 CPU 使用量和應用程式可能會封鎖的主執行緒的區域。
- **回應狀態轉換方法**-在您**AppDelegate.cs**檔案覆寫並回應狀態變更的方法，例如輸入，或在背景從傳回的應用程式。 釋放任何下一頁的資產，例如影像、 資料或檢視表和檢視控制器。
- **測試與記憶體密集應用程式並存**-執行使用投影片出和實體 iOS 硬體上的 [分割] 檢視與記憶體密集應用程式例如對應 （以附屬檢視模式） 的應用程式，並測試兩個應用程式保持回應性，以及未損毀。

請參閱 Apple [iOS 應用程式的能源效率指南](https://developer.apple.com/library/prerelease/ios/documentation/Performance/Conceptual/EnergyGuide-iOS/index.html#//apple_ref/doc/uid/TP40015243)如需有關資源管理。

<a name="Opting-Out-of-Multitasking" />

## <a name="opting-out-of-multitasking"></a>退出多工作業

雖然所有 iOS 9 應用程式都支援多工作業，Apple 建議，則可能有非常特定的理由，應用程式不太，如遊戲或相機應用程式需要全螢幕，才能正常運作。

Xamarin.iOS 應用程式，無法選擇不使用執行任一滑出面板中，或在分割檢視模式中，編輯專案的**Info.plist**檔，並檢查**需要全螢幕**:

[![](multitasking-images/fullscreen01.png "退出多工作業")](multitasking-images/fullscreen01.png#lightbox)

> [!IMPORTANT]
> 雖然退出多工作業，可防止您的應用程式執行的投影片或分割檢視，它無法防止另一個應用程式執行投影片或影片的圖片中的圖片中顯示您應用程式。

<a name="Disabling-PIP-Video-Playback" />

### <a name="disabling-pip-video-playback"></a>停用的 PIP 視訊播放

在大部分情況下，您的應用程式應該允許使用者玩它會顯示圖片，圖片浮動視窗中的任何視訊內容。 不過，可能會有一些情況，這可能不會想要的地方，例如遊戲的剪下的場景影片。

若要退出 PIP 播放視訊時，執行您的應用程式中的下列動作：

 - 如果您使用`AVPlayerViewController`若要顯示的影片，請設定`AllowsPictureInPicturePlayback`屬性設`false`。
 - 如果您使用`AVPlayerLayer`若要顯示視訊，不具現化`AVPictureInPictureController`。
 - 如果您使用`WKWebView`若要顯示的影片，請設定`AllowsPictureInPictureMediaPlayback`屬性設`false`。

<a name="Summary" />

## <a name="summary"></a>總結

這篇文章已涵蓋以確保 Xamarin.iOS 應用程式會執行，且在 Ipad 的 iOS 9 的新多工作業功能正常運作所需的步驟。 此外，它涵蓋了選擇向外延展多工作業，並不適合的應用程式。



## <a name="related-links"></a>相關連結

- [iOS 9 範例](https://developer.xamarin.com/samples/ios/iOS9/)
- [MultiTask （範例）](https://developer.xamarin.com/samples/monotouch/ios9/MultiTask/)
- [統一的分鏡腳本簡介](~/ios/user-interface/storyboards/unified-storyboards.md)
- [iOS 9 的開發人員](https://developer.apple.com/ios/pre-release/)
- [iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [在 iPad 上採用多工作業的增強功能](https://developer.apple.com/library/prerelease/ios/documentation/WindowsViews/Conceptual/AdoptingMultitaskingOniPad/index.html#//apple_ref/doc/uid/TP40015145)
- [部落格文章](https://blog.xamarin.com/using-auto-layouts-for-ios-9-splitview/)
