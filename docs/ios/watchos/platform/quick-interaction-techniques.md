---
title: "快速互動技術 watchOS 3"
description: "本文章涵蓋快速互動技術 watchOS 3，以及如何實作它們的 Apple Watch Xamarin.iOS 加入 Apple。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 26697F68-AF7E-4A36-988F-85E2674A4DD1
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/17/2017
ms.openlocfilehash: 75a8e807a68a3fccfa76fc7ba1f260818b25174d
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="quick-interaction-techniques-for-watchos-3"></a>快速互動技術 watchOS 3

_本文章涵蓋快速互動技術 watchOS 3，以及如何實作它們的 Apple Watch Xamarin.iOS 加入 Apple。_

提供快速的使用者互動，是必要條件建立吸引人的 Apple Watch 應用程式和複雜性。 新 watchOS 3，Apple 已加入的筆勢辨識器，存取數位皇冠和新的使用者通知與瀏覽技術支援。 這項目，以及已新增支援 SceneKit 並且 SpriteKit，可讓開發人員輕鬆地建立豐富、 一目了然會快速且回應迅速的介面。

## <a name="what-are-quick-interactions"></a>什麼是快速互動

為了讓用來建立適用於 iOS 或 macOS （使用者花費在應用程式與互動的時間量單位分鐘或小時） 的應用程式的開發人員設計 Apple Watch 的成功應用程式頗具挑戰性而且需要不同方法。

在 watchOS，使用者通常想要引發其抬、 快速應用程式互動 （通常為概要幾秒鐘），然後卸除其腕帶並繼續不論它是已執行動作。

在 Apple Watch 上的一般快速互動的一些範例如下：

- 啟動計時器。
- 正在檢查天氣。
- 將標示關閉 todo 清單項目。

若要達成這些目標，必須是 Apple Watch 上的應用程式：

- **一目了然**-也就是說，使用 快速概覽使用者應該要能夠取得所需的資訊。 
- **可付諸行動**-這表示使用者應該能夠做出更快速且充分了解。
- **回應**-這表示使用者應該永遠不會等候接收他們所需的資訊或達到他們想要的動作。

### <a name="quick-interactions-length"></a>快速互動長度

由於 Apple Watch 應用程式的一目了然本質，Apple 建議快速互動的理想的長度應該是兩秒或更少。 由於這兩個的第二個限制，開發人員將需要花相當多的時間，同時設計和實作 Apple Watch 應用程式。 

## <a name="new-watchos-3-features-and-apis"></a>新 watchOS 3 的功能和 Api

Apple 已加入了許多新功能和應用程式開發介面 WatchKit 協助開發人員快速互動加入其 Apple Watch 應用程式：

- watchOS 3 提供新的類型，例如輸入使用者的存取：
    - 筆勢辨識器
    - 數位皇冠旋轉 
- watchOS 3 提供新方法來顯示和更新的詳細資訊，例如：
    - 增強型的資料表瀏覽
    - 新的使用者通知 framework 支援
    - SpriteKit 和 SceneKit 整合

藉由實作這些新功能，開發人員可以確保其 watchOS 3 應用程式是 Glanceable、 可行和 Responsive。

### <a name="gesture-recognizer-support"></a>筆勢辨識器支援

如果開發人員已實作筆勢辨識器在 iOS 中，它們應該很熟悉筆勢辨識器 watchOS 3 中的運作方式。 若要重新整理，筆勢辨識器是可辨識、 預先定義的筆勢成剖析低階觸控事件的物件。

watchOS 3 支援下列四個筆勢辨識器：

- 離散的動作類型：
    - 撥動手勢 (`WKSwipeGestureRecognizer`)。
    - 點選手勢 (`WKTapGestureRecognizer`)。
- 連續的筆勢類型：
    - 取景位置調整筆勢 (`WKPanGestureRecognizer`)。
    - 長按筆勢 (`WKLongPressGestureRecognizer`)。

若要實作其中一個新的筆勢辨識器，只要將它拖曳至設計介面，在 iOS Visual Studio 中的設計工具中適用於 Mac，並設定其屬性。

在程式碼中，回應的動作來處理使用者所觸發的筆勢辨識器。 同樣地，這是相同的方式為在 iOS 中已的處理。

#### <a name="discrete-gesture-states"></a>分隔的筆勢狀態

