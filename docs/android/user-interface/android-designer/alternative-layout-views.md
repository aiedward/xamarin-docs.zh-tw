---
title: 替代的版面配置檢視
description: 本主題說明如何配置可以進行版本設定所使用的資源限定詞。 例如，可以有的版面配置，裝置會以橫向模式時才會使用某個版本，僅適用於縱向模式的版面配置版本。
ms.prod: xamarin
ms.assetid: 5EBF51FC-9048-F0CF-624A-D8782A91C1FD
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 08/21/2017
ms.openlocfilehash: d2228169ed5d8575c9e332c85d963fca0400dea8
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="alternative-layout-views"></a>替代的版面配置檢視

_本主題說明如何配置可以進行版本設定所使用的資源限定詞。例如，可以有的版面配置，裝置會以橫向模式時才會使用某個版本，僅適用於縱向模式的版面配置版本。_


## <a name="creating-alternative-layouts"></a>建立替代的配置

當您按一下**替代的版面配置檢視**圖示 (左邊**裝置**)，列出專案中可用的替代配置的預覽窗格隨即開啟。 如果沒有替代的配置，**預設**檢視會顯示： 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![替代的版面配置檢視 窗格](alternative-layout-views-images/vs/01-alt-layout-view-pane-sml.png "替代配置檢視 窗格")](alternative-layout-views-images/vs/01-alt-layout-view-pane.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![替代的版面配置檢視 窗格](alternative-layout-views-images/xs/01-alt-layout-view-pane-sml.png)](alternative-layout-views-images/xs/01-alt-layout-view-pane.png#lightbox)

-----

當您按一下綠色的加號，**新版**、**建立配置變化** 對話方塊隨即開啟，因此您可以選取的資源限定詞，此配置變化： 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![建立版面配置變化](alternative-layout-views-images/vs/02-create-layout-variation-sml.png "建立版面配置的變化")](alternative-layout-views-images/vs/02-create-layout-variation.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![建立版面配置的變化](alternative-layout-views-images/xs/02-create-layout-variation-sml.png)](alternative-layout-views-images/xs/02-create-layout-variation.png#lightbox)

-----


在下列範例中的資源限定詞**螢幕**設**橫向**，而**螢幕大小**變更為**大**. 這會建立名為的新配置版本**大型土地**:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![大型土地變化](alternative-layout-views-images/vs/03-large-land-sml.png "大土地變化")](alternative-layout-views-images/vs/03-large-land.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![大型土地變化](alternative-layout-views-images/xs/03-large-land-sml.png)](alternative-layout-views-images/xs/03-large-land.png#lightbox)

-----


請注意，在左側的 [預覽] 窗格會顯示的資源限定詞的選取項目影響。 按一下**新增**建立替代的版面配置，並切換至該版面配置的設計工具。 **替代版面配置檢視**預覽窗格中會指出哪個配置載入小右邊的指標透過設計工具，如下列螢幕擷取畫面所示： 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![載入的配置指標](alternative-layout-views-images/vs/04-new-layout-sml.png "載入的配置指標")](alternative-layout-views-images/vs/04-new-layout.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![載入的配置指標](alternative-layout-views-images/xs/04-new-layout-sml.png)](alternative-layout-views-images/xs/04-new-layout.png#lightbox)

-----



## <a name="editing-alternative-layouts"></a>編輯替代的配置

當您建立替代的配置時，通常會讓適用於所有版本分岔的版面配置的單一變更。 例如，您可以變更為黃色，所有的版面配置中的按鈕文字。 如果您有大量的配置，維護很快就麻煩又容易出錯如果您需要將傳播至所有版本的單一變更。 

為了簡化維護工作的多個配置版本，設計工具提供**多重編輯**跨一個或多個配置傳播變更的模式。 當一個以上的版面配置已存在，**多重編輯**圖示會顯示： 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![多重編輯圖示](alternative-layout-views-images/vs/05-multi-layout-icon-sml.png "多重編輯圖示")](alternative-layout-views-images/vs/05-multi-layout-icon.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![多重編輯圖示](alternative-layout-views-images/xs/05-multi-layout-icon-sml.png)](alternative-layout-views-images/xs/05-multi-layout-icon.png#lightbox)

-----


當您按一下**多重編輯**圖示，顯示，以指出其版面配置，會連結 （如下所示），也就是當您變更某個配置時，該變更會傳播至任何連結的版面配置。 您可以按一下圓形的圖示，下列螢幕擷取畫面所示，以取消連結所有配置： 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![取消連結所有配置](alternative-layout-views-images/vs/06-multi-linked-sml.png "取消都連結所有版面配置")](alternative-layout-views-images/vs/06-multi-linked.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![取消連結所有版面配置](alternative-layout-views-images/xs/06a-linked-sml.png)](alternative-layout-views-images/xs/06a-linked.png#lightbox)

-----


如果您有兩個以上的配置，您可以選擇性地切換每個配置預覽 以判斷哪一個配置會連結在一起的左邊的 編輯 按鈕。 例如，如果您想要進行單一變更傳播到的第一個和最後一個的三種配置，您會先取消連結中間配置如下所示： 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![取消連結中間配置](alternative-layout-views-images/vs/07-unlink-middle-layout-sml.png "取消中間版面配置")](alternative-layout-views-images/vs/07-unlink-middle-layout.png#lightbox)
 
# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![取消連結中間的版面配置](alternative-layout-views-images/xs/06b-multi-linked-sml.png)](alternative-layout-views-images/xs/06b-multi-linked.png#lightbox)
 
-----
 

在此範例中，變更為 **預設**或**長**配置會傳播至其他配置，而無法供**大型土地**版面配置。 



### <a name="multi-edit-example"></a>多重編輯的範例 

一般情況下，當您變更某個配置時，該相同的變更會傳播至所有其他連結的配置。 比方說，加入新`TextView`要 widget**預設**版面配置，並變更它的文字字串至`Portrait`會導致相同的變更，必須對所有連結的配置。 以下是它的外觀**預設**版面配置： 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![新增 TextView](alternative-layout-views-images/vs/08-add-textview-sml.png "新增 TextView")](alternative-layout-views-images/vs/08-add-textview.png#lightbox)
 
# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![新增 TextView](alternative-layout-views-images/xs/07-add-textview-sml.png)](alternative-layout-views-images/xs/07-add-textview.png#lightbox)
 
-----
 

`TextView`也會加入至**大型土地**版面配置檢視，因為它會連結至**預設**版面配置： 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![橫向 TextView](alternative-layout-views-images/vs/09-landscape-textview-sml.png "橫向 TextView")](alternative-layout-views-images/vs/09-landscape-textview.png#lightbox)
 
# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![橫向 TextView](alternative-layout-views-images/xs/08-landscape-textview-sml.png)](alternative-layout-views-images/xs/08-landscape-textview.png#lightbox)
 
-----
 

但要是您希望進行的變更，只有一個版面配置的本機 （亦即，您不想變更傳播至任何其他配置）？ 若要這樣做，您必須取消連結您想要變更再進行修改，接下來所述的配置。 



### <a name="making-local-changes"></a>在本機變更 

假設我們想要這兩種版面配置，以具有額外`TextView`，我們也想要變更中的文字字串，但**大型土地**配置`Landscape`而不是`Portrait`。 如果我們進行這項變更**大型土地**時連結兩種版面配置，變更會傳播回到**預設**版面配置。 因此，我們必須先取消連結兩個配置之前我們進行變更。 當我們修改中的文字**大型土地**至`Landscape`，設計工具會將標示紅色的框架，以指出變更為本機的這項變更**大型土地**版面配置，且*不*傳播回**預設**版面配置： 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![本機變更](alternative-layout-views-images/vs/10-local-change-sml.png "本機變更")](alternative-layout-views-images/vs/10-local-change.png#lightbox)
 
# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![本機變更](alternative-layout-views-images/xs/09-local-change-sml.png)](alternative-layout-views-images/xs/09-local-change.png#lightbox)
 
-----
 

當您按一下**預設**版面配置，來檢視它，`TextView`文字字串仍會設為`Portrait`。 



## <a name="handling-conflicts"></a>處理衝突 

如果您決定要變更文字的色彩**預設**綠色的版面配置，您會看到警告圖示會出現在連結的版面配置上。 按一下該配置會開啟以顯示衝突版面配置。 造成衝突的 widget 會反白顯示紅色的框架，而且會顯示下列訊息：*此種版面配置中最近的變更造成衝突*。 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![衝突變更](alternative-layout-views-images/vs/11-conflicting-change-sml.png "衝突變更")](alternative-layout-views-images/vs/11-conflicting-change.png#lightbox)
 
# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![衝突的變更](alternative-layout-views-images/xs/10-conflict-sml.png)](alternative-layout-views-images/xs/10-conflict.png#lightbox)
 
-----
 

A*衝突方塊*會顯示在右邊的小工具來說明衝突： 

[![衝突的警告](alternative-layout-views-images/xs/11-warning-sml.png)](alternative-layout-views-images/xs/11-warning.png#lightbox)

衝突方塊會顯示已變更的屬性清單，並列出其值。 按一下**忽略衝突**屬性變更僅適用於此 widget。 按一下**套用**屬性變更套用至這個小工具以及以在連結的對應項目 widget**預設**版面配置。 如果所有屬性變更會都套用，衝突會自動被捨棄。 


### <a name="view-group-conflicts"></a>檢視群組的衝突 

屬性變更不衝突的唯一來源。 插入或移除小工具時，可以偵測到衝突。 例如，當**大型土地**版面配置連結從**預設**版面配置，而`TextView`中**大型土地**版面配置是拖曳和卸除上方`Button`，設計工具會標示 [移動] widget 中，表示衝突：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![檢視群組衝突](alternative-layout-views-images/vs/12-view-group-conflict-sml.png "檢視群組的衝突")](alternative-layout-views-images/vs/12-view-group-conflict.png#lightbox)
 
# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![檢視群組的衝突](alternative-layout-views-images/xs/12-view-group-conflict-sml.png)](alternative-layout-views-images/xs/12-view-group-conflict.png#lightbox)
 
-----
 

不過，沒有任何標記上`Button`。 雖然位置`Button`已變更，`Button`顯示專屬於任何套用的變更**大型土地**組態。 

如果`CheckBox`加入至**預設**版面配置，會產生另一個衝突，並透過顯示警告圖示**大型土地**版面配置： 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![核取方塊衝突](alternative-layout-views-images/vs/13-checkbox-conflict-sml.png "核取方塊衝突")](alternative-layout-views-images/vs/13-checkbox-conflict.png#lightbox)
 
# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![核取方塊衝突](alternative-layout-views-images/xs/13-checkbox-conflict-sml.png)](alternative-layout-views-images/xs/13-checkbox-conflict.png#lightbox)
 
-----
 

按一下**大型土地**配置會顯示衝突。 會顯示下列訊息：*此種版面配置中最近的變更造成衝突*。 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Alt 配置衝突](alternative-layout-views-images/vs/14-alt-layout-conflict-sml.png "Alt 配置衝突")](alternative-layout-views-images/vs/14-alt-layout-conflict.png#lightbox)
 
# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![Alt 配置衝突](alternative-layout-views-images/xs/14-alt-layout-conflict-sml.png)](alternative-layout-views-images/xs/14-alt-layout-conflict.png#lightbox)
 
-----
 

此外，衝突方塊會顯示下列訊息：

[![衝突訊息](alternative-layout-views-images/xs/15-conflict-message-sml.png)](alternative-layout-views-images/xs/15-conflict-message.png#lightbox)

加入`CheckBox`導致衝突，因為**大型土地**版面配置中有變更`LinearLayout`包含它。 不過，在此情況下衝突方塊會顯示只插入 widget**預設**配置 ( `CheckBox`)。

如果您按一下**忽略衝突**，設計工具會解決衝突，允許遺漏 widget 所在拖放到版面配置衝突方塊中所顯示的小工具 (在此情況下，**大型陸地**配置): 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![解析群組衝突](alternative-layout-views-images/vs/15-resolved-group-conflict-sml.png "解析群組衝突")](alternative-layout-views-images/vs/15-resolved-group-conflict.png#lightbox)
 
# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![解析群組衝突](alternative-layout-views-images/xs/16-resolved-group-conflict-sml.png)](alternative-layout-views-images/xs/16-resolved-group-conflict.png#lightbox)
 
-----
 

與上述範例中所見`Button`、`CheckBox`不具有紅色變更標記，因為只有`LinearLayout`有變更套用**大型土地**版面配置。



### <a name="conflict-persistence"></a>衝突的持續性

衝突會保存在配置檔為 XML 註解，如下所示：

```xml
<!-- Widget Inserted Conflict | id:__root__ | @+id/checkBox1 -->
```

因此，專案關閉並重新開啟，所有衝突仍會有&ndash;即使已略過的項目。

