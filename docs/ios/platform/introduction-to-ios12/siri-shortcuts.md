---
title: 在 Xamarin 中 Siri 快捷方式
description: 本檔說明如何在 iOS 12 中使用 Siri 快捷方式。 它會討論 NSUserActivities、自訂意圖、指派語音快速鍵、相關的快捷方式等等。
ms.prod: xamarin
ms.assetid: 86424F79-3A7D-436E-927D-9A3267DA333B
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 08/08/2018
ms.openlocfilehash: fbd0f5025ab195ba7a81bf83870330662e3a6a01
ms.sourcegitcommit: 4bbf54d2bc1df96af69814e2e5dae47be12e0474
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2021
ms.locfileid: "102602706"
---
# <a name="siri-shortcuts-in-xamarinios"></a>在 Xamarin 中 Siri 快捷方式

在 [iOS 10](~/ios/platform/sirikit/index.md)中，Apple 推出了 SiriKit，讓您能夠建立與 Siri 互動的訊息、VoIP 通話、付款、workouts、下預約和相片搜尋應用程式。

在 [iOS 11](~/ios/platform/introduction-to-ios11/sirikit.md)中，SiriKit 獲得更多應用程式類型的支援，以及更大的 UI 自訂彈性。

iOS 12 新增了 Siri 快捷方式，可讓所有類型的應用程式將其功能公開至 Siri。 Siri 會在特定應用程式的工作與使用者最相關的情況下學習，並使用此知識透過 _快捷方式_ 來建議可能的動作。 使用「語音命令」來開啟快捷方式或叫用它，將會開啟應用程式或執行背景工作。

您可以使用快捷方式來加速使用者完成一般工作的能力-在許多情況下，甚至不需要開啟有問題的應用程式。

## <a name="sample-app-soup-chef"></a>範例應用程式： < A0 Chef

若要進一步瞭解 Siri 快捷方式，請參閱 [ A0 Chef](/samples/xamarin/ios-samples/ios12-soupchef) 範例應用程式。 < A0 Chef 可讓使用者從虛構 < a0 餐廳下訂購訂單、查看其訂單歷程記錄，並定義要在訂購 < a0 時使用的片語，方法是與 Siri 互動。