離散的筆勢辨識出筆勢時與狀態，呼叫的動作 (`WKGestureRecognizerState`) 指派做為：

[ ![](quick-interaction-techniques-images/quick01.png "分隔的筆勢狀態")](quick-interaction-techniques-images/quick01.png)

開始所有離散筆勢`Possible`狀態和轉換至`Failed`或`Recognized`狀態。 當使用不連續的筆勢，開發人員通常不會直接處理狀態。 相反地，他們依賴只辨識出筆勢時呼叫的動作。

#### <a name="continuous-gesture-states"></a>連續的筆勢狀態

連續的筆勢是離散的筆勢，其中的動作稱為多次如筆勢辨識出與稍有不同：

[ ![](quick-interaction-techniques-images/quick02.png "連續的筆勢狀態")](quick-interaction-techniques-images/quick02.png)

同樣地，連續的筆勢一開始為`Possible`狀態，但它們透過多個更新的進度。 此處，開發人員將需要考慮辨識器的狀態，並更新期間的應用程式的 UI`Changed`之前的動作是最後階段`Recognized`或`Canceled`。

#### <a name="gesture-recognizer-usage-tips"></a>筆勢辨識器使用提示

當在 watchOS 3 中使用的筆勢辨識器的 Apple 建議下列：

- 加入群組項目，而不是個別控制項中的筆勢辨識器。 由於 Apple Watch 具有較小的實體螢幕大小，因此通常是較大群組項目與使用者按下的輕鬆目標。 此外，筆勢辨識器發生衝突內建的已在原生 UI 控制項的筆勢。
- 設定相依性關聯性中監看式應用程式的分鏡腳本。
- 某些動作的優先順序高於其他筆勢類型，例如：
    - 捲動
    - 強制觸控
 
### <a name="digital-crown-rotation"></a>數位皇冠旋轉

藉由實作數位皇冠支援 watchOS 3 應用程式中，開發人員可以提供增加的瀏覽速度和精確度的互動使用者。

Apple Watch 應用程式可以使用 watchOS 2，因為`WKInterfacePicker`物件來存取提供一份數位皇冠`WKPickerItems`和選擇器樣式 （清單、 堆疊或映像的順序）。 watchOS 則允許使用者使用數位皇冠從清單中選取的項目。

當使用`WKInterfacePicker`，WatchKit 正在處理的大部分的工作：

- 繪圖清單和個別的介面項目。
- 正在處理的數位皇冠事件。
- 選取的項目時呼叫動作。

新增至 watchOS 3，開發人員現在可以直接存取數位皇冠旋轉事件可讓他們建立他們自己的回應旋轉值 UI 項目。

下列項目可提供數位皇冠存取：

- `WKCrownSequencer` -提供每秒旋轉的存取。
- `WKCrownDelegate` -提供存取旋轉差異事件。

#### <a name="rotations-per-second"></a>每秒旋轉

從數位皇冠存取旋轉 Per Second 時，使用物理基礎動畫。 若要存取旋轉秒，使用`CrownSequencer`屬性`WKInterfaceController`監看式延伸模組。 例如: 

```csharp
var rotationsPerSecond = CrownSequencer.RotationsPerSecond;
```

#### <a name="rotational-deltas"></a>旋轉的差異

您可以使用從數位皇冠旋轉差異旋轉的次數。 使用`CrownDidRotate`覆寫的方法`WKCrownDelegate`存取旋轉的差異。 例如: 

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

此應用程式會維護 accumulator (`AccumulatedRotations`) 若要判斷旋轉數目。 一個完整的數位皇冠輪替等於累積的差異`1.0`，因此會二分之一旋轉`0.5`。

Apple 已離開其開發人員可以決定旋轉計數如何對應至要更新的 UI 項目上變更的敏感性。

符號 (`+/-`) 旋轉差異表示使用者已開啟數位皇冠的方向：

[ ![](quick-interaction-techniques-images/quick03.png "旋轉差異的正負號表示使用者已開啟數位皇冠方向")](quick-interaction-techniques-images/quick03.png)


如果使用者向上捲動，WatchKit 會傳回正差異且如果向下捲動，然後負差異將會傳回，無論何種方向使用者穿中的監看式。

#### <a name="digital-crown-focus"></a>數位皇冠焦點

