---
title: Xamarin 中適用于 watchOS 3 的快速互動技術
description: 本文涵蓋 Apple 在 watchOS 3 中新增的快速互動技術，以及如何在適用于 Apple Watch 的 Xamarin 中加以執行。
ms.prod: xamarin
ms.assetid: 26697F68-AF7E-4A36-988F-85E2674A4DD1
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/17/2017
ms.openlocfilehash: 0252626cb58ff334e3cff2cc60e6544e4eba18c0
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91435384"
---
# <a name="quick-interaction-techniques-for-watchos-3-in-xamarin"></a>Xamarin 中適用于 watchOS 3 的快速互動技術

_本文涵蓋 Apple 在 watchOS 3 中新增的快速互動技術，以及如何在適用于 Apple Watch 的 Xamarin 中加以執行。_

提供快速的使用者互動對於建立吸引人的 Apple Watch 應用程式和複雜的重要。 Apple 新增了 watchOS 3，並新增了對手勢辨識器的支援、Digital Crown 的存取，以及新的使用者通知和流覽技術。 這項功能以及新增 SceneKit 和 SpriteKit 的支援，可讓開發人員輕鬆地建立既快速又快速的豐富 glanceable 介面。

## <a name="what-are-quick-interactions"></a>什麼是快速互動

針對用來建立 iOS 或 macOS 應用程式的開發人員 (當使用者花在幾分鐘或幾小時的時間來測量使用者與應用程式互動的時間量，) ，為 Apple Watch 設計成功的應用程式可能是一項挑戰，而且需要不同的方法。

在 watchOS 中，使用者通常想要提高手腕，快速與應用程式互動 (通常會有幾秒鐘的時間) ，然後卸載手腕，然後繼續進行。

以下是一些典型的 Apple Watch 快速互動範例：

- 啟動計時器。
- 檢查天氣。
- 將專案標記為待辦事項清單。

若要達成這些目標，Apple Watch 上的應用程式必須：

- **Glanceable** -這表示，快速概覽使用者應能取得所需的資訊。 
- 可**採取**動作-這表示使用者應該能夠進行快速且明智的決策。
- **回應** 式-這表示使用者絕對不應該等待接收所需的資訊，或達成所需的動作。

### <a name="quick-interactions-length"></a>快速互動長度

由於 Apple Watch apps 的本質，Apple 建議快速互動的理想長度應該是兩秒或更少。 由於這兩次的限制，開發人員必須花費相當長的時間來設計和執行 Apple Watch 應用程式。 

## <a name="new-watchos-3-features-and-apis"></a>新的 watchOS 3 功能和 Api

Apple 已將數個新功能和 Api 新增至 WatchKit，以協助開發人員在其 Apple Watch 應用程式中新增快速互動：

- watchOS 3 可讓您存取新種類的使用者輸入，例如：
  - 手勢辨識器
  - Digital Crown 旋轉 
- watchOS 3 提供新的方式來顯示和更新資訊，例如：
  - 增強的資料表導覽
  - 新的使用者通知架構支援
  - SpriteKit 和 SceneKit 整合

藉由實行這些新功能，開發人員可以確保其 watchOS 3 應用程式 Glanceable、可操作且有回應。

### <a name="gesture-recognizer-support"></a>手勢辨識器支援

如果開發人員已在 iOS 中實行手勢辨識器，則應該非常熟悉手勢辨識器在 watchOS 3 中的運作方式。 若要重新整理，軌跡辨識器是將低層級觸控事件剖析成可辨識、預先定義的手勢的物件。

watchOS 3 將支援下列四個手勢辨識器：

- 離散手勢類型：
  - 滑動手勢 (`WKSwipeGestureRecognizer`) 。
  - 點擊手勢 (`WKTapGestureRecognizer`) 。
- 連續手勢類型：
  - 平移手勢 (`WKPanGestureRecognizer`) 。
  - 長按的手勢 (`WKLongPressGestureRecognizer`) 。

若要執行其中一個新的手勢辨識器，只要在 Visual Studio for Mac 中將它拖曳到設計介面，然後設定其屬性即可。

在程式碼中，回應辨識器的動作來處理使用者所觸發的手勢。 同樣地，這會以 iOS 中處理的相同方式來完成。

#### <a name="discrete-gesture-states"></a>離散手勢狀態

