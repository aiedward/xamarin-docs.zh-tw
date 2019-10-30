---
title: 在 Xamarin 中 Siri 快捷方式
description: 本檔說明如何使用 iOS 12 中的 Siri 快捷方式。 其中討論 NSUserActivities、自訂意圖、指派語音快捷方式、相關快捷方式等等。
ms.prod: xamarin
ms.assetid: 86424F79-3A7D-436E-927D-9A3267DA333B
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 08/08/2018
ms.openlocfilehash: 40b7adbed3489d449e583b22fa477287d11bdf42
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73031875"
---
# <a name="siri-shortcuts-in-xamarinios"></a>在 Xamarin 中 Siri 快捷方式

在[iOS 10](~/ios/platform/sirikit/index.md)中，Apple 引進了 SiriKit，讓您可以建立與 Siri 互動的訊息、VoIP 通話、付款、workouts、操作預約和相片搜尋應用程式。

在[iOS 11](~/ios/platform/introduction-to-ios11/sirikit.md)中，SiriKit 取得更多應用程式類型的支援，並提供更大的彈性來進行 UI 自訂。

iOS 12 新增了 Siri 快捷方式，可讓所有類型的應用程式將其功能公開給 Siri。 Siri 會在某些以應用程式為基礎的工作與使用者最相關的情況下學習，並使用此知識透過_快捷方式_來建議可能的動作。 使用語音命令來點擊快捷方式或加以叫用，將會開啟應用程式或執行背景工作。

快捷方式應該用來加速使用者完成一般工作的能力–在許多情況下，甚至不需要開啟有問題的應用程式。

## <a name="sample-app-soup-chef"></a>範例應用程式：湯品 Chef

