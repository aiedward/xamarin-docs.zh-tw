---
title: Xamarin 中的 watchOS 複雜性
description: 本檔說明如何在 Xamarin 中使用 watchOS 複雜程度。 它討論如何新增複雜的、撰寫複雜的範本，並提供範例程式碼。
ms.prod: xamarin
ms.assetid: 7ACD9A2B-CF69-46EA-B0C8-10E7D81216E8
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 07/03/2017
ms.openlocfilehash: 5aea7ae094e0b79831a5fb84397108ca09e18360
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73028309"
---
# <a name="watchos-complications-in-xamarin"></a>Xamarin 中的 watchOS 複雜性

_watchOS 可讓開發人員針對監看臉部撰寫自訂的複雜功能_

此頁面說明各種可用的複雜類型，以及如何將複雜的新增至您的 watchOS 3 應用程式。

請注意，每個 watchOS 應用程式只能有一個複雜的。

一開始請先閱讀[Apple 的](https://developer.apple.com/library/watchos/documentation/General/Conceptual/WatchKitProgrammingGuide/ManagingComplications.html)檔，以判斷您的應用程式是否適用于複雜的情況。 有5種 `CLKComplicationFamily` 類型的顯示可供選擇：

[![](complications-images/all-complications-sml.png "The 5 CLKComplicationFamily types available: Circular Small, Modular Small, Modular Large, Utilitarian Small, Utilitarian Large")](complications-images/all-complications.png#lightbox)

應用程式只能執行一種樣式，或全部都是五，視所顯示的資料而定。
您也可以支援時間旅遊，在使用者輪流 Digital Crown 時，提供過去和/或未來時間的值。

<a name="adding" />

## <a name="adding-a-complication"></a>加入複雜的

### <a name="configuration"></a>Configuration

複雜性可以在建立期間新增至監看式應用程式，或手動新增至現有的解決方案。

### <a name="add-new-project"></a>加入新專案 。

[**新增專案 ...** ] wizard 包含一個核取方塊，會自動建立複雜的控制器類別，並設定**plist**檔案：

![](complications-images/file-new-project-sml.png "The Include Complication checkbox")

### <a name="existing-projects"></a>現有的專案

若要將複雜的加入至現有的專案：

1. 建立新的**ComplicationController.cs**類別檔案，並執行 `CLKComplicationDataSource`。
2. 設定應用程式的**Info. plist**以公開複雜的，並識別支援的複雜系列。

下面將更詳細說明這些步驟。

<a name="clkcomplicationcontroller" />

### <a name="clkcomplicationdatasource-class"></a>CLKComplicationDataSource 類別

下列C#範本包含用來執行`CLKComplicationDataSource`的最小必要方法。

```csharp
[Register ("ComplicationController")]
public class ComplicationController : CLKComplicationDataSource
{
  public ComplicationController ()
  {
  }
  public override void GetPlaceholderTemplate (CLKComplication complication, Action<CLKComplicationTemplate> handler)
  {
  }
  public override void GetCurrentTimelineEntry (CLKComplication complication, Action<CLKComplicationTimelineEntry> handler)
  {
  }
  public override void GetSupportedTimeTravelDirections (CLKComplication complication, Action<CLKComplicationTimeTravelDirections> handler)
  {
  }
}
```

請遵循[撰寫複雜](#writing)的指示，將程式碼新增至這個類別。

### <a name="infoplist"></a>Info.plist

Watch 延伸模組的**plist**檔案應該指定 `CLKComplicationDataSource` 的名稱，以及您想要支援的複雜系列：

[![](complications-images/complications-config-sml.png "The complication family types")](complications-images/complications-config.png#lightbox)

[**資料來源類別**] 專案清單會顯示類別名稱，其中會將包含您複雜邏輯 `CLKComplicationDataSource` 子類別化。

## <a name="clkcomplicationdatasource"></a>CLKComplicationDataSource

所有複雜的功能都是在單一類別中執行，覆寫來自 `CLKComplicationDataSource` 抽象類別的方法（這會實作為 `ICLKComplicationDataSource` 介面）。

### <a name="required-methods"></a>必要方法

您必須執行下列方法，才能執行複雜的：

- `GetPlaceholderTemplate`-傳回設定期間或當應用程式無法提供值時所使用的靜態顯示。
- `GetCurrentTimelineEntry`-當複雜的執行時，計算正確的顯示。
- `GetSupportedTimeTravelDirections`-從 `CLKComplicationTimeTravelDirections` （例如 `None`、`Forward`、`Backward`或 `Forward | Backward`）傳回選項。

### <a name="privacy"></a>隱私權

顯示個人資料的複雜性

- `GetPrivacyBehavior` - `CLKComplicationPrivacyBehavior.ShowOnLockScreen` 或 `HideOnLockScreen`

如果這個方法傳回 `HideOnLockScreen` 則當監看式鎖定時，複雜的會顯示圖示或應用程式名稱（而不是任何資料）。

### <a name="updates"></a>更新

- `GetNextRequestedUpdateDate`-傳回作業系統下次應查詢應用程式是否有更新的複雜顯示資料的時間。

您也可以從 iOS 應用程式強制更新。

### <a name="supporting-time-travel"></a>支援時間移動

時間移動支援是選擇性的，而且是由 `GetSupportedTimeTravelDirections` 方法所控制。 如果它傳回 `Forward`、`Backward`或 `Forward | Backward`，則您必須執行下列方法

- `GetTimelineStartDate`
- `GetTimelineEndDate`
- `GetTimelineEntriesBeforeDate`
- `GetTimelineEntriesAfterDate`

<a name="writing" />

## <a name="writing-a-complication"></a>撰寫複雜的

從簡單的資料顯示到複雜的影像，以及具有時間旅遊支援的資料轉譯，都能提供複雜性的範圍。 下列程式碼顯示如何建立簡單、單一範本的複雜情況。

<!--
The [sample]() for this article supports more template styles.
-->

## <a name="sample-code"></a>程式碼範例

這個範例僅支援 `UtilitarianLarge` 範本，因此只能在支援該類型複雜的特定監看式臉部上選取。 *選取*監看式的複雜畫面時，它會顯示**我的複雜**內容，而*執行時，* 它會顯示**分鐘_小時_** （含時間部分）。

```csharp
[Register ("ComplicationController")]
public class ComplicationController : CLKComplicationDataSource
{
    public ComplicationController ()
    {
    }
    public ComplicationController (IntPtr p) : base (p)
    {
    }
    public override void GetCurrentTimelineEntry (CLKComplication complication, Action<CLKComplicationTimelineEntry> handler)
    {
        CLKComplicationTimelineEntry entry = null;
    var complicationDisplay = "MINUTE " + DateTime.Now.Minute.ToString(); // text to display on watch face
        if (complication.Family == CLKComplicationFamily.UtilitarianLarge)
        {
            var textTemplate = new CLKComplicationTemplateUtilitarianLargeFlat();
            textTemplate.TextProvider = CLKSimpleTextProvider.FromText(complicationDisplay); // dynamic display
            entry = CLKComplicationTimelineEntry.Create(NSDate.Now, textTemplate);
        } else {
            Console.WriteLine("Complication family timeline not supported (" + complication.Family + ")");
        }
        handler (entry);
    }
    public override void GetPlaceholderTemplate (CLKComplication complication, Action<CLKComplicationTemplate> handler)
    {
        CLKComplicationTemplate template = null;
        if (complication.Family == CLKComplicationFamily.UtilitarianLarge) {
            var textTemplate = new CLKComplicationTemplateUtilitarianLargeFlat ();
            textTemplate.TextProvider = CLKSimpleTextProvider.FromText ("MY COMPLICATION"); // static display
            template = textTemplate;
        } else {
            Console.WriteLine ("Complication family placeholder not not supported (" + complication.Family + ")");
        }
        handler (template);
    }
    public override void GetSupportedTimeTravelDirections (CLKComplication complication, Action<CLKComplicationTimeTravelDirections> handler)
    {
        handler (CLKComplicationTimeTravelDirections.None);
    }
}
```

<a name="templates" />

## <a name="complication-templates"></a>複雜的範本

有許多不同的範本適用于每個複雜的樣式。
**環形**範本可讓您在複雜的周圍顯示進度樣式環，這可以用來以圖形方式顯示進度或其他值。

[Apple 的 CLKComplicationTemplate 檔](https://developer.apple.com/reference/clockkit/clkcomplicationtemplate)

### <a name="circular-small"></a>圓形小

這些範本類別名稱前面都會加上 `CLKComplicationTemplateCircularSmall`：

- **RingImage** -顯示單一影像，並在其周圍加上一個進度環。
- **RingText** -顯示一行文字，並在其周圍加上一個進度環。
- **SimpleImage** -只顯示小型的單一影像。
- **SimpleText** -只顯示一個小片段的文字。
- **StackImage** -顯示影像和一行文字，另一個
- **StackText** -顯示兩行文字。

### <a name="modular-small"></a>模組化小型

這些範本類別名稱前面都會加上 `CLKComplicationTemplateModularSmall`：

- **ColumnsText** -顯示文字值的小方格（2個數據列和2個數據行）。
- **RingImage** -顯示單一影像，並在其周圍加上一個進度環。
- **RingText** -顯示一行文字，並在其周圍加上一個進度環。
- **SimpleImage** -只顯示小型的單一影像。
- **SimpleText** -只顯示一個小片段的文字。
- **StackImage** -顯示影像和一行文字，另一個
- **StackText** -顯示兩行文字。

### <a name="modular-large"></a>模組化大型

這些範本類別名稱前面都會加上 `CLKComplicationTemplateModularLarge`：

- 資料行-顯示具有2個數據行的3個**資料列方格**，並選擇性地在每個資料列的左邊包含一個影像。
- **StandardBody** -顯示粗體標頭字串，其中包含兩個數據列的純文字。 標頭可以選擇性地在左側顯示影像。
- **資料表**-顯示粗體標頭字串，其底下有一個2x2 的文字格線。 標頭可以選擇性地在左側顯示影像。
- **TallBody** -顯示粗體標頭字串，下方有較大的字型單行文字。

### <a name="utilitarian-small"></a>小型強調實際

這些範本類別名稱前面都會加上 `CLKComplicationTemplateUtilitarianSmall`：

- 一般 **-在**一行上顯示影像和一些文字（文字應該很短）。
- **RingImage** -顯示單一影像，並在其周圍加上一個進度環。
- **RingText** -顯示一行文字，並在其周圍加上一個進度環。
- **方形**-顯示方形影像（分別為38mm 或 42mm Apple Watch 的40px 或44px 正方形）。

### <a name="utilitarian-large"></a>強調實際大型

這個複雜的樣式只有一個範本： `CLKComplicationTemplateUtilitarianLargeFlat`。
它會顯示單一影像和一些文字，全都放在同一行上。

## <a name="related-links"></a>相關連結

- [Apple 的檔](https://developer.apple.com/library/watchos/documentation/General/Conceptual/WatchKitProgrammingGuide/ComplicationEssentials.html)
- [WWDC 影片](https://developer.apple.com/videos/play/wwdc2015-209/)
