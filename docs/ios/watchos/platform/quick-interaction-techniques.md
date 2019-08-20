---
title: Xamarin 中 watchOS 3 的快速互動技術
description: 本文涵蓋 Apple 已在 watchOS 3 中新增的快速互動技術, 以及如何在 Apple Watch 的 Xamarin 中執行。
ms.prod: xamarin
ms.assetid: 26697F68-AF7E-4A36-988F-85E2674A4DD1
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: ddefae8ad24b74a3c9ed05bf46b54430c00beaea
ms.sourcegitcommit: 0df727caf941f1fa0aca680ec871bfe7a9089e7c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2019
ms.locfileid: "69620515"
---
# <a name="quick-interaction-techniques-for-watchos-3-in-xamarin"></a>Xamarin 中 watchOS 3 的快速互動技術

_本文涵蓋 Apple 已在 watchOS 3 中新增的快速互動技術, 以及如何在 Apple Watch 的 Xamarin 中執行。_

提供快速的使用者互動, 是建立引人注目的 Apple Watch 應用程式和複雜的必備要素。 WatchOS 3 的新手新增了對手勢辨識器的支援、Digital Crown 的存取權, 以及新的使用者通知和流覽技術。 這同時提供 SceneKit 和 SpriteKit 的支援, 可讓開發人員輕鬆地建立既快速又迅速的豐富 glanceable 介面。

## <a name="what-are-quick-interactions"></a>什麼是快速互動

針對用來建立 iOS 或 macOS 應用程式的開發人員 (其中使用者花費在數分鐘或數小時內進行與應用程式互動的時間量), 設計成功的 Apple Watch 應用程式可能會是一項挑戰, 而且需要不同的出價.

在 watchOS 中, 使用者通常會想要提高手腕、快速地與應用程式互動 (通常只需要短暫幾秒鐘), 然後卸載手腕並繼續進行。

以下是一些典型的 Apple Watch 快速互動範例:

- 啟動計時器。
- 檢查天氣。
- 將專案標記為待辦事項清單。

若要達成這些目標, Apple Watch 上的應用程式必須:

- **Glanceable** -這表示只要快速概覽, 使用者就應該能夠取得所需的資訊。 
- 可**操作**-這表示使用者應該能夠快速做出明智的決策。
- **回應**式-這表示使用者絕對不應等待收到所需的資訊, 或達到他們想要的動作。

### <a name="quick-interactions-length"></a>快速互動長度

基於 Apple Watch 應用程式的 glanceable 性質, Apple 建議快速互動的理想長度應為兩秒鐘或更少。 由於這兩個秒的限制, 開發人員需要花相當長的時間來設計和執行 Apple Watch 應用程式。 

## <a name="new-watchos-3-features-and-apis"></a>新的 watchOS 3 功能與 Api

Apple 已將數個新功能和 Api 新增至 WatchKit, 以協助開發人員新增其 Apple Watch 應用程式的快速互動:

- watchOS 3 提供對新使用者輸入類型的存取, 例如:
  - 手勢辨識器
  - Digital Crown 旋轉 
- watchOS 3 提供新的方式來顯示及更新資訊, 例如:
  - 增強型資料表流覽
  - 新的使用者通知架構支援
  - SpriteKit 和 SceneKit 整合

藉由實施這些新功能, 開發人員可以確保其 watchOS 3 應用程式 Glanceable、可採取動作且回應迅速。

### <a name="gesture-recognizer-support"></a>手勢辨識器支援

如果開發人員已在 iOS 中執行手勢辨識器, 則應該非常熟悉手勢辨識器在 watchOS 3 中的工作方式。 若要重新整理, 手勢辨識器是將低層級觸控事件剖析成可辨識的預先定義手勢的物件。

watchOS 3 將支援四個下列的手勢辨識器:

- 離散手勢類型:
  - 滑動手勢 (`WKSwipeGestureRecognizer`)。
  - 攻點手勢 (`WKTapGestureRecognizer`)。
- 連續手勢類型:
  - 平移手勢 (`WKPanGestureRecognizer`)。
  - 長按手勢 (`WKLongPressGestureRecognizer`)。

若要執行其中一個新的手勢辨識器, 只需將它拖曳到 iOS 設計工具的設計介面上 Visual Studio for Mac 並設定其屬性。

在程式碼中, 回應辨識器的動作, 以處理使用者所觸發的手勢。 同樣地, 這會以在 iOS 中處理的相同方式來完成。