就像任何其他介面元素，數位皇冠具有焦點的概念。 這項重點可以根據使用者互動如何監看式其他介面項目遠離數位皇冠移位。 

例如，下列控制項的任何無法竊取數位皇冠的重點：

- 選擇器
- 滑桿
- 捲動控制器

它是開發人員可以決定其自訂介面項目必須是數位皇冠焦點時。 Apple 建議使用新的筆勢辨識器取得自訂的 UI 項目中的焦點。

### <a name="vertical-paging"></a>垂直分頁

使用者瀏覽 watchOS 應用程式中的資料表檢視的標準方式，就是捲動到所需資料片段，請點選上特定資料列顯示詳細的檢視，請點選 [上一頁] 按鈕時完成檢視詳細資料並重複此程序的任何其他資訊，y 有興趣從資料表中：

[ ![](quick-interaction-techniques-images/quick04.png "資料表與詳細資料檢視之間移動")](quick-interaction-techniques-images/quick04.png)

新增至 watchOS 3，開發人員可以垂直分頁上啟用其資料表檢視表的控制項。 啟用這項功能，使用者可以向上捲動以尋找資料表 檢視的資料列，並點選要檢視其詳細資料與之前的資料列。 不過，它們可以現在向上撥動以選取下一個資料列，資料表中或下移來選取上一個資料列 （或使用數位皇冠），完全不必返回資料表檢視第一次：

[ ![](quick-interaction-techniques-images/quick05.png "資料表與詳細資料檢視之間移動和撥動向上和向下的其他資料列之間移動")](quick-interaction-techniques-images/quick05.png)

若要啟用此模式中，在 Xcode 中開啟 watchOS 應用程式的分鏡腳本，進行編輯，選取 [資料表] 檢視並檢查**垂直詳細分頁**核取方塊：

[ ![](quick-interaction-techniques-images/quick06.png "選取垂直分頁的詳細資料的核取方塊")](quick-interaction-techniques-images/quick06.png)

請確定資料表使用 Segues 來顯示詳細的檢視，然後儲存變更到分鏡腳本並返回 Visual Studio for Mac 同步處理。

開發人員可以透過程式設計方式進行垂直分頁某個資料列使用下列程式碼，針對資料表檢視：

```csharp
// Segue into Vertical Paging and select the first row
MenuTable.PerformSegue (0);
```

當使用垂直分頁時，開發人員必須注意 WatchKit 將會自動處理預先載入的控制站，如此一來，呼叫某些控制器存留週期方法實際上看到 UI 之前。

### <a name="notification-enhancements"></a>通知的增強功能

通知會在使用者通常會遇到 watchOS 的快速互動的主要形式，而且之後的第一個 Apple Watch 及 watchOS 1 已可用。

典型的通知快速互動如下所示：

1. 收到新的通知時，使用者會感覺 Haptic 通知。
2. 在引發其腕帶查看通知的簡短查詢的介面。
3. 如果他們繼續保留其腕帶引發，watchOS 會自動轉換成很長的查詢通知介面。

有數種方式，使用者可能回應的通知：

- 格式定義，並顯示通知，使用者將會執行任何動作，並只需關閉通知。
- 它們也可能會啟動 watchOS 應用程式通知的點選。
- 為支援自訂動作的通知，使用者可能會選取其中一個自訂動作。 這些可以是：
    - **前景動作**-這些啟動應用程式執行的動作。
    - **背景動作**-永遠路由傳送至 iPhone 的 watchOS 2，但會路由至 watchApp watchOS 3 中。

WatchOS 3： 的新功能

* 通知會使用類似的 API，跨所有平台 （iOS、 watchOS、 tvOS 及 macOS）。
* 本機通知可以在 Apple Watch 上排程。
* 如果排程在 Apple Watch 上時，背景通知將會路由至應用程式擴充功能。

#### <a name="notification-scheduling-and-delivery"></a>通知排程與傳遞

當發生下列情況時通知使用者的 iPhone 從會向前 Apple Watch 為：

* 在 iPhone 螢幕為關閉。
* Apple Watch 正在已損壞，且已解除鎖定。

WatchOS 3，在本機的通知可以在 Apple Watch 上排程，並只會遞送上監看式。 是由排程對應的 iPhone 通知所需的應用程式開發人員。

