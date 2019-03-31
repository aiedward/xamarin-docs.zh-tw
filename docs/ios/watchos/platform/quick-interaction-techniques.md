---
title: 針對在 Xamarin 中 watchos 3 多快速互動技術
description: 本文章涵蓋快速互動技術加入 Apple watchOS 3，以及如何在 Xamarin.iOS 中的 Apple Watch 實作它們。
ms.prod: xamarin
ms.assetid: 26697F68-AF7E-4A36-988F-85E2674A4DD1
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: 5086724b565fb95274c4988ca1b6e4bb11064575
ms.sourcegitcommit: 946ce514fd6575aa6b93ff24181e02a60b24b106
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/30/2019
ms.locfileid: "58677933"
---
# <a name="quick-interaction-techniques-for-watchos-3-in-xamarin"></a>針對在 Xamarin 中 watchos 3 多快速互動技術

_本文章涵蓋快速互動技術加入 Apple watchOS 3，以及如何在 Xamarin.iOS 中的 Apple Watch 實作它們。_

提供快速的使用者互動所必要建立吸引人的 Apple Watch 應用程式和複雜性。 新增到 watchOS 3，Apple 已新增支援筆勢辨識器，存取數位皇冠和新的使用者通知，並瀏覽技術。 這項目，以及新增 SceneKit 和 SpriteKit，支援可讓開發人員輕鬆地建立豐富、 glanceable 是快速且回應迅速的介面。

## <a name="what-are-quick-interactions"></a>快速互動有哪些？

開發人員用來建立適用於 iOS 或 macOS （使用者花在與應用程式互動的時間量測量分鐘或小時） 的應用程式，Apple Watch 設計成功的應用程式頗具挑戰性且需要不同方法。

在 watchOS，使用者通常想要引發其手腕、 快速的應用程式互動 （通常為簡短幾秒鐘），然後卸除其手腕並繼續無論它是他們所做。

在 Apple Watch 上的一般快速互動的一些範例如下：

- 啟動計時器。
- 正在檢查天氣。
- 關閉 待辦事項清單項目標示。

若要達成這些目標，必須在 Apple Watch 上的應用程式：

- **Glanceable** -也就是說，快速概覽使用者應該能夠取得所需的資訊。 
- **可採取動作**-這表示使用者應該能夠制定快速、 靈活的決策。
- **回應式**-這表示使用者永遠不應該等候接收所需的資訊，或達到想要的動作。

### <a name="quick-interactions-length"></a>快速互動長度

由於 glanceable 的 Apple Watch 應用程式，Apple 建議的互動，快速的理想的長度應該是兩秒或更少。 因為這兩個的第二個限制，而開發人員必須花相當多的時間，同時設計和實作的 Apple Watch 應用程式。 

## <a name="new-watchos-3-features-and-apis"></a>新 watchOS 3 的功能和 Api

Apple 已加入許多新功能和 Api 可協助開發人員加入他們的 Apple Watch 應用程式的快速互動 WatchKit:

- watchOS 3 提供新的類型，這類輸入使用者的存取：
    - 筆勢辨識器
    - 數位皇冠旋轉 
- watchOS 3 提供新方法來顯示和更新的詳細資訊，例如：
    - 增強的資料表巡覽
    - 新的使用者通知架構支援
    - SpriteKit 和 SceneKit 整合

藉由實作這些新功能，開發人員可以確保其 watchOS 3 應用程式是 Glanceable、 易於執行和回應式。

### <a name="gesture-recognizer-support"></a>筆勢辨識器支援

如果開發人員已在 iOS 中實作筆勢辨識器，它們應該很熟悉 watchOS 3 中的筆勢辨識器如何運作。 若要重新整理，筆勢辨識器會剖析成可辨識、 預先定義的筆勢的低層級的觸控事件的物件。

watchOS 3 支援下列四種筆勢辨識器：

- 離散的動作類型：
    - 撥動手勢 (`WKSwipeGestureRecognizer`)。
    - 點選手勢 (`WKTapGestureRecognizer`)。
- 連續的動作類型：
    - 移動瀏覽軌跡 (`WKPanGestureRecognizer`)。
    - 長時間按筆勢 (`WKLongPressGestureRecognizer`)。

若要實作其中一個新的筆勢辨識器，只要將它拖曳拖曳至設計介面，在 iOS 設計工具在 Visual Studio for Mac，並設定其屬性。

