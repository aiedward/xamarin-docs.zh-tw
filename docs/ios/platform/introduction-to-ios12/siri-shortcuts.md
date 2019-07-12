---
title: 在 Xamarin.iOS 中 Siri 快速鍵
description: 本文件說明如何在 iOS 12 中使用 Siri 快速鍵。 它討論 NSUserActivities，自訂的對應方式，指派語音快速鍵、 相關的快速鍵和更多功能。
ms.prod: xamarin
ms.assetid: 86424F79-3A7D-436E-927D-9A3267DA333B
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 08/08/2018
ms.openlocfilehash: 62b577393a498587ee2e2e899d20bfeec7988c4e
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2019
ms.locfileid: "67832378"
---
# <a name="siri-shortcuts-in-xamarinios"></a>在 Xamarin.iOS 中 Siri 快速鍵

在  [iOS 10](~/ios/platform/sirikit/index.md)，Apple 已引進 SiriKit，讓您能夠建置訊息、 VoIP 撥號、 付款、 健身，寫預約，和相片 Siri 與互動的搜尋應用程式。

在  [iOS 11](~/ios/platform/introduction-to-ios11/sirikit.md)，SiriKit 獲得更多類型的應用程式的支援和更大的彈性的 UI 自訂。

iOS 12 新增 Siri 快速鍵，讓所有類型的應用程式公開其功能以 Siri。 當特定應用程式為基礎的工作最相關的使用者，並建議可能的動作，透過使用這項知識，可學習 Siri_快速鍵_。 點選快顯，或使用語音命令叫用將會開啟應用程式，或執行背景工作。

快速鍵應該用來加速使用者的能力，來完成常見工作 – 在許多情況下，而不需要開啟有問題的應用程式中。

## <a name="sample-app-soup-chef"></a>範例應用程式：Soup Chef