藉由加入相同通知識別碼 Apple Watch 和通知的 iPhone 版本上，將會禁止重複的通知，不會再顯示在 監看式。 通知的 Apple Watch 版本將會優先於 iPhone 版本。

因為 watchOS 3 會使用相同`UINotification`API 架構，因為 iOS 10，請參閱我們的 iOS 10[使用者通知架構](~/ios/platform/user-notifications/index.md)文件以取得詳細資料。

### <a name="using-spritekit-and-scenekit"></a>使用 SpriteKit 和 SceneKit

新增至 watchOS 3，開發人員現在可以使用 SpritKit 和 SceneKit 物件在其應用程式使用者介面設計呈現 2D 和 3D 圖形。

若要支援這項功能已加入兩個新的介面類別：

- `WKInterfaceSKScene` -若為使用 SpriteKit 2D 圖形。
- `WKInterfaceSCNScene` -若為使用 SceneKit 3D 圖形。

若要使用這些物件，只要將它們拖曳到設計介面內 Xcode 的介面產生器中監看式應用程式的分鏡腳本，並使用**屬性偵測器**來設定它們。

從現在開始，使用 SpriteKit 或 SceneKit 場景的作用相同內部 iOS 應用程式。 監看式應用程式將會顯示`WKInterfaceSKScene`透過呼叫其中一個`Present`方法。 SceneKit，只要設定`Scene`屬性`WKInterfaceSCNScene`物件。

## <a name="actionable-complications"></a>可採取動作的複雜性

WatchOS 2，在 Apple 引進第 3 個合作對象應用程式的複雜性。 WatchOS 3，在 Apple 已展開的開發人員可以併入 WatchKit 複雜功能的能力。 

此外，多個內建在監看式字體現在可以包含的複雜性和現有監看式面臨已經支援的複雜性可以現在包含更多的複雜性。

也新增為能夠讓使用者快速撥動向左或右監看式面，它們已安裝在其 Apple Watch 的所有轉換。 Apple Watch 的附屬 iPhone 應用程式上使用新的圖庫，使用者可以新增並自訂新的監看式字體和任何它們可以包含的複雜。

這些新功能，因為 Apple 建議 Apple Watch 上的每個應用程式也應該包含至少一個複雜的問題，因此，所有的原生 Apple Watch 應用程式現在有複雜性。

複雜性會提供應用程式的下列功能：

- 因為它們永遠都存在於 watch 錶面上，它們是高一目了然。
- WatchOS 會經常更新的複雜性。 任何包含使用者的目前所顯示的 watch 錶面上複雜功能的應用程式會更新至少兩次一小時。
- 任何應用程式與使用者的目前所顯示的 watch 錶面上複雜功能會保留在記憶體，而使快速啟動應用程式，並且可改善的應用程式的回應速度。
- 複雜性，方便使用者啟動 watchOS 應用程式中的特定功能。

## <a name="glanceable-notification"></a>一目了然通知

Apple Watch 上的通知提供絕佳、 可自訂的方式，來快速地通知的事件或新的資訊，包括內送訊息或達到健身應用程式中的目標使用者。

藉由使用通知，寶貴的資訊可以快速地呈現給使用者。 在許多情況下，設計良好的通知可以移除您不再需要實際啟動應用程式的使用者。

新手 watchOS 3，所有通知現可支援：

- SpriteKit
- SceneKit
- 內嵌視訊

## <a name="enhanced-ui-with-spritekit-and-scenekit"></a>SpriteKit 與 SceneKit 增強的 UI

一般而言，開發人員可以將遊戲的 UI 時 SpriteKit 和 SceneKit 所述。 不過，SpriteKit 和 SceneKit 可用於建立非遊戲包括自訂版面配置，內容的 Ui 和沒有單獨 WatchKit 中可能有的動畫。

例如，從相片共用應用程式的使用者通知也可以使用 SpriteKit，可透過包括張貼的圖片，以及實際的影像和其他增強使用者體驗的自訂的資訊的使用者，提供豐富的使用者經驗。

此外，SpriteKit 和 SceneKit 可以混合與在應用程式的使用者介面設計的標準 WatchKit UI 項目。

## <a name="simple-navigation"></a>簡單的巡覽

