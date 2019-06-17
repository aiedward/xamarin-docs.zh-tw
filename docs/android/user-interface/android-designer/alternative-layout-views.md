---
title: 替代的版面配置檢視
description: 本主題說明如何配置可以進行版本設定使用的資源限定詞。 例如，可以有一個配置，以裝置為橫向模式時才會使用的版本和僅適用於直向模式的版面配置版本。
ms.prod: xamarin
ms.assetid: 5EBF51FC-9048-F0CF-624A-D8782A91C1FD
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 07/25/2018
ms.openlocfilehash: 03b80d3fb1ed7c8db108f86b3b3923c20e1d908f
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61089691"
---
# <a name="alternative-layout-views"></a>替代的版面配置檢視

_本主題說明您如何使用的資源限定詞可以版本配置。例如，建立裝置為橫向模式時才會使用配置的版本和僅適用於直向模式的版面配置版本。_

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

## <a name="creating-alternative-layouts"></a>建立替代的版面配置

當您按一下 **替代的版面配置檢視**圖示 (左邊**裝置**)，以列出專案中可用的替代版面配置會開啟 預覽 窗格。 如果沒有替代的版面配置**預設**檢視會顯示： 

[![替代的版面配置 檢視窗格](alternative-layout-views-images/vs/01-alt-layout-view-pane-sml.png "替代的版面配置 檢視窗格")](alternative-layout-views-images/vs/01-alt-layout-view-pane.png#lightbox)

當您按一下綠色的加號，接下來**新版**，則**建立版面配置變異** 對話方塊隨即開啟，讓您可以選取這個版面配置變化的資源限定詞： 

[![建立版面配置變異](alternative-layout-views-images/vs/02-create-layout-variation-sml.png "建立版面配置變異")](alternative-layout-views-images/vs/02-create-layout-variation.png#lightbox)

在下列範例中的資源限定詞**屏幕方向**設為**橫向**，而**螢幕大小**變更為**大型**. 這會建立名為新的版面配置版本**大型 land**:

[![大型 land 變化](alternative-layout-views-images/vs/03-large-land-sml.png "大型 land 變化")](alternative-layout-views-images/vs/03-large-land.png#lightbox)

請注意，在左側的 [預覽] 窗格會顯示資源限定詞選項的效果。 按一下 **新增**建立替代的版面配置，並切換至該版面配置的設計工具。 **替代版面配置檢視**預覽窗格可讓您指出哪個配置載入小型的右側指標透過設計工具，如下列螢幕擷取畫面所示： 

[![載入版面配置指標](alternative-layout-views-images/vs/04-new-layout-sml.png "載入版面配置指標")](alternative-layout-views-images/vs/04-new-layout.png#lightbox)


## <a name="editing-alternative-layouts"></a>編輯替代的版面配置

當您建立替代的版面配置時，它通常會進行單一變更套用至配置的所有分支的版本。 比方說，您可能要為所有版面配置中的黃色改變按鈕文字。 如果您有大量的版面配置，而您需要將傳播至所有版本的單一變更，麻煩又容易發生錯誤，可能很快就會維護。

若要簡化維護工作的多個配置版本，設計工具會提供**多重編輯**會將您的變更傳播跨一或多個配置的模式。 當一個以上的版面配置已存在，**多重編輯**圖示會顯示： 

[![多重編輯圖示](alternative-layout-views-images/vs/05-multi-layout-icon-sml.png "多重編輯圖示")](alternative-layout-views-images/vs/05-multi-layout-icon.png#lightbox)

當您按一下 **多重編輯**圖示，顯示，指出版面配置連結 （如下所示），也就是當您變更一個版面配置時，該變更會傳播到任何連結的版面配置。 您可以按一下下列的螢幕擷取畫面所示的圈選的圖示，以取消連結所有版面配置： 

[![取消連結所有版面配置](alternative-layout-views-images/vs/06-multi-linked-sml.png "取消連結所有版面配置")](alternative-layout-views-images/vs/06-multi-linked.png#lightbox)

如果您有兩個以上的版面配置時，您可以選擇性地切換 [編輯] 按鈕左邊的每個版面配置預覽，以判斷哪一個版面配置連結在一起。 例如，如果您想要讓單一變更傳播到的第一個和最後三個配置中，您會先取消連結中間的版面配置如下所示： 

[![取消連結中間的版面配置](alternative-layout-views-images/vs/07-unlink-middle-layout-sml.png "取消連結中間的版面配置")](alternative-layout-views-images/vs/07-unlink-middle-layout.png#lightbox)

在此範例中，變更為 **預設**或是**長**版面配置會傳播至其他配置，但不是**大型 land**版面配置。

### <a name="multi-edit-example"></a>多重編輯的範例 

一般情況下，如果您變更一個版面配置時，該相同的變更會傳播至所有其他連結版面配置。 比方說，新增`TextView`widget**預設**版面配置，並變更它的文字字串至`Portrait`會導致相同的變更會對所有連結的版面配置。 以下是它的外觀**預設**版面配置： 

[![新增 TextView](alternative-layout-views-images/vs/08-add-textview-sml.png "新增 TextView")](alternative-layout-views-images/vs/08-add-textview.png#lightbox)
 
`TextView`也會加入至**大型 land**版面配置檢視，因為它會連結至**預設**版面配置： 

[![橫向 TextView](alternative-layout-views-images/vs/09-landscape-textview-sml.png "橫向 TextView")](alternative-layout-views-images/vs/09-landscape-textview.png#lightbox)
 
但如果您要進行的變更，只有一個版面配置的本機 （亦即，您不想變更傳播到任何其他配置）？ 若要這樣做，您必須取消連結您想要變更再進行修改，如下所述的配置。 

### <a name="making-local-changes"></a>在本機的變更 

假設我們想讓已加入兩種版面配置`TextView`，但我們也想要變更中的文字字串**大型 land**版面配置`Landscape`而非`Portrait`。 如果我們做了這項變更**大型 land**同時連結兩種版面配置，變更會傳播回**預設**版面配置。 因此，我們必須先取消連結兩個配置之前我們進行變更。 當我們修改中的文字**大型 land**要`Landscape`，設計工具中標示這項變更以紅色的框架，以指出變更為本機**大型 land**版面配置，而且*不*傳播回到**預設**版面配置： 

[![本機變更](alternative-layout-views-images/vs/10-local-change-sml.png "本機變更")](alternative-layout-views-images/vs/10-local-change.png#lightbox)
 
當您按一下 [**預設**版面配置] 檢視，以`TextView`文字字串仍然設定為`Portrait`。 

## <a name="handling-conflicts"></a>處理衝突 

如果您決定要變更的文字色彩**預設**綠色的版面配置，您會看到警告圖示會出現在連結的版面配置。 按一下該版面配置，便會開啟版面配置，以顯示衝突。 造成衝突的小工具會反白顯示以紅色的框架，並會顯示下列訊息：*在此替代版面配置中最近的變更導致衝突*。 

[![衝突變更](alternative-layout-views-images/vs/11-conflicting-change-sml.png "衝突變更")](alternative-layout-views-images/vs/11-conflicting-change.png#lightbox)
 
A*衝突方塊*會顯示在右邊的小工具來說明衝突： 

[![衝突的警告](alternative-layout-views-images/xs/11-warning-sml.png)](alternative-layout-views-images/xs/11-warning.png#lightbox)

衝突 方塊會顯示已變更的屬性清單，並列出其值。 按一下 **忽略衝突**屬性變更僅適用於此小工具。 按一下 **套用**屬性變更套用至這個小工具以及對於對應中小工具的連結**預設**版面配置。 如果套用所有的屬性變更，會自動捨棄衝突。 

### <a name="view-group-conflicts"></a>檢視群組的衝突 

屬性變更不會衝突的唯一來源。 插入或移除小工具時，可以偵測到衝突。 比方說，當**大型 land**版面配置已從取消的連結**預設**版面配置，而`TextView`中**大型 land**配置拖放上方`Button`，設計工具會將標示要指出衝突的移動小工具：

[![檢視群組衝突](alternative-layout-views-images/vs/12-view-group-conflict-sml.png "檢視群組衝突")](alternative-layout-views-images/vs/12-view-group-conflict.png#lightbox)
 
不過，沒有任何標記上`Button`。 雖然的位置`Button`已變更，`Button`會顯示未套用的變更所特有**大型 land**組態。 

如果`CheckBox`新增至**預設**版面配置，會產生另一個衝突，並透過顯示警告圖示**大型 land**版面配置： 

[![核取方塊衝突](alternative-layout-views-images/vs/13-checkbox-conflict-sml.png "核取方塊衝突")](alternative-layout-views-images/vs/13-checkbox-conflict.png#lightbox)
 
按一下 **大型 land**版面配置會顯示衝突。 畫面顯示下列訊息：*在此替代版面配置中最近的變更導致衝突*: 

[![替代的版面配置衝突](alternative-layout-views-images/vs/14-alt-layout-conflict-sml.png "Alt 配置衝突")](alternative-layout-views-images/vs/14-alt-layout-conflict.png#lightbox)

此外，[衝突] 方塊會顯示下列訊息：

[![衝突訊息](alternative-layout-views-images/xs/15-conflict-message-sml.png)](alternative-layout-views-images/xs/15-conflict-message.png#lightbox)

新增`CheckBox`導致衝突，因為**大型 land**版面配置有變更`LinearLayout`包含它。 不過，在此情況下衝突方塊會顯示剛插入的 widget**預設**版面配置 ( `CheckBox`)。

如果您按一下**忽略衝突**，設計工具可以解決衝突，允許在衝突中拖放到配置小工具缺少其中顯示的小工具 (在此情況下，**大型陸地**版面配置): 

[![解決群組衝突](alternative-layout-views-images/vs/15-resolved-group-conflict-sml.png "解析群組衝突")](alternative-layout-views-images/vs/15-resolved-group-conflict.png#lightbox)

與上述範例所示`Button`，則`CheckBox`不具有紅色變更標記，因為只有`LinearLayout`已套用的變更**大型 land**版面配置。



# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

## <a name="creating-alternative-layouts"></a>建立替代的版面配置

當您按一下 **替代的版面配置檢視**圖示 (左邊**裝置**)，以列出專案中可用的替代版面配置會開啟 預覽 窗格。 如果沒有替代的版面配置**預設**檢視會顯示： 

[![替代的版面配置 檢視窗格](alternative-layout-views-images/xs/01-alt-layout-view-pane-sml.png)](alternative-layout-views-images/xs/01-alt-layout-view-pane.png#lightbox)

當您按一下綠色的加號，接下來**新版**，則**建立版面配置變異** 對話方塊隨即開啟，讓您可以選取這個版面配置變化的資源限定詞： 

[![建立版面配置變異](alternative-layout-views-images/xs/02-create-layout-variation-sml.png)](alternative-layout-views-images/xs/02-create-layout-variation.png#lightbox)

在下列範例中的資源限定詞**屏幕方向**設為**橫向**，而**螢幕大小**變更為**大型**. 這會建立名為新的版面配置版本**大型 land**:

[![大型 land 變化](alternative-layout-views-images/xs/03-large-land-sml.png)](alternative-layout-views-images/xs/03-large-land.png#lightbox)

請注意，在左側的 [預覽] 窗格會顯示資源限定詞選項的效果。 按一下 **新增**建立替代的版面配置，並切換至該版面配置的設計工具。 **替代版面配置檢視**預覽窗格可讓您指出哪個配置載入小型的右側指標透過設計工具，如下列螢幕擷取畫面所示： 

[![載入版面配置指標](alternative-layout-views-images/xs/04-new-layout-sml.png)](alternative-layout-views-images/xs/04-new-layout.png#lightbox)

## <a name="editing-alternative-layouts"></a>編輯替代的版面配置

當您建立替代的版面配置時，它通常會進行單一變更套用至配置的所有分支的版本。 比方說，您可能要為所有版面配置中的黃色改變按鈕文字。 如果您有大量的版面配置，而您需要將傳播至所有版本的單一變更，麻煩又容易發生錯誤，可能很快就會維護。

若要簡化維護工作的多個配置版本，設計工具會提供**多重編輯**會將您的變更傳播跨一或多個配置的模式。 當一個以上的版面配置已存在，**多重編輯**圖示會顯示： 

[![多重編輯圖示](alternative-layout-views-images/xs/05-multi-layout-icon-sml.png)](alternative-layout-views-images/xs/05-multi-layout-icon.png#lightbox)

當您按一下 **多重編輯**圖示，顯示，指出版面配置連結 （如下所示），也就是當您變更一個版面配置時，該變更會傳播到任何連結的版面配置。 您可以按一下下列的螢幕擷取畫面所示的圈選的圖示，以取消連結所有版面配置： 

[![取消連結所有版面配置](alternative-layout-views-images/xs/06a-linked-sml.png)](alternative-layout-views-images/xs/06a-linked.png#lightbox)

如果您有兩個以上的版面配置時，您可以選擇性地切換 [編輯] 按鈕左邊的每個版面配置預覽，以判斷哪一個版面配置連結在一起。 例如，如果您想要讓單一變更傳播到的第一個和最後三個配置中，您會先取消連結中間的版面配置如下所示： 

[![取消連結中間的版面配置](alternative-layout-views-images/xs/06b-multi-linked-sml.png)](alternative-layout-views-images/xs/06b-multi-linked.png#lightbox)

在此範例中，變更為 **預設**或是**長**版面配置會傳播至其他配置，但不是**大型 land**版面配置。 

### <a name="multi-edit-example"></a>多重編輯的範例 

一般情況下，如果您變更一個版面配置時，該相同的變更會傳播至所有其他連結版面配置。 比方說，新增`TextView`widget**預設**版面配置，並變更它的文字字串至`Portrait`會導致相同的變更會對所有連結的版面配置。 以下是它的外觀**預設**版面配置： 

[![新增 TextView](alternative-layout-views-images/xs/07-add-textview-sml.png)](alternative-layout-views-images/xs/07-add-textview.png#lightbox)

`TextView`也會加入至**大型 land**版面配置檢視，因為它會連結至**預設**版面配置： 

[![橫向 TextView](alternative-layout-views-images/xs/08-landscape-textview-sml.png)](alternative-layout-views-images/xs/08-landscape-textview.png#lightbox)
 
但如果您要進行的變更，只有一個版面配置的本機 （亦即，您不想變更傳播到任何其他配置）？ 若要這樣做，您必須取消連結您想要變更再進行修改，如下所述的配置。 

### <a name="making-local-changes"></a>在本機的變更 

假設我們想讓已加入兩種版面配置`TextView`，但我們也想要變更中的文字字串**大型 land**版面配置`Landscape`而非`Portrait`。 如果我們做了這項變更**大型 land**同時連結兩種版面配置，變更會傳播回**預設**版面配置。 因此，我們必須先取消連結兩個配置之前我們進行變更。 當我們修改中的文字**大型 land**要`Landscape`，設計工具中標示這項變更以紅色的框架，以指出變更為本機**大型 land**版面配置，而且*不*傳播回到**預設**版面配置： 

[![本機變更](alternative-layout-views-images/xs/09-local-change-sml.png)](alternative-layout-views-images/xs/09-local-change.png#lightbox)

當您按一下 [**預設**版面配置] 檢視，以`TextView`文字字串仍然設定為`Portrait`。 

## <a name="handling-conflicts"></a>處理衝突 

如果您決定要變更的文字色彩**預設**綠色的版面配置，您會看到警告圖示會出現在連結的版面配置。 按一下該版面配置，便會開啟版面配置，以顯示衝突。 造成衝突的小工具會反白顯示以紅色的框架，並會顯示下列訊息：*在此替代版面配置中最近的變更導致衝突*。 

[![衝突的變更](alternative-layout-views-images/xs/10-conflict-sml.png)](alternative-layout-views-images/xs/10-conflict.png#lightbox)

A*衝突方塊*會顯示在右邊的小工具來說明衝突： 

[![衝突的警告](alternative-layout-views-images/xs/11-warning-sml.png)](alternative-layout-views-images/xs/11-warning.png#lightbox)

衝突 方塊會顯示已變更的屬性清單，並列出其值。 按一下 **忽略衝突**屬性變更僅適用於此小工具。 按一下 **套用**屬性變更套用至這個小工具以及對於對應中小工具的連結**預設**版面配置。 如果套用所有的屬性變更，會自動捨棄衝突。 

### <a name="view-group-conflicts"></a>檢視群組的衝突 

屬性變更不會衝突的唯一來源。 插入或移除小工具時，可以偵測到衝突。 比方說，當**大型 land**版面配置已從取消的連結**預設**版面配置，而`TextView`中**大型 land**配置拖放上方`Button`，設計工具會將標示要指出衝突的移動小工具：

[![檢視群組的衝突](alternative-layout-views-images/xs/12-view-group-conflict-sml.png)](alternative-layout-views-images/xs/12-view-group-conflict.png#lightbox)
 
不過，沒有任何標記上`Button`。 雖然的位置`Button`已變更，`Button`會顯示未套用的變更所特有**大型 land**組態。 

如果`CheckBox`新增至**預設**版面配置，會產生另一個衝突，並透過顯示警告圖示**大型 land**版面配置： 

[![核取方塊衝突](alternative-layout-views-images/xs/13-checkbox-conflict-sml.png)](alternative-layout-views-images/xs/13-checkbox-conflict.png#lightbox)
 
按一下 **大型 land**版面配置會顯示衝突。 畫面顯示下列訊息：*在此替代版面配置中最近的變更導致衝突*。 

[![替代的版面配置衝突](alternative-layout-views-images/xs/14-alt-layout-conflict-sml.png)](alternative-layout-views-images/xs/14-alt-layout-conflict.png#lightbox)
 
此外，[衝突] 方塊會顯示下列訊息：

[![衝突訊息](alternative-layout-views-images/xs/15-conflict-message-sml.png)](alternative-layout-views-images/xs/15-conflict-message.png#lightbox)

新增`CheckBox`導致衝突，因為**大型 land**版面配置有變更`LinearLayout`包含它。 不過，在此情況下衝突方塊會顯示剛插入的 widget**預設**版面配置 ( `CheckBox`)。

如果您按一下**忽略衝突**，設計工具可以解決衝突，允許在衝突中拖放到配置小工具缺少其中顯示的小工具 (在此情況下，**大型陸地**版面配置): 

[![解決群組的衝突](alternative-layout-views-images/xs/16-resolved-group-conflict-sml.png)](alternative-layout-views-images/xs/16-resolved-group-conflict.png#lightbox)
 
與上述範例所示`Button`，則`CheckBox`不具有紅色變更標記，因為只有`LinearLayout`已套用的變更**大型 land**版面配置。


-----


### <a name="conflict-persistence"></a>衝突的持續性

衝突會保存在版面配置檔做為 XML 註解，如下所示：

```xml
<!-- Widget Inserted Conflict | id:__root__ | @+id/checkBox1 -->
```

因此，當專案已關閉，並重新開啟，所有衝突仍是有&ndash;甚至已略過的項目。