> [!TIP]
> 在 iOS 12 模擬器或裝置上測試 < A0 Chef 之前，請啟用下列兩項設定，這些設定在偵錯工具快捷方式時很有用：
>
> - 在 [ **設定** ] 應用程式中，啟用 [ **開發人員 > 顯示最近的快捷方式**。
> - 在 [ **設定** ] 應用程式中，讓 **開發人員 > 在鎖定畫面上顯示捐贈**。
>
> 這些偵錯工具設定可讓您輕鬆地找到最近建立的 (，而不是 iOS 鎖定畫面和搜尋畫面上的預測) 快捷方式。

若要使用範例應用程式：

- Install and run the Soup Chef sample app on an iOS 12 simulator or [device](#testing-on-device).
- 按一下 **+** 右上角的按鈕，以建立新的訂單。
- Select a type of soup, specify a quantity and options, and tap **Place Order**.
- 在 [ **訂單歷程記錄** ] 畫面上，按一下新建立的訂單來查看其詳細資料。
- 在 [訂單詳細資料] 畫面的底部，按一下 [ **加入至 Siri**。
- 記錄要與訂單相關聯的語音片語，然後點擊 [ **完成**]。
- 將 < A0 Chef 降至最低、叫用 Siri，然後使用您記錄的聲音片語再次放置順序。
- After Siri completes the order, reopen Soup Chef and notice that the new order is listed on the **Order History** screen.

範例應用程式示範如何：

- [使用 NSUserActivity 快捷方式來開啟應用程式](#using-an-nsuseractivity-shortcut-to-open-an-app)。
- [使用自訂意圖快速鍵來執行工作](#using-a-custom-intent-shortcut-to-perform-a-task)。
- [提供自訂意圖的使用者介面](#providing-a-user-interface-for-a-custom-intent)。
- [建立語音快捷方式](#creating-a-voice-shortcut)。

## <a name="infoplist-and-entitlementsplist"></a>Plist 和權利. plist

Before digging more deeply in to the Soup Chef code, take a look at its **Info.plist** and **Entitlements.plist** files.

### <a name="infoplist"></a>Info.plist

**SoupChef** 專案中的 **plist** 檔案會將套件組合 **識別碼** 定義為 `com.xamarin.SoupChef` 。 此套件組合識別碼將作為本檔稍後討論之意圖和意圖 UI 延伸套件組合識別碼的前置詞。

**Plist** 檔案也包含下列專案：

```xml
<key>NSUserActivityTypes</key>
<array>
    <string>OrderSoupIntent</string>
    <string>com.xamarin.SoupChef.viewMenu</string>
</array>
```

This `NSUserActivityTypes` key/value pair indicates that Soup Chef knows how to handle an `OrderSoupIntent`, and an [`NSUserActivity`](xref:Foundation.NSUserActivity) having an [`ActivityType`](xref:Foundation.NSUserActivity.ActivityType) of "com.xamarin.SoupChef.viewMenu".

傳遞給應用程式本身的活動和自訂意圖（相對於其擴充功能）會在 `AppDelegate` 方法的 (中處理 [`UIApplicationDelegate`](xref:UIKit.UIApplicationDelegate) [`ContinueUserActivity`](xref:UIKit.UIApplicationDelegate.ContinueUserActivity*) 。

### <a name="entitlementsplist"></a>Entitlements.plist

**SoupChef** 專案中的 **plist** 檔案包含下列專案：

```xml
<key>com.apple.security.application-groups</key>
<array>
    <string>group.com.xamarin.SoupChef</string>
</array>
<key>com.apple.developer.siri</key>
<true/>
```

此設定表示應用程式會使用 "SoupChef" 應用程式群組。 **SoupChefIntents** 應用程式延伸模組會使用這個相同的應用程式群組，這可讓兩個專案共用 [`NSUserDefaults`](xref:Foundation.NSUserDefaults)
定型

此索引 `com.apple.developer.siri` 鍵表示應用程式會與 Siri 互動。

> [!NOTE]
> **SoupChef** 專案的組建設定會設定 Plist 的 **自訂權利** **。**

## <a name="using-an-nsuseractivity-shortcut-to-open-an-app"></a>使用 NSUserActivity 快捷方式開啟應用程式

若要建立開啟應用程式以顯示特定內容的快捷方式，請建立， `NSUserActivity` 並將它附加至您想要開啟快捷方式之畫面的視圖控制器。

### <a name="setting-up-an-nsuseractivity"></a>設定 NSUserActivity

在功能表畫面上 `SoupMenuViewController` 建立， `NSUserActivity` 並將它指派給 view 控制器的 [`UserActivity`](xref:UIKit.UIResponder.UserActivity) 屬性：

```csharp
public override void ViewDidLoad()
{
    base.ViewDidLoad();
    UserActivity = NSUserActivityHelper.ViewMenuActivity;
}
```

將 `UserActivity` _donates_ 活動的屬性設定為 Siri。 在這項捐贈中，Siri 會取得此活動與使用者的相關時間和位置的相關資訊，並學習未來更清楚的建議。

`NSUserActivityHelper` 是 **SoupChef** 方案中包含在 **SoupKit** 類別庫中的公用程式類別。 它會建立 `NSUserActivity` ，並設定與 Siri 和搜尋相關的各種屬性：

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

請特別注意下列功能：

- 將設定 `EligibleForPrediction` 為， `true` 表示 Siri 可以預測此活動並將其呈現為快捷方式。
- [`ContentAttributeSet`](xref:Foundation.NSUserActivity.ContentAttributeSet)陣列是一種標準 [`CSSearchableItemAttributeSet`](xref:CoreSpotlight.CSSearchableItemAttributeSet) ，用來 `NSUserActivity` 在 iOS 搜尋結果中包含。
- [`SuggestedInvocationPhrase`](xref:Foundation.NSUserActivity.SuggestedInvocationPhrase) 這是一種片語，Siri 會在將片語指派給快捷方式時，建議使用者做為可能的選擇。

### <a name="handling-an-nsuseractivity-shortcut"></a>處理 NSUserActivity 快捷方式

若要處理 `NSUserActivity` 使用者叫用的快捷方式，iOS 應用程式必須覆寫 `ContinueUserActivity` 類別的方法 `AppDelegate` ，並根據 `ActivityType` 傳入物件的欄位回應 `NSUserActivity` ：

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

這個方法會呼叫 `HandleUserActivity` ，以尋找功能表畫面的 segue 並叫用它：

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

若要將片語指派給 `NSUserActivity` ，請開啟 IOS **設定** 應用程式，然後選擇 [ **Siri & 搜尋] > 我的快捷方式**。 然後選取快捷方式 (在此案例中為 [訂購午餐] ) 並記錄片語。

叫用 Siri 並使用這個片語將會開啟 < A0 Chef 至功能表畫面。

## <a name="using-a-custom-intent-shortcut-to-perform-a-task"></a>使用自訂意圖快速鍵來執行工作

### <a name="defining-a-custom-intent"></a>定義自訂意圖

若要提供可讓使用者快速完成與您應用程式相關之特定工作的快捷方式，請建立自訂意圖。 自訂意圖代表使用者可能要完成的工作、與該工作相關的參數，以及工作執行所產生的潛在回應。 根據自訂意圖的定義方式，叫用它可以開啟您的應用程式或執行背景工作。

使用 Xcode 10 建立自訂意圖。 在 [SoupChef 存放庫](https://github.com/xamarin/ios-samples/tree/master/ios12/SoupChef)中，自訂意圖是在 **OrderSoupIntentCodeGen** 中定義，也就是目標 C 專案。 開啟此專案，然後選取 [**專案導覽器**] 中的 **intentdefinition** 檔案，以查看 **OrderSoup** 意圖。

請注意下列功能：

- 此意圖具有 **Order** 的 **類別**。 有各種預先定義的類別可用於自訂意圖;選取最符合您的自訂意圖將啟用之工作的最接近的一個。 Since this solution is a soup ordering app, **OrderSoupIntent** uses **Order**.
- **確認** 核取方塊會指出 Siri 是否必須在執行工作之前要求確認。 For the **Order Soup** intent in Soup Chef, this option is enabled since the user is making a purchase.
- **Intentdefinition** 檔案的 **parameters** 區段會定義與快捷方式相關的參數。 若要進行 < a0 順序，< A0 Chef 必須知道 < a0 的類型、其數量，以及任何相關聯的選項。
每個參數都有一個類型;無法以預先定義的類型表示的參數會設定為 **Custom**。
- **快速鍵類型** 介面描述 Siri 在建議您的快捷方式時可使用的各種參數組合。 相關聯的標題 **和子標題區段** 可讓您定義在向使用者呈現建議的快捷方式時，Siri 將使用的訊息。
- 針對可執行檔任何快捷方式，都應該選取 [ **支援背景執行** ] 核取方塊，以供進一步的使用者互動。

### <a name="defining-custom-intent-responses"></a>定義自訂意圖回應

The **Response** item nested below the **OrderSoup** intent represents the potential responses that result from a soup order.

在 **OrderSoup** 意圖的回應定義中，請注意下列功能：

- 您可以使用回應的 **屬性** 來自訂訊息傳回給使用者的訊息。 **OrderSoup** 意圖回應有 **a0** 和 **等候時間** 屬性。
- **回應範本** 會指定各種成功和失敗訊息，以用來指出意圖工作完成後的狀態。
- 應針對表示成功的回應選取 [ **成功** ] 核取方塊。
- The **OrderSoupIntent** success response uses the **soup** and **waitTime** properties to provide a friendly and useful message describing when the soup order will be ready.

### <a name="generating-code-for-the-custom-intent"></a>產生自訂意圖的程式碼

建立包含這個自訂意圖定義的 Xcode 專案，會導致 Xcode 產生程式碼，以透過程式設計方式與自訂意圖及其回應互動。

若要查看這個產生的程式碼：

- 開啟 **AppDelegate。**
- 將匯入新增至自訂意圖的標頭檔： `#import "OrderSoupIntent.h"`
- 在類別的任何方法中，加入的參考 `OrderSoupIntent` 。
- 以滑鼠右鍵按一下 `OrderSoupIntent` 並選擇 [ **跳至定義**]。
- 以滑鼠右鍵按一下新開啟的檔案 **OrderSoupIntent**，然後選取 [ **在 Finder 中顯示**]。
- 此動作會開啟一個搜尋 **工具** 視窗，其中包含包含所產生之程式碼的 **.h** 和 **. m** 檔案。

這個產生的程式碼包括：

- `OrderSoupIntent` –表示自訂意圖的類別。
- `OrderSoupIntentHandling` –定義方法的通訊協定，這些方法將用來確認應執行意圖，以及實際執行該意圖的方法。
- `OrderSoupIntentResponseCode` –定義各種回應狀態的列舉。
- `OrderSoupIntentResponse` –表示意圖執行回應的類別。

### <a name="creating-a-binding-to-the-custom-intent"></a>建立自訂意圖的系結

若要在 Xamarin iOS 應用程式中使用 Xcode 所產生的程式碼，請為它建立 c # 系結。

#### <a name="creating-a-static-library-and-c-binding-definitions"></a>建立靜態程式庫和 c # 綁定定義

在 [SoupChef 存放庫](https://github.com/xamarin/ios-samples/tree/master/ios12/SoupChef)中，查看 **OrderSoupIntentStaticLib** 資料夾，然後開啟 **OrderSoupIntentStaticLib. .xcodeproj** Xcode 專案。

這個 **Cocoa Touch 靜態程式庫** 專案包含 Xcode 所產生的 **OrderSoupIntent .h** 和 **OrderSoupIntent. m** 檔案。

#### <a name="configuring-the-static-library-project-build-settings"></a>設定靜態程式庫專案組建設定

在 Xcode **專案導覽器** 中，選取最上層專案、 **OrderSoupIntentStaticLib**，然後流覽至 [ **組建階段] > 編譯來源**。
請注意，匯入) **OrderSoupIntent** 的 **OrderSoupIntent** (會列在此處。 在 [ **連結二進位檔與程式庫**] 中，請注意包含的是 **架構** 和 **基礎架構** 。
使用這些設定之後，架構將會正確建立。

#### <a name="building-the-static-library-and-generating-c-bindings-definitions"></a>建立靜態程式庫並產生 c # 系結定義

若要建立靜態程式庫並為其產生 c # 系結定義，請遵循下列步驟：

- [安裝目標 Sharpie](../../../cross-platform/macios/binding/objective-sharpie/get-started.md?context=xamarin%252fmac#installing-objective-sharpie)，這是用來從 Xcode 所建立之 **.h 和 .h** 檔案產生系結定義的 **工具。**

- 將您的系統設定為使用 Xcode 10 **命令列工具**：

  > [!WARNING]
  > 更新選取的 **命令列工具** 會影響您系統上所有已安裝的 Xcode 版本。 當您使用 < A0 Chef 範例應用程式完成時，請務必將此設定還原為其原始設定。

  - 在 [Xcode] 中，選擇 [ **Xcode > 喜好設定 > 位置** ]，並將 **命令列工具** 設定為系統上最新可用的 Xcode 10 安裝。

- 在終端機中， `cd` 指向 **OrderSoupIntentStaticLib** 目錄。

- 類型 `make` ，其組建：

  - 靜態程式庫 **libOrderSoupIntentStaticLib。**
  - 在 [ **bo** 輸出目錄] 中，c # 系結定義：
    - **ApiDefinitions.cs**
    - **StructsAndEnums.cs**

相依于此靜態程式庫及其關聯系結定義的 **OrderSoupIntentBindings** 專案會自動建立這些專案。
但是，透過上述進程手動執行，將可確保其如預期般建立。

如需有關建立靜態程式庫及使用目標 Sharpie 來建立 c # 系結定義的詳細資訊，請參閱系結 [IOS 目標-C 程式庫](../binding-objective-c/walkthrough.md?tabs=vsmac#creating-a-static-library) 逐步解說。

#### <a name="creating-a-bindings-library"></a>建立系結程式庫

建立靜態程式庫和 c # 系結定義之後，在 Xamarin 中取用 Xcode 產生之意圖相關程式碼所需的剩餘部分就是系結程式庫。

在 [ A0 Chef 存放庫](https://github.com/xamarin/ios-samples/tree/master/ios12/SoupChef)中，開啟 **SoupChef** 檔案。 除此之外，此解決方案還包含 **OrderSoupIntentBinding**，這是稍早所產生之靜態程式庫的系結程式庫。

請特別注意，此專案包含：

- **ApiDefinitions.cs** –目標 Sharpie 先前產生的檔案，並新增至此專案。 這個檔案的 **組建動作** 會設定為 **ObjcBindingApiDefinition**。
- **StructsAndEnums.cs** –由目標 Sharpie 先前產生的另一個檔案，並新增至此專案。 這個檔案的 **組建動作** 會設定為 **ObjcBindingCoreSource**。
- **LibOrderSoupIntentStaticLib** 的 **原生參考**，這是稍早建立的靜態程式庫。 更新原生參考屬性，並指定下列值：

    1. Framework = `Foundation Intents`
    1. 智慧連結 = `On`
    1. 強制載入 = `On`
    1. Kind = `Static`

> [!NOTE]
> **ApiDefinitions.cs** 和 **StructsAndEnums.cs** 都包含的屬性，例如 `[Watch (5,0), iOS (12,0)]` 。 這些由目標 Sharpie 產生的屬性已被批註化，因為這不是此專案的必要專案。

如需有關建立 c # 系結程式庫的詳細資訊，請參閱「 [IOS 目標-C 程式庫](../binding-objective-c/walkthrough.md?tabs=vsmac#create-a-xamarinios-binding-project) 的系結」逐步解說。

請注意， **SoupChef** 專案包含 **OrderSoupIntentBinding** 的參考，這表示它現在可以存取它所包含的類別、介面和列舉：

- `OrderSoupIntent`
- `OrderSoupIntentHandling`
- `OrderSoupIntentResponse`
- `OrderSoupIntenseResponseCode`

### <a name="creating-a-swift-framework"></a>建立 Swift 架構

使用原生專案的語言時，Xcode 預設會產生意圖定義機器碼。 如果您在 Swift 專案中定義 **intentdefinition** 檔案，Xcode 會為您產生一個具有所有必要類別的 swift 檔案，您可以使用這些類別來建立 Swift 架構。

> [!TIP]
> 您可以在 Xcode 組建設定中，為產生的意圖程式碼選取所需的語言。 移至 **意圖目標 > 組建設定 > 意圖定義編譯器-產生程式碼** ，然後選取 [Swift] 或 [目標-C]。 您也可以讓它自動符合您的目的語言。

建立 Swift 架構的程式類似于稍早所述的程式：

1. 建立新的 Swift framework 專案。
1. 使用意圖程式碼將自動產生的 Swift 檔案複製到這個專案中，您可以找到此檔案[，如下所述。](#generating-code-for-the-custom-intent)
1. 啟用 [ [目標-c] 橋接標頭](../binding-swift/walkthrough.md#build-a-native-library)，因此會自動以目標-c sharpie 標頭檔所需的架構來產生架構。

建立架構之後，請遵循稍早所述的 [相同步驟](#creating-a-bindings-library) 來建立 Xamarin 系結。 您可以在 [這裡](../binding-swift/walkthrough.md)深入瞭解如何建立 Swift 架構的系結。

### <a name="adding-the-intent-definition-file-to-your-solution"></a>將意圖定義檔新增至您的方案

在 c # **SoupChef** 方案中， **SoupKit** 專案包含應用程式及其延伸模組之間共用的程式碼。 **Intentdefinition** 檔案已放在 **SoupKit** 的 **>.lproj** 目錄中，而且它具有 **內容** 的 **組建動作**。 組建程式會將此檔案複製到 < A0 Chef 應用程式套件組合，應用程式必須在此套件組合中正確運作。

### <a name="donating-an-intent"></a>捐贈意圖

為了讓 Siri 建議快捷方式，它必須先瞭解快捷方式的相關時機。

To give Siri this understanding, Soup Chef _donates_ an intent to Siri each time the user places a soup order. 根據這項捐贈，當它被捐贈時，它所包含的參數會 Siri 學習何時建議未來的快捷方式。

**SoupChef** 使用 `SoupOrderDataManager` 類別來放置捐贈。
When called to place a soup order for a user, the `PlaceOrder` method in turn calls [`DonateInteraction`](xref:Intents.INInteraction.DonateInteraction*):

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

提取意圖之後，它會包裝在中 [`INInteraction`](xref:Intents.INInteraction) 。
`INInteraction`會提供[`Identifier`](xref:Intents.INInteraction.Identifier*)
符合訂單的唯一識別碼 (它會在稍後刪除不再有效) 的意圖捐贈時很有説明。 然後，互動就會捐贈給 Siri。

對 getter 的呼叫 `order.Intent` `OrderSoupIntent` 會藉由設定其、、和影像來提取表示順序的， `Quantity` `Soup` `Options` 以及當使用者為 Siri 記錄要與意圖相關聯的片語時，用來做為建議的調用片語：

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

請務必移除不再有效的捐贈，讓 Siri 不會產生無用或令人困惑的快捷方式建議。

In Soup Chef, the **Configure Menu** screen can be used to mark a menu item as unavailable. Siri 應該不會再建議用來排序 [無法使用] 功能表項目的快捷方式，因此的 `RemoveDonation` 方法 `SoupMenuManager` 會刪除無法再使用之功能表項目的捐贈。 應用程式會透過下列方式來執行此功能：

- 尋找與 [現在無法使用] 功能表項目相關聯的訂單。
- 抓取其識別碼。
- 正在刪除具有相同識別碼的互動。

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

#### <a name="validating-successful-donations"></a>驗證成功的捐贈

解決方案包含多個專案和特定設定。 在某些情況下，應用程式可能因為設定不完整而損毀，但在其他情況下，它可能會以無訊息模式捐贈互動。 請務必驗證成功的捐贈，以及 iOS 開發人員設定的協助。 流覽至 [ **設定 > 開發人員** ]，並啟用下列開發人員選項以查看最近的捐贈和快捷方式：

- 顯示最新的快速鍵
- 在鎖定畫面上顯示捐贈

啟用之後，每次成功的捐贈都會出現在鎖定畫面上，以及 Siri 建議選項下方。 如果您在執行您的應用程式之後看不到那裡的捐贈，請參閱下列疑難排解案例：

1. 應用程式無法建立，發生 `OrderSoupIntent` 下列錯誤：

    > 無法建立類型 ' NativeLibrary. OrderSoupIntent ' 的原生實例：尚未載入原生類別。

    此錯誤表示 Xamarin 無法透過 Xamarin 系結載入原生類別。 若要修正此問題，請確認原生程式庫包含系結專案所參考的必要程式碼，並已設定適當的旗標（如下 [所述），將](#creating-a-bindings-library)旗標設 `Force Load` 為 `On` 。

1. 應用程式無法初始化意圖類別的已載入原生實例，發生下列錯誤：

    > 無法初始化類型 ' NativeLibrary. OrderSoupIntent ' 的實例：原生 ' init ' 方法傳回了 nil。

    問題與遺漏意圖定義檔有關。 Xamarin 應用程式應該包含具有類型的原始意圖定義檔 `Content` ，如下所述[](#adding-the-intent-definition-file-to-your-solution)。

1. 應用程式會建立意圖，並在沒有損毀的情況下呼叫捐贈方法，但主控台輸出會顯示不明意圖類型的警告，而不會進行任何捐贈：

    > 無法與沒有有效快捷方式類型的 OrderSoupIntent 捐贈互動

    若要修正此問題，必須 [在 plist 中正確定義](#infoplist)意圖，必須透過專案設定為目前的組建設定啟用和選取 [Siri 權利](#entitlementsplist) 。

    應用程式的 **plist 資訊** ：

    ```plist
    <key>NSUserActivityTypes</key>
    <array>
        <string>ScheduleMeetingIntent</string>
    </array>
    ```

    具有 Siri 功能的應用程式 **plist** ：

    ```plist
    <key>com.apple.developer.siri</key>
    <true/>
    ```

    應選取目標群組建設定的自訂權利。 移至 [ **專案設定] > 建立 > iOS** 套件組合簽署]，並將 **自訂權利** 設定為包含必要權利的 **plist** 檔案。

### <a name="creating-an-intents-extension"></a>建立意圖延伸模組

Siri 叫用意圖時所執行的程式碼會放置在意圖延伸中，此延伸模組可新增為新的專案，以作為現有 Xamarin iOS 應用程式（例如 < A0 Chef）的相同方案。 在 **SoupChef** 方案中，此延伸模組稱為 **SoupChefIntents**。

#### <a name="soupchefintents--infoplist-and-entitlementsplist"></a>SoupChefIntents – Info. plist 和 plist

##### <a name="soupchefintents--infoplist"></a>SoupChefIntents – Info. plist

**SoupChefIntents** 專案中的 **plist** 會將套件組合 **識別碼** 定義為 `com.xamarin.SoupChef.SoupChefIntents` 。

**Plist** 檔案也包含下列專案：

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

在上述 **資訊中 plist**：

- `IntentsRestrictedWhileLocked` 列出裝置解除鎖定時要處理的意圖。
- `IntentsSupported` 列出此延伸模組所處理的意圖。
- `NSExtensionPointIdentifier` 指定應用程式延伸模組的類型。 如需詳細資訊，請參閱 [Apple 的檔](https://developer.apple.com/library/archive/documentation/General/Reference/InfoPlistKeyReference/Articles/AppExtensionKeys.html#//apple_ref/doc/uid/TP40014212-SW15)。
- `NSExtensionPrincipalClass` 指定應該用來處理此延伸模組所支援之意圖的類別。

##### <a name="soupchefintents--entitlementsplist"></a>SoupChefIntents –權利. plist

**SoupChefIntents** 專案中的 **Plist** 具有 **應用程式群組** 功能。 這項功能已設定為使用與 **SoupChef** 專案相同的應用程式群組：

```xml
<key>com.apple.security.application-groups</key>
<array>
    <string>group.com.xamarin.SoupChef</string>
</array>
```

Soup Chef persists data with `NSUserDefaults`. 為了在應用程式與應用程式延伸模組之間共用資料，他們會在其 **plist** 檔案中參考相同的應用程式群組。

> [!NOTE]
> **SoupChefIntents** 專案的組建設定會設定 Plist 的 **自訂權利** **。**

#### <a name="handling-an-ordersoupintent-background-task"></a>處理 OrderSoupIntent 背景工作

意圖延伸會根據自訂意圖來執行快捷方式所需的背景工作。

Siri 會呼叫 [`GetHandler`](xref:Intents.INExtension.GetHandler*) 類別的方法 `IntentHandler` (在 **Info. plist** 中定義為 `NSExtensionPrincipalClass`) ，以取得可延伸之類別的實例 `OrderSoupIntentHandling` ，而這個實例可以用來處理 `OrderSoupIntent` ：

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

`OrderSoupIntentHandler`（定義于共用程式碼的 **SoupKit** 專案中）會執行兩個重要的方法：

- `ConfirmOrderSoup` –確認是否應實際執行與意圖相關聯的工作
- `HandleOrderSoup` – Places the soup order and responds to the user by calling the passed-in completion handler

#### <a name="handling-an-ordersoupintent-that-opens-the-app"></a>處理開啟應用程式的 OrderSoupIntent

應用程式必須適當地處理不在背景中執行的意圖。
在的方法中，這些意圖的處理方式與 `NSUserActivity` 快捷方式相同 `ContinueUserActivity` `AppDelegate` ：

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

意圖 UI 延伸模組提供意圖延伸的自訂使用者介面。 在 **SoupChef** 方案中， **SoupChefIntentsUI** 是一種意圖 UI 延伸模組，可提供 **SoupChefIntents** 的介面。

### <a name="soupchefintentsui--infoplist-and-entitlementsplist"></a>SoupChefIntentsUI – Info. plist 和 plist

#### <a name="soupchefintentsui--infoplist"></a>SoupChefIntentsUI – Info. plist

**SoupChefIntentsUI** 專案中的 **plist** 會將套件組合 **識別碼** 定義為 `com.xamarin.SoupChef.SoupChefIntentsui` 。

**Plist** 檔案也包含下列專案：

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

在上述 **資訊中 plist**：

- `IntentsSupported` 表示由此 `OrderSoupIntent` 意圖 UI 延伸模組處理。
- `NSExtensionPointIdentifier` 指定應用程式延伸模組的類型。 如需詳細資訊，請參閱 [Apple 的檔](https://developer.apple.com/library/archive/documentation/General/Reference/InfoPlistKeyReference/Articles/AppExtensionKeys.html#//apple_ref/doc/uid/TP40014212-SW15)。
- `NSExtensionMainStoryboard` 指定腳本來定義此延伸模組的主要介面

#### <a name="soupchefintentsui--entitlementsplist"></a>SoupChefIntentsUI –權利. plist

**SoupChefIntentsUI** 專案不需要 **plist** 檔案。

### <a name="creating-the-user-interface"></a>建立使用者介面

由於 **SoupChefIntentsUI** 的 **plist** 會將 `NSExtensionMainStoryboard` 金鑰設為，因此 `MainInterface` **MainInterace** 會定義意圖 UI 延伸模組的介面。

在這個腳本中，有一個 **IntentViewController** 類型的單一視圖控制器。 它會參考兩個視圖：

- **invoiceView**，類型為 `InvoiceView`
- **confirmationView**，類型為 `ConfirmOrderView`

> [!NOTE]
> **InvoiceView** 和 **confirmationView** 的介面是在 **主要** 的分鏡腳本中定義為次要視圖。 Visual Studio for Mac 和 Visual Studio 2017 不支援觀看或編輯次要視圖;若要這樣做，請在 Xcode 的 Interface Builder 中開啟 **Main。**

`IntentViewController` 實行 [`IINUIHostedViewControlling`](xref:IntentsUI.IINUIHostedViewControlling)
介面，用來在使用 Siri 意圖時提供自訂介面。 [`ConfigureView`](xref:IntentsUI.INUIHostedViewControlling_Extensions.ConfigureView*)
系統會呼叫方法來自訂介面、顯示確認或發票，取決於是否正在確認互動 ([`INIntentHandlingStatus.Ready`](xref:Intents.INIntentHandlingStatus)) 或已成功執行 ([`INIntentHandlingStatus.Success`](xref:Intents.INIntentHandlingStatus)) ：

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
> 如需有關此方法的詳細資訊 `ConfigureView` ，請觀賞 Apple 的 WWDC 2017 簡報， [SiriKit 的新功能](https://developer.apple.com/videos/play/wwdc2017/214/)。

## <a name="creating-a-voice-shortcut"></a>建立語音快捷方式

< A0 Chef 提供一個介面，可將語音快捷方式指派給每個訂單，讓您可以使用 Siri 訂購 < a0。 事實上，用來錄製和指派語音快捷方式的介面是由 iOS 提供，且需要很少的自訂程式碼。

在中 `OrderDetailViewController` ，當使用者按下資料表的 [ **加入] Siri** 資料列時，此方法會 [`RowSelected`](xref:UIKit.UITableViewSource.RowSelected*) 顯示一個畫面來新增或編輯語音快捷方式：

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

根據目前顯示之順序是否存在現有的語音快捷方式，會 `RowSelected` 顯示類型或的視圖控制器 [`INUIEditVoiceShortcutViewController`](xref:IntentsUI.INUIEditVoiceShortcutViewController) [`INUIAddVoiceShortcutViewController`](xref:IntentsUI.INUIAddVoiceShortcutViewController) 。
在每個案例中， `OrderDetailViewController` 會將本身設定為 view controller `Delegate` ，這也是它也會執行的原因 [`IINUIAddVoiceShortcutViewControllerDelegate`](xref:IntentsUI.IINUIAddVoiceShortcutViewControllerDelegate)
和 [`IINUIEditVoiceShortcutViewControllerDelegate`](xref:IntentsUI.IINUIEditVoiceShortcutViewControllerDelegate)。

## <a name="testing-on-device"></a>在裝置上測試

若要在裝置上執行 < A0 Chef，請遵循本節中的指示。 另請閱讀 [有關自動](#automatic-provisioning)布建的注意事項。

### <a name="app-group-app-ids-provisioning-profiles"></a>應用程式群組，應用程式識別碼，布建設定檔

在 [Apple 開發人員入口網站](https://developer.apple.com/)的 [**憑證、識別碼 & 設定檔**] 區段中，執行下列步驟：

- 建立應用程式群組，以便在 < A0 Chef 應用程式與其延伸模組之間共用資料。 例如： **>com.lookout.enterprise.yourcompanyname. SoupChef**

- 建立三個應用程式識別碼：一個用於應用程式本身，一個用於意圖延伸，另一個用於意圖 UI 延伸模組。 例如：

  - 應用程式： **.com. >com.lookout.enterprise.yourcompanyname. SoupChef**
    - 針對此應用程式識別碼，指派 SiriKit 和 **應用程式群組** 功能。

  - 意圖延伸： **>com.lookout.enterprise.yourcompanyname. SoupChef 意圖**
    - 針對此應用程式識別碼，指派 **應用程式群組** 功能。

  - 意圖 UI 延伸模組： **.com. >com.lookout.enterprise.yourcompanyname. SoupChef. Intentsui**
    - 此應用程式識別碼不需要任何特殊功能。

- 建立上述的應用程式識別碼之後，請編輯指派給應用程式和意圖延伸模組的 **應用程式群組** 功能，並指定稍早建立的特定應用程式群組。

- 建立三個新的開發提供設定檔，每個新的應用程式識別碼各一個。

- 下載這些布建設定檔，然後按兩下每一個設定檔以進行安裝。 如果 Visual Studio for Mac 或 Visual Studio 2017 已在執行，請將它重新開機，以確定它會註冊新的布建設定檔。

### <a name="editing-infoplist-entitlementsplist-and-source-code"></a>編輯 Info. plist、plist 和 source 程式碼

在 Visual Studio for Mac 或 Visual Studio 2017 中，執行下列步驟：

- 更新解決方案中的各種 **plist** 檔案。 將應用程式、意圖延伸和意圖 UI 延伸套件組合識別碼設定為稍早定義的應用程式 **識別碼** ：

  - 應用程式： **.com. >com.lookout.enterprise.yourcompanyname. SoupChef**
  - 意圖延伸： **>com.lookout.enterprise.yourcompanyname. SoupChef 意圖**
  - 意圖 UI 延伸模組： **.com. >com.lookout.enterprise.yourcompanyname. SoupChef. Intentsui**

- 更新 **SoupChef** 專案的 **plist** 檔案：
  - 針對 [ **應用程式群組** ] 功能，將群組設定為上述範例中稍早建立的新應用程式群組 (，其為 **>com.lookout.enterprise.yourcompanyname. SoupChef**) 。
  - 請確定已啟用 **SiriKit** 。

- 更新 **SoupChefIntents** 專案的 **plist** 檔案：
  - 針對 [ **應用程式群組** ] 功能，將群組設定為上述範例中稍早建立的新應用程式群組 (，其為 **>com.lookout.enterprise.yourcompanyname. SoupChef**) 。

- 最後，開啟 **NSUserDefaultsHelper.cs**。 將 `AppGroup` 變數設定為新應用程式群組的值 (例如，將它設定為 `group.com.yourcompanyname.SoupChef`) 。

### <a name="configuring-the-build-settings"></a>設定組建設定

在 Visual Studio for Mac 或 Visual Studio 2017 中：

- 開啟 **SoupChef** 專案的選項/屬性。 在 [ **iOS** 套件組合簽署] 索引標籤上，將 [ **簽署身分識別** ] 設定為 [自動]，並將 **設定檔** 設定為您稍早建立的新應用程式

- 開啟 **SoupChefIntents** 專案的選項/屬性。 在 [ **iOS** 套件組合簽署] 索引標籤上，將 [ **簽署身分識別** ] 設定為 [自動]，並將 **設定檔** 設定為您稍早建立的新意圖擴充功能專用

- 開啟 **SoupChefIntentsUI** 專案的選項/屬性。 在 [ **iOS** 套件組合簽署] 索引標籤上，將 [ **簽署身分識別** ] 設定為 [自動]，並將 **設定檔** 設定為您稍早建立的新意圖 UI 延伸模組特定

進行這些變更之後，應用程式將會在 iOS 裝置上執行。

### <a name="automatic-provisioning"></a>自動佈建

您可以使用 [自動](../../get-started/installation/device-provisioning/automatic-provisioning.md) 布建，直接在 IDE 中完成許多這些布建工作。
不過，自動布建不會設定應用程式群組。 您將需要以您想要使用的應用程式組名手動設定 **plist** 檔案、流覽 Apple 開發人員入口網站來建立應用程式群組、將該應用程式群組指派給自動布建所建立的每個應用程式識別碼、重新產生布建設定檔 (應用程式、意圖擴充功能、意圖 UI 延伸模組) 以包含新建立的應用程式群組，以及下載並

## <a name="related-links"></a>相關連結

- [ A0 Chef (Xamarin) ](/samples/xamarin/ios-samples/ios12-soupchef)
- [ A0 Chef (Swift) ](https://developer.apple.com/documentation/sirikit/accelerating_app_interactions_with_shortcuts?language=objc)
- [SiriKit (Apple) ](https://developer.apple.com/sirikit/)
- [Siri 快捷方式簡介– WWDC 2018](https://developer.apple.com/videos/play/wwdc2018/211/)
- [使用 Siri 快捷方式建立語音-WWDC 2018](https://developer.apple.com/videos/play/wwdc2018/214/)
- [Siri Watch 臉部的 Siri 快捷方式– WWDC 2018](https://developer.apple.com/videos/play/wwdc2018/217/)
- [SiriKit 的新功能– WWDC 2017](https://developer.apple.com/videos/play/wwdc2017/214/)
- [ (Apple) 建立意圖應用程式擴充功能 ](https://developer.apple.com/documentation/sirikit/creating_an_intents_app_extension?language=objc)
- [系結 iOS Swift 程式庫](../binding-swift/walkthrough.md)