watchOS 3 提供數種方式，開發人員可以簡化其 watchOS 應用程式，例如新內瀏覽[垂直分頁](#Vertical-Paging)，[筆勢辨識器支援](#Gesture-Recognizer-Support)和[數位皇冠旋轉](#Digital-Crown-Rotation)以上所顯示的功能。

數位皇冠是 Apple Watch 唯一的而且可以許多不同的方式，來簡化瀏覽。 例如，計時器應用程式可以使用數位皇冠透過可用的計時器長度快轉。

自訂軌跡可以呈現新且唯一的方式，讓使用者與監看式應用程式互動，也可以用來簡化應用程式瀏覽。

Apple 建議找尋結合所有 watchOS 3 提供豐富、 方便而快速使用 watchOS 應用程式介面中加入的新快速互動功能。

## <a name="finishing-the-quick-interaction"></a>完成快速的互動

設計良好的快速互動體驗將會授與使用者的信心，要卸除其腕帶 （並卸除與應用程式） 當他們完成目前的互動。

這特別會變成其中一個問題就時監看式應用程式會執行任何類型的網路連線或使用其附屬 iPhone 應用程式共用資訊。 這可以通常會導致等候指標進行交易時，快速互動期間不需要這樣做。 請使用以下範例：

[ ![](quick-interaction-techniques-images/quick07.png "監看式應用程式進行網路連線，並且使用其附屬 iPhone 應用程式共用資訊的圖表")](quick-interaction-techniques-images/quick07.png)

1. 使用者選擇要監看式上購買的項目。
2. 他們點選 [購買] 按鈕。
3. 應用程式會啟動網路交易，並顯示載入指標。
4. 稍後，在交易完成和應用程式顯示訂單確認。
5. 使用者會卸除其腕帶並 disengages 與應用程式。

從使用者點選 [購買] 按鈕，直到交易完成的時，他們會有引發其腕帶，查看正在載入指標。 若要解決這種情況下，Apple 建議開發人員應該提供立即的回應，而不會顯示載入指標使用者。 

使用 Apple 的建議的模型，看看相同快速互動一次：

[ ![](quick-interaction-techniques-images/quick08.png "蘋果建議的模型圖表")](quick-interaction-techniques-images/quick08.png)

1. 使用者選擇要監看式上購買的項目。
2. 他們點選 [購買] 按鈕。
3. 應用程式會啟動網路交易，並顯示訊息，指出購買程序已順利啟動。
4. 使用者會卸除其腕帶並 disengages 與應用程式。
5. 當在交易成功完成之後的某個時間時，應用程式會顯示本機通知來通知成功購買的使用者。

這個時間，當使用者點選 [購買] 按鈕則會顯示訊息指出，購買程序啟動後，好讓您可以有信心地卸除其腕帶並結束目前的快速互動。 稍後就會接到通知的成功或失敗的使用者通知中的交易。 如此一來，使用者只與應用程式互動的程序的 「 作用中 」 階段。

針對正在網路功能的應用程式，他們可以使用背景`NSURLSession`來處理與下載工作之間的網路通訊。 這可讓應用程式被喚醒，在背景處理下載的資訊。 對於需要背景處理應用程式，使用背景工作判斷提示來處理要求的處理。

## <a name="quick-interaction-design-tips"></a>快速互動設計秘訣

由於快速互動所需的長度是兩秒或更少，開發人員應該專注於應用程式的設計程序一開始從互動方式設計。 尋找這些互動 （使用上述的技巧） 可簡化的位置，並使用 watchOS 3 的新功能進行快速且回應迅速的應用程式的區域。

Apple 建議下列各項：

- 焦點放在快速互動的向前帶的應用程式最常使用的功能。
- 使用複雜和使用者通知介面通用的功能和函式。
- SceneKit 與 SpriteKit 建立豐富、 一目了然使用者介面。
- 您應該盡可能簡化應用程式內的導覽。
- 永不使等一下，讓他們要卸除其腕帶並儘速解除與應用程式的使用者。

## <a name="summary"></a>總結

這篇文章已涵蓋快速互動技術 watchOS 3，以及如何實作它們的 Apple Watch Xamarin.iOS 加入 Apple。

## <a name="related-links"></a>相關連結

- [watchOS 範例](https://developer.xamarin.com/samples/watchos/all/)
