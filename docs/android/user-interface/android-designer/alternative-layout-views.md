---
title: 替代的版面配置檢視
description: 本主題說明如何使用資源限定詞來建立版面配置的版本。 例如，只有在裝置處於橫向模式時才會使用版面配置的版本，以及僅適用于直向模式的版面配置版本。
ms.prod: xamarin
ms.assetid: 5EBF51FC-9048-F0CF-624A-D8782A91C1FD
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 07/25/2018
ms.openlocfilehash: 017d2d05c04dfaf2378ad1b0129eb4a75be2e777
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73019481"
---
# <a name="alternative-layout-views"></a>替代版面配置視圖

_本主題說明如何使用資源限定詞來進行版本配置。例如，建立僅在裝置處於橫向模式時使用的版面配置版本，以及僅適用于直向模式的版面配置版本。_

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

## <a name="creating-alternative-layouts"></a>建立替代版面配置

當您按一下**替代版面配置視圖**圖示（**裝置**左側）時，會開啟預覽窗格以列出專案中可用的替代配置。 如果沒有替代的版面配置，則會顯示**預設**的 view： 

[![替代版面配置視圖窗格](alternative-layout-views-images/vs/01-alt-layout-view-pane-sml.png "替代版面配置視圖窗格")](alternative-layout-views-images/vs/01-alt-layout-view-pane.png#lightbox)

當您按一下 [**新版本**] 旁的綠色加號時，會開啟 [**建立配置變異**] 對話方塊，讓您可以選取此配置變化的資源限定詞： 

[![建立版面配置變異](alternative-layout-views-images/vs/02-create-layout-variation-sml.png "建立版面配置變異")](alternative-layout-views-images/vs/02-create-layout-variation.png#lightbox)

在下列範例中，**螢幕方向**的資源辨識符號設定為 [**橫向**]，而**螢幕大小**會變更為 [**大**]。 這會建立名為「**大型土地**的新版面配置版本：

[![大型土地變化](alternative-layout-views-images/vs/03-large-land-sml.png "大型土地變化")](alternative-layout-views-images/vs/03-large-land.png#lightbox)

請注意，左側的預覽窗格會顯示資源限定詞選取範圍的效果。 按一下 [**新增**] 會建立替代配置，並將設計工具切換為該配置。 **替代的版面配置視圖**預覽窗格會透過如下列螢幕擷取畫面所示的小型右指標，指出要將哪一個版面配置載入設計工具中： 

[![已載入的版面配置指標](alternative-layout-views-images/vs/04-new-layout-sml.png "已載入的版面配置指標")](alternative-layout-views-images/vs/04-new-layout.png#lightbox)

## <a name="editing-alternative-layouts"></a>編輯替代版面配置

當您建立替代版面配置時，通常會想要進行單一變更，以套用至版面配置的所有分支版本。 例如，您可能會想要將所有版面配置中的按鈕文字變更為黃色。 如果您有大量的版面配置，而且需要將單一變更傳播到所有版本，則維護可能很快就會變得很麻煩，而且容易出錯。

為了簡化多個版面配置版本的維護，設計工具會提供**多重編輯**模式，將您的變更傳播到一個或多個版面配置。 當有一個以上的版面配置時，就會顯示**多重編輯**圖示： 

[![多重編輯圖示](alternative-layout-views-images/vs/05-multi-layout-icon-sml.png "多重編輯圖示")](alternative-layout-views-images/vs/05-multi-layout-icon.png#lightbox)

當您按一下 [**多重編輯**] 圖示時，會出現行，指出配置已連結（如下所示）;也就是說，當您對一個版面配置進行變更時，該變更會傳播至任何連結的配置。 您可以按一下下列螢幕擷取畫面中所示的圓形圖示，取消所有版面配置的連結： 

[![取消連結所有版面配置](alternative-layout-views-images/vs/06-multi-linked-sml.png "取消連結所有版面配置")](alternative-layout-views-images/vs/06-multi-linked.png#lightbox)

如果您有兩個以上的配置，可以選擇性地切換每個版面配置預覽左邊的 [編輯] 按鈕，以判斷要連結在一起的版面配置。 例如，如果您想要進行單一變更，將傳播至三個版面配置的第一個和最後一個，您會先取消中間的配置，如下所示： 

[![取消連結中間版面配置](alternative-layout-views-images/vs/07-unlink-middle-layout-sml.png "取消連結中間版面配置")](alternative-layout-views-images/vs/07-unlink-middle-layout.png#lightbox)

在此範例中，對**預設**或**長**配置所做的變更將會傳播到其他配置，但不會散佈到**大型土地**配置。

### <a name="multi-edit-example"></a>多重編輯範例 

一般而言，當您變更一個版面配置時，相同的變更會傳播到所有其他連結的版面配置。 例如，將新的 `TextView` widget 新增至**預設**版面配置，並將其文字字串變更為 `Portrait`，將會對所有連結的配置進行相同的變更。 以下是它在**預設**版面配置中的外觀： 

[![新增 TextView](alternative-layout-views-images/vs/08-add-textview-sml.png "新增 TextView")](alternative-layout-views-images/vs/08-add-textview.png#lightbox)

`TextView` 也會加入到**大型土地**版面配置視圖中，因為它會連結至**預設**配置： 

[![橫向 TextView](alternative-layout-views-images/vs/09-landscape-textview-sml.png "橫向 TextView")](alternative-layout-views-images/vs/09-landscape-textview.png#lightbox)

但是，如果您想要在只有一個版面配置中進行變更（也就是您不想要將變更傳播到任何其他版面配置）呢？ 若要這樣做，您必須先取消連結您想要變更的版面配置，再進行修改，如下所述。 

### <a name="making-local-changes"></a>進行本機變更 

假設我們想要讓兩個配置都具有加入的 `TextView`，但我們也想要將**大型土地**配置中的文字字串變更為 `Landscape`，而不是 `Portrait`。 如果我們在兩個配置都已連結時，對**大型土地**進行這項變更，則變更會傳播回**預設**配置。 因此，我們必須先取消這兩個配置的連結，然後再進行變更。 當我們將**大型土地**中的文字修改為 `Landscape` 時，設計工具會將這項變更標示為紅色框架，以指出變更在**大型土地**配置的區域，而且*不*會傳播回**預設**配置： 

[![本機變更](alternative-layout-views-images/vs/10-local-change-sml.png "本機變更")](alternative-layout-views-images/vs/10-local-change.png#lightbox)

當您按一下**預設**版面配置來加以觀看時，`TextView` 的文字字串仍然會設定為 `Portrait`。 

## <a name="handling-conflicts"></a>處理衝突 

如果您決定將**預設**版面配置中的文字色彩變更為綠色，則會在連結的版面配置上看到警告圖示。 按一下該版面配置，就會開啟配置來顯示衝突。 造成衝突的 widget 會以紅色框架反白顯示，並顯示下列訊息：*最近的變更在此替代配置中造成衝突*。 

[![衝突的變更](alternative-layout-views-images/vs/11-conflicting-change-sml.png "衝突的變更")](alternative-layout-views-images/vs/11-conflicting-change.png#lightbox)

[*衝突*] 方塊會顯示在小工具的右邊，以說明衝突： 

[![衝突警告](alternative-layout-views-images/xs/11-warning-sml.png)](alternative-layout-views-images/xs/11-warning.png#lightbox)

[衝突] 方塊會顯示已變更的屬性清單，並列出其值。 按一下 [**忽略衝突**] 只會將屬性變更套用至這個 widget。 按一下 [套用]**會將屬性**變更套用至這個小工具，以及連結**預設**版面配置中的對應 widget。 如果套用所有屬性變更，則會自動捨棄衝突。 

### <a name="view-group-conflicts"></a>視圖群組衝突 

屬性變更不是唯一的衝突來源。 插入或移除小工具時，可以偵測到衝突。 例如，當**大型土地**版面配置從**預設**配置取消連結，而且**大型土地**版面配置中的 `TextView` 在 `Button` 上方拖放時，設計工具會標示已移動的 widget 來指出衝突：

[![視圖群組衝突](alternative-layout-views-images/vs/12-view-group-conflict-sml.png "視圖群組衝突")](alternative-layout-views-images/vs/12-view-group-conflict.png#lightbox)

不過，`Button` 上沒有標記。 雖然 `Button` 的位置已變更，但 `Button` 不會顯示**大型土地**設定的特定變更。 

如果將 `CheckBox` 新增至**預設**版面配置，則會產生另一個衝突，並在**大型 land**版面配置上顯示警告圖示： 

[![核取方塊衝突](alternative-layout-views-images/vs/13-checkbox-conflict-sml.png "核取方塊衝突")](alternative-layout-views-images/vs/13-checkbox-conflict.png#lightbox)

按一下**大型 land**版面配置會顯示衝突。 隨即顯示下列訊息： [*最近的變更] 在此替代配置中造成衝突*： 

[![替換版面配置衝突](alternative-layout-views-images/vs/14-alt-layout-conflict-sml.png "替換版面配置衝突")](alternative-layout-views-images/vs/14-alt-layout-conflict.png#lightbox)

此外，[衝突] 方塊會顯示下列訊息：

[![衝突訊息](alternative-layout-views-images/xs/15-conflict-message-sml.png)](alternative-layout-views-images/xs/15-conflict-message.png#lightbox)

新增 `CheckBox` 會造成衝突，因為**大型土地**配置在包含它的 `LinearLayout` 中有變更。 不過，在此情況下，[衝突] 方塊會顯示剛插入至**預設**配置的 widget （`CheckBox`）。

如果您按一下 [**忽略衝突**]，設計工具就會解決衝突，讓 [衝突] 方塊中顯示的 widget 可以拖曳並放入缺少 widget 的配置中（在此案例中為**大型 land**版面配置）： 

[![已解決的群組衝突](alternative-layout-views-images/vs/15-resolved-group-conflict-sml.png "已解決的群組衝突")](alternative-layout-views-images/vs/15-resolved-group-conflict.png#lightbox)

如前一個範例中的 `Button` 所示，`CheckBox` 沒有紅色的變更標記，因為只有 `LinearLayout` 具有在**大型 land**版面配置中套用的變更。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

## <a name="creating-alternative-layouts"></a>建立替代版面配置

當您按一下**替代版面配置視圖**圖示（**裝置**左側）時，會開啟預覽窗格以列出專案中可用的替代配置。 如果沒有替代的版面配置，則會顯示**預設**的 view： 

[![替代的版面配置視圖窗格](alternative-layout-views-images/xs/01-alt-layout-view-pane-sml.png)](alternative-layout-views-images/xs/01-alt-layout-view-pane.png#lightbox)

當您按一下 [**新版本**] 旁的綠色加號時，會開啟 [**建立配置變異**] 對話方塊，讓您可以選取此配置變化的資源限定詞： 

[![建立版面配置變異](alternative-layout-views-images/xs/02-create-layout-variation-sml.png)](alternative-layout-views-images/xs/02-create-layout-variation.png#lightbox)

在下列範例中，**螢幕方向**的資源辨識符號設定為 [**橫向**]，而**螢幕大小**會變更為 [**大**]。 這會建立名為「**大型土地**的新版面配置版本：

[![大型土地變化](alternative-layout-views-images/xs/03-large-land-sml.png)](alternative-layout-views-images/xs/03-large-land.png#lightbox)

請注意，左側的預覽窗格會顯示資源限定詞選取範圍的效果。 按一下 [**新增**] 會建立替代配置，並將設計工具切換為該配置。 **替代的版面配置視圖**預覽窗格會透過如下列螢幕擷取畫面所示的小型右指標，指出要將哪一個版面配置載入設計工具中： 

[![載入的版面配置指標](alternative-layout-views-images/xs/04-new-layout-sml.png)](alternative-layout-views-images/xs/04-new-layout.png#lightbox)

## <a name="editing-alternative-layouts"></a>編輯替代版面配置

當您建立替代版面配置時，通常會想要進行單一變更，以套用至版面配置的所有分支版本。 例如，您可能會想要將所有版面配置中的按鈕文字變更為黃色。 如果您有大量的版面配置，而且需要將單一變更傳播到所有版本，則維護可能很快就會變得很麻煩，而且容易出錯。

為了簡化多個版面配置版本的維護，設計工具會提供**多重編輯**模式，將您的變更傳播到一個或多個版面配置。 當有一個以上的版面配置時，就會顯示**多重編輯**圖示： 

[![多重編輯圖示](alternative-layout-views-images/xs/05-multi-layout-icon-sml.png)](alternative-layout-views-images/xs/05-multi-layout-icon.png#lightbox)

當您按一下 [**多重編輯**] 圖示時，會出現行，指出配置已連結（如下所示）;也就是說，當您對一個版面配置進行變更時，該變更會傳播至任何連結的配置。 您可以按一下下列螢幕擷取畫面中所示的圓形圖示，取消所有版面配置的連結： 

[![取消連結所有版面配置](alternative-layout-views-images/xs/06a-linked-sml.png)](alternative-layout-views-images/xs/06a-linked.png#lightbox)

如果您有兩個以上的配置，可以選擇性地切換每個版面配置預覽左邊的 [編輯] 按鈕，以判斷要連結在一起的版面配置。 例如，如果您想要進行單一變更，將傳播至三個版面配置的第一個和最後一個，您會先取消中間的配置，如下所示： 

[![取消連結中間版面配置](alternative-layout-views-images/xs/06b-multi-linked-sml.png)](alternative-layout-views-images/xs/06b-multi-linked.png#lightbox)

在此範例中，對**預設**或**長**配置所做的變更將會傳播到其他配置，但不會散佈到**大型土地**配置。 

### <a name="multi-edit-example"></a>多重編輯範例 

一般而言，當您變更一個版面配置時，相同的變更會傳播到所有其他連結的版面配置。 例如，將新的 `TextView` widget 新增至**預設**版面配置，並將其文字字串變更為 `Portrait`，將會對所有連結的配置進行相同的變更。 以下是它在**預設**版面配置中的外觀： 

[![新增 TextView](alternative-layout-views-images/xs/07-add-textview-sml.png)](alternative-layout-views-images/xs/07-add-textview.png#lightbox)

`TextView` 也會加入到**大型土地**版面配置視圖中，因為它會連結至**預設**配置： 

[![橫向 TextView](alternative-layout-views-images/xs/08-landscape-textview-sml.png)](alternative-layout-views-images/xs/08-landscape-textview.png#lightbox)

但是，如果您想要在只有一個版面配置中進行變更（也就是您不想要將變更傳播到任何其他版面配置）呢？ 若要這樣做，您必須先取消連結您想要變更的版面配置，再進行修改，如下所述。 

### <a name="making-local-changes"></a>進行本機變更 

假設我們想要讓兩個配置都具有加入的 `TextView`，但我們也想要將**大型土地**配置中的文字字串變更為 `Landscape`，而不是 `Portrait`。 如果我們在兩個配置都已連結時，對**大型土地**進行這項變更，則變更會傳播回**預設**配置。 因此，我們必須先取消這兩個配置的連結，然後再進行變更。 當我們將**大型土地**中的文字修改為 `Landscape` 時，設計工具會將這項變更標示為紅色框架，以指出變更在**大型土地**配置的區域，而且*不*會傳播回**預設**配置： 

[![本機變更](alternative-layout-views-images/xs/09-local-change-sml.png)](alternative-layout-views-images/xs/09-local-change.png#lightbox)

當您按一下**預設**版面配置來加以觀看時，`TextView` 的文字字串仍然會設定為 `Portrait`。 

## <a name="handling-conflicts"></a>處理衝突 

如果您決定將**預設**版面配置中的文字色彩變更為綠色，則會在連結的版面配置上看到警告圖示。 按一下該版面配置，就會開啟配置來顯示衝突。 造成衝突的 widget 會以紅色框架反白顯示，並顯示下列訊息：*最近的變更在此替代配置中造成衝突*。 

[![衝突變更](alternative-layout-views-images/xs/10-conflict-sml.png)](alternative-layout-views-images/xs/10-conflict.png#lightbox)

[*衝突*] 方塊會顯示在小工具的右邊，以說明衝突： 

[![衝突警告](alternative-layout-views-images/xs/11-warning-sml.png)](alternative-layout-views-images/xs/11-warning.png#lightbox)

[衝突] 方塊會顯示已變更的屬性清單，並列出其值。 按一下 [**忽略衝突**] 只會將屬性變更套用至這個 widget。 按一下 [套用]**會將屬性**變更套用至這個小工具，以及連結**預設**版面配置中的對應 widget。 如果套用所有屬性變更，則會自動捨棄衝突。 

### <a name="view-group-conflicts"></a>視圖群組衝突 

屬性變更不是唯一的衝突來源。 插入或移除小工具時，可以偵測到衝突。 例如，當**大型土地**版面配置從**預設**配置取消連結，而且**大型土地**版面配置中的 `TextView` 在 `Button` 上方拖放時，設計工具會標示已移動的 widget 來指出衝突：

[![視圖群組衝突](alternative-layout-views-images/xs/12-view-group-conflict-sml.png)](alternative-layout-views-images/xs/12-view-group-conflict.png#lightbox)

不過，`Button` 上沒有標記。 雖然 `Button` 的位置已變更，但 `Button` 不會顯示**大型土地**設定的特定變更。 

如果將 `CheckBox` 新增至**預設**版面配置，則會產生另一個衝突，並在**大型 land**版面配置上顯示警告圖示： 

[![核取方塊衝突](alternative-layout-views-images/xs/13-checkbox-conflict-sml.png)](alternative-layout-views-images/xs/13-checkbox-conflict.png#lightbox)

按一下**大型 land**版面配置會顯示衝突。 隨即顯示下列訊息： [*最近的變更] 在此替代配置中造成衝突*。 

[![Alt 版面配置衝突](alternative-layout-views-images/xs/14-alt-layout-conflict-sml.png)](alternative-layout-views-images/xs/14-alt-layout-conflict.png#lightbox)

此外，[衝突] 方塊會顯示下列訊息：

[![衝突訊息](alternative-layout-views-images/xs/15-conflict-message-sml.png)](alternative-layout-views-images/xs/15-conflict-message.png#lightbox)

新增 `CheckBox` 會造成衝突，因為**大型土地**配置在包含它的 `LinearLayout` 中有變更。 不過，在此情況下，[衝突] 方塊會顯示剛插入至**預設**配置的 widget （`CheckBox`）。

如果您按一下 [**忽略衝突**]，設計工具就會解決衝突，讓 [衝突] 方塊中顯示的 widget 可以拖曳並放入缺少 widget 的配置中（在此案例中為**大型 land**版面配置）： 

[![已解決的群組衝突](alternative-layout-views-images/xs/16-resolved-group-conflict-sml.png)](alternative-layout-views-images/xs/16-resolved-group-conflict.png#lightbox)

如前一個範例中的 `Button` 所示，`CheckBox` 沒有紅色的變更標記，因為只有 `LinearLayout` 具有在**大型 land**版面配置中套用的變更。

-----

### <a name="conflict-persistence"></a>衝突持續性

衝突會以 XML 批註的形式保存在配置檔案中，如下所示：

```xml
<!-- Widget Inserted Conflict | id:__root__ | @+id/checkBox1 -->
```

因此，關閉並重新開啟專案時，仍會有所有衝突 &ndash; 即使已被忽略。
