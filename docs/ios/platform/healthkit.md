---
title: 在 Xamarin 中 HealthKit
description: 本檔說明 HealthKit，這是在 iOS 8 中引進的架構，可針對健康情況相關資訊提供集中、協調及安全的資料存放區。 它會討論如何布建 HealthKit 應用程式，以及如何撰寫使用 HealthKit 架構的程式碼。
ms.prod: xamarin
ms.assetid: E3927A21-507C-43BA-A2AD-957716BA9B52
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/19/2017
ms.openlocfilehash: 3d5bdfd78658803dcbb159101050aea48008b69e
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91435236"
---
# <a name="healthkit-in-xamarinios"></a>在 Xamarin 中 HealthKit

健康情況套件會針對使用者的健康情況相關資訊，提供安全的資料存放區。 健康情況套件應用程式可能會有使用者的明確許可權、讀取和寫入此資料存放區，以及在加入相關資料時接收通知。 應用程式可以顯示資料，或使用者可以使用 Apple 提供的健康情況應用程式來查看其所有資料的儀表板。

因為健康狀態相關資料是敏感性且重要的，所以健康情況套件是強型別，且具有測量單位，以及記錄的資訊類型的明確關聯， (例如，血糖層級或核心速率) 。 此外，健康情況套件應用程式必須使用明確權利、必須要求存取特定類型的資訊，且使用者必須明確地將這些資料類型的存取權授與應用程式。

本文將介紹：

- 健康套件的安全性需求，包括應用程式布建和要求使用者權限來存取健康情況套件資料庫;
- 健全狀況套件的類型系統，可將錯誤套用或錯誤解譯資料的可能性降至最低;
- 寫入共用的全系統健康情況套件資料存放區。

本文將不會涵蓋更多的 advanced 主題，例如查詢資料庫、在度量單位之間轉換，或接收新資料的通知。

在本文中，我們將建立一個範例應用程式來記錄使用者的核心速率：