若為離散手勢，在辨識手勢並將狀態)  (指派為時，會呼叫動作 `WKGestureRecognizerState` ：

[![離散手勢狀態](quick-interaction-techniques-images/quick01.png)](quick-interaction-techniques-images/quick01.png#lightbox)

所有的離散手勢都會在狀態中開始 `Possible` ，並轉換成 `Failed` 或 `Recognized` 狀態。 使用離散手勢時，開發人員通常不會直接處理狀態。 相反地，它們會依賴只有當手勢被辨識時所呼叫的動作。

#### <a name="continuous-gesture-states"></a>持續手勢狀態

連續手勢與離散手勢稍微不同，因為在辨識手勢時，會多次呼叫動作：

[![持續手勢狀態](quick-interaction-techniques-images/quick02.png)](quick-interaction-techniques-images/quick02.png#lightbox)

同樣地，連續手勢會在狀態中開始 `Possible` ，但會在多個更新中進行。 在此，開發人員必須考慮辨識器的狀態，並在該階段中更新應用程式的 UI， `Changed` 直到手勢終於 `Recognized` 或 `Canceled` 。

#### <a name="gesture-recognizer-usage-tips"></a>手勢辨識器使用秘訣

當使用 watchOS 3 中的手勢辨識器時，Apple 建議下列各項：

- 將手勢辨識器新增至群組專案，而不是個別控制項。 由於 Apple Watch 的實體螢幕大小較小，因此群組專案通常會變得更大且更容易叫用的目標使用者。 此外，手勢辨識器可能會與已在原生 UI 控制項中的內建手勢產生衝突。
- 設定 watch 應用程式分鏡腳本中的相依性關聯性。
- 某些手勢優先于其他筆勢類型，例如：
  - 捲動
  - Force Touch

### <a name="digital-crown-rotation"></a>Digital Crown 旋轉

藉由在 watchOS 3 應用程式中實施 Digital Crown 支援，開發人員可以為其使用者提供更高的導覽速度和精確度互動。

自 watchOS 2 起，Apple Watch 應用程式可以使用 `WKInterfacePicker` 物件來存取 Digital Crown，方法是提供 `WKPickerItems` (清單、堆疊或影像序列) 的選擇器樣式清單。 watchOS 接著允許使用者使用 Digital Crown 從清單中選取專案。

使用時 `WKInterfacePicker` ，WatchKit 會處理大部分的工作，方法是：

- 繪製清單和個別介面元素。
- 處理 Digital Crown 事件。
- 在選取專案時呼叫動作。

WatchOS 3 的新功能，開發人員現在可以直接存取 Digital Crown 的旋轉事件，讓他們能夠建立自己的 UI 元素來回應旋轉值。

Digital Crown 存取是由下列元素提供：

- `WKCrownSequencer` -提供每秒旋轉的存取。
- `WKCrownDelegate` -提供對旋轉差異事件的存取。

#### <a name="rotations-per-second"></a>每秒的旋轉

使用以物理為基礎的動畫時，從 Digital Crown 存取每秒的旋轉會很有用。 若要存取每秒的旋轉，請使用 `CrownSequencer` `WKInterfaceController` 監看式延伸模組之的屬性。 例如：

```csharp
var rotationsPerSecond = CrownSequencer.RotationsPerSecond;
```

#### <a name="rotational-deltas"></a>旋轉差異

使用 Digital Crown 的旋轉差異來計算旋轉的數目。 使用的覆 `CrownDidRotate` 寫方法 `WKCrownDelegate` 來存取旋轉差異。 例如：

```csharp
using System;
using WatchKit;
using Foundation;

namespace MonkeyWatch.MonkeySeeExtension
{
  public class CrownDelegate : WKCrownDelegate
  {
    #region Computed Properties
    public double AccumulatedRotations { get; set;}
    #endregion

    #region Constructors
    public CrownDelegate ()
    {
    }
    #endregion

    #region Override Methods
    public override void CrownDidRotate (WKCrownSequencer crownSequencer, double rotationalDelta)
    {
      base.CrownDidRotate (crownSequencer, rotationalDelta);

      // Accumulate rotations
      AccumulatedRotations += rotationalDelta;
    }
    #endregion
  }
}
```

應用程式會在此維護累積的 (`AccumulatedRotations`) 來判斷旋轉的數目。 Digital Crown 的一個完整旋轉等於的累積差異 `1.0` ，而一半的旋轉則為 `0.5` 。

Apple 已將其保留給開發人員，以判斷旋轉計數如何對應至所更新之 UI 元素的變更敏感度。

「旋轉差異」的正負號 (`+/-`) 表示使用者正在轉換 Digital Crown 的方向：

[![旋轉差異的正負號表示使用者正在開啟 Digital Crown](quick-interaction-techniques-images/quick03.png)](quick-interaction-techniques-images/quick03.png#lightbox)

如果使用者正在進行滾動，WatchKit 會傳回正面的差異，而且如果向下滾動，則會傳回負差異，無論使用者在哪個方向上監看。

#### <a name="digital-crown-focus"></a>Digital Crown 焦點

就像任何其他介面專案一樣，Digital Crown 也具有焦點的概念。 您可以根據使用者與 watch 互動的方式，將此焦點從 Digital Crown 移到其他介面元素。 

例如，下列任一控制項都可以竊取 Digital Crown 的焦點：

- Picker
- 滑桿
- 捲軸控制器

開發人員必須決定何時要將其自訂介面專案設定為 Digital Crown 的焦點。 Apple 建議使用新的手勢辨識器來獲得自訂 UI 元素的焦點。

### <a name="vertical-paging"></a>垂直分頁

使用者在 watchOS 應用程式中流覽資料表視圖的標準方式是，將滑鼠移至所需的資料片段、按一下特定的資料列來顯示詳細的視圖、完成查看詳細資料時，請按 [上一步] 按鈕，然後針對其他任何在資料表中感興趣的資訊重複此程式：

[![在資料表和詳細資料檢視之間移動](quick-interaction-techniques-images/quick04.png)](quick-interaction-techniques-images/quick04.png#lightbox)

WatchOS 3 的新手，開發人員可以在其資料表視圖控制項上啟用垂直分頁。 啟用這項功能之後，使用者可以滾動尋找資料表視圖資料列，並依之前的資料列來查看其詳細資料。 不過，他們現在可以向上切入來選取資料表中的下一個資料列，或選取下一個資料列 (或使用 Digital Crown) ，而不需要先返回資料表視圖：

[![在資料表和詳細資料檢視之間移動，並向上和向下調整以在其他資料列之間移動](quick-interaction-techniques-images/quick05.png)](quick-interaction-techniques-images/quick05.png#lightbox)

若要啟用此模式，請在 Xcode 中開啟 watchOS 應用程式的分鏡腳本進行編輯，並選取資料表視圖，然後選取 [ **垂直詳細資料分頁** ] 核取方塊：

[![核取 [垂直詳細資料] 分頁核取方塊](quick-interaction-techniques-images/quick06.png)](quick-interaction-techniques-images/quick06.png#lightbox)

確定資料表使用 Segue 來顯示詳細的視圖，並將變更儲存至腳本，並返回 Visual Studio for Mac 進行同步處理。

開發人員可以使用下列程式碼，針對資料表視圖，以程式設計方式對特定資料列進行垂直分頁：

```csharp
// Segue into Vertical Paging and select the first row
MenuTable.PerformSegue (0);
```

使用垂直分頁時，開發人員必須注意，WatchKit 會自動處理控制器的預先載入，因此，您可以在 UI 實際可見之前呼叫某些控制器生命週期方法。

### <a name="notification-enhancements"></a>通知增強功能

通知是一種主要形式的快速互動，使用者通常會在 watchOS 和第一次 Apple Watch 和 watchOS 1 之後提供使用。

一般通知快速互動如下所示：

1. 當收到新的通知時，使用者感覺到通知 Haptic。
2. 他們會引發手腕，以查看通知的簡短外觀介面。
3. 如果他們繼續保持手腕，watchOS 會自動轉換成完整外觀通知介面。

有幾種方式可讓使用者回應通知：

- 針對妥善定義和呈現的通知，使用者將不會執行任何動作，而只會關閉通知。
- 他們可能也會利用通知來啟動 watchOS 應用程式。
- 針對支援自訂動作的通知，使用者可以選取其中一個自訂動作。 這些可以是：
  - **前景動作** -這些動作會啟動應用程式來執行動作。
  - **背景動作** -一律會路由傳送至 watchOS 2 中的 iPhone，但可以路由傳送至 watchOS 3 中的 watchApp。

WatchOS 3 的新內容：

- 通知會在所有平臺上使用類似的 API， (iOS、watchOS、tvOS 和 macOS) 。
- 本機通知可以在 Apple Watch 上排程。
- 如果排程在 Apple Watch 上，背景通知將會路由傳送至應用程式的擴充功能。

#### <a name="notification-scheduling-and-delivery"></a>通知排程和傳遞

當發生下列情況時，來自使用者 iPhone 的通知將會轉送至 Apple Watch：

- IPhone 的畫面已關閉。
- Apple Watch 正在磨損，已解除鎖定。

在 watchOS 3 中，本機通知可以在 Apple Watch 上排程，而且只能在監看式上傳遞。 如果應用程式需要對應的 iPhone 通知，開發人員會進行排程。

藉由在 Apple Watch 和 iPhone 版本的通知中包含相同的通知識別碼，可防止在監看顯示重複的通知。 通知的 Apple Watch 版本將會優先于 iPhone 版本。

由於 watchOS 3 使用的 `UINotification` API 架構與 ios 10 相同，因此如需詳細資料，請參閱我們的 ios 10 [使用者通知架構](~/ios/platform/user-notifications/index.md) 檔。

### <a name="using-spritekit-and-scenekit"></a>使用 SpriteKit 和 SceneKit

WatchOS 3 的新功能，開發人員現在可以在應用程式的消費者介面設計中同時使用 SpritKit 和 SceneKit 物件來呈現2D 和3D 圖形。

已新增兩個新介面類別別來支援這項功能：

- `WKInterfaceSKScene` -適用于使用 SpriteKit 2D 圖形。
- `WKInterfaceSCNScene` -用於使用 SceneKit 3D 圖形。

若要使用這些物件，只要將它們拖曳到設計介面上 Xcode 的 Interface Builder 中的 watch 應用程式分鏡腳本內，然後使用 [屬性] 偵測 **器** 來設定它們即可。

從現在開始，使用 SpriteKit 或 SceneKit 場景的運作方式，與在 iOS 應用程式中的運作方式相同。 Watch 應用程式會藉 `WKInterfaceSKScene` 由呼叫其中一個方法來呈現 `Present` 。 若是 SceneKit，只要設定 `Scene` 物件的屬性即可 `WKInterfaceSCNScene` 。

## <a name="actionable-complications"></a>可採取動作的複雜

在 watchOS 2 中，Apple 引進了協力廠商應用程式的複雜功能。 在 watchOS 3 中，Apple 已擴充開發人員可在 WatchKit 複雜中包含的功能。 

此外，有更多內建的監看式臉部現在可以包含複雜的複雜功能，而且現有的觀賞臉部現在已支援複雜的功能，現在也有更複雜的功能。

另外還有一項新功能，就是使用者可以快速地向左或向右滑動，以轉換其在 Apple Watch 上安裝的所有監看式臉部。 使用者可以在 Apple Watch 的隨附 iPhone 應用程式上使用新的資源庫，新增和自訂新的監看臉部，以及其所能包含的任何複雜性。

由於有這些新功能，Apple 建議 Apple Watch 上的每個應用程式也都必須包含至少一個複雜的情況，因此，所有原生 Apple Watch 應用程式現在都有複雜的功能。

複雜性為應用程式提供下列功能：

- 因為它們一律出現在監看式臉部上，所以其高度 glanceable。
- 複雜的 watchOS 經常更新。 任何包含使用者目前顯示之監看式臉部的應用程式，都至少會更新兩次。
- 任何在使用者目前顯示的監看式臉部上有複雜的應用程式都會保留在記憶體中，讓應用程式快速啟動，並改善應用程式的回應速度。
- 複雜性讓使用者可以輕鬆地在 watchOS 應用程式中啟動特定功能。

## <a name="glanceable-notification"></a>Glanceable 通知

Apple Watch 的通知提供絕佳的可自訂方式，可快速通知使用者事件或新資訊（例如內送訊息）或達成測驗應用程式的目標。

藉由使用通知，可以快速向使用者呈現寶貴的資訊。 在許多情況下，設計完善的通知可以移除使用者實際啟動應用程式的必要。

WatchOS 3 的新手，所有通知現在都支援：

- SpriteKit
- SceneKit
- 內嵌影片

## <a name="enhanced-ui-with-spritekit-and-scenekit"></a>使用 SpriteKit 和 SceneKit 增強的 UI

一般情況下，開發人員可能會在提及 SpriteKit 和 SceneKit 時思考遊戲 UI。 不過，SpriteKit 和 SceneKit 都有助於建立非遊戲 Ui，其中包含不會在 WatchKit 中提供的自訂版面配置、內容和動畫。

例如，來自相片分享應用程式的使用者通知可以使用 SpriteKit 來提供豐富的使用者體驗，方法是包含張貼圖片的使用者，以及擴充使用者體驗的實際影像和其他自訂資訊。

此外，SpriteKit 和 SceneKit 可以與應用程式消費者介面設計中的標準 WatchKit UI 元素混合。

## <a name="simple-navigation"></a>簡單導覽

watchOS 3 提供數種方式，讓開發人員可以在其 watchOS 應用程式中簡化導覽，例如新的 [垂直分頁](#vertical-paging)、 [手勢辨識器支援](#gesture-recognizer-support) 和上面所顯示 [Digital Crown 旋轉](#digital-crown-rotation) 功能。

Digital Crown 對 Apple Watch 而言是唯一的，而且可以用許多不同的方式來簡化導覽。 例如，計時器應用程式可以使用 Digital Crown 來清除可用的計時器長度。

自訂筆勢可以提供新的和獨特的方式，讓使用者與 watch 應用程式互動，也可以用來簡化應用程式導覽。

Apple 建議您尋找結合 watchOS 3 新增的所有快速互動功能的方式，以呈現豐富、簡單快速的 watchOS 應用程式介面。

## <a name="finishing-the-quick-interaction"></a>完成快速互動

設計完善的快速互動體驗，可讓使用者安心卸載手腕 (，並在應用程式完成目前的互動時，與應用程式) 。

當 watch 應用程式正在執行任何類型的網路連線，或使用其隨附的 iPhone 應用程式共用資訊時，這會是特別的問題。 當交易正在進行時，這通常會導致等候中的指標，這在快速互動期間並不理想。 以下列範例為例：

[![使用其隨附 iPhone 應用程式進行網路連線及共用資訊的 watch 應用程式圖表](quick-interaction-techniques-images/quick07.png)](quick-interaction-techniques-images/quick07.png#lightbox)

1. 使用者選擇要在監看時購買的專案。
2. 他們會按一下 [購買] 按鈕。
3. 應用程式會啟動網路交易並顯示載入指標。
4. 一段時間之後，交易就會完成，而應用程式會顯示購買職務確認。
5. 使用者會透過應用程式卸載手腕和 disengages。

從使用者按下 [購買] 按鈕直到交易完成為止，他們的手腕也會在查看載入指標時產生。 為了解決這種情況，Apple 建議開發人員應該向使用者顯示立即的意見反應，而不是顯示載入指標。 

使用 Apple 的建議模型，再次查看相同的快速互動：

[![蘋果建議的模型圖](quick-interaction-techniques-images/quick08.png)](quick-interaction-techniques-images/quick08.png#lightbox)

1. 使用者選擇要在監看時購買的專案。
2. 他們會按一下 [購買] 按鈕。
3. 應用程式會啟動網路交易，並顯示一則訊息，指出已成功開始購買。
4. 使用者會透過應用程式卸載手腕和 disengages。
5. 當交易成功完成一些時間之後，應用程式會顯示本機通知，以通知使用者是否已成功購買。

這次只要使用者按下 [購買] 按鈕，就會顯示一則訊息，指出已開始購買，因此他們可以安心地卸載手腕並結束快速互動。 稍後會在使用者通知中收到交易成功或失敗的通知。 如此一來，使用者只會在進程的「作用中」階段進行應用程式的互動。

針對正在進行網路功能的應用程式，他們可以使用背景 `NSURLSession` 來處理與下載工作的網路通訊。 這可讓應用程式在背景中喚醒，以處理下載的資訊。 針對需要背景處理的應用程式，請使用背景工作判斷提示來處理所需的處理。

## <a name="quick-interaction-design-tips"></a>快速互動設計秘訣

由於所需的快速互動長度是兩秒或更少，因此開發人員應該將焦點放在應用程式從設計程式開始的互動設計。 使用上述的技巧，找出可簡化這些互動 (的區域) 並使用 watchOS 3 的新功能，讓應用程式快速且迅速地回應。

Apple 建議下列各項：

- 將應用程式最常使用的功能帶入最常使用的功能，以專注于快速互動。
- 使用複雜和使用者通知來呈現一般功能和功能。
- 使用 SceneKit 和 SpriteKit 建立豐富的 glanceable 消費者介面。
- 盡可能簡化應用程式中的導覽。
- 絕對不要讓使用者等待，讓他們卸載手腕，並儘快將應用程式拉離。

## <a name="summary"></a>摘要

本文涵蓋了 Apple 在 watchOS 3 中新增的快速互動技術，以及如何在適用于 Apple Watch 的 Xamarin 中加以執行。

## <a name="related-links"></a>相關連結

- [watchOS 範例](/samples/browse/?products=xamarin&term=Xamarin.iOS%2bwatchOS)