在程式碼中，回應的動作來處理使用者所觸發的筆勢辨識器。 同樣地，這是在相同的方式為在 iOS 中已的處理。

#### <a name="discrete-gesture-states"></a>離散的動作狀態

離散的筆勢辨識並狀態，呼叫的動作 (`WKGestureRecognizerState`) 指派為：

[![](quick-interaction-techniques-images/quick01.png "離散的動作狀態")](quick-interaction-techniques-images/quick01.png#lightbox)

開始所有離散筆勢`Possible`狀態，並轉換成`Failed`或`Recognized`狀態。 當使用不連續的筆勢，開發人員通常不會直接處理的狀態。 相反地，它們依賴時只可辨識的筆勢，呼叫的動作。

#### <a name="continuous-gesture-states"></a>持續的筆勢狀態

持續的筆勢是離散的筆勢，其中動作多次呼叫，因為所辨識的筆勢稍有不同：

[![](quick-interaction-techniques-images/quick02.png "持續的筆勢狀態")](quick-interaction-techniques-images/quick02.png#lightbox)

同樣地，持續筆勢一開始為`Possible`透過多個更新的狀態，但其進度。 在此開發人員必須考慮辨識器的狀態，並更新期間的應用程式的 UI`Changed`階段之前的動作是最後`Recognized`或`Canceled`。

#### <a name="gesture-recognizer-usage-tips"></a>筆勢辨識器的使用秘訣

在 watchOS 3 中使用筆勢辨識器時，Apple 建議下列：

- 加入群組項目，而不是個別控制項中的筆勢辨識器。 由於 Apple Watch 具有較小的實體螢幕大小，因此群組項目通常會變大和使用者按下的輕鬆目標。 此外，筆勢辨識器可能會發生衝突與內建的已在原生 UI 控制項的筆勢。
- 在 監看式應用程式的分鏡腳本設定相依性關聯性。
- 某些動作的優先順序高於其他筆勢類型，例如：
    - 捲動
    - 強制觸控
 
### <a name="digital-crown-rotation"></a>數位皇冠旋轉

藉由實作數位皇冠支援 watchOS 3 應用程式中，開發人員可以提供更高的瀏覽速度和精確度的互動使用者。

Apple Watch 應用程式可以使用 watchOS 2，因為`WKInterfacePicker`物件，以藉由提供一份存取數位皇冠`WKPickerItems`和選擇器樣式 （清單、 堆疊或映像順序）。 watchOS 則允許使用者使用數位皇冠，若要從清單中選取項目。

當使用`WKInterfacePicker`，WatchKit 會處理大部分的運作方式：

- 繪製清單和個別的介面項目。
- 正在處理的數位皇冠事件。
- 選取的項目時，請呼叫動作。

新 watchOS 3，以開發人員現在可以直接存取數位皇冠旋轉事件可讓他們建立他們自己回應旋轉值的 UI 項目。

數位皇冠存取是由下列項目提供：

- `WKCrownSequencer` -提供每秒旋轉的存取。
- `WKCrownDelegate` -提供旋轉差異事件的存取權。

#### <a name="rotations-per-second"></a>每秒旋轉

使用物理學基礎動畫時，旋轉每秒由數位皇冠很有用的。 若要存取旋轉秒加入佇列，請使用`CrownSequencer`屬性`WKInterfaceController`監看式延伸模組。 例如: 

```csharp
var rotationsPerSecond = CrownSequencer.RotationsPerSecond;
```

#### <a name="rotational-deltas"></a>旋轉的差異

您可以使用從數位皇冠旋轉的差異計算輪替個數。 使用`CrownDidRotate`覆寫方法`WKCrownDelegate`存取旋轉的差異。 例如: 

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

在此應用程式會維護累計值 (`AccumulatedRotations`) 來判斷旋轉的數目。 一個完整數位皇冠輪替是相等的累積的差異`1.0`一半的循環，且`0.5`。

Apple 已離開其由開發人員決定如何旋轉計數對應到正在更新的 UI 項目上的變更的敏感性。

符號 (`+/-`) 旋轉的差異表示使用者已開啟數位皇冠的方向：

[![](quick-interaction-techniques-images/quick03.png "旋轉的差異的正負號表示使用者已開啟數位皇冠方向")](quick-interaction-techniques-images/quick03.png#lightbox)


如果使用者向上捲動，WatchKit 就會傳回正數差異如果向下捲動，則負差異將會傳回，無論何種方向使用者穿著中的監看式。

#### <a name="digital-crown-focus"></a>數位皇冠焦點

就像任何其他介面項目數位皇冠具有焦點的概念。 這項重點可離開數位皇冠轉移給其他介面項目，根據使用者互動監看式的方式。 

例如，任何下列控制項可能竊取數位皇冠的重點：

- 選擇器
- 滑桿
- 捲動控制器

它是由開發人員判斷其自訂介面項目必須是數位皇冠焦點時。 Apple 建議使用新的筆勢辨識器，以取得自訂的 UI 項目中的焦點。

### <a name="vertical-paging"></a>垂直分頁

使用者瀏覽資料表檢視中的 watchOS 應用程式的標準方式，就是捲動到所需的一份資料，請點選特定的資料列，以顯示詳細的檢視，請點選 [上一頁] 按鈕完成檢視詳細資料並重複此程序的任何其他資訊，y 想要從資料表中：

[![](quick-interaction-techniques-images/quick04.png "資料表與詳細資料檢視之間移動")](quick-interaction-techniques-images/quick04.png#lightbox)

新 watchOS 3，以開發人員可以垂直分頁上啟用其資料表檢視控制項。 啟用此功能，使用者可以捲動以尋找資料表 檢視的資料列，然後點選以檢視其詳細資料為之前的資料列。 不過，它們可以現在向上撥動以選取下一個資料列，資料表中，或向下選取先前的資料列 （或使用數位皇冠），而不需要傳回至資料表檢視第一次：

[![](quick-interaction-techniques-images/quick05.png "資料表與詳細資料檢視之間移動和撥動向上和向下移動其他資料列之間")](quick-interaction-techniques-images/quick05.png#lightbox)

若要啟用此模式中，在 Xcode 中開啟進行編輯的 watchOS 應用程式的分鏡腳本，選取 [資料表] 檢視並檢查**垂直詳細資料分頁**核取方塊：

[![](quick-interaction-techniques-images/quick06.png "核取垂直分頁的詳細資料 核取方塊")](quick-interaction-techniques-images/quick06.png#lightbox)

請確定資料表使用 Segue 顯示詳細的檢視，然後將變更儲存到分鏡腳本並返回 Visual Studio for Mac 中進行同步處理。

開發人員以程式設計的方式都能垂直分頁至特定的資料列，使用下列程式碼，針對資料表檢視：

```csharp
// Segue into Vertical Paging and select the first row
MenuTable.PerformSegue (0);
```

當使用垂直分頁時，開發人員必須留意 WatchKit 會自動處理的控制站的預先載入，而且如此一來，某些控制器生命週期方法可以呼叫可以實際看見 UI 之前。

### <a name="notification-enhancements"></a>通知的增強功能

通知是主要表單的使用者通常發生在 watchOS 的快速互動，以及第一個 Apple Watch 和 watchOS 1 起已正式推出。

典型的通知快速互動如下所示：

1. 收到新的通知時，使用者會感覺 Haptic 通知。
2. 它們會引發其手腕，若要查看通知的簡短查詢的介面。
3. 如果它們仍然保留其引發的手腕，watchOS 便會自動轉換成很長的查詢通知的介面。

有數種方式，使用者可能回應的通知：

- 良好定義，並顯示通知，使用者將會執行任何動作，而且只需關閉通知。
- 他們也可能會啟動 watchOS 應用程式通知的點選。
- 支援自訂動作的通知，使用者可能會選取其中一個自訂動作。 這些可以是：
    - **前景動作**-這些啟動應用程式執行的動作。
    - **背景動作**-一律已路由傳送至 iPhone 的 watchOS 2，但可以路由至在 watchOS 3 watchApp。

WatchOS 3： 的新功能

* 通知會使用類似的 API，跨所有平台 （iOS、 watchOS、 tvOS 和 macOS）。
* 本機通知可以在 Apple Watch 上設定排程。
* 如果它們排定在 Apple Watch 背景通知會傳送至應用程式的延伸模組。

#### <a name="notification-scheduling-and-delivery"></a>通知排程與傳遞

下列情況時，從使用者的 iPhone 通知會轉寄至 Apple Watch:

* IPhone 的畫面就是關閉。
* Apple Watch 在穿戴期間，已解除鎖定。

WatchOS 3，在本機通知可以排程在 Apple Watch 上，才傳送上監看式。 它是開發人員能夠排程對應 iPhone 通知所需的應用程式。

藉由包含相同通知識別碼上的 Apple Watch 和通知的 iPhone 版本，它會防止重複的通知，不會再顯示在 監看式。 Apple Watch 版本通知的優先順序高於的 iPhone 版本。

因為 watchOS 3 會使用相同`UINotification`API 架構，因為 iOS 10，請參閱我們的 iOS 10[使用者通知架構](~/ios/platform/user-notifications/index.md)文件，如需詳細資訊。

### <a name="using-spritekit-and-scenekit"></a>使用 SpriteKit 和 SceneKit

新 watchOS 3，以開發人員現在可以使用 SpritKit 和 SceneKit 物件在其應用程式的使用者介面設計來呈現 2D 和 3D 圖形。

若要支援這項功能已新增兩個新的介面類別：

- `WKInterfaceSKScene` -若為使用 SpriteKit 的 2D 圖形。
- `WKInterfaceSCNScene` -若為使用 SceneKit 的 3D 圖形。

若要使用這些物件，只要將它們拖曳至設計介面內 Xcode 的 Interface Builder 中監看式應用程式的分鏡腳本，並使用**屬性偵測器**針對它們加以設定。

從這裡開始，使用 SpriteKit 或 SceneKit 場景的作用相同內部 iOS 應用程式。 Watch 應用程式將會顯示`WKInterfaceSKScene`藉由呼叫其中一個`Present`方法。 SceneKit，只要設定`Scene`屬性`WKInterfaceSCNScene`物件。

## <a name="actionable-complications"></a>可操作的複雜性

在 watchOS 2，Apple 已引進第 3 個合作對象應用程式的複雜性。 在 watchOS 3，Apple 已擴充的功能，可為開發人員可以納入 WatchKit 複雜功能。 

此外，多個內建在監看式臉部現在可以包含複雜功能和現有的監看式面臨已經支援的複雜功能可以現在包含更多的複雜性。

也新是讓使用者快速撥動左邊或右邊的監看式臉部其 Apple Watch 安裝所有轉換。 在 Apple Watch 的小幫手 iPhone 應用程式中使用新的資源庫，使用者可以新增並自訂新的監看式表面和任何它們可以包含複雜功能。

因為這些新功能，Apple 建議在 Apple Watch 上的每個應用程式也應該包含至少一個複雜的問題，因此，所有原生 Apple Watch 應用程式現在有很複雜的情況。

複雜功能提供給應用程式的下列功能：

- 因為它們永遠存在 watch 錶面上，它們是高度 glanceable。
- WatchOS 會經常更新很複雜的情況。 任何包含使用者的目前顯示的 watch 錶面上的複雜功能的應用程式會更新兩次至少一小時的時間。
- 任何應用程式與使用者的目前顯示的 watch 錶面上的複雜功能會保留在記憶體能夠快速啟動應用程式，並縮短從應用程式的回應速度。
- 複雜功能方便使用者啟動的 watchOS 應用程式中的特定功能。

## <a name="glanceable-notification"></a>Glanceable 通知

Apple Watch 上的通知提供絕佳、 可自訂的方式，快速通知的事件或新的資訊，例如內送訊息或達到健身應用程式的目標使用者。

藉由使用通知，寶貴的資訊可以快速地呈現給使用者。 在許多情況下，設計良好的通知可以移除實際啟動應用程式使用者的必要性。

剛接觸 watchOS 3，所有的通知現在支援：

- SpriteKit
- SceneKit
- 內嵌影片

## <a name="enhanced-ui-with-spritekit-and-scenekit"></a>SceneKit SpriteKit 與增強的 UI

一般而言，開發人員可以將遊戲的 UI 時 SpriteKit 和 SceneKit 所述。 不過，SpriteKit 和 SceneKit 可以是適合用來建立非遊戲包含自訂的版面配置，內容的 Ui 和沒有在 WatchKit 單獨的動畫。

比方說，使用者通知從照片分享應用程式可以使用 SpriteKit 藉由包括張貼的圖片，以及實際的映像和其他增強使用者體驗的自訂的資訊的使用者提供的豐富使用者體驗。

此外，SpriteKit 和 SceneKit 可以混合使用與在應用程式的使用者介面設計的標準 WatchKit UI 項目。

## <a name="simple-navigation"></a>簡單的巡覽

watchOS 3 提供數種方式，開發人員可以簡化中自己的 watchOS 應用程式，例如，新的巡覽[垂直分頁](#vertical-paging)，[支援筆勢辨識器](#gesture-recognizer-support)和[數位皇冠旋轉](#digital-crown-rotation)以上所顯示的功能。

數位皇冠是唯一的 Apple Watch，並可以用於許多不同的方式，來簡化瀏覽。 例如，計時器應用程式可以使用數位皇冠快轉到可用的計時器長度。

自訂軌跡帶來新的且唯一的方式，讓使用者可用來監控應用程式進行互動，也可用來簡化應用程式瀏覽。

Apple 建議尋找方法來結合所有 watchOS 3，以呈現豐富、 簡單又快速使用 watchOS 應用程式介面中加入新的快速互動功能。

## <a name="finishing-the-quick-interaction"></a>完成快速的互動

設計良好的快速互動體驗會授與使用者的信心來卸除其手腕 （和解除與應用程式） 時，會在完成目前的互動。

在此特別會變成問題時，watch 應用程式會執行任何類型的網路連線或其隨附的 iPhone 應用程式與分享資訊。 這可能經常會導致等候指示器交易進行時，也就是不需要快速互動期間。 請使用以下範例：

[![](quick-interaction-techniques-images/quick07.png "Watch 應用程式進行網路連線和其隨附的 iPhone 應用程式與分享資訊的圖表")](quick-interaction-techniques-images/quick07.png#lightbox)

1. 使用者選擇要在監看式購買的項目。
2. 他們點選 [購買] 按鈕。
3. 應用程式會啟動網路交易，並顯示載入指標。
4. 稍後，在交易完成，並應用程式顯示購買包含。
5. 使用者會卸除其手腕和 disengages 與應用程式。

從使用者交易完成之前，請點選 [購買] 按鈕時，他們必須引發其手腕，查看正在載入指標。 若要解決這種情況下，Apple 建議開發人員應該呈現給使用者，而不會顯示載入指標的即時意見反應。 

使用 Apple 的建議的模型時，看看相同快速互動一次：

[![](quick-interaction-techniques-images/quick08.png "Apple 建議的模型圖表")](quick-interaction-techniques-images/quick08.png#lightbox)

1. 使用者選擇要在監看式購買的項目。
2. 他們點選 [購買] 按鈕。
3. 應用程式會啟動網路交易，並顯示訊息，指出購買程序已順利啟動。
4. 使用者會卸除其手腕和 disengages 與應用程式。
5. 當在異動成功完成之後的某個時間時，應用程式就會顯示本機通知，通知使用者已成功購買。

此時，當使用者點選 [購買] 按鈕則會顯示訊息，指出，購買程序已啟動，所以它們可以自信滿滿地卸除其手腕結束目前的快速互動。 稍後，他們會通知的成功或失敗的使用者通知中的交易。 如此一來，使用者只使用應用程式互動的程序的 「 作用中 」 階段。

針對會執行網路功能的應用程式，他們可以使用背景`NSURLSession`來處理與下載工作的網路通訊。 這可讓應用程式被喚醒，在背景處理已下載的資訊。 對於需要背景處理的應用程式，使用背景工作判斷提示來處理必要的處理。

## <a name="quick-interaction-design-tips"></a>快速互動設計秘訣

由於快速互動所需的長度是兩秒或更少，開發人員應著重在從一開始的設計程序的應用程式的互動的設計中。 尋找的方面互動 （使用上述的技術） 可簡化使用 watchOS 3 的新功能，以便快速且回應迅速的應用程式。

Apple 建議下列各項：

- 開始將應用程式最常使用的功能，以專注於快速的互動。
- 使用複雜和使用者通知來呈現通用的功能和函式。
- 建立使用 SceneKit 和 SpriteKit 的豐富、 glanceable 使用者介面。
- 您應該盡可能簡化應用程式內的導覽。
- 永遠不會讓使用者等候，允許它們卸除其手腕並儘速解除與應用程式。

## <a name="summary"></a>總結

這篇文章已涵蓋快速互動技術加入 Apple watchOS 3，以及如何在 Xamarin.iOS 中的 Apple Watch 實作它們。

## <a name="related-links"></a>相關連結

- [watchOS 範例](https://developer.xamarin.com/samples/watchos/all/)