若要進一步瞭解 Siri 快捷方式，請參閱[湯品 Chef](https://docs.microsoft.com/samples/xamarin/ios-samples/ios12-soupchef)範例應用程式。 湯品 Chef 可讓使用者將來自虛構湯品餐廳的訂單、查看訂單歷程記錄，以及定義在排序湯品時使用的片語（藉由與 Siri 互動）。

> [!TIP]
> 在 iOS 12 模擬器或裝置上測試湯品 Chef 之前，請啟用下列兩項設定，這在偵錯工具快捷方式時很有用：
>
> - 在 **設定** 應用程式中，啟用 **開發人員 > 顯示最近使用的快捷方式**。
> - 在 [**設定**] 應用程式中，啟用 [**開發人員 > 在鎖定畫面上顯示捐贈**]。
>
> 這些 debug 設定可讓您輕鬆地在 iOS 鎖定畫面和搜尋畫面上尋找最近建立的（而不是預測）快捷方式。

若要使用範例應用程式：

- 在 iOS 12 模擬器或[裝置](#testing-on-device)上安裝並執行湯品 Chef 範例應用程式。
- 按一下右上方的 [ **+** ] 按鈕，以建立新的訂單。
- 選取 [湯品] 類型、指定 [數量] 和 [選項]，然後按一下 [**下單**]。
- 在 [**訂購歷程記錄**] 畫面上，按一下新建立的訂單，以查看其詳細資料。
- 在 [訂單詳細資料] 畫面底部，按一下 [**新增至 Siri**]。
- 錄製與訂單相關聯的語音片語，然後按 [**完成**]。
- 將湯品 Chef 最小化，叫用 Siri，然後使用您剛才錄製的語音片語再次放置順序。
- Siri 完成訂單後，請重新開啟湯品 Chef，並注意新訂單會列在 [訂單歷程**記錄**] 畫面上。

範例應用程式會示範如何：

- [使用 NSUserActivity 快捷方式來開啟應用程式](#using-an-nsuseractivity-shortcut-to-open-an-app)。
- [使用自訂意圖快捷方式來執行工作](#using-a-custom-intent-shortcut-to-perform-a-task)。
- [提供自訂意圖的使用者介面](#providing-a-user-interface-for-a-custom-intent)。
- [建立語音快捷方式](#creating-a-voice-shortcut)。

## <a name="infoplist-and-entitlementsplist"></a>Plist 和權利. plist

在深入探討更深入的湯品 Chef 程式碼之前，請先查看其**plist**和**權利. plist**檔案。

### <a name="infoplist"></a>Info.plist

**SoupChef**專案中的**plist**檔案會將套件組合**識別碼**定義為 `com.xamarin.SoupChef`。 此套件組合識別碼將用來做為本檔稍後討論之意圖和意圖 UI 延伸模組的套件組合識別碼的前置詞。

Plist 檔案也包含下列**資訊**：

```xml
<key>NSUserActivityTypes</key>
<array>
    <string>OrderSoupIntent</string>
    <string>com.xamarin.SoupChef.viewMenu</string>
</array>
```

這個 `NSUserActivityTypes` 的索引鍵/值組表示湯品 Chef 知道如何處理 `OrderSoupIntent`，以及具有[`ActivityType`](xref:Foundation.NSUserActivity.ActivityType) "SoupChef. viewMenu" 的[`NSUserActivity`](xref:Foundation.NSUserActivity) 。

傳遞至應用程式本身的活動和自訂意圖（相對於其延伸模組）會在 `AppDelegate` 中處理（ [`ContinueUserActivity`](xref:UIKit.UIApplicationDelegate.ContinueUserActivity*)方法的[`UIApplicationDelegate`](xref:UIKit.UIApplicationDelegate) 。

### <a name="entitlementsplist"></a>Entitlements.plist

**SoupChef**專案中的**plist**檔案包含下列各項：

```xml
<key>com.apple.security.application-groups</key>
<array>
    <string>group.com.xamarin.SoupChef</string>
</array>
<key>com.apple.developer.siri</key>
<true/>
```

此設定表示應用程式會使用 "SoupChef" 應用程式群組。 **SoupChefIntents**應用程式延伸模組會使用這個相同的應用程式群組，讓這兩個專案共用[`NSUserDefaults`](xref:Foundation.NSUserDefaults)
資料。

`com.apple.developer.siri` 鍵指出應用程式會與 Siri 互動。

> [!NOTE]
> **SoupChef**專案的組建設定會將**自訂權利**設為**plist**。

## <a name="using-an-nsuseractivity-shortcut-to-open-an-app"></a>使用 NSUserActivity 快捷方式開啟應用程式

若要建立開啟應用程式以顯示特定內容的快捷方式，請建立 `NSUserActivity`，並將它附加到您想要開啟快捷方式之畫面的視圖控制器。

### <a name="setting-up-an-nsuseractivity"></a>設定 NSUserActivity

在功能表畫面上，`SoupMenuViewController` 建立 `NSUserActivity`，並將它指派給 view controller 的[`UserActivity`](xref:UIKit.UIResponder.UserActivity)屬性：

```csharp
public override void ViewDidLoad()
{
    base.ViewDidLoad();
    UserActivity = NSUserActivityHelper.ViewMenuActivity;
}
```

設定 `UserActivity` 屬性會將活動_donates_至 Siri。 從這項捐贈中，Siri 會取得有關此活動與使用者相關的時機和位置資訊，並在未來學習更好的建議。

`NSUserActivityHelper` 是公用程式類別，包含在**SoupChef**方案的**SoupKit**類別庫中。 它會建立 `NSUserActivity`，並設定與 Siri 和搜尋相關的各種屬性：

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

請特別注意下列事項：

- 將 `EligibleForPrediction` 設定為 `true` 表示 Siri 可以預測此活動，並將其呈現為快捷方式。
- [`ContentAttributeSet`](xref:Foundation.NSUserActivity.ContentAttributeSet)陣列是用來在 iOS 搜尋結果中包含 `NSUserActivity` 的標準[`CSSearchableItemAttributeSet`](xref:CoreSpotlight.CSSearchableItemAttributeSet) 。
- [`SuggestedInvocationPhrase`](xref:Foundation.NSUserActivity.SuggestedInvocationPhrase)是一種片語，在將片語指派給快捷方式時，Siri 會建議使用者做為可能的選擇。

### <a name="handling-an-nsuseractivity-shortcut"></a>處理 NSUserActivity 快捷方式

若要處理使用者所叫用的 `NSUserActivity` 快捷方式，iOS 應用程式必須覆寫 `AppDelegate` 類別的 `ContinueUserActivity` 方法，並根據傳入 `NSUserActivity` 物件的 `ActivityType` 欄位回應：

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

這個方法會呼叫 `HandleUserActivity`，以尋找功能表畫面的 segue 並叫用它：

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

若要將片語指派給 `NSUserActivity`，請開啟 [iOS**設定**] 應用程式，然後選擇 [ **Siri & 搜尋] > [我的快捷方式**]。 然後，選取快捷方式（在此案例中為「訂購午餐」）並記錄片語。

叫用 Siri 並使用此片語將會開啟湯品 Chef 至功能表畫面。

## <a name="using-a-custom-intent-shortcut-to-perform-a-task"></a>使用自訂意圖快捷方式來執行工作

### <a name="defining-a-custom-intent"></a>定義自訂意圖

若要提供快捷方式，讓使用者能夠快速完成與您應用程式相關的特定工作，請建立自訂意圖。 自訂意圖代表使用者可能想要完成的工作、與該工作相關的參數，以及工作執行所產生的潛在回應。 視自訂意圖的定義方式而定，叫用它可以開啟您的應用程式或執行背景工作。

使用 Xcode 10 來建立自訂意圖。 在[SoupChef 存放庫](https://github.com/xamarin/ios-samples/tree/master/ios12/SoupChef)中，自訂意圖定義于**OrderSoupIntentCodeGen**中，即目標 C 專案。 開啟此專案，然後選取 [**專案導覽器**] 中的**intentdefinition**檔案，以查看**OrderSoup**意圖。

請注意以下各點：

- 此意圖的**類別**為**Order**。 有各種預先定義的類別可用於自訂意圖;選取最符合您的自訂意圖將啟用之工作的專案。 由於這是湯品訂購應用程式，因此**OrderSoupIntent**會使用**訂單**。
- **確認**核取方塊會指出 Siri 是否必須在執行工作之前要求確認。 針對湯品 Chef 中的**Order 湯品**意圖，此選項會在使用者進行購買後啟用。
- Intentdefinition 檔案的**parameters**區段會定義與快捷方式相關的參數。 若要進行湯品順序，湯品 Chef 必須知道湯品的類型、其數量，以及任何相關聯的選項。
每個參數都有類型。無法以預先定義的類型表示的參數會設定為**Custom**。
- **快捷方式類型**介面會描述 Siri 可在建議您的快捷方式時使用的各種參數組合。 關聯的**標題**和**副標題**區段可讓您定義 Siri 將在向使用者呈現建議的快捷方式時使用的訊息。
- 應選取 [**支援背景執行**] 核取方塊，以在不開啟應用程式的情況下，針對進一步的使用者互動執行任何快捷方式。

### <a name="defining-custom-intent-responses"></a>定義自訂意圖回應

在**OrderSoup**意圖底下的**回應**專案，代表湯品順序所產生的潛在回應。

在**OrderSoup**意圖的回應定義中，請注意下列事項：

- 回應的**屬性**可以用來自訂訊息傳回給使用者的訊息。 **OrderSoup**意圖回應具有**湯品**和**waitTime**屬性。
- **回應範本**會指定各種成功和失敗訊息，以在意圖的工作完成之後，用來表示狀態。
- 應該針對表示成功的回應選取 [**成功**] 核取方塊。
- **OrderSoupIntent**成功回應會使用**湯品**和**waitTime**屬性來提供易記且有用的訊息，說明湯品訂單何時就緒。

### <a name="generating-code-for-the-custom-intent"></a>產生自訂意圖的程式碼

建立包含這個自訂意圖定義的 Xcode 專案，會使 Xcode 產生可用於以程式設計方式與自訂意圖和其回應互動的程式碼。

若要查看此產生的程式碼：

- 開啟**AppDelegate**。
- 將匯入新增至自訂意圖的標頭檔： `#import "OrderSoupIntent.h"`
- 在類別中的任何方法中，加入 `OrderSoupIntent`的參考。
- 以滑鼠右鍵按一下 `OrderSoupIntent`，然後選擇 [**跳至定義**]。
- 在新開啟的檔案（ **OrderSoupIntent**）上按一下滑鼠右鍵，然後選取 [**在搜尋工具中顯示**]。
- 這會開啟 [搜尋**工具**] 視窗，其中包含包含所產生之程式碼的 .h 和. m 檔案。

這個產生的程式碼包括：

- `OrderSoupIntent` –代表自訂意圖的類別。
- `OrderSoupIntentHandling` –定義將用來確認應該執行意圖之方法的通訊協定，以及實際執行它的方法。
- `OrderSoupIntentResponseCode` –定義各種回應狀態的列舉。
- `OrderSoupIntentResponse` –代表意圖執行回應的類別。

### <a name="creating-a-binding-to-the-custom-intent"></a>建立自訂意圖的系結

若要在 Xamarin iOS 應用程式中使用 Xcode 所產生的程式碼， C#請為它建立系結。

#### <a name="creating-a-static-library-and-c-binding-definitions"></a>建立靜態程式庫和C#系結定義

在[SoupChef 存放庫](https://github.com/xamarin/ios-samples/tree/master/ios12/SoupChef)中，查看**OrderSoupIntentStaticLib**資料夾，然後開啟**OrderSoupIntentStaticLib .xcodeproj** Xcode 專案。

這個**Cocoa Touch 靜態程式庫**專案包含 Xcode 所產生的**OrderSoupIntent**和**OrderSoupIntent. m**檔案。

#### <a name="configuring-the-static-library-project-build-settings"></a>設定靜態程式庫專案組建設定

在 Xcode**專案導覽器** 中，選取最上層專案**OrderSoupIntentStaticLib**，然後流覽至 **組建階段 > 編譯來源**。
請注意，這裡列出**OrderSoupIntent** （匯入**OrderSoupIntent**）。 在 [**連結二進位檔與程式庫**] 中，請注意已包含 [**意圖**] 和 [**基礎] 架構**。
這些設定都備妥之後，架構就會正確建立。

#### <a name="building-the-static-library-and-generating-c-bindings-definitions"></a>建立靜態程式庫和產生C#系結定義

若要建立靜態程式庫並為C#其產生系結定義，請遵循下列步驟：

- [安裝目標 Sharpie](https://docs.microsoft.com/xamarin/cross-platform/macios/binding/objective-sharpie/get-started?context=xamarin/mac#installing-objective-sharpie)，這是用來從 Xcode 所建立的 .h 和. m 檔案產生系結定義的工具。

- 將您的系統設定為使用 Xcode 10 命令列工具：

  > [!WARNING]
  > 更新選取的命令列工具會影響系統上所有已安裝的 Xcode 版本。 當您使用湯品 Chef 範例應用程式完成時，請務必將此設定還原為其原始設定。

  - 在 Xcode 中，選擇  **Xcode > 喜好設定 > 位置**，並將**命令列工具**設為您系統上可用的最新 Xcode 10 安裝。

- 在終端機中，`cd` 至**OrderSoupIntentStaticLib**目錄。

- 輸入 `make`，它會建立：

  - 靜態程式庫（ **libOrderSoupIntentStaticLib）。**
  - 在**bo**輸出目錄中， C#系結定義：
    - **ApiDefinitions.cs**
    - **StructsAndEnums.cs**

依賴此靜態程式庫及其相關系結定義的**OrderSoupIntentBindings**專案會自動建立這些專案。
不過，透過上述程式手動執行會確保它會如預期般建立。

如需建立靜態程式庫並使用目標 Sharpie 建立C#系結定義的詳細資訊，請參閱系結[IOS 目標-C 程式庫](https://docs.microsoft.com/xamarin/ios/platform/binding-objective-c/walkthrough?tabs=vsmac#creating-a-static-library)逐步解說。

#### <a name="creating-a-bindings-library"></a>建立系結程式庫

建立靜態程式庫和C#系結定義之後，在 Xamarin iOS 專案中使用 Xcode 產生的意圖相關程式碼所需的剩餘部分就是系結程式庫。

在[湯品 Chef 存放庫](https://github.com/xamarin/ios-samples/tree/master/ios12/SoupChef)中，開啟**SoupChef**檔案。 此外，此解決方案包含**OrderSoupIntentBinding**，也就是上述所產生之靜態程式庫的系結程式庫。

請特別注意，此專案包含：

- **ApiDefinitions.cs** –目標 Sharpie 所產生的檔案，並已新增至此專案。 這個檔案的**組建動作**會設定為**ObjcBindingApiDefinition**。
- **StructsAndEnums.cs** –目標 Sharpie 所產生的另一個檔案，並已新增至此專案。 這個檔案的**組建動作**會設定為**ObjcBindingCoreSource**。
- **LibOrderSoupIntentStaticLib**的**原生參考**，也就是先前建立的靜態程式庫。

> [!NOTE]
> **ApiDefinitions.cs**和**StructsAndEnums.cs**都包含 `[Watch (5,0), iOS (12,0)]`之類的屬性。 這些由目標 Sharpie 所產生的屬性已標記為批註，因為這不是此專案所需。

如需建立C#系結程式庫的詳細資訊，請參閱系結[IOS 目標-C 程式庫](https://docs.microsoft.com/xamarin/ios/platform/binding-objective-c/walkthrough?tabs=vsmac#create-a-xamarinios-binding-project)逐步解說。

請注意， **SoupChef**專案包含**OrderSoupIntentBinding**的參考，這表示它現在可以存取、中C#的類別、介面和其所包含的列舉：

- `OrderSoupIntent`
- `OrderSoupIntentHandling`
- `OrderSoupIntentResponse`
- `OrderSoupIntenseResponseCode`

### <a name="adding-the-intentdefinition-file-to-your-solution"></a>將 intentdefinition 檔案新增至您的方案

C#在**SoupChef**方案中， **SoupKit**專案包含應用程式及其擴充功能之間共用的程式碼。 **Intentdefinition**檔案已放在**SoupKit**的 **.lproj**目錄中，而且它具有 [**內容**] 的 [建立]**動作**。 組建程式會將此檔案複製到湯品 Chef 應用程式套件組合，讓應用程式正常運作。

### <a name="donating-an-intent"></a>捐贈意圖

為了讓 Siri 建議快捷方式，它必須先瞭解快捷方式的相關時機。

為了讓 Siri 瞭解這一點，湯品 Chef _donates_的目的是要在每次使用者放置 Siri 訂單時湯品。 根據這項捐贈–當其在捐贈時，其所包含的參數，Siri 會學習何時建議未來的快捷方式。

**SoupChef**會使用 `SoupOrderDataManager` 類別來放置捐贈。
呼叫來為使用者下湯品順序時，`PlaceOrder` 方法會接著呼叫[`DonateInteraction`](xref:Intents.INInteraction.DonateInteraction*)：

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

提取意圖之後，它會包裝在[`INInteraction`](xref:Intents.INInteraction)中。
`INInteraction` 提供[`Identifier`](xref:Intents.INInteraction.Identifier*)
，符合訂單的唯一識別碼（稍後刪除不再有效的意圖捐贈時，這會很有説明）。 然後，互動會捐贈給 Siri。

呼叫 `order.Intent` getter 會提取代表訂單的 `OrderSoupIntent`，方法是設定其 `Quantity`、`Soup`、`Options`和影像，以及當使用者記錄 Siri 要與意圖相關聯的片語時，要做為建議使用的調用片語:

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

#### <a name="removing-invalid-donations"></a>移除不正確捐贈

請務必移除不再有效的捐贈，讓 Siri 不會建立無用或令人困惑的快捷方式建議。

在湯品 Chef 中，您可以使用 [**設定] 功能表**畫面，將功能表項目標示為無法使用。 Siri 應該不會再建議快捷方式來排序無法使用的功能表項目，因此 `SoupMenuManager` 的 `RemoveDonation` 方法會刪除已無法再使用之功能表項目的捐贈。 它的運作方式如下：

- 尋找與 [立即可用] 功能表項目相關聯的訂單。
- 抓取其識別碼。
- 刪除具有相同識別碼的互動。

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

### <a name="creating-an-intents-extension"></a>建立意圖延伸模組

當 Siri 叫用意圖時所執行的程式碼會放在意圖延伸中，而此擴充功能可以當做新專案加入至與現有 Xamarin iOS 應用程式相同的方案，例如湯品 Chef。 在**SoupChef**方案中，延伸模組稱為**SoupChefIntents**。

#### <a name="soupchefintents-infoplist-and-entitlementsplist"></a>SoupChefIntents – Info. plist 和權利. plist

##### <a name="soupchefintents-infoplist"></a>SoupChefIntents – Info. plist

**SoupChefIntents**專案中的**plist**會將套件組合**識別碼**定義為 `com.xamarin.SoupChef.SoupChefIntents`。

Plist 檔案也包含下列**資訊**：

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

在上述**資訊中。 plist**：

- `IntentsRestrictedWhileLocked` 列出只有在裝置解除鎖定時才會處理的意圖。
- `IntentsSupported` 列出此延伸模組所處理的意圖。
- `NSExtensionPointIdentifier` 會指定應用程式延伸模組的類型（如需詳細資訊，請參閱[Apple 的檔](https://developer.apple.com/library/archive/documentation/General/Reference/InfoPlistKeyReference/Articles/AppExtensionKeys.html#//apple_ref/doc/uid/TP40014212-SW15)）。
- `NSExtensionPrincipalClass` 指定應該用來處理此延伸模組所支援意圖的類別。

##### <a name="soupchefintents-entitlementsplist"></a>SoupChefIntents –權利. plist

**SoupChefIntents**專案中的**Plist**具有**應用程式群組**功能。 這項功能設定為使用與**SoupChef**專案相同的應用程式群組：

```xml
<key>com.apple.security.application-groups</key>
<array>
    <string>group.com.xamarin.SoupChef</string>
</array>
```

湯品 Chef 會使用 `NSUserDefaults`來保存資料。 為了在應用程式與應用程式擴充功能之間共用資料，它們會在其**plist**檔案中參考相同的應用程式群組。

> [!NOTE]
> **SoupChefIntents**專案的組建設定會將**自訂權利**設為**plist**。

#### <a name="handling-an-ordersoupintent-background-task"></a>處理 OrderSoupIntent 背景工作

意圖延伸會根據自訂意圖來執行快捷方式的必要背景工作。

Siri 會呼叫 `IntentHandler` 類別的[`GetHandler`](xref:Intents.INExtension.GetHandler*)方法（定義于**Info. plist**作為 `NSExtensionPrincipalClass`），以取得擴充 `OrderSoupIntentHandling`的類別實例，這可用來處理 `OrderSoupIntent`：

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

`OrderSoupIntentHandler`（在共用程式碼的**SoupKit**專案中定義）會執行兩個重要的方法：

- `ConfirmOrderSoup` –確認是否應該實際執行與意圖相關聯的工作。
- `HandleOrderSoup` –藉由呼叫傳入的完成處理常式，來放置湯品訂單並回應使用者

#### <a name="handling-an-ordersoupintent-that-opens-the-app"></a>處理開啟應用程式的 OrderSoupIntent

應用程式必須正確處理不會在背景中執行的意圖。
在 `AppDelegate`的 `ContinueUserActivity` 方法中，它們的處理方式與 `NSUserActivity` 快捷方式相同：

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

## <a name="providing-a-user-interface-for-a-custom-intent"></a>提供自訂意圖的使用者介面

意圖 UI 延伸模組會提供意圖延伸的自訂使用者介面。 在**SoupChef**方案中， **SoupChefIntentsUI**是提供**SoupChefIntents**介面的意圖 UI 延伸模組。

### <a name="soupchefintentsui--infoplist-and-entitlementsplist"></a>SoupChefIntentsUI – Info. plist 和權利. plist

#### <a name="soupchefintentsui-infoplist"></a>SoupChefIntentsUI – Info. plist

**SoupChefIntentsUI**專案中的**plist**會將套件組合**識別碼**定義為 `com.xamarin.SoupChef.SoupChefIntentsui`。

Plist 檔案也包含下列**資訊**：

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

在上述**資訊中。 plist**：

- `IntentsSupported` 表示此 `OrderSoupIntent` 是由這個意圖 UI 延伸模組所處理。
- `NSExtensionPointIdentifier` 會指定應用程式延伸模組的類型（如需詳細資訊，請參閱[Apple 的檔](https://developer.apple.com/library/archive/documentation/General/Reference/InfoPlistKeyReference/Articles/AppExtensionKeys.html#//apple_ref/doc/uid/TP40014212-SW15)）。
- `NSExtensionMainStoryboard` 指定定義此延伸模組之主要介面的分鏡腳本

#### <a name="soupchefintentsui-entitlementsplist"></a>SoupChefIntentsUI –權利. plist

**SoupChefIntentsUI**專案不需要**plist**檔案。

### <a name="creating-the-user-interface"></a>建立使用者介面

由於**SoupChefIntentsUI**的**plist**會將 `NSExtensionMainStoryboard` 金鑰設定為 `MainInterface`，因此**MAININTERACE**檔案會定義意圖 UI 延伸模組的介面。

在此分鏡腳本中，有一個**IntentViewController**類型的單一視圖控制器。 它會參考兩個視圖：

- **invoiceView**，屬於類型 `InvoiceView`
- **confirmationView**，屬於類型 `ConfirmOrderView`

> [!NOTE]
> **InvoiceView**和**confirmationView**的介面會在**主要的**分鏡腳本中定義為次要 views。 Visual Studio for Mac 和 Visual Studio 2017 中的 iOS 設計工具不支援觀看或編輯次要視圖;若要這麼做，請在 Xcode 的 Interface Builder 中開啟**Main.** 分鏡腳本。

`IntentViewController` 會執行[`IINUIHostedViewControlling`](xref:IntentsUI.IINUIHostedViewControlling)
介面，用於在使用 Siri 意圖時提供自訂介面。 [`ConfigureView`](xref:IntentsUI.INUIHostedViewControlling_Extensions.ConfigureView*)
呼叫方法來自訂介面、顯示確認或發票，取決於是否已確認互動（[`INIntentHandlingStatus.Ready`](xref:Intents.INIntentHandlingStatus)），或是否已成功執行（[`INIntentHandlingStatus.Success`](xref:Intents.INIntentHandlingStatus)）：

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
> 如需 `ConfigureView` 方法的詳細資訊，請觀看 Apple 的 WWDC 2017 簡報， [SiriKit 的新功能](https://developer.apple.com/videos/play/wwdc2017/214/)。

## <a name="creating-a-voice-shortcut"></a>建立語音快捷方式

湯品 Chef 提供一個介面，可將語音快捷方式指派給每個訂單，讓您可以使用 Siri 訂購湯品。 事實上，用於記錄和指派語音快捷方式的介面是由 iOS 所提供，而且只需要很少的自訂程式碼。

在 `OrderDetailViewController`中，當使用者按下資料表的 [**新增至 Siri** ] 資料列時， [`RowSelected`](xref:UIKit.UITableViewSource.RowSelected*)方法會顯示一個畫面來新增或編輯語音快捷方式：

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

根據目前顯示的順序是否有現有的語音快捷方式存在，`RowSelected` 會呈現[`INUIEditVoiceShortcutViewController`](xref:IntentsUI.INUIEditVoiceShortcutViewController)或[`INUIAddVoiceShortcutViewController`](xref:IntentsUI.INUIAddVoiceShortcutViewController)類型的視圖控制器。
在每個案例中，`OrderDetailViewController` 都會將本身設定為 view controller 的 `Delegate`，這也是它的執行原因[`IINUIAddVoiceShortcutViewControllerDelegate`](xref:IntentsUI.IINUIAddVoiceShortcutViewControllerDelegate)
和[`IINUIEditVoiceShortcutViewControllerDelegate`](xref:IntentsUI.IINUIEditVoiceShortcutViewControllerDelegate)。

## <a name="testing-on-device"></a>在裝置上進行測試

若要在裝置上執行湯品 Chef，請遵循下列指示。 另請參閱[關於自動](#automatic-provisioning)布建的注意事項。

### <a name="app-group-app-ids-provisioning-profiles"></a>應用程式群組、應用程式識別碼、布建設定檔

在[Apple 開發人員入口網站](https://developer.apple.com/)的 [**憑證、識別碼 & 設定檔**] 區段中，執行下列動作：

- 建立應用程式群組，以在湯品 Chef 應用程式及其擴充功能之間共用資料。 例如：**取代 com.lookout.enterprise.yourcompanyname. SoupChef**

- 建立三個應用程式識別碼：一個用於應用程式本身、一個用於意圖延伸，另一個用於意圖 UI 延伸模組。 例如:

  - 應用程式： **com. 取代 com.lookout.enterprise.yourcompanyname. SoupChef**
    - 針對此應用程式識別碼，指派 SiriKit 和**應用程式群組**功能。

  - 意圖延伸模組：**取代 com.lookout.enterprise.yourcompanyname. SoupChef. 意圖**
    - 針對此應用程式識別碼，指派 [**應用程式群組**] 功能。

  - 意圖 UI 延伸模組：**取代 com.lookout.enterprise.yourcompanyname. SoupChef. Intentsui**
    - 此應用程式識別碼不需要任何特殊功能。

- 建立上述應用程式識別碼之後，請編輯指派給應用程式的**應用程式群組**功能和意圖延伸模組，並指定先前建立的特定應用程式群組。

- 建立三個新的開發布建設定檔，每個新的應用程式識別碼各一個。

- 請下載這些布建設定檔，然後按兩下每一項來安裝它。 如果 Visual Studio for Mac 或 Visual Studio 2017 已在執行，請將它重新開機，以確定它會註冊新的布建設定檔。

### <a name="editing-infoplist-entitlementsplist-and-source-code"></a>編輯 Info. plist、plist 和 source 程式碼

在 Visual Studio for Mac 或 Visual Studio 2017 中，執行下列動作：

- 更新解決方案中的各種**plist**檔案。 將應用程式、意圖延伸和意圖 UI 延伸模組套件組合**識別碼**設定為上述定義的應用程式識別碼：

  - 應用程式： **com. 取代 com.lookout.enterprise.yourcompanyname. SoupChef**
  - 意圖延伸模組：**取代 com.lookout.enterprise.yourcompanyname. SoupChef. 意圖**
  - 意圖 UI 延伸模組：**取代 com.lookout.enterprise.yourcompanyname. SoupChef. Intentsui**

- 更新**SoupChef**專案的**plist**檔案：
  - 針對 [**應用程式群組**] 功能，將群組設定為上方建立的新應用程式群組（在上述範例中，它是**取代 com.lookout.enterprise.yourcompanyname. SoupChef**）。
  - 請確定已啟用**SiriKit** 。

- 更新**SoupChefIntents**專案的**plist**檔案：
  - 針對 [**應用程式群組**] 功能，將群組設定為上方建立的新應用程式群組（在上述範例中，它是**取代 com.lookout.enterprise.yourcompanyname. SoupChef**）。

- 最後，開啟**NSUserDefaultsHelper.cs**。 將 `AppGroup` 變數設定為新應用程式群組的值（例如，將其設定為 `group.com.yourcompanyname.SoupChef`）。

### <a name="configuring-the-build-settings"></a>正在進行組建設定

在 Visual Studio for Mac 或 Visual Studio 2017：

- 開啟**SoupChef**專案的 [選項]/[屬性]。 在 [ **iOS**套件組合簽署] 索引標籤上，將 [**簽署身分識別**] 設定為 [自動]，並將**設定檔**布建到您在上面建立的新應用

- 開啟**SoupChefIntents**專案的 [選項]/[屬性]。 在 [ **iOS**套件組合簽署] 索引標籤上，將 [**簽署身分識別**] 設為 [自動]，並將**設定檔**設定為您在上方建立的新意圖延伸模組專屬

- 開啟**SoupChefIntentsUI**專案的 [選項]/[屬性]。 在 [ **iOS**套件組合簽署] 索引標籤上，將 [**簽署身分識別**] 設定為 [自動和布建**設定檔**]，以供您在上方建立的新意圖 UI 延伸模組

進行這些變更之後，應用程式就會在 iOS 裝置上執行。

### <a name="automatic-provisioning"></a>自動布建

請注意，您可以使用[自動](https://docs.microsoft.com/xamarin/ios/get-started/installation/device-provisioning/automatic-provisioning)布建，直接在 IDE 中完成許多這些布建工作。
不過，自動布建不會設定應用程式群組。 您必須以您想要使用的應用程式組名手動設定**plist**檔案，請流覽 Apple Developer 入口網站來建立應用程式群組、將該應用程式群組指派給自動布建所建立的每個應用程式識別碼、重新產生布建設定檔（應用程式、意圖延伸、意圖 UI 延伸模組），用以包含新建立的應用程式群組，並下載並安裝它們。

## <a name="related-links"></a>相關連結

- [湯品 Chef （Xamarin）](https://docs.microsoft.com/samples/xamarin/ios-samples/ios12-soupchef)
- [湯品 Chef （Swift）](https://developer.apple.com/documentation/sirikit/accelerating_app_interactions_with_shortcuts?language=objc)
- [SiriKit （Apple）](https://developer.apple.com/sirikit/)
- [Siri 快捷方式簡介– WWDC 2018](https://developer.apple.com/videos/play/wwdc2018/211/)
- [使用 Siri 快速鍵建立語音– WWDC 2018](https://developer.apple.com/videos/play/wwdc2018/214/)
- [Siri Watch 表面上的 Siri 快捷方式– WWDC 2018](https://developer.apple.com/videos/play/wwdc2018/217/)
- [SiriKit 的新功能– WWDC 2017](https://developer.apple.com/videos/play/wwdc2017/214/)
- [建立意圖應用程式延伸模組（Apple）](https://developer.apple.com/documentation/sirikit/creating_an_intents_app_extension?language=objc)
