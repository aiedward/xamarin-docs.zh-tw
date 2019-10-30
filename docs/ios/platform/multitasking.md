---
title: Xamarin 中 iPad 的多工作業
description: iOS 9 使用 [滑過] 或 [分割] 視圖，同時支援兩個同時執行的應用程式。 它也支援以圖片顯示圖片。
ms.prod: xamarin
ms.assetid: 0F2266D7-21FF-404D-A148-0CFDE76B12AA
ms.technology: xamarin-ios
ms.custom: xamu-video
author: davidortinau
ms.author: daortin
ms.date: 03/20/2017
ms.openlocfilehash: aeb3d01a3d0f7edbe92c9959073d859fc63486a6
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73031647"
---
# <a name="multitasking-for-ipad-in-xamarinios"></a>Xamarin 中 iPad 的多工作業

_iOS 9 使用 [滑過] 或 [分割] 視圖，同時支援兩個同時執行的應用程式。它也支援以圖片顯示圖片。_

![](multitasking-images/about02-sml.png "分割畫面範例") ![](multitasking-images/about03-sml.png "圖片-圖片中的範例")

iOS 9 增加了多工支援，可在特定 iPad 硬體上同時執行兩個應用程式。 透過下列功能可支援 iPad 的多工作業：

- [**滑過**](#Slide-Over)-允許使用者在 [滑出面板] 中暫時執行第二個 iOS 應用程式（根據語言方向，在畫面的右側或左側），其中涵蓋約為目前正在執行之主要應用程式的25%。 滑過僅適用于 iPad Pro、iPad Air、iPad Air 2、iPad 迷你2、iPad 迷你3或 iPad 迷你4。
- [**分割視圖**](#Split-View)-在支援的 ipad 硬體上（僅限 ipad Air 2、ipad 迷你4和 ipad Pro），使用者可以挑選第二個應用程式，並在分割螢幕模式中與目前執行中的應用程式並存執行。 使用者可以控制每個應用程式所佔用主畫面的百分比。
- [**圖片**](#Picture-in-Picture)-針對播放影片內容的應用程式，現在可以在可移動且可調整大小的視窗中播放影片，並將其浮動到目前正在 iOS 裝置上執行的其他應用程式。 使用者可以完全掌控這個視窗的大小和位置。 圖片中的圖片僅適用于 iPad Pro、iPad Air、iPad Air 2、iPad 迷你2、iPad 迷你3或 iPad 迷你4。

在[您的應用程式中支援多工](#Supporting-Multitasking-in-your-App)時，需要考慮幾件事，包括：

- [螢幕大小和方向](#Screen-Size-Considerations)
- [自訂硬體鍵盤快速鍵](#Custom-Hardware-Keyboard-Shortcuts)
- [資源管理](#Resource-Management-Considerations)

身為應用程式開發人員，您也可以選擇不使用[多工](#Opting-Out-of-Multitasking)作業，包括[停用 PIP 影片播放](#Disabling-PIP-Video-Playback)。

本文將涵蓋確保您的 Xamarin iOS 應用程式在多工的環境中正確執行所需的步驟，或如果不適合您的應用程式，則如何選擇不使用多工。

> [!VIDEO https://youtube.com/embed/GctYAozoLr8]

**IPad 影片的多工作業**

<a name="Multitasking-QuickStart" />

## <a name="multitasking-quickstart"></a>多工快速入門

若要支援 [**滑過**] 或 [**分割] 視圖**，您的應用程式必須執行下列動作：

- 針對 iOS 9 （或更新版本）建立。
- 針對其啟動畫面（而非影像資產）使用分鏡腳本。
- 針對其 UI 使用具有自動調整和大小類別的分鏡腳本。
- 支援所有4個 iOS 裝置方向（直向、上下顛倒、橫向 & 橫向）。

<a name="Multitasking" />

## <a name="about-multitasking-for-ipad"></a>關於 iPad 的多工作業

iOS _9 在 iPad_上提供新的多工功能，包含_投影片_、_分割視圖_（ipad Air 2、ipad 迷你4和 ipad Pro）和圖片。 我們將在下列各節中進一步瞭解這些功能。

<a name="Slide-Over" />

### <a name="slide-over"></a>滑過

[滑過] 功能可讓使用者挑選第二個應用程式，並將它顯示在小型滑動面板中，以提供快速互動。 [滑過] 面板是暫時性的，而且會在使用者回到主應用程式再次使用時關閉。

[![](multitasking-images/about01.png "The Slide Over panel")](multitasking-images/about01.png#lightbox)

要記住的重點是，使用者決定哪兩個應用程式會並存執行，而開發人員也無法控制這個流程。 因此，您必須執行幾項工作，以確保您的 Xamarin iOS 應用程式在滑動面板上正確執行：

- **使用 [自動調整] 和 [大小] 類別**—因為您現在可以在滑出側邊面板中執行您的 Xamarin iOS 應用程式，所以您無法再依賴裝置、其螢幕大小或其方向來配置您的 UI。 若要確保您的應用程式能夠正確地調整其介面，您必須使用「自動調整」和「大小」類別。 如需詳細資訊，請參閱我們的整合分鏡指令檔[簡介](~/ios/user-interface/storyboards/unified-storyboards.md)。
- **有效率地使用資源**-因為您的應用程式現在可以與另一個執行中的應用程式共用系統，所以您的應用程式必須有效率地使用系統資源。 當記憶體變得稀疏時，系統會自動終止耗用最多記憶體的應用程式。 如需詳細資訊，請參閱適用[于 IOS 應用程式的 Apple 能源效率指南](https://developer.apple.com/library/prerelease/ios/documentation/Performance/Conceptual/EnergyGuide-iOS/index.html#//apple_ref/doc/uid/TP40015243)。

滑過僅適用于 iPad Pro、iPad Air、iPad Air 2、iPad 迷你2、iPad 迷你3或 iPad 迷你4。 若要深入瞭解如何準備您的應用程式以進行滑動，請參閱 Apple[在 iPad 上採用多工增強功能](https://developer.apple.com/library/prerelease/ios/documentation/WindowsViews/Conceptual/AdoptingMultitaskingOniPad/index.html#//apple_ref/doc/uid/TP40015145)檔。

<a name="Split-View" />

### <a name="split-view"></a>分割檢視

在支援的 iPad 硬體上（僅限 ipad Air 2、iPad 迷你4和 iPad Pro），使用者可以挑選第二個應用程式，並在分割螢幕模式下與目前執行中的應用程式並存執行。 使用者可以藉由拖曳螢幕上的分隔線，控制每個應用程式所佔用主畫面的百分比。

[![](multitasking-images/about02.png "The Split View")](multitasking-images/about02.png#lightbox)

如同滑過，使用者會決定要並存執行的兩個應用程式，而開發人員無法控制此進程。 因此，分割視圖會將類似的需求放在 Xamarin iOS 應用程式上：

- **使用自動調整和大小類別**—因為您的 Xamarin iOS 應用程式現在可以在使用者指定大小的分割螢幕模式下執行，所以您無法再依賴裝置、其螢幕大小或其方向來配置您的 UI。 若要確保您的應用程式能夠正確地調整其介面，您必須使用「自動調整」和「大小」類別。 如需詳細資訊，請參閱我們的整合分鏡指令檔[簡介](~/ios/user-interface/storyboards/unified-storyboards.md)。
- **有效率地使用資源**-因為您的應用程式現在可以與另一個執行中的應用程式共用系統，所以您的應用程式必須有效率地使用系統資源。 當記憶體變得稀疏時，系統會自動終止耗用最多記憶體的應用程式。 如需詳細資訊，請參閱適用[于 IOS 應用程式的 Apple 能源效率指南](https://developer.apple.com/library/prerelease/ios/documentation/Performance/Conceptual/EnergyGuide-iOS/index.html#//apple_ref/doc/uid/TP40015243)。

若要深入瞭解如何針對分割視圖準備您的應用程式，請參閱 Apple[在 iPad 上採用多工增強功能](https://developer.apple.com/library/prerelease/ios/documentation/WindowsViews/Conceptual/AdoptingMultitaskingOniPad/index.html#//apple_ref/doc/uid/TP40015145)檔。

<a name="Picture-in-Picture" />

### <a name="picture-in-picture"></a>圖片圖片

[圖片] 功能中的新圖片（也稱為_PIP_）可讓使用者在小型的浮動視窗中觀看影片，使用者可以在畫面上的任何位置上放置其他執行中的應用程式。

[![](multitasking-images/about03.png "An example Picture in Picture floating window")](multitasking-images/about03.png#lightbox)

如同 [滑過] 和 [分割視圖]，使用者可以完全控制圖片模式中的影片。 如果您應用程式的主要功能是觀看影片，則需要進行一些修改，才能在 PIP 模式中正確運作。 否則，不需要任何變更即可支援 PIP。

若要讓您的應用程式在使用者的要求中顯示 PIP 影片，您必須使用_AVKit_或_AV Foundation api_。 媒體播放機架構已在 iOS 9 中進行過折舊，而且不支援 PIP。

圖片中的圖片僅適用于 iPad Pro、iPad Air、iPad Air 2、iPad 迷你2、iPad 迷你3或 iPad 迷你4。 如需詳細資訊，請參閱我們的[PictureInPicture 範例應用程式](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS9)和[圖片快速入門檔中](https://developer.apple.com/library/prerelease/ios/documentation/WindowsViews/Conceptual/AdoptingMultitaskingOniPad/QuickStartForPictureInPicture.html#//apple_ref/doc/uid/TP40015145-CH14)的 Apple 圖片。

<a name="Supporting-Multitasking-in-your-App" />

## <a name="supporting-multitasking-in-your-app"></a>在您的應用程式中支援多工作業

針對任何現有的 Xamarin iOS 應用程式，只要您的應用程式已遵循 Apple 的設計指南和 iOS 8 的最佳作法，支援多工作業就是一項透明的工作。 這表示應用程式應該使用包含自動調整和大小類別的分鏡腳本來進行使用者介面配置（如需詳細資訊，請參閱我們的整合分鏡腳本[簡介](~/ios/user-interface/storyboards/unified-storyboards.md)）。

對於這些應用程式，不需要進行任何變更，即可支援多工，並在其中進行良好的行為。 如果您的應用程式 UI 是使用其他方法所建立，例如直接在程式碼中C#定位和調整大小 UI 元素，或者它依賴特定裝置螢幕大小或方向，則需要進行大量修改才能正確支援 iOS 9 的工作。

若要在任何新的 Xamarin iOS 應用程式上支援 iOS 9 多工作業，請針對所有應用程式的使用者介面配置，再次使用包含自動調整和大小類別的分鏡腳本，並執行下列各節中的指示。

<a name="Screen-Size-Considerations" />

### <a name="screen-size-and-orientation-considerations"></a>螢幕大小和方向考慮

在 iOS 9 之前，您可以針對特定裝置螢幕大小和方向來設計您的應用程式。 因為應用程式現在可以在 [滑出] 面板或 [分割視圖] 模式中執行，所以不論裝置的實體方向或螢幕大小為何，它都可以在 iPad 上的精簡或一般水準大小類別中尋找其本身。

[![](multitasking-images/sizeclasses01.png "Screen Size and Orientation Considerations")](multitasking-images/sizeclasses01.png#lightbox)

在 iPad 上，全螢幕應用程式具有一般的水準和垂直大小類別。 所有 Iphone，但是 iPhone 6 加上 iPhone 6s Plus，都有各種方向的精簡型類別。 在橫向模式中的 iPhone 6 Plus 和 iPhone 6s Plus 具有一般的水準大小類別和精簡的垂直大小類別（非常類似 iPad 迷你）。

在支援 [滑過] 和 [分割] 視圖的 Ipad 上，您可以使用下列組合：

| **方向** | **主要應用程式** | **次要應用程式** |
|--- |--- |--- |
| **直向** |75% 的畫面<br />水準壓縮<br />一般垂直|25% 的畫面<br />水準壓縮<br />一般垂直|
| **Sap** |75% 的畫面<br />一般水準<br />一般垂直|25% 的畫面<br />水準壓縮<br />一般垂直|
| **Sap** |50% 的畫面<br />水準壓縮<br />一般垂直|50% 的畫面<br />水準壓縮<br />一般垂直|

在範例[MuliTask](https://docs.microsoft.com/samples/xamarin/ios-samples/ios9-multitask)應用程式中，如果在 [橫向] 模式的 iPad 上以全螢幕方式執行，則會同時顯示清單和詳細資料檢視：

[![](multitasking-images/sizeclasses03.png "The list and the detail view presented at the same time")](multitasking-images/sizeclasses03.png#lightbox)

如果在滑動面板上執行相同的應用程式，則會將它配置為精簡的水準大小類別，而且只會顯示清單：

[![](multitasking-images/sizeclasses04.png "Only the list presented when the device is horizontal")](multitasking-images/sizeclasses04.png#lightbox)

為了確保您的應用程式在這些情況下的行為正確，您應該採用特性集合以及大小類別，並符合 `IUIContentContainer` 和 `IUITraitEnvironment` 介面。 如需詳細資訊，請參閱 Apple 的[UITraitCollection 類別參考](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UITraitCollection_ClassReference/index.html#//apple_ref/doc/uid/TP40014202)和我們的整合分鏡腳本指南[簡介](~/ios/user-interface/storyboards/unified-storyboards.md)。

此外，您不能再依賴 [裝置] 螢幕界限來定義應用程式的可見區域，而必須改用應用程式的視窗界限。 由於視窗界限完全由使用者控制，因此您無法以程式設計方式調整它們，或防止使用者變更這些界限。

最後，您的應用程式必須使用分鏡腳本檔案來呈現其啟動畫面，而不是使用一組 **.png**影像檔，並支援全部四個介面方向（直向、向下、向左和向右、橫向和橫向），以視為在滑過面板或分割視圖模式中執行。

<a name="Custom-Hardware-Keyboard-Shortcuts" />

### <a name="custom-hardware-keyboard-shortcuts"></a>自訂硬體鍵盤快速鍵

在 iPad 上執行的 iOS 9 中，Apple 已擴充硬體鍵盤的支援。 Ipad 一律包含透過藍牙的基本外部鍵盤支援，而某些鍵盤製造商建立了包含硬式 iOS 特定金鑰的鍵盤。

現在，使用 iOS 9，應用程式可以建立自己的自訂鍵盤快速鍵。 此外，有一些基本鍵盤快速鍵可供使用，例如**命令-C** （複製）、**命令 X** （剪下）、命令- **V** （貼上）和**命令-Shift-H** （home），而不需要特別撰寫應用程式來回應它們。

**命令**索引標籤會顯示應用程式切換器，讓使用者可以從鍵盤快速切換應用程式，就像 Mac OS：

[![](multitasking-images/keyboard01.png "The app switcher")](multitasking-images/keyboard01.png#lightbox)

如果 iOS 9 應用程式包含鍵盤快速鍵，則使用者可以按住**命令**、**選項**或**控制**鍵，在快顯視窗中顯示它們：

[![](multitasking-images/keyboard02.png "The keyboard shortcuts popup")](multitasking-images/keyboard02.png#lightbox)

#### <a name="defining-custom-keyboard-shortcuts"></a>定義自訂鍵盤快速鍵

如果我們將下列程式碼新增至應用程式中的 View 或 View 控制器，則當該視圖或控制器可見時，將會提供自訂的鍵盤快速鍵：

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

首先，我們會覆寫 `CanBecomeFirstResponder` 屬性並傳回 `true`，讓 View 或 View 控制器可以接收鍵盤輸入。 

接下來，我們會覆寫 `KeyCommands` 屬性，並建立**命令-N**按鍵的新 `UIKeyCommand`。 當擊鍵啟動時，我們會呼叫 `NewEntry` 方法（使用 `Export` 命令向 iOS 9 公開）來執行要求的動作。

如果我們在已連接硬體鍵盤的 iPad 上執行此應用程式，且使用者輸入**Command-N**，則會將新的專案新增至清單。 如果使用者按住**命令**鍵，將會顯示快捷方式清單：

[![](multitasking-images/keyboard03.png "The keyboard shortcuts popup")](multitasking-images/keyboard03.png#lightbox)

如需範例的執行方式，請參閱範例工作[應用程式](https://docs.microsoft.com/samples/xamarin/ios-samples/ios9-multitask)。

<a name="Resource-Management-Considerations" />

### <a name="resource-management-considerations"></a>資源管理考慮

即使應用程式已經使用 iOS 8 的設計指南和最佳作法，有效率的資源管理還是可能發生問題。 在 iOS 9 中，應用程式不再具有記憶體、CPU 或其他系統資源的獨佔使用。

因此，您必須微調您的 Xamarin iOS 應用程式，以有效地使用系統資源，或在記憶體不足的情況下面對終止。 這同樣適用于選擇不使用多工的應用程式，因為第二個應用程式可能仍會在滑過的面板或圖片視窗中的圖片上執行，而需要額外的資源，或導致重新整理頻率低於每秒60個畫面格。

請考慮下列使用者動作及其含意：

- **在滑動面板中輸入文字**-即使您的應用程式沒有文字輸入，系統鍵盤現在也可以透過其 UI 顯示。 因此，應用程式可能需要回應鍵盤顯示通知（例如顯示和隱藏鍵盤）。
- **在滑過的面板中執行第二個應用**程式-新的應用程式現在會在前景執行，並與現有的應用程式與系統資源（例如記憶體和 CPU 週期）進行競爭。
- **在 PIP 視窗中播放影片**-此視窗不僅涵蓋應用程式介面的一部分，而且啟動影片的應用程式仍會在背景中執行，並耗用 CPU 和記憶體資源。

若要確保您的應用程式有效率地使用資源，您應該執行下列動作：

- 對**應用程式進行分析**，並檢查記憶體流失的情況、明確 CPU 使用量，以及應用程式可能封鎖主執行緒的區域。
- **回應狀態轉換方法**-在您的**AppDelegate.cs**檔案中，覆寫並回應狀態變更方法，例如應用程式從背景進入或返回。 發行任何 unrequired 的資產，例如影像、資料或視圖，以及視圖控制器。
- **並行測試與記憶體密集型應用程式**-在實體 iOS 硬體上使用 [滑出] 和 [分割] 視圖來執行您的應用程式（如對應（在附屬視圖模式中），並測試這兩個應用程式是否仍有回應且不會損毀。

如需資源管理的詳細資訊，請參閱適用于[IOS 應用程式的 Apple 能源效率指南](https://developer.apple.com/library/prerelease/ios/documentation/Performance/Conceptual/EnergyGuide-iOS/index.html#//apple_ref/doc/uid/TP40015243)。

<a name="Opting-Out-of-Multitasking" />

## <a name="opting-out-of-multitasking"></a>退出多工作業

雖然 Apple 建議所有 iOS 9 應用程式都支援多工作業，但也可能是應用程式的特定原因（例如需要全螢幕正常運作的遊戲或相機應用程式）。

若要讓您的 Xamarin iOS 應用程式選擇不要在 [滑出面板] 或 [分割視圖] 模式中執行，請編輯專案的**plist**檔案，然後勾選 [**需要全螢幕**]：

[![](multitasking-images/fullscreen01.png "Opting Out of Multitasking")](multitasking-images/fullscreen01.png#lightbox)

> [!IMPORTANT]
> 雖然選擇不執行多工作業可防止您的應用程式在 [滑出] 或 [分割] 視圖中執行，但它不會防止另一個應用程式在投影片中執行，也不會使圖片影片中的圖片與應用程式一起顯示。

<a name="Disabling-PIP-Video-Playback" />

### <a name="disabling-pip-video-playback"></a>停用 PIP 影片播放

在大部分情況下，您的應用程式應該允許使用者播放它在圖片浮動視窗中顯示的任何影片內容。 不過，在某些情況下，可能不需要這麼做，例如遊戲剪下場景影片。

若要退出宣告 PIP 影片播放，請在您的應用程式中執行下列動作：

- 如果您使用 `AVPlayerViewController` 來顯示影片，請將 `AllowsPictureInPicturePlayback` 屬性設定為 [`false`]。
- 如果您使用 `AVPlayerLayer` 顯示影片，請勿將 `AVPictureInPictureController`具現化。
- 如果您使用 `WKWebView` 來顯示影片，請將 `AllowsPictureInPictureMediaPlayback` 屬性設定為 [`false`]。

<a name="Summary" />

## <a name="summary"></a>總結

本文涵蓋了確保 Xamarin iOS 應用程式在 iOS 9 的 Ipad 的新多工功能中執行和正常運作所需的步驟。 此外，它還涵蓋了針對應用程式退出宣告多工的功能，而這不是很適合的選擇。

## <a name="related-links"></a>相關連結

- [iOS 9 範例](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS9)
- [[進行中] （範例）](https://docs.microsoft.com/samples/xamarin/ios-samples/ios9-multitask)
- [整合分鏡腳本簡介](~/ios/user-interface/storyboards/unified-storyboards.md)
- [iOS 9 開發人員](https://developer.apple.com/ios/pre-release/)
- [iOS 9。0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [在 iPad 上採用多工增強功能](https://developer.apple.com/library/prerelease/ios/documentation/WindowsViews/Conceptual/AdoptingMultitaskingOniPad/index.html#//apple_ref/doc/uid/TP40015145)
- [Blog 文章](https://blog.xamarin.com/using-auto-layouts-for-ios-9-splitview/)