[![記錄使用者核心費率的範例應用程式](healthkit-images/image01.png)](healthkit-images/image01.png#lightbox)

## <a name="requirements"></a>需求

若要完成本文中所述的步驟，必須具備下列專案：

- **Xcode 7 和 ios 8 (或更高的) ** –必須在開發人員的電腦上安裝和設定 Apple 的最新 Xcode 和 ios api。
- **Visual Studio for Mac 或 Visual Studio** –應在開發人員的電腦上安裝並設定最新版本的 Visual Studio for Mac。
- **ios 8 (或更高) 裝置** –執行最新版 ios 8 或更新版本的 ios 裝置以進行測試。

> [!IMPORTANT]
> Health 套件是在 iOS 8 中引進。 目前，在 iOS 模擬器上無法使用健康情況套件，且必須連接到實體 iOS 裝置。

## <a name="creating-and-provisioning-a-health-kit-app"></a>建立和布建健康情況套件應用程式
在 Xamarin iOS 8 應用程式可以使用 HealthKit API 之前，必須先妥善設定和布建。 本節將討論正確設定 Xamarin 應用程式所需的步驟。

健康情況套件應用程式需要：

- 明確的 **應用程式識別碼**。
- 與該明確**應用程式識別碼**和**健全狀況套件**許可權相關聯的布建**設定檔**。
- `Entitlements.plist`，其 `com.apple.developer.healthkit` 屬性的型別 `Boolean` 設定為 `Yes` 。
- ， `Info.plist` 其 `UIRequiredDeviceCapabilities` 索引鍵包含具有值的 `String` 專案 `healthkit` 。
- `Info.plist`也必須有適當的隱私權說明專案： `String` `NSHealthUpdateUsageDescription` 如果應用程式即將寫入資料，以及 `String` `NSHealthShareUsageDescription` 當應用程式即將讀取健康套件資料時，金鑰的說明。

若要深入瞭解如何布建 iOS 應用程式，Xamarin**消費者入門**系列中的[裝置](~/ios/get-started/installation/device-provisioning/index.md)布建文章會說明開發人員憑證、應用程式識別碼、布建設定檔和應用程式權利之間的關聯性。

<a name="explicit-appid"></a>

### <a name="explicit-app-id-and-provisioning-profile"></a>明確的應用程式識別碼和布建設定檔

建立明確的 **應用程式識別碼** 和適當的布建 **設定檔** 是在 Apple 的 [iOS 開發人員中心](https://developer.apple.com/devcenter/ios/index.action)內完成。 

您目前的 **應用程式識別碼** 會列在開發人員中心的 [憑證、識別碼 & 設定檔](https://developer.apple.com/account/ios/identifiers/bundle/bundleList.action) 區段內。 這份清單通常會顯示的**識別碼**值 `*` ，表示**應用程式識別碼**  -  **名稱**可以與任意數目的尾碼搭配使用。 這類 *萬用字元應用程式識別碼* 無法與健康情況套件一起使用。

若要建立明確的 **應用程式識別碼**，請按一下右上角的按鈕，將 **+** 您帶到 [ **註冊 iOS 應用程式識別碼** ] 頁面：

[![在 Apple Developer 入口網站上註冊應用程式](healthkit-images/image02.png)](healthkit-images/image02.png#lightbox)

如上圖所示，在建立應用程式描述之後，請使用 **明確的應用程式識別碼** 區段來建立應用程式的識別碼。 在 [**應用程式服務**] 區段中，勾選 [**啟用服務**] 區段中的**健全狀況套件**。

當您完成時，請按 [ **繼續** ] 按鈕，在您的帳戶中註冊 **應用程式識別碼** 。 您將會回到 [ **憑證、識別碼和設定檔** ] 頁面。 按一下 [布建 **設定檔** ]，將您帶到目前的布建配置檔案清單，然後按一下 **+** 右上角的按鈕，將您帶到 [ **新增 IOS 布建設定檔** ] 頁面。 選取 [ **IOS 應用程式開發** ] 選項，然後按一下 [ **繼續** ] 以進入 [ **選取應用程式識別碼** ] 頁面。 在這裡，選取您先前指定的明確 **應用程式識別碼** ：

[![選取明確的應用程式識別碼](healthkit-images/image03.png)](healthkit-images/image03.png#lightbox)

按一下 [**繼續**]，然後流覽其餘畫面，您將在其中**指定開發人員憑證 (s) **、**裝置 (s) **，以及此布建**設定檔**的**名稱**：

[![正在產生布建設定檔](healthkit-images/image04.png)](healthkit-images/image04.png#lightbox)

按一下 [ **產生** ]，並等候建立您的設定檔。 下載檔案並按兩下以在 Xcode 中安裝。 您可以在 Xcode > 喜好設定 > 帳戶下確認其是否已安裝 **> 查看詳細資料 ...** 您應該會看到您剛剛安裝的布建設定檔，且其 **權利** 資料列中應該有健康情況套件和任何其他特殊服務的圖示：

[![在 Xcode 中查看設定檔](healthkit-images/image05.png)](healthkit-images/image05.png#lightbox)

<a name="associating-appid"></a>

### <a name="associating-the-app-id-and-provisioning-profile-with-your-xamarinios-app"></a>將應用程式識別碼和布建設定檔與您的 Xamarin iOS 應用程式產生關聯

一旦您建立並安裝適當的布建 **設定檔** （如所述），通常就可以在 Visual Studio for Mac 或 Visual Studio 中建立解決方案。 健康情況套件存取可供任何 iOS c # 或 F # 專案使用。

您可以開啟連結到本文的範例應用程式， (其中包含預先建立的分鏡腳本和程式碼) ，而不是以手動方式逐步建立 Xamarin iOS 8 專案的程式。 若要將範例應用程式與已啟用健康情況套件的布建 **設定檔**產生關聯，請在 [ **Solution Pad**中，以滑鼠右鍵按一下您的專案，並顯示其 [ **選項** ] 對話方塊。 切換至 [ **IOS 應用程式** ] 面板，然後輸入您先前建立的明確 **應用** 程式識別碼作為應用程式的套件組合 **識別碼**：

[![輸入明確的應用程式識別碼](healthkit-images/image06.png)](healthkit-images/image06.png#lightbox)

現在切換至 IOS 套件組合 **簽署** 面板。 您最近安裝的布建 **設定檔**及其與明確 **應用程式識別碼**的關聯，現在將可作為布建 **設定檔**：

[![選取布建設定檔](healthkit-images/image07.png)](healthkit-images/image07.png#lightbox)

如果布建**設定檔**無法使用，請在 [ **ios 應用程式**] 面板中，按兩下 [ios 應用程式] 面板中指定的套件組合**識別碼** **，以及安裝**布建**設定檔** (**Xcode > 喜好設定 > 帳戶 > 查看詳細資料 ...**) 。

選取 [已啟用健康情況套件的布建 **設定檔** ] 時，按一下 **[確定** ] 以關閉 [專案選項] 對話方塊。

### <a name="entitlementsplist-and-infoplist-values"></a>Plist 和 Info. plist 值

範例應用程式包含 `Entitlements.plist`) 的健康套件啟用的應用程式所需的檔案 (，且不包含在每個專案範本中。 如果您的專案未包含權利，請在專案上按一下滑鼠右鍵，選擇 [檔案] > [新增檔案] **> iOS > plist** ，以手動方式新增一個。

最後，您 `Entitlements.plist` 必須具有下列索引鍵和值組：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>com.apple.developer.HealthKit</key>
    <true/>
</dict>
</plist>

```

同樣地， `Info.plist` 應用程式的值必須 `healthkit` 與索引鍵相關聯 `UIRequiredDeviceCapabilities` ：

```xml
<key>UIRequiredDeviceCapabilities</key>
<array>
<string>armv7</string>
    <string>healthkit</string>
</array>

```

本文提供的範例應用程式包含預先設定 `Entitlements.plist` 的，其中包含所有必要的金鑰。

<a name="programming"></a>

## <a name="programming-health-kit"></a>程式設計健全狀況套件

健康情況套件資料存放區是私人、使用者專屬的資料存放區，會在應用程式間共用。 因為健康狀態資訊很敏感，所以使用者必須採取正面步驟來允許資料存取。 這種存取權可以是部分 (寫入但無法讀取、某些資料類型的存取權 ) ，但不能存取其他類型的資料，而是可能隨時撤銷。 健康情況套件應用程式應該以依舊撰寫, 撰寫，並瞭解許多使用者將會遲疑有關儲存其健康情況相關資訊。

健康情況套件資料僅限於 Apple 指定的類型。 這些類型會嚴格地定義：某些（例如血糖類型）僅限於 Apple 提供之列舉的特定值，而其他類型則結合量值的單位 (例如，字母、卡路里和升) 。 即使是共用相容量值的資料，也是由它們來區分 `HKObjectType` ，例如，型別系統將會攔截將 `HKQuantityTypeIdentifier.NumberOfTimesFallen` 值儲存到預期的欄位（ `HKQuantityTypeIdentifier.FlightsClimbed` 即使兩者都使用測量單位）的嘗試 `HKUnit.Count` 。

健康情況套件資料存放區中存放的類型為的子類別 `HKObjectType` 。 `HKCharacteristicType` 物件會儲存生物性別、血型別和出生日期。 不過，更常見的是 `HKSampleType` 物件，代表在特定時間或一段時間內取樣的資料。 

[![HKSampleType 物件圖表](healthkit-images/image08.png)](healthkit-images/image08.png#lightbox)

`HKSampleType` 是抽象的，且有四個具象的子類別。 目前只有一種類型的 `HKCategoryType` 資料，也就是睡眠分析。 健康情況套件中大部分的資料都屬於類型， `HKQuantityType` 並將其資料儲存在 `HKQuantitySample` 物件中，而這些物件是使用熟悉的 Factory 設計模式所建立的：

[![健康情況套件中大部分的資料類型都是 HKQuantityType，並將其資料儲存在 HKQuantitySample 物件中](healthkit-images/image09.png)](healthkit-images/image09.png#lightbox)

`HKQuantityType` 類型的範圍從 `HKQuantityTypeIdentifier.ActiveEnergyBurned` 到到 `HKQuantityTypeIdentifier.StepCount` 。 

<a name="requesting-permission"></a>

### <a name="requesting-permission-from-the-user"></a>要求使用者的許可權

終端使用者必須採取正面步驟，才能讓應用程式讀取或寫入健康情況套件資料。 這是透過預先安裝在 iOS 8 裝置上的健康情況應用程式來完成。 第一次執行健康情況套件應用程式時，使用者會看到系統控制的 **健全狀況存取** 對話方塊：

[![使用者會看到系統控制的健全狀況存取對話方塊](healthkit-images/image10.png)](healthkit-images/image10.png#lightbox)

之後，使用者可以使用健康情況應用程式的 [ **來源** ] 對話方塊來變更許可權：

[![使用者可以使用健康情況應用程式來源對話方塊來變更許可權](healthkit-images/image11.png)](healthkit-images/image11.png#lightbox)

由於健全狀況資訊非常敏感，因此應用程式開發人員應該依舊撰寫, 撰寫程式，並預期在應用程式執行時將會拒絕和變更許可權。 最常見的用法是在方法中要求許可權 `UIApplicationDelegate.OnActivated` ，然後適當地修改使用者介面。

### <a name="permissions-walkthrough"></a>許可權逐步解說

在健康情況套件布建的專案中，開啟檔案 `AppDelegate.cs` 。 請注意在檔案頂端使用的語句 `HealthKit` 。

下列程式碼與健康情況套件許可權相關：

```csharp
private HKHealthStore healthKitStore = new HKHealthStore ();

public override void OnActivated (UIApplication application)
{
        ValidateAuthorization ();
}

private void ValidateAuthorization ()
{
        var heartRateId = HKQuantityTypeIdentifierKey.HeartRate;
        var heartRateType = HKObjectType.GetQuantityType (heartRateId);
        var typesToWrite = new NSSet (new [] { heartRateType });
        var typesToRead = new NSSet ();
        healthKitStore.RequestAuthorizationToShare (
                typesToWrite, 
                typesToRead, 
                ReactToHealthCarePermissions);
}

void ReactToHealthCarePermissions (bool success, NSError error)
{
        var access = healthKitStore.GetAuthorizationStatus (HKObjectType.GetQuantityType (HKQuantityTypeIdentifierKey.HeartRate));
        if (access.HasFlag (HKAuthorizationStatus.SharingAuthorized)) {
                HeartRateModel.Instance.Enabled = true;
        } else {
                HeartRateModel.Instance.Enabled = false;
        }
}

```

這些方法中的所有程式碼都可以內嵌方式完成 `OnActivated` ，但是範例應用程式會使用不同的方法來更清楚地提供意圖： `ValidateAuthorization()` 需要必要的步驟，以要求存取所寫入的特定類型 (和讀取; 如果應用程式所需的) ，而且 `ReactToHealthCarePermissions()` 是在使用者與 Health 中的 [許可權] 對話方塊互動之後啟動的回呼。

的工作 `ValidateAuthorization()` 是建立 `HKObjectTypes` 應用程式將寫入的集合，並要求授權以更新該資料。 在範例應用程式中， `HKObjectType` 是金鑰的 `KHQuantityTypeIdentifierKey.HeartRate` 。 此類型會新增至集合 `typesToWrite` ，而集合 `typesToRead` 會保留為空白。 這些集合和回呼的參考 `ReactToHealthCarePermissions()` 會傳遞至 `HKHealthStore.RequestAuthorizationToShare()` 。

`ReactToHealthCarePermissions()`回呼將會在使用者與許可權對話互動之後呼叫，並且傳遞兩項資訊： `bool` `true` 當使用者與許可權對話方塊互動，以及 `NSError` 如果非 null 時，會指出與呈現許可權對話方塊相關聯的某種錯誤。

> [!IMPORTANT]
> 若要清楚瞭解此函式的引數： _success_ 和 _error_ 參數不會指出使用者是否已授與存取健康套件資料的許可權！ 他們只會指出使用者已獲得允許存取資料的機會。

若要確認應用程式是否有權存取資料，請 `HKHealthStore.GetAuthorizationStatus()` 使用來傳入 `HKQuantityTypeIdentifierKey.HeartRate` 。 應用程式會根據傳回的狀態，啟用或停用輸入資料的能力。 沒有處理拒絕存取的標準使用者經驗，還有許多可能的選項。 在範例應用程式中，狀態會設定在單一物件上，而後者則會 `HeartRateModel` 引發相關事件。

## <a name="model-view-and-controller"></a>模型、視圖和控制器

若要檢查 `HeartRateModel` 單一物件，請開啟檔案 `HeartRateModel.cs` ：

```csharp
using System;
using HealthKit;
using Foundation;

namespace HKWork
{
        public class GenericEventArgs<T> : EventArgs
        {
                public T Value { get; protected set; }
                public DateTime Time { get; protected set; }

                public GenericEventArgs (T value)
                {
                        this.Value = value;
                        Time = DateTime.Now;
                }
        }

        public delegate void GenericEventHandler<T> (object sender,GenericEventArgs<T> args);

        public sealed class HeartRateModel : NSObject
        {
                private static volatile HeartRateModel singleton;
                private static object syncRoot = new Object ();

                private HeartRateModel ()
                {
                }

                public static HeartRateModel Instance {
                        get {
                                //Double-check lazy initialization
                                if (singleton == null) {
                                        lock (syncRoot) {
                                                if (singleton == null) {
                                                        singleton = new HeartRateModel ();
                                                }
                                        }
                                }

                                return singleton;
                        }
                }

                private bool enabled = false;

                public event GenericEventHandler<bool> EnabledChanged;
                public event GenericEventHandler<String> ErrorMessageChanged;
                public event GenericEventHandler<Double> HeartRateStored;

                public bool Enabled { 
                        get { return enabled; }
                        set {
                                if (enabled != value) {
                                        enabled = value;
                                        InvokeOnMainThread(() => EnabledChanged (this, new GenericEventArgs<bool>(value)));
                                }
                        }
                }

                public void PermissionsError(string msg)
                {
                        Enabled = false;
                        InvokeOnMainThread(() => ErrorMessageChanged (this, new GenericEventArgs<string>(msg)));
                }

                //Converts its argument into a strongly-typed quantity representing the value in beats-per-minute
                public HKQuantity HeartRateInBeatsPerMinute(ushort beatsPerMinute)
                {
                        var heartRateUnitType = HKUnit.Count.UnitDividedBy (HKUnit.Minute);
                        var quantity = HKQuantity.FromQuantity (heartRateUnitType, beatsPerMinute);

                        return quantity;
                }
                        
                public void StoreHeartRate(HKQuantity quantity)
                {
                        var bpm = HKUnit.Count.UnitDividedBy (HKUnit.Minute);
                        //Confirm that the value passed in is of a valid type (can be converted to beats-per-minute)
                        if (! quantity.IsCompatible(bpm))
                        {
                                InvokeOnMainThread(() => ErrorMessageChanged(this, new GenericEventArgs<string> ("Units must be compatible with BPM")));
                        }

                        var heartRateId = HKQuantityTypeIdentifierKey.HeartRate;
                        var heartRateQuantityType = HKQuantityType.GetQuantityType (heartRateId);
                        var heartRateSample = HKQuantitySample.FromType (heartRateQuantityType, quantity, new NSDate (), new NSDate (), new HKMetadata());

                        using (var healthKitStore = new HKHealthStore ()) {
                                healthKitStore.SaveObject (heartRateSample, (success, error) => {
                                        InvokeOnMainThread (() => {
                                                if (success) {
                                                        HeartRateStored(this, new GenericEventArgs<Double>(quantity.GetDoubleValue(bpm)));
                                                } else {
                                                        ErrorMessageChanged(this, new GenericEventArgs<string>("Save failed"));
                                                }
                                                if (error != null) {
                                                        //If there's some kind of error, disable 
                                                        Enabled = false;
                                                        ErrorMessageChanged (this, new GenericEventArgs<string>(error.ToString()));
                                                }
                                        });
                                });
                        }
                }
        }
}

```

第一個區段是用來建立一般事件和處理常式的未定案程式碼。 類別的初始部分 `HeartRateModel` 也可以用來建立安全線程的單一物件。

然後，會 `HeartRateModel` 公開三個事件： 

- `EnabledChanged` -表示已啟用或停用的核心速率儲存 (請注意，一開始會停用儲存體) 。 
- `ErrorMessageChanged` -對於此範例應用程式，我們有一個非常簡單的錯誤處理模型：具有最後一個錯誤的字串。 
- `HeartRateStored` -當核心速率儲存在健康情況套件資料庫時引發。

請注意，每當引發這些事件時，它就會透過執行 `NSObject.InvokeOnMainThread()` ，這可讓訂閱者更新 UI。 或者，事件可能會記錄在背景執行緒上引發，以及確保可以將相容性留給其處理常式的責任。 執行緒考慮對健康情況套件應用程式很重要，因為許多函數（例如許可權要求）都是非同步，而且會在非主要執行緒上執行其回呼。

中的 Heath 套件特定程式碼位於 `HeartRateModel` 兩個函數 `HeartRateInBeatsPerMinute()` 和 `StoreHeartRate()` 。 

`HeartRateInBeatsPerMinute()` 將其引數轉換成強型別的健康情況套件 `HKQuantity` 。 數量的類型是由指定， `HKQuantityTypeIdentifierKey.HeartRate` 而數量的單位會 `HKUnit.Count` 除以 `HKUnit.Minute` (換句話說，每分鐘的單位是 *每分鐘*) 。 

函 `StoreHeartRate()` 式會採用 `HKQuantity` 範例應用程式中的 (，) 建立一個應用程式 `HeartRateInBeatsPerMinute()` 。 為了驗證其資料，它會使用 `HKQuantity.IsCompatible()` 方法， `true` 如果物件的單位可以轉換成引數中的單位，就會傳回此方法。 如果以這種方式建立的數量 `HeartRateInBeatsPerMinute()` 會很明顯地傳回 `true` ，但 `true` 如果建立的數量為，例如 *每小時*的速率，也會傳回。 更常見的情況 `HKQuantity.IsCompatible()` 是，可以用來驗證使用者或裝置可能會在一種測量系統中輸入或顯示的大量、距離和能源 (例如英制單位) 但可能儲存在其他系統 (例如度量單位) 。 

一旦驗證數量的相容性之後， `HKQuantitySample.FromType()` 就會使用 factory 方法來建立強型別 `heartRateSample` 物件。 `HKSample` 物件具有開始和結束日期;針對瞬間讀取，這些值應該相同，就像在範例中一樣。 此範例也不會在其引數中設定任何索引鍵/值資料 `HKMetadata` ，但您可以使用下列程式碼來指定感應器位置：

```csharp
var hkm = new HKMetadata();
hkm.HeartRateSensorLocation = HKHeartRateSensorLocation.Chest;

```

一旦建立之後，程式碼就會使用 `heartRateSample` using 區塊來建立與資料庫的新連接。 在該區塊內， `HKHealthStore.SaveObject()` 方法會嘗試對資料庫進行非同步寫入。 對 lambda 運算式所產生的呼叫會觸發相關的事件，不論是 `HeartRateStored` 或 `ErrorMessageChanged` 。

既然模型已經過程式設計，就可以看到控制器如何反映模型的狀態。 開啟 `HKWorkViewController.cs` 檔案。 此函式只是將 `HeartRateModel` 單一專案指向事件處理方法 (同樣地，您可以使用 lambda 運算式以內嵌方式完成，但是不同的方法會讓意圖稍微更明顯) ：

```csharp
public HKWorkViewController (IntPtr handle) : base (handle)
{
     HeartRateModel.Instance.EnabledChanged += OnEnabledChanged;
     HeartRateModel.Instance.ErrorMessageChanged += OnErrorMessageChanged;
     HeartRateModel.Instance.HeartRateStored += OnHeartBeatStored;
}

```

相關的處理常式如下：

```csharp
void OnEnabledChanged (object sender, GenericEventArgs<bool> args)
{
        StoreData.Enabled = args.Value;
        PermissionsLabel.Text = args.Value ? "Ready to record" : "Not authorized to store data.";
        PermissionsLabel.SizeToFit ();
}

void OnErrorMessageChanged (object sender, GenericEventArgs<string> args)
{
        PermissionsLabel.Text = args.Value;
}

void OnHeartBeatStored (object sender, GenericEventArgs<double> args)
{
        PermissionsLabel.Text = String.Format ("Stored {0} BPM", args.Value);
}

```

顯然，在具有單一控制器的應用程式中，您可以避免建立個別的模型物件，並使用事件進行控制流程，但使用模型物件更適合真實世界的應用程式。

## <a name="running-the-sample-app"></a>執行範例應用程式

IOS 模擬器不支援健康情況套件。 必須在執行 iOS 8 的實體裝置上執行調試。

將正確布建的 iOS 8 開發裝置連接到您的系統。 在 Visual Studio for Mac 中選取它作為部署目標，然後從功能表中選擇 [ **執行 > Debug**]。

> [!IMPORTANT]
> 布建的相關錯誤會在此時呈現。 若要針對錯誤進行疑難排解，請參閱上方的建立和布建健康情況套件應用程式一節。 這些元件包括： 
>
> - **IOS 開發人員中心** -明確的應用程式識別碼 & 健康套件已啟用布建設定檔。 
> - **專案選項** -套件組合識別碼 (明確的應用程式識別碼) & 布建設定檔。
> - **原始程式碼** -權利. Plist & Info. plist

假設已正確設定這些條款，您的應用程式就會啟動。 當它到達其 `OnActivated` 方法時，它會要求健康情況套件授權。 當作業系統第一次發生這種情況時，您的使用者將會看到下列對話方塊：

[![使用者將會看到此對話方塊](healthkit-images/image12.png)](healthkit-images/image12.png#lightbox)

讓您的應用程式更新核心速率資料，您的應用程式將會再次出現。 `ReactToHealthCarePermissions`回呼將會以非同步方式啟動。 這會導致屬性變更（這會引發事件），這會 `HeartRateModel’s` `Enabled` 導致事件 `EnabledChanged` `HKPermissionsViewController.OnEnabledChanged()` 處理常式執行，這會啟用 `StoreData` 按鈕。 下圖顯示順序：

[![下圖顯示事件的順序](healthkit-images/image13.png)](healthkit-images/image13.png#lightbox)

按下 [ **錄製** ] 按鈕。 這會導致 `StoreData_TouchUpInside()` 處理常式執行，這會嘗試剖析 `heartRate` 文字欄位的值、透過先前討論的函式轉換成， `HKQuantity` 並將 `HeartRateModel.HeartRateInBeatsPerMinute()` 該數量傳遞給 `HeartRateModel.StoreHeartRate()` 。 如先前所討論，這將會嘗試儲存資料，並且會引發 `HeartRateStored` 或 `ErrorMessageChanged` 事件。

按兩下裝置上的 [ **首頁** ] 按鈕，然後開啟 [健全狀況] 應用程式。 按一下 [ **來源** ] 索引標籤，您會看到列出的範例應用程式。 選擇它並不允許更新核心速率資料的許可權。 按兩下 [ **首頁** ] 按鈕，然後切換回您的應用程式。 同樣地， `ReactToHealthCarePermissions()` 將會呼叫，但這次因為拒絕存取，所以會停用 [ **StoreData** ] 按鈕 (請注意，這會以非同步方式發生，使用者介面中的變更可能會顯示給終端使用者) 。

## <a name="advanced-topics"></a>進階主題

從健康情況套件資料庫讀取資料與寫入資料非常類似：一個會指定一個嘗試存取的資料類型、要求授權，而且如果授與該授權的話，就可以使用資料，並自動轉換成相容的度量單位。

有一些更精密的查詢函數，可在更新相關資料時，允許以述詞為基礎的查詢和查詢執行更新。 

健康情況套件應用程式的開發人員應該參閱 Apple [應用程式審查指導方針](https://developer.apple.com/app-store/review/guidelines/#healthkit)的健康情況套件一節。

瞭解安全性和類型系統模型之後，儲存和讀取共用健康情況套件資料庫中的資料相當簡單。 健康情況套件中的許多函式會以非同步方式運作，而應用程式開發人員必須適當地撰寫其程式。

本文撰寫本文時，目前沒有與 Android 或 Windows Phone 中的健康情況套件相等的專案。

## <a name="summary"></a>摘要

在本文中，我們已瞭解健康情況套件如何讓應用程式儲存、取出和共用健康情況相關資訊，同時也提供可讓使用者存取及控制此資料的標準健康情況應用程式。 

我們也已瞭解隱私權、安全性和資料完整性如何覆寫健全狀況相關資訊的考慮，而使用健康情況套件的應用程式必須處理應用程式管理方面的複雜性增加 (布建) 、撰寫 (健康套件的類型系統) ，以及使用者體驗 (使用者透過系統對話方塊和健康情況應用程式) 的許可權控制。 

最後，我們會使用包含的範例應用程式來查看健全狀況套件的簡單執行，此應用程式會將心跳資料寫入健康情況套件存放區，並具有非同步感知的設計。

## <a name="related-links"></a>相關連結

- [HKWork (範例) ](/samples/xamarin/ios-samples/ios8-introtohealthkit)
- [iOS 8 簡介](~/ios/platform/introduction-to-ios8.md)