#### <a name="discrete-gesture-states"></a>離散手勢狀態

針對離散手勢, 當手勢已辨識且狀態 (`WKGestureRecognizerState`) 指派為時, 就會呼叫此動作:

[![](quick-interaction-techniques-images/quick01.png "離散手勢狀態")](quick-interaction-techniques-images/quick01.png#lightbox)

所有的離散手勢都會以`Possible`狀態開始, 並轉換成`Failed`或`Recognized`狀態。 使用離散手勢時, 開發人員通常不會直接處理狀態。 相反地, 它們會依賴只辨識手勢時所呼叫的動作。

#### <a name="continuous-gesture-states"></a>連續手勢狀態

連續手勢與離散手勢稍有不同, 因為在辨識手勢時, 會多次呼叫動作:

[![](quick-interaction-techniques-images/quick02.png "連續手勢狀態")](quick-interaction-techniques-images/quick02.png#lightbox)

同樣地, 連續手勢會在`Possible`狀態下開始, 但它們會在多個更新中進行。 在這裡, 開發人員必須考慮辨識器的狀態, 並在`Changed`階段期間更新應用程式的 UI, 直到手勢最後`Canceled` `Recognized`或。

#### <a name="gesture-recognizer-usage-tips"></a>手勢辨識器使用提示

使用 watchOS 3 中的手勢辨識器時, Apple 建議下列各項:

- 將手勢辨識器新增至群組元素, 而不是個別的控制項。 由於 Apple Watch 的實體螢幕大小較小, 因此群組元素通常會是更大且更容易進行的目標, 讓使用者得以點擊。 此外, 筆勢辨識器可能會與已經在原生 UI 控制項中的內建手勢衝突。
- 在監看式應用程式的分鏡腳本中設定相依性關聯性。
- 有些手勢的優先順序高於其他手勢類型, 例如:
  - 捲動
  - Force Touch
 
### <a name="digital-crown-rotation"></a>Digital Crown 旋轉

藉由在 watchOS 3 應用程式中執行 Digital Crown 支援, 開發人員可以為其使用者提供更高的流覽速度和精確度互動。

從 watchOS 2 開始, Apple Watch 應用程式可以藉`WKInterfacePicker`由提供`WKPickerItems`和選擇器樣式 (清單、堆疊或影像序列) 的清單, 來使用物件來存取 Digital Crown。 然後, watchOS 會允許使用者使用 Digital Crown 從清單中選取專案。

使用`WKInterfacePicker`時, WatchKit 會處理大部分的工作, 方法是:

- 繪製清單和個別介面元素。
- 處理 Digital Crown 事件。
- 在選取專案時呼叫動作。

WatchOS 3 的新手, 開發人員現在可以直接存取 Digital Crown 的輪替事件, 讓他們能夠建立自己的 UI 元素來回應旋轉值。

Digital Crown 存取是由下列元素所提供:

- `WKCrownSequencer`-提供每秒旋轉的存取權。
- `WKCrownDelegate`-提供對旋轉 delta 事件的存取。

#### <a name="rotations-per-second"></a>每秒旋轉數

當您使用以物理為基礎的動畫時, 從 Digital Crown 存取每秒的旋轉就很有用。 若要存取每秒的旋轉, 請`CrownSequencer`使用 Watch 延伸`WKInterfaceController`模組之的屬性。 例如：

```csharp
var rotationsPerSecond = CrownSequencer.RotationsPerSecond;
```

#### <a name="rotational-deltas"></a>旋轉差異

使用 Digital Crown 的旋轉差異來計算旋轉次數。 使用的覆`WKCrownDelegate`寫方法來存取旋轉的差異。 `CrownDidRotate` 例如：

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

在這裡, 應用程式會維護`AccumulatedRotations`累計 (), 以判斷旋轉的數目。 Digital Crown 的一個完整旋轉等於的累積差異`1.0` , 而一半的旋轉則是。 `0.5`

Apple 已將其留給開發人員, 決定如何將旋轉計數對應至所要更新之 UI 元素上的變更敏感度。

旋轉差異的`+/-`正負號 () 表示使用者正在開啟 Digital Crown 的方向:

[![](quick-interaction-techniques-images/quick03.png "旋轉差異的正負號表示使用者正在開啟的方向 Digital Crown")](quick-interaction-techniques-images/quick03.png#lightbox)


如果使用者正在向上滾動, 則 WatchKit 會傳回正面差異, 而且如果向下滾動, 則不論使用者在監看哪一個方向, 都將傳回負面差異。

#### <a name="digital-crown-focus"></a>Digital Crown 焦點

就像任何其他介面元素一樣, Digital Crown 具有焦點的概念。 這個焦點可以根據使用者與 watch 互動的方式, 從 Digital Crown 移至其他介面元素。 

例如, 下列任何控制項可能會竊取 Digital Crown 的焦點:

- Picker
- 滑桿
- 滾動控制器

由開發人員決定其自訂介面元素何時必須是 Digital Crown 的焦點。 Apple 建議您使用新的手勢辨識器來取得自訂 UI 元素中的焦點。

### <a name="vertical-paging"></a>垂直分頁

使用者在 watchOS 應用程式中流覽資料表視圖的標準方式, 是滾動到所需的資料片段, 並在特定的列上按一下以顯示詳細的視圖, 然後在完成詳細資料的查看後按 [上一步] 按鈕, 並為任何其他資訊重複此程式。y 對資料表中的感興趣:

[![](quick-interaction-techniques-images/quick04.png "在資料表與詳細資料檢視之間移動")](quick-interaction-techniques-images/quick04.png#lightbox)

WatchOS 3 的新手, 開發人員可以在其資料表視圖控制項上啟用垂直分頁。 啟用這項功能後, 使用者可以滾動到尋找資料表查看列, 然後按資料列來查看其詳細資料。 不過, 他們現在可以向上滑動來選取資料表中的下一個資料列, 或選取下一個資料列 (或使用 Digital Crown), 而不需要先回到資料表視圖:

[![](quick-interaction-techniques-images/quick05.png "在資料表和詳細資料檢視之間移動, 並向上和向下移動, 以在其他資料列之間移動")](quick-interaction-techniques-images/quick05.png#lightbox)

若要啟用此模式, 請在 Xcode 中開啟 watchOS 應用程式的腳本以進行編輯, 選取資料表視圖並核取 [**垂直詳細資料分頁**] 核取方塊:

[![](quick-interaction-techniques-images/quick06.png "核取 [垂直詳細資料分頁] 核取方塊")](quick-interaction-techniques-images/quick06.png#lightbox)

請確定資料表使用 Segue 來顯示詳細的視圖, 並將變更儲存至分鏡腳本, 並返回 Visual Studio for Mac 進行同步處理。

開發人員可以使用下列針對資料表視圖的程式碼, 以程式設計方式對特定資料列進行垂直分頁:

```csharp
// Segue into Vertical Paging and select the first row
MenuTable.PerformSegue (0);
```

使用垂直分頁時, 開發人員必須注意, WatchKit 會自動處理控制器的預先載入, 因此在實際顯示 UI 之前, 可以呼叫某些控制器生命週期方法。

### <a name="notification-enhancements"></a>通知增強功能

通知是一種主要的快速互動形式, 使用者通常會在 watchOS 時體驗, 並在第一 Apple Watch 和 watchOS 1 之後提供。

一般通知快速互動如下所示:

1. 當收到新通知時, 使用者會感覺到通知 Haptic。
2. 他們會引發手腕, 以查看通知的簡短外觀介面。
3. 如果他們繼續保持手腕, watchOS 會自動轉換成長時間的外觀通知介面。

有數種方式可讓使用者回應通知:

- 針對定義良好且呈現的通知, 使用者不會執行任何動作, 只會關閉通知。
- 他們也可以按一下通知來啟動 watchOS 應用程式。
- 對於支援自訂動作的通知, 使用者可能會選取其中一個自訂動作。 這些可以是:
  - **前景動作**-這些會啟動應用程式來執行動作。
  - **背景動作**-一律會路由傳送至 watchOS 2 中的 iPhone, 但可以路由至 watchOS 3 中的 watchApp。

WatchOS 3 的新內容:

* 通知會在所有平臺 (iOS、watchOS、tvOS 和 macOS) 上使用類似的 API。
* 本機通知可以在 Apple Watch 上排程。
* 背景通知會路由傳送至應用程式的延伸模組 (如果已在 Apple Watch 上排程)。

#### <a name="notification-scheduling-and-delivery"></a>通知排程和傳遞

當下列情況發生時, 來自使用者 iPhone 的通知將會轉送到 Apple Watch:

* IPhone 的畫面已關閉。
* Apple Watch 已磨損, 而且已解除鎖定。

在 watchOS 3 中, 本機通知可以在 Apple Watch 上排程, 而且只會在監看式上傳遞。 開發人員必須排程對應的 iPhone 通知 (如果應用程式需要的話)。

藉由在 Apple Watch 和 iPhone 版本的通知上包含相同的通知識別碼, 可防止在監看式上顯示重複的通知。 通知的 Apple Watch 版本將優先于 iPhone 版本。

由於 watchOS 3 使用與 ios `UINotification` 10 相同的 API 架構, 請參閱我們的 ios 10[使用者通知架構](~/ios/platform/user-notifications/index.md)檔, 以取得更多詳細資料。

### <a name="using-spritekit-and-scenekit"></a>使用 SpriteKit 和 SceneKit

WatchOS 3 的新功能, 開發人員現在可以在其應用程式的使用者介面設計中同時使用 SpritKit 和 SceneKit 物件, 以同時呈現2D 和3D 圖形。

已加入兩個新介面類別別來支援這項功能:

- `WKInterfaceSKScene`-適用于使用 SpriteKit 2D 圖形。
- `WKInterfaceSCNScene`-適用于使用 SceneKit 3D 圖形。

若要使用這些物件, 只要將其拖曳至 Xcode 的 Interface Builder 中監看式應用程式分鏡腳本內的設計介面, 然後使用 [屬性] 偵測**器**加以設定即可。

從這裡開始, 使用 SpriteKit 或 SceneKit 場景, 其運作方式與在 iOS 應用程式中相同。 Watch 應用程式會藉`WKInterfaceSKScene`由呼叫其中一個`Present`方法來呈現。 針對 SceneKit, 只需設定`Scene` `WKInterfaceSCNScene`物件的屬性即可。

## <a name="actionable-complications"></a>可操作的複雜性

在 watchOS 2 中, Apple 引進了協力廠商應用程式的複雜性。 在 watchOS 3 中, Apple 已擴充開發人員可以在 WatchKit 中包含的功能。 

此外, 多個內建的監看式臉部現在可以包含複雜性, 而且現有的監看表面已經支援複雜, 現在也包含更複雜的功能。

此外, 使用者也可以快速地向左或向右滑動, 以透過其 Apple Watch 上所安裝的所有監看面進行轉換。 使用 Apple Watch 隨附 iPhone 應用程式上的新圖庫, 使用者可以新增和自訂新的監看面, 以及他們可以包含的任何複雜資訊。

基於這些新功能, Apple 建議 Apple Watch 上的每個應用程式也應該包含至少一個複雜的, 因此, 所有的原生 Apple Watch 應用程式現在都有複雜的複雜性。

複雜性會將下列功能提供給應用程式:

- 它們非常 glanceable, 因為它們一定會出現在監看式臉部上。
- WatchOS 經常會更新複雜的情況。 在使用者目前顯示的監看式臉部上, 任何包含複雜的應用程式, 至少會更新兩次。
- 在使用者目前顯示的監看式表面上有複雜的任何應用程式都會保留在記憶體中, 讓應用程式快速啟動, 並提升應用程式回應的速度。
- 複雜性可讓使用者輕鬆地在 watchOS 應用程式中啟動特定功能。

## <a name="glanceable-notification"></a>Glanceable 通知

Apple Watch 的通知提供了絕佳、可自訂的方式, 可快速通知使用者事件或新資訊, 例如內送訊息或在健身應用程式中達到目標。

藉由使用通知, 您可以快速地向使用者呈現寶貴的資訊。 在許多情況下, 設計良好的通知可以移除使用者實際啟動應用程式的必要。

WatchOS 3 的新手, 所有通知現在都支援:

- SpriteKit
- SceneKit
- 內嵌影片

## <a name="enhanced-ui-with-spritekit-and-scenekit"></a>具有 SpriteKit 和 SceneKit 的增強型 UI

一般而言, 開發人員可能會在提及 SpriteKit 和 SceneKit 時, 考慮遊戲 UI。 不過, SpriteKit 和 SceneKit 可以用來建立非遊戲 Ui, 其中包括不可能單獨 WatchKit 的自訂版面配置、內容和動畫。

例如, 來自相片分享應用程式的使用者通知可以使用 SpriteKit 來提供豐富的使用者體驗, 方法是包含張貼圖片的使用者以及來擴充使用者體驗的其他自訂資訊。

此外, SpriteKit 和 SceneKit 都可以與應用程式的使用者介面設計中的標準 WatchKit UI 元素混合使用。

## <a name="simple-navigation"></a>簡單導覽

watchOS 3 提供幾種方法, 讓開發人員可以在其 watchOS 應用程式中簡化導覽, 例如新的[垂直分頁](#vertical-paging)、[手勢辨識器支援](#gesture-recognizer-support)和以上所示的[Digital Crown 旋轉](#digital-crown-rotation)功能。

Digital Crown 對 Apple Watch 而言是唯一的, 而且可以用許多不同的方式來簡化導覽。 例如, 計時器應用程式可以使用 Digital Crown 來清理可用的計時器長度。

自訂手勢可以提供全新且獨特的方式, 讓使用者與 watch 應用程式互動, 也可以用來簡化應用程式導覽。

Apple 建議尋找方法來結合 watchOS 3 中新增的所有快速互動功能, 以提供豐富、簡單快速的 watchOS 應用程式介面。

## <a name="finishing-the-quick-interaction"></a>完成快速互動

設計良好的快速互動體驗, 可讓使用者在完成目前的互動時, 安心卸載其手腕 (並卸離應用程式)。

當監看式應用程式正在執行任何類型的網路連線, 或與隨附的 iPhone 應用程式共用資訊時, 就會發生此問題。 當交易正在進行時, 這通常會導致等候指示器, 這在快速互動期間並不理想。 請使用以下範例：

[![](quick-interaction-techniques-images/quick07.png "監看式應用程式執行網路連線和與隨附 iPhone 應用程式共用資訊的圖表")](quick-interaction-techniques-images/quick07.png#lightbox)

1. 使用者選擇要在 watch 上購買的專案。
2. 他們會按一下 [購買] 按鈕。
3. 應用程式會啟動網路交易, 並顯示載入指示器。
4. 稍後, 交易會完成, 而應用程式會顯示購買確認。
5. 使用者透過應用程式卸載其手腕和 disengages。

從使用者按下 [購買] 按鈕直到交易完成為止, 他們會看到其手腕出現了一個載入指示器。 為了解決這種情況, Apple 建議開發人員應該向使用者呈現立即的意見反應, 而不是顯示載入指示器。 

使用 Apple 的建議模型, 再次查看相同的快速互動:

[![](quick-interaction-techniques-images/quick08.png "蘋果建議的模型圖表")](quick-interaction-techniques-images/quick08.png#lightbox)

1. 使用者選擇要在 watch 上購買的專案。
2. 他們會按一下 [購買] 按鈕。
3. 應用程式會啟動網路交易, 並顯示一則訊息, 指出已成功啟動購買。
4. 使用者透過應用程式卸載其手腕和 disengages。
5. 當交易成功完成一些時間之後, 應用程式會顯示本機通知, 通知使用者已成功購買。

這次, 只要使用者按下 [購買] 按鈕, 就會顯示一則訊息, 指出已開始購買, 讓他們可以放心地放置手腕, 並在此時結束快速互動。 之後, 他們會收到使用者通知中交易成功或失敗的通知。 如此一來, 使用者只會在程式的「作用中」階段與應用程式互動。

對於正在進行網路功能的應用程式, 他們可以使用`NSURLSession`背景來處理與下載工作的網路通訊。 這可讓應用程式在背景中喚醒, 以處理下載的資訊。 針對需要背景處理的應用程式, 請使用背景工作判斷提示來處理所需的處理。

## <a name="quick-interaction-design-tips"></a>快速互動設計秘訣

由於快速互動所需的長度是兩秒或更少, 因此開發人員應該將焦點放在應用程式從設計流程的一開始就進行的互動設計。 尋找可簡化這些互動的區域 (使用上述的技術), 並使用 watchOS 3 的新功能, 讓應用程式快速且迅速回應。

Apple 建議下列各項:

- 藉由將應用程式最常使用的功能帶往前, 將焦點放在快速互動。
- 使用複雜和使用者通知來呈現常用的功能和功能。
- 使用 SceneKit 和 SpriteKit 建立豐富、glanceable 的使用者介面。
- 請盡可能簡化應用程式內的流覽。
- 絕對不要讓使用者等待, 讓他們卸載手腕, 並儘快卸載應用程式。

## <a name="summary"></a>總結

本文涵蓋 Apple 已在 watchOS 3 中新增的快速互動技術, 以及如何在 Apple Watch 的 Xamarin 中執行。

## <a name="related-links"></a>相關連結

- [watchOS 範例](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+watchOS)