若要進一步了解 Siri 快速鍵，看看[Soup Chef](https://developer.xamarin.com/samples/monotouch/ios12/SoupChef/)範例應用程式。 Soup Chef 可讓使用者從虛數 soup 餐廳訂購、 檢視其訂單歷程記錄，以及定義排序 soup Siri 與互動時所要使用的片語。

> [!TIP]
> 在測試之前 Soup Chef iOS 12 模擬器或裝置上，啟用下列兩種設定，偵錯快速鍵時非常有用：
>
> - 在 **設定**應用程式，讓**開發人員 > 顯示最近使用的快速鍵**。
> - 在 **設定**應用程式，讓**開發人員 > 在鎖定畫面上顯示捐贈**。
>
> 這些偵錯設定讓您輕鬆地尋找最近建立 （而不是預測） 快速鍵，在 ios 鎖定螢幕及搜尋畫面。

若要使用範例應用程式：

- 安裝和執行 Soup Chef iOS 12 模擬器上的範例應用程式或[裝置](#testing-on-device)。
- 按一下  **+** 中用以建立新的訂單右上方的按鈕。
- 選取一種詳細，請指定 [數量] 和 [選項]，並點選**訂購**。
- 在 **訂單記錄**畫面上，點選的新建立的順序，以檢視其詳細資料。
- 訂單詳細資料螢幕底部點選**加入 Siri**。
- 記錄順序相關聯，然後點選 的語音片語**完成**。
- 最小化 Soup Chef、 叫用 Siri，並再次下訂單，使用您剛才記錄的語音片語。
- Siri 完成訂單後，重新開啟 Soup Chef，並請注意新的訂單會列在**訂單記錄**螢幕。

範例應用程式示範如何：

- [若要開啟 應用程式使用 NSUserActivity 捷徑](#using-an-nsuseractivity-shortcut-to-open-an-app)。
- [若要執行的工作中使用自訂的意圖快顯](#using-a-custom-intent-shortcut-to-perform-a-task)。
- [提供的使用者介面自訂意圖](#providing-a-user-interface-for-a-custom-intent)。
- [建立語音捷徑](#creating-a-voice-shortcut)。

## <a name="infoplist-and-entitlementsplist"></a>Info.plist 和的 Entitlements.plist

之前更深入挖掘，Soup Chef 程式碼，看看它**Info.plist**並**Entitlements.plist**檔案。

### <a name="infoplist"></a>Info.plist

**Info.plist**中的檔案**SoupChef**專案定義**套件組合識別碼**為`com.xamarin.SoupChef`。 這個套件組合識別碼將用於做為前置詞的意圖和意圖 UI 的套件組合識別碼在本文件稍後討論的延伸模組。

**Info.plist**檔案也包含下列：

```xml
<key>NSUserActivityTypes</key>
<array>
    <string>OrderSoupIntent</string>
    <string>com.xamarin.SoupChef.viewMenu</string>
</array>
```

這`NSUserActivityTypes`Soup Chef 知道如何處理的索引鍵/值組表示`OrderSoupIntent`，和[ `NSUserActivity` ](xref:Foundation.NSUserActivity)具有[ `ActivityType` ](xref:Foundation.NSUserActivity.ActivityType)的"com.xamarin.SoupChef.viewMenu 」。

處理活動和傳遞至應用程式本身，而不是其擴充功能，自訂意圖`AppDelegate`( [ `UIApplicationDelegate` ](xref:UIKit.UIApplicationDelegate)由[ `ContinueUserActivity` ](xref:UIKit.UIApplicationDelegate.ContinueUserActivity*)方法。

### <a name="entitlementsplist"></a>Entitlements.plist

**Entitlements.plist**中的檔案**SoupChef**專案包含下列：

```xml
<key>com.apple.security.application-groups</key>
<array>
    <string>group.com.xamarin.SoupChef</string>
</array>
<key>com.apple.developer.siri</key>
<true/>
```

此設定表示應用程式使用 「 group.com.xamarin.SoupChef 」 應用程式群組。 **SoupChefIntents**應用程式擴充功能會使用這個相同的應用程式群組，可讓兩個專案共用 [`NSUserDefaults`](xref:Foundation.NSUserDefaults)
資料。

`com.apple.developer.siri`而 key 則代表應用程式互動 Siri。

> [!NOTE]
> **SoupChef**專案的組建組態集**自訂權利**來**Entitlements.plist**。

## <a name="using-an-nsuseractivity-shortcut-to-open-an-app"></a>若要開啟 應用程式使用 NSUserActivity 快顯

若要建立捷徑所開啟的應用程式，以顯示特定內容，建立`NSUserActivity`並將它附加至您想要開啟快顯畫面的檢視控制器。

### <a name="setting-up-an-nsuseractivity"></a>設定 NSUserActivity

在 [功能表] 畫面`SoupMenuViewController`會建立`NSUserActivity`並將它指派給檢視控制器[ `UserActivity` ](xref:UIKit.UIResponder.UserActivity)屬性：

```csharp
public override void ViewDidLoad()
{
    base.ViewDidLoad();
    UserActivity = NSUserActivityHelper.ViewMenuActivity;
}
```

設定`UserActivity`屬性_donates_ Siri 的活動。 從這個捐贈，Siri 獲得何時何地這個活動是與使用者相關，並學習更建議未來的相關資訊。

`NSUserActivityHelper` 公用程式類別包含在**SoupChef**方案，請在**SoupKit**類別庫。 它會建立`NSUserActivity`並設定各種 Siri 和搜尋相關的屬性：

```csharp
public static string ViewMenuActivityType = "com.xamarin.SoupChef.viewMenu";

public static NSUserActivity ViewMenuActivity {
    get
    {
        var userActivity = new NSUserActivity(ViewMenuActivityType)
        {
            Title = NSBundleHelper.SoupKitBundle.GetLocalizedString("ORDER_LUNCH_TITLE", "View menu activity title"),
            EligibleForSearch = true,
            EligibleForPrediction = true
        };

        var attributes = new CSSearchableItemAttributeSet(NSUserActivityHelper.SearchableItemContentType)
        {
            ThumbnailData = UIImage.FromBundle("tomato").AsPNG(),
            Keywords = ViewMenuSearchableKeywords,
            DisplayName = NSBundleHelper.SoupKitBundle.GetLocalizedString("ORDER_LUNCH_TITLE", "View menu activity title"),
            ContentDescription = NSBundleHelper.SoupKitBundle.GetLocalizedString("VIEW_MENU_CONTENT_DESCRIPTION", "View menu content description")
        };
        userActivity.ContentAttributeSet = attributes;

        var phrase = NSBundleHelper.SoupKitBundle.GetLocalizedString("ORDER_LUNCH_SUGGESTED_PHRASE", "Voice shortcut suggested phrase");
        userActivity.SuggestedInvocationPhrase = phrase;
        return userActivity;
    }
}
```

請注意下列特別的是：

- 設定`EligibleForPrediction`至`true`指出 Siri 能夠預測此活動，與介面的捷徑。
- [ `ContentAttributeSet` ](xref:Foundation.NSUserActivity.ContentAttributeSet)陣列是一種標準[ `CSSearchableItemAttributeSet` ](xref:CoreSpotlight.CSSearchableItemAttributeSet)用來包含`NSUserActivity`iOS 搜尋結果中。
- [`SuggestedInvocationPhrase`](xref:Foundation.NSUserActivity.SuggestedInvocationPhrase) 是 Siri 會建議使用者可能選擇指派為快速鍵的片語時的片語。

### <a name="handling-an-nsuseractivity-shortcut"></a>處理 NSUserActivity 快顯

若要處理`NSUserActivity`由使用者叫用捷徑，iOS 應用程式必須覆寫`ContinueUserActivity`方法`AppDelegate`類別，回應取決於`ActivityType`欄位傳入`NSUserActivity`物件：

```csharp
public override bool ContinueUserActivity(UIApplication application, NSUserActivity userActivity, UIApplicationRestorationHandler completionHandler)
{
    // ...
    else if (userActivity.ActivityType == NSUserActivityHelper.ViewMenuActivityType)
    {
        HandleUserActivity();
        return true;
    }
    // ...
}
```

這個方法會呼叫`HandleUserActivity`，這會尋找功能表螢幕的 segue 和叫用它：

```csharp
void HandleUserActivity()
{
    var rootViewController = Window?.RootViewController as UINavigationController;
    var orderHistoryViewController = rootViewController?.ViewControllers?.FirstOrDefault() as OrderHistoryTableViewController;
    if (orderHistoryViewController is null)
    {
        Console.WriteLine("Failed to access OrderHistoryTableViewController.");
        return;
    }
    var segue = OrderHistoryTableViewController.SegueIdentifiers.SoupMenu;
    orderHistoryViewController.PerformSegue(segue, null);
}
```

### <a name="assigning-a-phrase-to-an-nsuseractivity"></a>將片語指派給 NSUserActivity

若要將指派的片語`NSUserActivity`，開啟 iOS**設定**應用程式，然後選擇**Siri 與搜尋 > 我的捷徑**。 然後，選取 [捷徑] （在此案例中，「 訂單的午餐 」），並記錄片語。

叫用 Siri 和使用這個片語將會開啟詳細 Chef [功能表] 畫面。

## <a name="using-a-custom-intent-shortcut-to-perform-a-task"></a>若要執行的工作中使用自訂的意圖快顯

### <a name="defining-a-custom-intent"></a>定義自訂的意圖

若要提供可讓使用者迅速完成您的應用程式的相關特定工作的捷徑，請建立自訂的意圖。 自訂意圖表示使用者可能會想要完成，該工作，並從工作的執行產生的可能回應的相關參數的工作。 根據自訂的意圖的定義方式，叫用它可以開啟您的應用程式，或執行背景工作。

使用 Xcode 10 來建立自訂的意圖。 在  [SoupChef 儲存機制](https://github.com/xamarin/ios-samples/tree/master/ios12/SoupChef)中, 定義自訂的意圖**OrderSoupIntentCodeGen**，OBJECTIVE-C 專案。 開啟這個專案，然後選取**Intents.intentdefinition**中的檔案**專案導覽器**若要檢視**OrderSoup**意圖。

請注意以下各點：

- 具有意圖**分類**的**順序**。 有各種預先定義的類別可用於自訂的對應方式;選取最符合工作可讓您自訂的目的。 由於這是排序的應用程式，soup **OrderSoupIntent**會使用**順序**。
- **確認**核取方塊會指出是否使用 Siri 必須要求執行工作前先行確認。 針對**順序 Soup**意圖 Soup Chef 中，啟用此選項，因為使用者進行購買。
- **參數**.intentdefinition 檔案區段會定義相關的捷徑的參數。 若要訂購 soup，Soup Chef 必須知道類型 soup、 其數量，以及任何相關聯的選項。
每個參數具有類型;無法預先定義的類型所代表的參數設定為**自訂**。
- **快顯類型**介面描述的各種不同的參數組合，建議您快顯時，可以使用 Siri。 相關聯**標題**並**副標題**區段可讓您定義的訊息呈現給使用者的建議快顯時，會使用 Siri。
- **支援背景執行**應該選取核取方塊，為任何可執行而不需要開啟需要使用者互動的應用程式的捷徑。

### <a name="defining-custom-intent-responses"></a>定義自訂的意圖回應

**回應**下方的項目巢狀**OrderSoup**意圖表示因 soup 訂單的可能回應。

在  **OrderSoup**意圖的回應定義，請注意下列事項：

- 回應**屬性**可用來自訂訊息傳達給使用者。 **OrderSoup**意圖回應具有**soup**並**waittime 偏**屬性。
- **回應範本**指定可用的意圖工作完成後，指出狀態的各種成功和失敗訊息。
- **成功**應該選取核取方塊，表示作業成功的回應。
- **OrderSoupIntent**成功回應會使用**soup**並**waittime 偏**屬性，以提供易記且實用的訊息，描述當 soup 順序就可以開始。

### <a name="generating-code-for-the-custom-intent"></a>產生自訂的意圖的程式碼

建置包含此自訂的意圖定義的 Xcode 專案時，會導致產生程式碼，可用來以程式設計方式使用自訂的意圖和其回應的 Xcode。

若要檢視產生的程式碼：

- 開啟**AppDelegate.m**。
- 將匯入新增至自訂的意圖標頭檔中： `#import "OrderSoupIntent.h"`
- 在類別中的任何方法，將參考加入至`OrderSoupIntent`。
- 以滑鼠右鍵按一下`OrderSoupIntent`，然後選擇 **移至定義**。
- 在新開啟檔案中，以滑鼠右鍵按一下**OrderSoupIntent.h**，然後選取**顯示於 Finder**。
- 這會開啟**Finder**視窗，其中包含含有產生的程式碼的.h 冓.m 檔案。

產生的程式碼包括：

- `OrderSoupIntent` – 表示自訂意圖的類別。
- `OrderSoupIntentHandling` – 一種通訊協定可定義將用來確認，應該執行目的的方法和它實際執行的方法。
- `OrderSoupIntentResponseCode` – 定義各種不同的回應狀態的列舉。
- `OrderSoupIntentResponse` – 表示意圖執行回應的類別。

### <a name="creating-a-binding-to-the-custom-intent"></a>建立自訂的意圖繫結

若要使用由 Xcode 所產生的 Xamarin.iOS 應用程式中的程式碼，建立C#，繫結。

#### <a name="creating-a-static-library-and-c-binding-definitions"></a>建立靜態程式庫和C#繫結定義

在  [SoupChef 儲存機制](https://github.com/xamarin/ios-samples/tree/master/ios12/SoupChef)，瞧一瞧**OrderSoupIntentStaticLib**資料夾，然後開啟**OrderSoupIntentStaticLib.xcodeproj** Xcode 專案。

這**Cocoa Touch 靜態程式庫**專案中包含**OrderSoupIntent.h**並**OrderSoupIntent.m**由 Xcode 所產生的檔案。

#### <a name="configuring-the-static-library-project-build-settings"></a>設定靜態程式庫專案組建設定

在 Xcode 中**專案導覽器**，選取最上層專案中， **OrderSoupIntentStaticLib**，然後瀏覽至**建置階段 > 編譯來源**。
請注意， **OrderSoupIntent.m** (會匯入**OrderSoupIntent.h**) 列於此處。 在 **連結二進位與程式庫**，請注意， **Intents.framework**並**Foundation.framework**包含。
這些設定之後，會正確地建置架構。

#### <a name="building-the-static-library-and-generating-c-bindings-definitions"></a>建置靜態程式庫，並產生C#繫結定義

若要建置靜態程式庫，並產生C#繫結定義，請遵循下列步驟：

- [安裝目標 Sharpie](https://docs.microsoft.com/xamarin/cross-platform/macios/binding/objective-sharpie/get-started?context=xamarin/mac#installing-objective-sharpie)，用來從 Xcode 所建立的.h 冓.m 檔案產生繫結定義的工具。

- 設定您的系統，才能使用 Xcode 10 命令列工具：

    > [!WARNING]
    > 更新選取的命令列工具，會影響所有已安裝在您的系統上的 Xcode 版本。 當您完成使用 Soup Chef 範例應用程式，請務必還原這設為其原始設定。

    - 在 Xcode 中，選擇**Xcode > 喜好設定 > 位置**並設定**命令列工具**系統上可用的最新 Xcode 10 安裝。

- 在終端機中，`cd`要**OrderSoupIntentStaticLib**目錄。

- 型別`make`，哪些組建：

    - 靜態程式庫， **libOrderSoupIntentStaticLib.a**
    - 在  **bo**輸出目錄中，C#繫結定義：
        - **ApiDefinitions.cs**
        - **StructsAndEnums.cs**

**OrderSoupIntentBindings**專案，依賴此靜態程式庫和其相關聯的繫結定義，會自動建立這些項目。
不過，手動執行上述程序可確保它建立如預期般運作。

如需有關建立靜態程式庫，並使用目標 Sharpie 建立C#繫結定義，看看[繫結 iOS OBJECTIVE-C 程式庫](https://docs.microsoft.com/xamarin/ios/platform/binding-objective-c/walkthrough?tabs=vsmac#creating-a-static-library)逐步解說。

#### <a name="creating-a-bindings-library"></a>建立繫結程式庫

使用靜態程式庫和C#建立繫結定義，其餘部分使用 Xcode 產生時所需要的意圖相關 Xamarin.iOS 專案中的程式碼會繫結程式庫。

在  [Soup Chef 儲存機制](https://github.com/xamarin/ios-samples/tree/master/ios12/SoupChef)，接著請開啟**SoupChef.sln**檔案。 除此之外，此解決方案內含**OrderSoupIntentBinding**，上面所產生的靜態程式庫的繫結程式庫。

請注意，特別是此專案包含：

- **ApiDefinitions.cs** – 檔案的目標 Sharpie 上面所產生，並新增至此專案。 這個檔案**建置動作**設為**ObjcBindingApiDefinition**。
- **StructsAndEnums.cs** – 另一個檔案的目標 Sharpie 上面所產生，並加入至這個專案。 這個檔案**建置動作**設為**ObjcBindingCoreSource**。
- A**原生參考**要**libOrderSoupIntentStaticLib.a**，上方建置靜態程式庫。

> [!NOTE]
> 兩者**ApiDefinitions.cs**並**StructsAndEnums.cs**包含的屬性，例如`[Watch (5,0), iOS (12,0)]`。 這些屬性，產生的目標 Sharpie 有已取消註解，因為它們不需要為此專案。

如需有關建立C#繫結程式庫，可看一下[繫結 iOS OBJECTIVE-C 程式庫](https://docs.microsoft.com/xamarin/ios/platform/binding-objective-c/walkthrough?tabs=vsmac#create-a-xamarinios-binding-project)逐步解說。

請注意， **SoupChef**專案中包含的參考**OrderSoupIntentBinding**，也就是說，它現在可以存取，在C#，類別、 介面和列舉，其中包含：

- `OrderSoupIntent`
- `OrderSoupIntentHandling`
- `OrderSoupIntentResponse`
- `OrderSoupIntenseResponseCode`

### <a name="adding-the-intentdefinition-file-to-your-solution"></a>將.intentdefinition 檔案新增至您的方案

在C# **SoupChef**解決方案**SoupKit**專案包含應用程式和其擴充功能之間共用的程式碼。 **Intents.intentdefinition**檔案置於**Base.lproj**目錄**SoupKit**，而且它具有**建置動作**的**內容**。 建置程序會將此檔案複製到 Soup Chef 應用程式套件組合，並需要應用程式能夠正常運作。

### <a name="donating-an-intent"></a>捐贈意圖

為了讓 Siri 建議快顯，它必須先了解快顯時相關。

若要讓 Siri 此了解，Soup Chef _donates_之意圖 Siri 每次使用者將放入 soup 順序。 當它已由他人捐贈，其中它已由他人捐贈，根據此捐贈 – 它所包含的參數 – Siri 學習未來建議快顯的時機。

**SoupChef**使用`SoupOrderDataManager`放置捐贈的類別。
呼叫至訂購詳細的使用者，當`PlaceOrder`方法會呼叫[ `DonateInteraction` ](xref:Intents.INInteraction.DonateInteraction*):

```csharp
void DonateInteraction(Order order)
{
    var interaction = new INInteraction(order.Intent, null);
    interaction.Identifier = order.Identifier.ToString();
    interaction.DonateInteraction((error) =>
    {
        // ...
    });
}
```

之後擷取意圖，它會包裝在[ `INInteraction` ](xref:Intents.INInteraction)。
`INInteraction`指定 [`Identifier`](xref:Intents.INInteraction.Identifier*)
符合 （這會很有幫助稍後刪除已不再有效的意圖捐贈時） 的順序的唯一識別碼。 然後，互動會捐獻給 Siri。

呼叫`order.Intent`getter 提取`OrderSoupIntent`表示 splittunneling 順序及其`Quantity`， `Soup`， `Options`，和映像，以及使用建議的形式，使用者記錄建立關聯的 Siri 片語時叫用片語與意圖：

```csharp
public OrderSoupIntent Intent
{
    get
    {
        var orderSoupIntent = new OrderSoupIntent();
        orderSoupIntent.Quantity = new NSNumber(Quantity);
        orderSoupIntent.Soup = new INObject(MenuItem.ItemNameKey, MenuItem.LocalizedString);

        var image = UIImage.FromBundle(MenuItem.IconImageName);
        if (!(image is null))
        {
            var data = image.AsPNG();
            orderSoupIntent.SetImage(INImage.FromData(data), "soup");
        }

        orderSoupIntent.Options = MenuItemOptions
            .ToArray<MenuItemOption>()
            .Select<MenuItemOption, INObject>(arg => new INObject(arg.Value, arg.LocalizedString))
            .ToArray<INObject>();

        var comment = "Suggested phrase for ordering a specific soup";
        var phrase = NSBundleHelper.SoupKitBundle.GetLocalizedString("ORDER_SOUP_SUGGESTED_PHRASE", comment);
        orderSoupIntent.SuggestedInvocationPhrase = String.Format(phrase, MenuItem.LocalizedString);

        return orderSoupIntent;
    }
}
```

#### <a name="removing-invalid-donations"></a>移除無效的捐贈

請務必移除捐贈，讓 Siri 不會進行無用或令人混淆的快顯的建議，已不再有效。

在詳細 Chef**設定功能表**螢幕可以用來標示功能表項目為無法使用。 Siri 不會再提供建議的捷徑來排序不可用功能表項目，因此`RemoveDonation`方法的`SoupMenuManager`刪除捐贈不再可用的功能表項目。 它的運作方式：

- 尋找目前無法使用的功能表項目相關聯的訂單。
- 抓取其識別項。
- 正在刪除具有該相同的識別項的互動。

```csharp
void RemoveDonation(MenuItem menuItem)
{
    if (!menuItem.IsAvailable)
    {
        Order[] orderHistory = OrderManager?.OrderHistory.ToArray<Order>();
        if (orderHistory is null)
        {
            return;
        }

        string[] orderIdentifiersToRemove = orderHistory
            .Where<Order>((order) => order.MenuItem.ItemNameKey == menuItem.ItemNameKey)
            .Select<Order, string>((order) => order.Identifier.ToString())
            .ToArray<string>();

        INInteraction.DeleteInteractions(orderIdentifiersToRemove, (error) =>
        {
            if (!(error is null))
            {
                Console.WriteLine($"Failed to delete interactions with error {error.ToString()}");
            }
            else
            {
                Console.WriteLine("Successfully deleted interactions");
            }
        });
    }
}
```

### <a name="creating-an-intents-extension"></a>建立 Intents 延伸模組

執行時使用 Siri 叫用意圖的程式碼會放在 Intents 延伸模組，也可以為新的專案加入至現有的 Xamarin.iOS 應用程式等 Soup Chef 與相同的方案。 在  **SoupChef**方案中，呼叫擴充功能**SoupChefIntents**。

#### <a name="soupchefintents-infoplist-and-entitlementsplist"></a>SoupChefIntents – Info.plist 和的 Entitlements.plist

##### <a name="soupchefintents-infoplist"></a>SoupChefIntents – Info.plist

**Info.plist**中**SoupChefIntents**專案定義**套件組合識別碼**為`com.xamarin.SoupChef.SoupChefIntents`。

**Info.plist**檔案也包含下列：

```xml
<key>NSExtension</key>
<dict>
    <key>NSExtensionAttributes</key>
    <dict>
        <key>IntentsRestrictedWhileLocked</key>
        <array/>
        <key>IntentsSupported</key>
        <array>
            <string>OrderSoupIntent</string>
        </array>
        <key>IntentsRestrictedWhileProtectedDataUnavailable</key>
        <array/>
    </dict>
    <key>NSExtensionPointIdentifier</key>
    <string>com.apple.intents-service</string>
    <key>NSExtensionPrincipalClass</key>
    <string>IntentHandler</string>
</dict>
```

在上述**Info.plist**:

- `IntentsRestrictedWhileLocked` 列出應該只在解除鎖定裝置時才處理的意圖。
- `IntentsSupported` 列出此延伸模組處理的意圖。
- `NSExtensionPointIdentifier` 指定類型的應用程式擴充功能 (請參閱[Apple 的文件](https://developer.apple.com/library/archive/documentation/General/Reference/InfoPlistKeyReference/Articles/AppExtensionKeys.html#//apple_ref/doc/uid/TP40014212-SW15)如需詳細資訊)。
- `NSExtensionPrincipalClass` 指定應該用來處理此延伸模組支援的對應方式的類別。

##### <a name="soupchefintents-entitlementsplist"></a>SoupChefIntents – Entitlements.plist

**Entitlements.plist**中**SoupChefIntents**專案具有**應用程式群組**功能。 這項功能已設定為使用相同的應用程式群組，作為**SoupChef**專案：

```xml
<key>com.apple.security.application-groups</key>
<array>
    <string>group.com.xamarin.SoupChef</string>
</array>
```

Soup Chef 保存資料與`NSUserDefaults`。 若要共用應用程式和應用程式擴充功能之間的資料，它們會參考相同的應用程式群組中其**Entitlements.plist**檔案。

> [!NOTE]
> **SoupChefIntents**專案的組建組態集**自訂權利**來**Entitlements.plist**。

#### <a name="handling-an-ordersoupintent-background-task"></a>處理 OrderSoupIntent 背景工作

Intents 延伸模組，執行必要的背景工作的捷徑，根據自訂的目的。

Siri 呼叫[ `GetHandler` ](xref:Intents.INExtension.GetHandler*)方法`IntentHandler`類別 (定義於**Info.plist**作為`NSExtensionPrincipalClass`) 來取得擴充的類別執行個體`OrderSoupIntentHandling`，可用若要處理`OrderSoupIntent`:

```csharp
[Register("IntentHandler")]
public class IntentHandler : INExtension
{
    public override NSObject GetHandler(INIntent intent)
    {
        if (intent is OrderSoupIntent)
        {
            return new OrderSoupIntentHandler();
        }
        throw new Exception("Unhandled intent type: ${intent}");
    }

    protected IntentHandler(IntPtr handle) : base(handle) { }
}
```

`OrderSoupIntentHandler`定義於**SoupKit**專案共用程式碼，會實作兩個重要的方法：

- `ConfirmOrderSoup` – 確認與意圖相關聯的工作應該實際執行
- `HandleOrderSoup` – 放入詳細的訂單，並回應使用者呼叫傳入的完成處理常式

#### <a name="handling-an-ordersoupintent-that-opens-the-app"></a>處理開啟應用程式 OrderSoupIntent

應用程式必須適當地處理無法在背景中執行的對應方式。
這些在相同的方式處理`NSUserActivity`快速鍵，請在`ContinueUserActivity`方法`AppDelegate`:

```csharp
public override bool ContinueUserActivity(UIApplication application, NSUserActivity userActivity, UIApplicationRestorationHandler completionHandler)
{
    var intent = userActivity.GetInteraction()?.Intent as OrderSoupIntent;
    if (!(intent is null))
    {
        HandleIntent(intent);
        return true;
    }
    // ...
}  
```

## <a name="providing-a-user-interface-for-a-custom-intent"></a>提供的使用者介面自訂的意圖

Intents UI 延伸模組會提供 Intents 延伸模組中的自訂使用者介面。 在  **SoupChef**解決方案**SoupChefIntentsUI**是 Intents UI 延伸模組所提供的介面**SoupChefIntents**。

### <a name="soupchefintentsui--infoplist-and-entitlementsplist"></a>SoupChefIntentsUI – Info.plist 和的 Entitlements.plist

#### <a name="soupchefintentsui-infoplist"></a>SoupChefIntentsUI – Info.plist

**Info.plist**中**SoupChefIntentsUI**專案定義**套件組合識別碼**為`com.xamarin.SoupChef.SoupChefIntentsui`。

**Info.plist**檔案也包含下列：

```xml
<key>NSExtension</key>
<dict>
    <key>NSExtensionAttributes</key>
    <dict>
        <key>IntentsSupported</key>
        <array>
            <string>OrderSoupIntent</string>
        </array>
        <!-- ... -->
    </dict>
    <key>NSExtensionPointIdentifier</key>
    <string>com.apple.intents-ui-service</string>
    <key>NSExtensionMainStoryboard</key>
    <string>MainInterface</string>
</dict>
```

在上述**Info.plist**:

- `IntentsSupported` 表示`OrderSoupIntent`由這個 Intents UI 延伸模組。
- `NSExtensionPointIdentifier` 指定類型的應用程式擴充功能 (請參閱[Apple 的文件](https://developer.apple.com/library/archive/documentation/General/Reference/InfoPlistKeyReference/Articles/AppExtensionKeys.html#//apple_ref/doc/uid/TP40014212-SW15)如需詳細資訊)。
- `NSExtensionMainStoryboard` 指定定義此延伸模組的主要介面將分鏡腳本

#### <a name="soupchefintentsui-entitlementsplist"></a>SoupChefIntentsUI – Entitlements.plist

**SoupChefIntentsUI**專案不需要**Entitlements.plist**檔案。

### <a name="creating-the-user-interface"></a>建立使用者介面

由於**Info.plist** for **SoupChefIntentsUI**設定`NSExtensionMainStoryboard`機碼`MainInterface`，則**MainInterace.storyboard**檔案定義的介面Intents UI 延伸模組。

這個分鏡腳本，在中，沒有單一檢視控制器的型別**IntentViewController**。 它會參考兩個檢視：

- **invoiceView**，型別 `InvoiceView`
- **confirmationView**，型別 `ConfirmOrderView`

> [!NOTE]
> 介面**invoiceView**並**confirmationView**中所定義**Main.storyboard**做為次要的檢視。 IOS 設計工具在 Visual Studio for Mac 和 Visual Studio 2017 不提供用於檢視或編輯次要檢視; 支援若要這樣做，請開啟**Main.storyboard** Xcode 的 Interface Builder 中。

`IntentViewController` 實作 [`IINUIHostedViewControlling`](xref:IntentsUI.IINUIHostedViewControlling)
介面，用來提供一種自訂介面時使用 Siri 意圖。 的 [`ConfigureView`](xref:IntentsUI.INUIHostedViewControlling_Extensions.ConfigureView*)
會呼叫方法以自訂介面，顯示的確認或發票，取決於是否確認互動之後 ([`INIntentHandlingStatus.Ready`](xref:Intents.INIntentHandlingStatus)) 或已執行成功 ([ `INIntentHandlingStatus.Success`](xref:Intents.INIntentHandlingStatus)):

```csharp
[Export("configureViewForParameters:ofInteraction:interactiveBehavior:context:completion:")]
public void ConfigureView(
    NSSet<INParameter> parameters,
    INInteraction interaction,
    INUIInteractiveBehavior interactiveBehavior,
    INUIHostedViewContext context,
    INUIHostedViewControllingConfigureViewHandler completion)
{
    // ...
    if (interaction.IntentHandlingStatus == INIntentHandlingStatus.Ready)
    {
        desiredSize = DisplayInvoice(order, intent);
    }
    else if(interaction.IntentHandlingStatus == INIntentHandlingStatus.Success)
    {
        var response = interaction.IntentResponse as OrderSoupIntentResponse;
        if (!(response is null))
        {
            desiredSize = DisplayOrderConfirmation(order, intent, response);
        }
    }
    completion(true, parameters, desiredSize);
}
```

> [!TIP]
> 如需詳細資訊`ConfigureView`方法中，觀看 Apple WWDC 2017 簡報[What's New in SiriKit](https://developer.apple.com/videos/play/wwdc2017/214/)。

## <a name="creating-a-voice-shortcut"></a>建立語音捷徑

Soup Chef 提供介面將語音捷徑指派給每個訂單，使其能夠使用 Siri 的訂單詳細。 事實上，用來記錄，並指派語音快速鍵介面 iOS 所提供，而且需要一些自訂程式碼。

在  `OrderDetailViewController`，當使用者點選之資料表的**加入 Siri**資料列， [ `RowSelected` ](xref:UIKit.UITableViewSource.RowSelected*)方法顯示以新增或編輯語音快顯畫面：

```csharp
public override void RowSelected(UITableView tableView, NSIndexPath indexPath)
{
    // ...
    else if (TableConfiguration.Sections[indexPath.Section].Type == OrderDetailTableConfiguration.SectionType.VoiceShortcut)
    {
        INVoiceShortcut existingShortcut = VoiceShortcutDataManager?.VoiceShortcutForOrder(Order);
        if (!(existingShortcut is null))
        {
            var editVoiceShortcutViewController = new INUIEditVoiceShortcutViewController(existingShortcut);
            editVoiceShortcutViewController.Delegate = this;
            PresentViewController(editVoiceShortcutViewController, true, null);
        }
        else
        {
            // Since the app isn't yet managing a voice shortcut for
            // this order, present the add view controller
            INShortcut newShortcut = new INShortcut(Order.Intent);
            if (!(newShortcut is null))
            {
                var addVoiceShortcutVC = new INUIAddVoiceShortcutViewController(newShortcut);
                addVoiceShortcutVC.Delegate = this;
                PresentViewController(addVoiceShortcutVC, true, null);
            }
        }
    }
}
```

根據現有的語音捷徑是否存在目前顯示的順序，請`RowSelected`呈現的型別檢視控制器[ `INUIEditVoiceShortcutViewController` ](xref:IntentsUI.INUIEditVoiceShortcutViewController)或是[ `INUIAddVoiceShortcutViewController` ](xref:IntentsUI.INUIAddVoiceShortcutViewController)。
在每個案例中，`OrderDetailViewController`本身設為檢視控制器的`Delegate`，這也是為什麼它也會實作 [`IINUIAddVoiceShortcutViewControllerDelegate`](xref:IntentsUI.IINUIAddVoiceShortcutViewControllerDelegate)
並[ `IINUIEditVoiceShortcutViewControllerDelegate` ](xref:IntentsUI.IINUIEditVoiceShortcutViewControllerDelegate)。

## <a name="testing-on-device"></a>在裝置上測試

若要在裝置上執行詳細 Chef，請遵循下列指示。 另請閱讀[附註內容自動佈建](#automatic-provisioning)。

### <a name="app-group-app-ids-provisioning-profiles"></a>應用程式群組中，應用程式識別碼 」 佈建設定檔

在 **憑證、 識別碼與設定檔**一節[Apple 開發人員入口網站](https://developer.apple.com/)，執行下列動作：

- 建立應用程式群組，以詳細 Chef 應用程式和其擴充功能之間共用資料。 例如： **group.com.yourcompanyname.SoupChef**

- 建立三個應用程式識別碼： 一個用於應用程式本身，另一個用於 Intents 延伸模組，一個用於 Intents UI 延伸模組。 例如：

    - 應用程式： **com.yourcompanyname.SoupChef**
        - 此應用程式識別碼，將指派 SiriKit 並**應用程式群組**功能。

    - Intents 延伸模組： **com.yourcompanyname.SoupChef.Intents**
        - 此應用程式識別碼，將指派**應用程式群組**功能。

    - Intents UI 延伸模組： **com.yourcompanyname.SoupChef.Intentsui**
        - 此應用程式識別碼需要任何特殊的功能。

- 建立上述的應用程式識別碼之後, 編輯**應用程式群組**上面建立的應用程式和 Intents 延伸模組，指定特定的應用程式群組指派的功能。

- 建立三個新開發佈建設定檔，一個用於每個新的應用程式識別碼。

- 下載這些佈建設定檔，然後按兩下每個憑證，來安裝它。 如果 Visual Studio for Mac 或 Visual Studio 2017 已在執行中，重新啟動，以確保它會註冊新的佈建設定檔。

### <a name="editing-infoplist-entitlementsplist-and-source-code"></a>編輯 Info.plist、 Entitlements.plist 和原始碼

在 Visual Studio for Mac 或 Visual Studio 2017 中，執行下列動作：

- 更新各種**Info.plist**方案中的檔案。 設定應用程式、 Intents 延伸模組，以及 Intents UI 延伸模組**套件組合識別碼**上面定義的應用程式識別碼：

    - 應用程式： **com.yourcompanyname.SoupChef**
    - Intents 延伸模組： **com.yourcompanyname.SoupChef.Intents**
    - Intents UI 延伸模組： **com.yourcompanyname.SoupChef.Intentsui**

- 更新**Entitlements.plist**申請**SoupChef**專案：
    - 針對**應用程式群組**功能，設定為上面所建立的新應用程式群組的群組 (在上述範例中，它已**group.com.yourcompanyname.SoupChef**)。
    - 請確定**SiriKit**已啟用。

- 更新**Entitlements.plist**申請**SoupChefIntents**專案：
    - 針對**應用程式群組**功能，設定為上面所建立的新應用程式群組的群組 (在上述範例中，它已**group.com.yourcompanyname.SoupChef**)。

- 最後，開啟**NSUserDefaultsHelper.cs**。 設定`AppGroup`變數設為新的應用程式群組的值 (例如，將它設定為`group.com.yourcompanyname.SoupChef`)。

### <a name="configuring-the-build-settings"></a>設定組建設定

在 Visual Studio for Mac 或 Visual Studio 2017:

- 開啟選項/屬性**SoupChef**專案。 在  **iOS 套件組合簽署**索引標籤上，設定**簽署身分識別**為自動並**佈建設定檔**給新的應用程式特定佈建設定檔，您在上面建立。

- 開啟選項/屬性**SoupChefIntents**專案。 在上**iOS 套件組合簽署**索引標籤上，設定**簽署身分識別**為自動並**佈建設定檔**至您建立新 Intents 延伸模組特定佈建設定檔上方。

- 開啟選項/屬性**SoupChefIntentsUI**專案。 在  **iOS 套件組合簽署**索引標籤上，設定**簽署身分識別**為自動並**佈建設定檔**新 Intents ui 延伸模組特定佈建設定檔您上面所建立。

使用這些變更之後，將 iOS 裝置上執行應用程式。

### <a name="automatic-provisioning"></a>自動佈建

請注意，您可以使用[自動佈建](https://docs.microsoft.com/xamarin/ios/get-started/installation/device-provisioning/automatic-provisioning)來完成許多種佈建直接在 IDE 中的工作。
不過，自動佈建不會設定應用程式群組。 您必須手動設定**Entitlements.plist**具有您想要使用，應用程式群組名稱的檔案，請前往 「 Apple 開發人員入口網站建立應用程式群組，請將該應用程式群組指派給所自動建立每個應用程式識別碼佈建，重新產生應用程式、 Intents 延伸模組 （Intents UI 延伸模組） 來包含新建立的應用程式群組，並下載及安裝佈建設定檔。

## <a name="related-links"></a>相關連結

- [Soup Chef (Xamarin)](https://developer.xamarin.com/samples/monotouch/ios12/SoupChef/)
- [Soup Chef (Swift)](https://developer.apple.com/documentation/sirikit/accelerating_app_interactions_with_shortcuts?language=objc)
- [SiriKit (Apple)](https://developer.apple.com/sirikit/)
- [Siri 快速鍵 – WWDC 2018 簡介](https://developer.apple.com/videos/play/wwdc2018/211/)
- [建置使用 Siri 快速鍵 – WWDC 2018 的語音](https://developer.apple.com/videos/play/wwdc2018/214/)
- [Siri 錶 – WWDC 2018 Siri 快速鍵](https://developer.apple.com/videos/play/wwdc2018/217/)
- [什麼是 SiriKit – WWDC 2017 的新功能](https://developer.apple.com/videos/play/wwdc2017/214/)
- [建立 Intents 應用程式延伸模組 (Apple)](https://developer.apple.com/documentation/sirikit/creating_an_intents_app_extension?language=objc)
