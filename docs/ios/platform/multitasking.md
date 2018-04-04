---
title: 適用於 iPad 的多工
description: iOS 9 支援在同一時間、 透過使用投影片或分割檢視執行的兩個應用程式。 它也支援視訊播放圖片的圖片。
ms.prod: xamarin
ms.assetid: 0F2266D7-21FF-404D-A148-0CFDE76B12AA
ms.technology: xamarin-ios
ms.custom: xamu-video
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/20/2017
ms.openlocfilehash: 553fd6e45df897037d6ad90ef9211eef0bf27aae
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="multitasking-for-ipad"></a>適用於 iPad 的多工

_iOS 9 支援在同一時間、 透過使用投影片或分割檢視執行的兩個應用程式。它也支援視訊播放圖片的圖片。_

![](multitasking-images/about02-sml.png "分割畫面範例") ![ ](multitasking-images/about03-sml.png "圖片中圖片的範例")

iOS 9 加入多工作業支援特定的 iPad 硬體上同時執行兩個應用程式。 適用於 iPad 的多工可支援透過下列功能：

- [**投影片上**](#Slide-Over) -可讓使用者暫時將投影片出面板 （無論是根據語言方向螢幕的右側或左側一邊），包括大約 25%的主要應用程式目前正在執行中執行的第二個 iOS 應用程式。 投影片上只部。 iPad Pro、 iPad 空中、 iPad 空中 2、 iPad 迷你 2、 iPad 迷你 3 或 iPad 迷你 4
- [**分割檢視**](#Split-View) -支援的 iPad 的硬體上 (iPad 空中 2，iPad 迷你 4 和 iPad Pro 只)，使用者可以選取第二個應用程式，並與目前執行的應用程式分割螢幕模式中執行的並行。 使用者可以控制主畫面的每個應用程式所佔的百分比。
- [**圖片中的圖片**](#Picture-in-Picture) -播放視訊內容，現在視訊可以播放的可移動且可調整大小視窗中，漂浮在目前在 iOS 裝置上執行的應用程式的應用程式。 使用者擁有的大小和位置，此視窗的完整控制權。 IPad Pro、 iPad 空中、 iPad 空中 2、 iPad 迷你 2、 iPad 迷你 3 或 iPad 迷你 4 上的只有時圖片中的圖片。

有許多時應考慮的事項[應用程式中支援多工作業](#Supporting-Multitasking-in-your-App)，包括：

- [螢幕大小和方向](#Screen-Size-Considerations)
- [自訂硬體鍵盤快速鍵](#Custom-Hardware-Keyboard-Shortcuts)
- [資源管理](#Resource-Management-Considerations)

身為應用程式開發人員也可以[退出多工作業](#Opting-Out-of-Multitasking)，包括[停用 PIP 視訊播放](#Disabling-PIP-Video-Playback)。

本文將說明如何確保 Xamarin.iOS 應用程式能夠正確執行多工作業環境中，或如何退出多工作業，如果不是適合您的應用程式所需的步驟。

> [!VIDEO https://youtube.com/embed/GctYAozoLr8]

**適用於 iPad，多工由[Xamarin 大學](https://university.xamarin.com)**


<a name="Multitasking-QuickStart" />

## <a name="multitasking-quickstart"></a>多工快速入門

若要支援**投影片上**或**分割檢視**您的應用程式必須執行下列動作：

 - 建置針對 iOS 9 （或更高）。
 - 用於啟動螢幕的分鏡腳本，並不影像資產。
 - 分鏡腳本與自動版面配置和大小類別搭配使用其 UI。
 - 支援所有 4 個 iOS 裝置的方向 （直向、 顛倒直向、 橫向左及橫向右邊）。

<a name="Multitasking" />

## <a name="about-multitasking-for-ipad"></a>關於適用於 iPad 的多工

iOS 9 提供新推出的 iPad 上的多工能力_投影片上_，_分割檢視_(iPad 空中 2，iPad 迷你 4 和 iPad Pro 只) 和_圖片中的圖片_。 我們將詳述這些功能在下列各節中。

<a name="Slide-Over" />

### <a name="slide-over"></a>透過投影片

透過投影片 功能可讓使用者選擇第二個應用程式，並顯示於小型的滑動面板，以提供快速的互動。 投影片上面板是暫時性的和當使用者在使用主應用程式將會關閉。

[![](multitasking-images/about01.png "投影片上面板")](multitasking-images/about01.png#lightbox)

要記得的重點是使用者決定將執行的兩個應用程式，來並行和開發人員可以控制這個程序。 如此一來，有幾件事您必須確保 Xamarin.iOS 應用程式能夠正確執行投影片上面板中：

- **使用 自動版面配置和大小類別**— Xamarin.iOS 應用程式現在可以執行 投影片外側邊 面板中，因為您可以不再依賴在裝置、 其螢幕大小或其方向來配置您的 UI。 若要確保您的應用程式會正確地縮放它的介面，您必須使用 自動版面配置和大小類別。 如需詳細資訊，請參閱我們[簡介統一分鏡腳本](~/ios/user-interface/storyboards/unified-storyboards.md)文件。
- **有效的資源使用**— 您的應用程式可以現在系統與共用另一個執行中應用程式，因為它是關鍵應用程式會有效率地使用系統資源。 疏鬆記憶體時，系統會自動終止應用程式會耗用最多的記憶體。 請參閱 Apple[的 iOS 應用程式的能源效率指南](https://developer.apple.com/library/prerelease/ios/documentation/Performance/Conceptual/EnergyGuide-iOS/index.html#//apple_ref/doc/uid/TP40015243)如需詳細資訊。

投影片上只部。 iPad Pro、 iPad 空中、 iPad 空中 2、 iPad 迷你 2、 iPad 迷你 3 或 iPad 迷你 4 若要深入了解的投影片上準備應用程式，請參閱 Apple[採用在 iPad 上的多工增強功能](https://developer.apple.com/library/prerelease/ios/documentation/WindowsViews/Conceptual/AdoptingMultitaskingOniPad/index.html#//apple_ref/doc/uid/TP40015145)文件。

<a name="Split-View" />

### <a name="split-view"></a>分割檢視

支援的 iPad 的硬體上 (iPad 空中 2，iPad 迷你 4 和 iPad Pro 只)，使用者可以選取第二個應用程式，並與目前執行的應用程式分割螢幕模式中執行的並行。 使用者可以控制每個應用程式佔用拖曳主要畫面的百分比螢幕上分割線。

[![](multitasking-images/about02.png "分割檢視")](multitasking-images/about02.png#lightbox)

投影片上，例如使用者決定哪些兩個應用程式執行的並行，同樣地，開發人員無法控制這個程序。 如此一來，分割檢視類似需要耗用 Xamarin.iOS 應用程式：

- **使用 自動版面配置和大小類別**— Xamarin.iOS 應用程式現在可以在使用者的指定大小的分割螢幕模式執行，因為您可以不再依賴在裝置、 其螢幕大小或其方向來配置您的 UI。 若要確保您的應用程式會正確地縮放它的介面，您必須使用 自動版面配置和大小類別。 如需詳細資訊，請參閱我們[簡介統一分鏡腳本](~/ios/user-interface/storyboards/unified-storyboards.md)文件。
- **有效的資源使用**— 您的應用程式可以現在系統與共用另一個執行中應用程式，因為它是關鍵應用程式會有效率地使用系統資源。 疏鬆記憶體時，系統會自動終止應用程式會耗用最多的記憶體。 請參閱 Apple[的 iOS 應用程式的能源效率指南](https://developer.apple.com/library/prerelease/ios/documentation/Performance/Conceptual/EnergyGuide-iOS/index.html#//apple_ref/doc/uid/TP40015243)如需詳細資訊。

若要深入了解您的應用程式正在準備分割檢視，請參閱 Apple[採用在 iPad 上的多工增強功能](https://developer.apple.com/library/prerelease/ios/documentation/WindowsViews/Conceptual/AdoptingMultitaskingOniPad/index.html#//apple_ref/doc/uid/TP40015145)文件。

<a name="Picture-in-Picture" />

### <a name="picture-in-picture"></a>圖片中的圖片

圖片功能中新的圖片 (也稱為_PIP_) 可讓使用者觀看影片的小型的浮動視窗中，使用者可以高於其他執行中應用程式的螢幕上任何位置定位。

[![](multitasking-images/about03.png "範例圖片浮動視窗中的圖片")](multitasking-images/about03.png#lightbox)

做為與投影片上分割檢視，使用者可以完全掌控觀賞圖片模式中的圖片中的視訊。 如果您的應用程式的 main 函式是觀賞視訊，就需要一些修改，以便在 PIP 模式下正確運作。 否則，任何變更，不才能支援 PIP。

應用程式，無法在使用者要求顯示 PIP 視訊，您必須使用_AVKit_或_AV Foundation Api_。 Media Player framework 具有已停用 iOS 9 中，並不支援 PIP。

IPad Pro、 iPad 空中、 iPad 空中 2、 iPad 迷你 2、 iPad 迷你 3 或 iPad 迷你 4 上的只有時圖片中的圖片。 如需詳細資訊，請參閱我們[PictureInPicture 範例應用程式](https://developer.xamarin.com/samples/ios/iOS9/)與 Apple[在圖片快速入門中的圖片](https://developer.apple.com/library/prerelease/ios/documentation/WindowsViews/Conceptual/AdoptingMultitaskingOniPad/QuickStartForPictureInPicture.html#//apple_ref/doc/uid/TP40015145-CH14)文件。

<a name="Supporting-Multitasking-in-your-App" />

## <a name="supporting-multitasking-in-your-app"></a>在您的應用程式中支援多工作業

任何現有的 Xamarin.iOS 應用程式中，支援多工作業是透明的工作，只要您的應用程式已經依照 Apple 的設計指南及 ios 8 的最佳作法。 這表示，應用程式應該使用分鏡腳本與自動版面配置和大小類別對於其使用者介面版面配置 (請參閱我們[簡介統一分鏡腳本](~/ios/user-interface/storyboards/unified-storyboards.md)如需詳細資訊)。

這些應用程式中，幾乎不需要進行變更以支援多工作業，並內它的行為相同。 如果您的應用程式的 UI，建立使用其他方法，例如直接定位和調整大小 UI 項目，在 C# 程式碼，或它是依賴特定裝置的螢幕大小或方向，必須正確支援 iOS 9 多工作業的大幅修改。

若要支援 iOS 9 多工作業，任何新的 Xamarin.iOS 應用程式，再次使用分鏡腳本自動版面配置和大小類別所有的應用程式的使用者介面版面配置並實作下列各節中的指示。

<a name="Screen-Size-Considerations" />

### <a name="screen-size-and-orientation-considerations"></a>螢幕大小和方向的考量

IOS 9 之前，您可以設計您的應用程式，針對特定裝置的螢幕大小和方向。 因為滑出面板中，或在分割檢視模式中，現在可以執行應用程式，它可以尋找執行中壓縮或規則的水平大小類別 ipad，無論裝置的實體的方向或螢幕大小本身。

[![](multitasking-images/sizeclasses01.png "螢幕大小和方向的考量")](multitasking-images/sizeclasses01.png#lightbox)

在 iPad 上全螢幕應用程式會有標準的水平和垂直大小類別。 所有 Iphone 但 iPhone 6 Plus 和 iPhone 6s Plus，有壓縮大小類別雙向任一方向。 IPhone 6 Plus 和 iPhone 6s Plus 以橫向模式有規則的水平大小類別和資料壓縮的垂直大小類別 （例如 iPad 迷你多）。

在支援透過 投影片和分割檢視的 Ipad，您可以得到下列組合：

| **方向** | **主要的應用程式** | **次要應用程式** |
|--- |--- |--- |
| **Portrait** |75%的螢幕<br />Compact 水平<br />規則的垂直|25%的螢幕<br />Compact 水平<br />規則的垂直|
| **橫向** |75%的螢幕<br />規則的水平<br />規則的垂直|25%的螢幕<br />Compact 水平<br />規則的垂直|
| **橫向** |50%的螢幕<br />Compact 水平<br />規則的垂直|50%的螢幕<br />Compact 水平<br />規則的垂直|

在範例[MuliTask](https://developer.xamarin.com/samples/monotouch/ios9/MultiTask/)應用程式中，如果它執行全螢幕上以橫向模式 iPad，它將會顯示清單和詳細資料檢視，在相同的時間：

[![](multitasking-images/sizeclasses03.png "清單和詳細資料檢視，顯示在相同的時間")](multitasking-images/sizeclasses03.png#lightbox)

如果投影片上面板中執行相同的應用程式時，它會配置為 Compact 的水平大小類別，並顯示只有清單：

[![](multitasking-images/sizeclasses04.png "僅限時裝置是水平顯示清單")](multitasking-images/sizeclasses04.png#lightbox)

若要確保您的應用程式在這些情況下正確運作，您應該採用特性集合，以及大小類別，並符合`IUIContentContainer`和`IUITraitEnvironment`介面。 請參閱 Apple [UITraitCollection 類別參考](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UITraitCollection_ClassReference/index.html#//apple_ref/doc/uid/TP40014202)以及我們[簡介統一分鏡腳本](~/ios/user-interface/storyboards/unified-storyboards.md)指南以取得詳細的資訊。

此外，您可以不再依賴裝置螢幕範圍來定義應用程式的可見區域，您必須改為使用您的應用程式視窗界限。 由於視窗界限完全控制的使用者，無法以程式設計方式調整它們，或防止使用者變更這些界限。

最後，您的應用程式必須使用分鏡腳本檔案來顯示啟動螢幕而不是使用一組**.png**影像檔，並支援所有的四個介面方向 （直向、 顛倒直向、 橫向左右橫向）若要在投影片上面板中，或在分割檢視模式中執行的考量。

<a name="Custom-Hardware-Keyboard-Shortcuts" />

### <a name="custom-hardware-keyboard-shortcuts"></a>自訂硬體鍵盤快速鍵

在 iOS 9 iPad 上執行，Apple 有延伸的支援的硬體鍵盤。 Ipad 永遠包含基本外接式鍵盤支援藍芽和某些鍵盤製造商建立鍵盤包含固 iOS 特定索引鍵。

現在，使用 iOS 9，應用程式可以建立自己的自訂鍵盤快速鍵。 此外，某些基本的鍵盤快速鍵可像**命令 C** （複製），**命令 X** （剪下）**命令 V** （貼上） 和**命令按 Shift H** （組織），而不被特別寫入的回應的應用程式。

**命令 索引標籤**畫面可讓使用者從鍵盤，非常類似 Mac OS 的應用程式之間快速切換應用程式切換器上隨即顯示：

[![](multitasking-images/keyboard01.png "應用程式切換器")](multitasking-images/keyboard01.png#lightbox)

使用者如果 iOS 9 應用程式包含鍵盤快速鍵，可以按住**命令**，**選項**或**控制項**它們顯示在快顯視窗中的索引鍵：

[![](multitasking-images/keyboard02.png "鍵盤快速鍵快顯視窗")](multitasking-images/keyboard02.png#lightbox)

#### <a name="defining-custom-keyboard-shortcuts"></a>定義自訂鍵盤快速鍵

如果我們加入下列程式碼檢視或檢視控制器在我們的應用程式中，當出現該檢視或控制站時，將可提供自訂的鍵盤快速鍵：

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

首先，我們會覆寫`CanBecomeFirstResponder`屬性和傳回`true`讓檢視或檢視控制器可以接收鍵盤輸入。 

接下來，我們會覆寫`KeyCommands`屬性並建立新`UIKeyCommand`如**命令 N**按鍵輸入。 當啟動按鍵輸入時，我們稱之為`NewEntry`方法 (我們公開於 iOS 9 使用`Export`命令) 來執行要求的動作。

如果我們在 iPad 上執行此應用程式連接的硬體鍵盤與使用者類型**命令 N**，新的項目將會新增至清單。 如果使用者按住**命令**索引鍵，快速鍵的清單隨即出現：

[![](multitasking-images/keyboard03.png "鍵盤快速鍵快顯視窗")](multitasking-images/keyboard03.png#lightbox)

此範例，請參閱[MultiTask 應用程式](http://developer.xamarin.com/samples/monotouch/ios9/MultiTask/)範例實作。

<a name="Resource-Management-Considerations" />

### <a name="resource-management-considerations"></a>資源管理考量

針對已經在使用 iOS 8 的設計指南及最佳作法的應用程式，即使有效的資源管理仍然可能發生問題。 在 iOS 9 中，應用程式不再需要記憶體、 CPU 或其他系統資源的獨佔的使用。

如此一來，您必須調整成 Xamarin.iOS 應用程式以有效地使用系統資源或面臨記憶體不足的情況下終止。 這同樣適退出多工作業的應用程式，因為第二個應用程式可能仍以投影片透過 [控制台] 或需要額外的資源，或導致重新整理頻率的圖片視窗中的圖片，低於 60 秒畫面格數。

請考慮下列的使用者動作和及其含意：

- **投影片上面板中輸入文字**-即使您的應用程式的任何文字輸入，系統鍵盤現在顯示在其 UI。 如此一來，應用程式可能需要回應鍵盤顯示通知 （例如，在顯示或隱藏鍵盤）。
- **執行第二個應用程式在投影片上面板中**-新的應用程式現在在前景執行並與現有的應用程式，例如記憶體和 CPU 循環的系統資源的競爭。
- **播放視訊在 PIP 視窗**-不只可以此視窗包含您的應用程式介面的一部分，但啟動視訊應用程式仍在背景執行並消耗 CPU 和記憶體資源。

若要確保您的應用程式會有效率地使用資源，您應該執行下列作業：

- **分析應用程式與儀器**-檢查是否有記憶體流失，明顯的 CPU 使用量和應用程式可能會封鎖的主執行緒的區域。
- **回應狀態轉換方法**-在您**d**檔案覆寫和回應的狀態變更的方法，例如輸入，或在背景從傳回的應用程式。 釋放任何受的資產，例如影像、 資料或檢視表和檢視控制器。
- **測試與記憶體密集應用程式並存**-執行滑出和實體 Io 的硬體上的 [分割] 檢視中使用記憶體密集應用程式，例如對應 （以附屬檢視模式） 的應用程式，並測試這兩個應用程式仍能繼續回應，而不損毀。

請參閱 Apple[的 iOS 應用程式的能源效率指南](https://developer.apple.com/library/prerelease/ios/documentation/Performance/Conceptual/EnergyGuide-iOS/index.html#//apple_ref/doc/uid/TP40015243)如需有關資源管理。

<a name="Opting-Out-of-Multitasking" />

## <a name="opting-out-of-multitasking"></a>選擇退出多工作業

Apple 所示，所有 iOS 9 應用程式都支援多工作業，那里可能非常特定的理由要將應用程式不太，如遊戲或相機應用程式需要全螢幕才能正常運作。

退出任一滑出面板中，或在分割檢視模式中執行您 Xamarin.iOS 應用程式中，編輯專案的**Info.plist**檔，並檢查**需要全螢幕**:

[![](multitasking-images/fullscreen01.png "選擇退出多工作業")](multitasking-images/fullscreen01.png#lightbox)

> [!IMPORTANT]
> 退出多工作業可防止您的應用程式執行的投影片或分割檢視，而它不會阻止另一個應用程式顯示您應用程式執行的投影片或視訊的圖片中的圖片。

<a name="Disabling-PIP-Video-Playback" />

### <a name="disabling-pip-video-playback"></a>停用 PIP 視訊播放

在大部分情況下，您的應用程式應該允許使用者来播放的圖片浮動視窗中的圖片中顯示的任何視訊內容。 不過，可能會有這可能不會想要的地方，例如遊戲剪下的場景視訊的情況。

若要退出 PIP 播放視訊時，執行您的應用程式中的下列動作：

 - 如果您使用`AVPlayerViewController`若要顯示視訊，請設定`AllowsPictureInPicturePlayback`屬性`false`。
 - 如果您使用`AVPlayerLayer`來顯示視訊，不具現化`AVPictureInPictureController`。
 - 如果您使用`WKWebView`若要顯示視訊，請設定`AllowsPictureInPictureMediaPlayback`屬性`false`。

<a name="Summary" />

## <a name="summary"></a>總結

這篇文章已涵蓋確保 Xamarin.iOS 應用程式將會執行，並在 Ipad 的 iOS 9 的新多工作業功能正確運作所需的步驟。 此外，它涵蓋選擇向外多工作業的應用程式並不適合。



## <a name="related-links"></a>相關連結

- [iOS 9 範例](https://developer.xamarin.com/samples/ios/iOS9/)
- [MultiTask （範例）](https://developer.xamarin.com/samples/monotouch/ios9/MultiTask/)
- [統一的分鏡腳本的簡介](~/ios/user-interface/storyboards/unified-storyboards.md)
- [iOS 9 的開發人員](https://developer.apple.com/ios/pre-release/)
- [iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [在 iPad 上採用多工作業的增強功能](https://developer.apple.com/library/prerelease/ios/documentation/WindowsViews/Conceptual/AdoptingMultitaskingOniPad/index.html#//apple_ref/doc/uid/TP40015145)
- [部落格文章](https://blog.xamarin.com/using-auto-layouts-for-ios-9-splitview/)
