---
title: 在 Xamarin.iOS 中 HealthKit
description: 本文件說明 HealthKit，在 iOS 8 的健全狀況的相關資訊提供集中式、 協調及安全的資料存放區中導入的架構。 它討論如何佈建 HealthKit 應用程式，以及如何撰寫使用 HealthKit framework 的程式碼。
ms.prod: xamarin
ms.assetid: E3927A21-507C-43BA-A2AD-957716BA9B52
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/19/2017
ms.openlocfilehash: df0b0e8dd57129917f2d8dab07115551ca675acf
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61163195"
---
# <a name="healthkit-in-xamarinios"></a>在 Xamarin.iOS 中 HealthKit

健全狀況套件會提供安全的資料存放區，如使用者的健康狀態相關資訊。 健全狀況套件應用程式可能會與使用者的明確權限，讀取和寫入此資料存放區以及加入相關的資料時收到通知。 應用程式可以呈現資料，或使用者可以使用 Apple 提供的健全狀況應用程式，來檢視其所有資料的儀表板。

因為健康狀態相關的資料是因此機密和重要，健全狀況套件強型別，與量值和明確關聯 （比方說，捐血血糖測量層級或核心的比率） 所記錄的資訊類型的單位。 此外，健全狀況套件應用程式必須使用明確的權利，必須要求存取至特定類型的資訊，且使用者必須明確授與應用程式存取這些類型的資料。

本文將介紹：

- 健全狀況套件的安全性需求，包括應用程式佈建和要求存取健康情況套件資料庫的使用者權限
- 健全狀況的套件類型系統，正確套用或錯誤解譯資料的可能性降至最低
- 寫入共用的全系統健全狀況套件資料存放區。

這篇文章將涵蓋更進階的主題，例如查詢資料庫、 的測量單位之間轉換或接收通知的新資料。

在本文中，我們將建立的範例應用程式，以記錄使用者的核心速率：

[![](healthkit-images/image01.png "範例應用程式記錄的使用者核心比率")](healthkit-images/image01.png#lightbox)

## <a name="requirements"></a>需求

以下被必要設定來完成這篇文章所述的步驟：

- **Xcode 7 和 iOS 8 （或更新版本）** – Apple 的最新 Xcode 和 iOS Api 需要安裝並設定開發人員電腦上。
- **Visual Studio for Mac 或 Visual Studio** – 應該安裝並設定開發人員電腦上的 Visual Studio for Mac 的最新版本。
- **iOS 8 （或更新版本） 裝置**– iOS 裝置執行最新版的 iOS 8 或更新版本進行測試。

> [!IMPORTANT]
> 在 iOS 8 中引進了健全狀況的套件。 目前，健全狀況套件並不適用於 iOS 模擬器中，並偵錯需要實體 iOS 裝置的連線。




## <a name="creating-and-provisioning-a-health-kit-app"></a>建立和佈建的健康情況套件應用程式
Xamarin iOS 8 應用程式可以使用 HealthKit API 之前，它必須適當地設定和佈建。 本節將討論正確設定您的 Xamarin 應用程式所需的步驟。

健全狀況套件應用程式需要：

- 明確**應用程式識別碼**。
- A**佈建設定檔**相關聯的明確**應用程式識別碼**且**健全狀況套件**權限。
- `Entitlements.plist`具有`com.apple.developer.healthkit`型別的屬性`Boolean`設定為`Yes`。
- `Info.plist`其`UIRequiredDeviceCapabilities`機碼包含的項目`String`值`healthkit`。
- `Info.plist`也必須擁有適當的隱私權說明項目：`String`金鑰的說明`NSHealthUpdateUsageDescription`如果應用程式即將寫入資料和`String`機碼的說明`NSHealthShareUsageDescription`如果應用程式會讀取健全狀況套件資料。

若要深入了解佈建 iOS 應用程式[裝置佈建](~/ios/get-started/installation/device-provisioning/index.md)Xamarin 的文章**開始使用**系列說明開發人員憑證，應用程式識別碼 」 之間的關聯性佈建設定檔，以及應用程式的權利。

<a name="explicit-appid" />

### <a name="explicit-app-id-and-provisioning-profile"></a>明確的應用程式識別碼和佈建設定檔

建立明確**應用程式識別碼**且適當**佈建設定檔**在 Apple 的內完成[iOS 開發人員中心](https://developer.apple.com/devcenter/ios/index.action)。 

您目前**應用程式識別碼**中所列[憑證、 識別碼與設定檔](https://developer.apple.com/account/ios/identifiers/bundle/bundleList.action)開發人員中心的一節。 通常，這份清單會顯示**識別碼**的值`*`，這表示，**應用程式識別碼** - **名稱**可以搭配任何數目的尾碼。 這類*萬用字元應用程式識別碼*不能使用健全狀況的套件。
 
若要建立明確 **應用程式識別碼**，按一下 **+** 帶您到右上方中的按鈕 **註冊 iOS 應用程式識別碼** 頁面：


[![](healthkit-images/image02.png "註冊 Apple 開發人員入口網站上的應用程式")](healthkit-images/image02.png#lightbox)

如圖所示，建立應用程式描述之後，使用**明確的應用程式識別碼**一節，以建立您的應用程式的識別碼。 在**應用程式服務**區段中，按一下**健全狀況套件**中**啟用 Services**一節。

當您完成之後時，按下**繼續**按鈕以註冊**應用程式識別碼**在您的帳戶。 您將會回復**憑證、 識別碼和設定檔**頁面。 按一下 **佈建設定檔**帶您前往您目前的佈建設定檔的清單，然後按一下**+** 帶您前往右上角的按鈕**Add iOS佈建設定檔**頁面。 選取**iOS App Development**選項，然後按一下**繼續**前往**選取 [應用程式識別碼]** 頁面。 在這裡，選取 明確**應用程式識別碼**您先前指定：


[![](healthkit-images/image03.png "選取 明確的應用程式識別碼")](healthkit-images/image03.png#lightbox)

按一下 **繼續**並逐步完成其餘的畫面，您將在其中指定您**開發人員憑證**，**裝置**，和**名稱**這個**佈建設定檔**:

[![](healthkit-images/image04.png "產生佈建設定檔")](healthkit-images/image04.png#lightbox)

按一下 **產生**和 await 進行建立您的設定檔。 下載檔案，然後按兩下它以安裝在 Xcode 中。 您可以確認它的安裝下**Xcode > 喜好設定 > 帳戶 > 檢視詳細資料...** 您應該會看到您剛安裝佈建設定檔，且它應該圖示健全狀況的套件和其他任何特殊服務，在其**權利**資料列：

[![](healthkit-images/image05.png "在 Xcode 中檢視設定檔")](healthkit-images/image05.png#lightbox)

<a name="associating-appid" />

### <a name="associating-the-app-id-and-provisioning-profile-with-your-xamarinios-app"></a>建立關聯的應用程式識別碼和佈建設定檔與您的 Xamarin.iOS 應用程式

一旦您已建立並安裝適當**佈建設定檔**如所述，通常為 for Mac 或 Visual Studio，在 Visual Studio 中建立解決方案的時間。 健全狀況套件存取可供任何 iOSC#或F#專案。

而不是逐步解說以手動方式建立 Xamarin iOS 8 專案的程序，請開啟範例應用程式連接到這份文件 （其中包含預先建置的分鏡腳本和程式碼）。 您已啟用的健全狀況套件相關聯的範例應用程式**佈建設定檔**，請在**Solution Pad**，以滑鼠右鍵按一下您的專案，並顯示其**選項**對話方塊。 切換至**iOS 應用程式** 面板，然後輸入明確**應用程式識別碼**您先前建立的應用程式**套件組合識別碼**:

[![](healthkit-images/image06.png "輸入明確的應用程式識別碼")](healthkit-images/image06.png#lightbox)

現在請切換到**iOS 套件組合簽署**面板。 您最近安裝**佈建設定檔**，而其關聯為明確**應用程式識別碼**，就會變成可**佈建設定檔**:

[![](healthkit-images/image07.png "選取 佈建設定檔")](healthkit-images/image07.png#lightbox)

如果**佈建設定檔**沒有，請仔細檢查**套件組合識別碼**中**iOS 應用程式**面板中指定與**iOS開發人員中心**且**佈建設定檔**已安裝 (**Xcode > 喜好設定 > 帳戶 > 檢視詳細資料...**).

當健全狀況套件啟用**佈建設定檔**是選取狀態，按一下 **[確定]** 以關閉 [專案選項] 對話方塊。

### <a name="entitlementsplist-and-infoplist-values"></a>Entitlements.plist 和 Info.plist 值

範例應用程式包含`Entitlements.plist`檔案 （此為必要的啟用應用程式的健全狀況套件），並不包含在每個專案範本。 如果您的專案不包含權利，以滑鼠右鍵按一下您的專案中，選擇**檔案 > 新增檔案 > iOS > Entitlements.plist**以手動方式新增一個。

最後，您`Entitlements.plist`必須具有下列索引鍵和值組：

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

同樣地，`Info.plist`應用程式必須具有值`healthkit`聯`UIRequiredDeviceCapabilities`機碼：

```xml
<key>UIRequiredDeviceCapabilities</key>
<array>
<string>armv7</string>
    <string>healthkit</string>
</array>

```

本文提供範例應用程式包含預先設定`Entitlements.plist`，包含所有必要的金鑰。

<a name="programming" />

## <a name="programming-health-kit"></a>程式設計健全狀況套件

健全狀況套件資料存放區是在應用程式之間共用的私用、 使用者特定資料存放區。 健全狀況資訊十分敏感，因為使用者必須採取正數的步驟，以允許資料存取。 這種存取可以是 partial (寫入但無法讀取、 存取某些類型的資料，而不是其他，等等)，隨時都可能會遭撤銷。 健全狀況套件應用程式應該以謹慎的方式，撰寫，了解許多使用者會遲疑儲存其健康狀態相關資訊。

健全狀況套件資料僅限於 Apple 指定型別。 這些類型嚴格定義： 部分，例如捐血類型受限於特定列舉之值的 Apple 提供，而其他人將量級結合 （例如字母組、 卡路里和公升） 的度量單位。 甚至是資料共用相容的量值單位的區別在於他們`HKObjectType`; 比方說，型別系統將會攔截錯誤的嘗試儲存`HKQuantityTypeIdentifier.NumberOfTimesFallen`值欄位應`HKQuantityTypeIdentifier.FlightsClimbed`即使兩者都使用` HKUnit.Count`測量單位。

類型，可存放健全狀況套件資料存放區中的所有子類別的`HKObjectType`。 `HKCharacteristicType` 物件會儲存生物性別、 捐血型別，以及的出生日期。 不過，是較常見`HKSampleType`物件，代表在特定時間或經過一段時間進行取樣的資料。 

[![](healthkit-images/image08.png "HKSampleType 物件圖表")](healthkit-images/image08.png#lightbox)

`HKSampleType` 是抽象的而且有四個具體子類別。 目前只有一種類型`HKCategoryType`是睡眠分析的資料。 大多數的健全狀況套件中的資料都屬於型別`HKQuantityType`並將其資料在儲存`HKQuantitySample`使用熟悉的 Factory 設計模式所建立的物件：

[![](healthkit-images/image09.png "大多數的健全狀況套件中的資料類型 HKQuantityType，並將其資料儲存在 HKQuantitySample 物件")](healthkit-images/image09.png#lightbox)

`HKQuantityType` 類型的範圍從`HKQuantityTypeIdentifier.ActiveEnergyBurned`至`HKQuantityTypeIdentifier.StepCount`。 

<a name="requesting-permission" />

### <a name="requesting-permission-from-the-user"></a>從使用者要求權限

使用者必須採取正數的步驟，以允許讀取或寫入套件健全狀況資料的應用程式。 這是透過已預先安裝在 iOS 8 裝置的健全狀況應用程式。 第一次執行的健全狀況套件應用程式時，使用者會看見系統控制**健全狀況存取**對話方塊：

[![](healthkit-images/image10.png "使用者會看見系統控制健全狀況的存取 對話方塊")](healthkit-images/image10.png#lightbox)

更新版本中，使用者可以變更使用狀況應用程式的權限**來源**對話方塊：

[![](healthkit-images/image11.png "使用者可以變更權限使用健全狀況的應用程式來源 對話方塊")](healthkit-images/image11.png#lightbox)

健全狀況資訊是非常機密，因為應用程式開發人員應該撰寫其程式以謹慎的方式，並預期情況下將會拒絕權限和應用程式執行時變更。 最常見慣用語是要求中的權限`UIApplicationDelegate.OnActivated`方法，然後修改為適當的使用者介面。

### <a name="permissions-walkthrough"></a>逐步解說中的權限

在健全狀況套件佈建專案中，開啟`AppDelegate.cs`檔案。 請注意陳述式使用`HealthKit`; 在檔案頂端。


下列程式碼相關項目健全狀況套件的權限：

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

所有這些方法中的程式碼可以完成內嵌`OnActivated`，但範例應用程式使用不同的方法，將其意圖更清楚：`ValidateAuthorization()`具有特定類型寫入 （並讀取，視應用程式） 來要求存取所需的步驟和`ReactToHealthCarePermissions()`回呼，就啟動之後使用者已產生互動 Health.app 中的 [權限] 對話方塊。

作業`ValidateAuthorization()`是建置一組`HKObjectTypes`應用程式會撰寫，並要求授權，無法更新該資料。 範例應用程式中`HKObjectType`金鑰是`KHQuantityTypeIdentifierKey.HeartRate`。 這個類型會加入至集合`typesToWrite`，而集合`typesToRead`留空。 這些設定，並參考`ReactToHealthCarePermissions()`回呼，會傳遞至`HKHealthStore.RequestAuthorizationToShare()`。

`ReactToHealthCarePermissions()`會呼叫回呼之後使用者已產生互動的權限 對話方塊，並傳遞兩項資訊,：`bool`值，將會是`true`如果使用者已產生互動的權限對話方塊及`NSError`，如果不是 null，表示的某種與呈現的權限對話方塊相關聯的錯誤。

> [!IMPORTANT]
> 若要清楚了解此函式的引數：_成功_並_錯誤_參數並不表示使用者是否已授與存取健康情況套件資料的權限 ！ 它們只是表示使用者有已指定允許存取資料的機會。

若要確認應用程式是否具有存取資料，`HKHealthStore.GetAuthorizationStatus()`使用時，傳入`HKQuantityTypeIdentifierKey.HeartRate`。 根據傳回的狀態，應用程式啟用或停用輸入資料的能力。 尚無標準使用者體驗，來處理拒絕存取而且有許多可能的選項。 在範例應用程式上設定狀態`HeartRateModel`單一物件，會引發相關事件。

## <a name="model-view-and-controller"></a>模型、 檢視和控制器

若要檢閱`HeartRateModel`單一物件，開啟`HeartRateModel.cs`檔案：

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

第一個區段是用於建立泛型事件和處理常式的未定案程式碼。 初始部分`HeartRateModel`類別也是用於建立安全執行緒的單一物件的未定案。

然後， `HeartRateModel` 3 個事件公開 （expose): 

- `EnabledChanged` -指出核心速率儲存體已啟用或停用 （請注意，儲存體一開始會停用）。 
- `ErrorMessageChanged` -對於此範例應用程式中，我們有非常簡單的錯誤處理模型： 最後一個錯誤的字串。 
- `HeartRateStored` -核心速率儲存在健全狀況套件資料庫時引發。

請注意，每當觸發這些事件，它會透過`NSObject.InvokeOnMainThread()`，可讓訂閱者來更新 UI。 或者，事件無法記錄為背景執行緒上引發，並確保相容性的責任可能保留至其處理常式。 執行緒考量是健全狀況套件應用程式中很重要，因為許多函式，例如權限要求，都是非同步，而且在非主要執行緒上執行其回呼。

中的健全狀況套件特定程式碼`HeartRateModel`兩個函式中`HeartRateInBeatsPerMinute()`和`StoreHeartRate()`。 

`HeartRateInBeatsPerMinute()` 將其引數轉換成強類型的健全狀況套件`HKQuantity`。 數量的型別是由指定`HKQuantityTypeIdentifierKey.HeartRate`的數量，單位也`HKUnit.Count`除以`HKUnit.Minute`(亦即，已包含單位*每分鐘節拍數*)。 

`StoreHeartRate()`函式會接受`HKQuantity`(在範例應用程式中，建立一個由`HeartRateInBeatsPerMinute()`)。 若要驗證其資料，它會使用`HKQuantity.IsCompatible()`方法，以傳回`true`如果物件的單位可以轉換為引數中的單位。 如果您建立的數量時`HeartRateInBeatsPerMinute()`這很明顯地會傳回`true`，但它也會傳回`true`數量已建立為，比方說，如果*勝過每小時*。 通常，`HKQuantity.IsCompatible()`可用於驗證大量，距離和能源的使用者或裝置可能會輸入或顯示的度量 （例如英制單位） 的一個系統中，但其可能儲存於另一個系統 （例如度量單位）。 

已驗證的相容性的數量，一旦`HKQuantitySample.FromType()`factory 方法來建立強型別`heartRateSample`物件。 `HKSample` 物件有開始和結束的日期，針對即時讀數，這些值應該是相同，因為它們位於此範例。 此範例也不會設定任何索引鍵 / 值的資料其`HKMetadata`引數，但無法使用程式碼，如下列程式碼來指定感應器位置：

```csharp
var hkm = new HKMetadata();
hkm.HeartRateSensorLocation = HKHeartRateSensorLocation.Chest;

```

一次`heartRateSample`已建立，程式碼會建立新的資料庫連接使用區塊。 在該區塊中，內`HKHealthStore.SaveObject()`方法會嘗試對資料庫的非同步寫入。 Lambda 運算式的結果呼叫觸發程序相關的事件，請`HeartRateStored`或`ErrorMessageChanged`。

既然已程式設計模型，就可以看到控制器如何反射模型狀態。 開啟`HKWorkViewController.cs`檔案。 建構函式直接連接`HeartRateModel`單一事件處理方法 （同樣地，進行內嵌的 lambda 運算式，但不同的方法，讓目的更明顯）：

```csharp
public HKWorkViewController (IntPtr handle) : base (handle)
{
     HeartRateModel.Instance.EnabledChanged += OnEnabledChanged;
     HeartRateModel.Instance.ErrorMessageChanged += OnErrorMessageChanged;
     HeartRateModel.Instance.HeartRateStored += OnHeartBeatStored;
}

```

以下是相關的處理常式：

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

很明顯地，在具有單一控制器的應用程式，有可能避免個別的模型物件的建立和使用事件的控制流程，但模型物件的使用是較適用於真實世界應用程式。

## <a name="running-the-sample-app"></a>執行範例應用程式

IOS 模擬器不支援健全狀況的套件。 執行 iOS 8 的實體裝置上必須執行偵錯。

將正確佈建的 iOS 8 開發裝置連接到您的系統。 選取它做為部署目標，在 Visual Studio for Mac，然後從功能表中選擇**執行 > 偵錯**。

> [!IMPORTANT]
> 佈建與相關的錯誤會在此時會出現。 若要針對錯誤進行疑難排解，檢閱 「 建立和佈建上述的健全狀況套件應用程式 」 一節。 元件如下： 
>
> - **iOS 開發人員中心**-明確的應用程式識別碼] & [健全狀況套件啟用佈建設定檔。 
> - **專案選項**-套件組合識別碼 （明確的應用程式識別碼） 和佈建設定檔。
> - **原始碼**-Entitlements.plist & Info.plist

假設已正確設定佈建，會啟動您的應用程式。 當它到達其`OnActivated`方法，它會要求健康情況套件授權。 這作業系統中，所遇到的第一次您的使用者將會顯示下列對話方塊：


[![](healthkit-images/image12.png "使用者會看到這個對話方塊")](healthkit-images/image12.png#lightbox)

啟用您的應用程式更新核心速率的資料，您的應用程式將會重新出現。 `ReactToHealthCarePermissions`回呼便會以非同步方式啟動。 這會導致`HeartRateModel’s``Enabled`屬性設為變更，這將會引發`EnabledChanged`事件，這會導致`HKPermissionsViewController.OnEnabledChanged()`若要執行，可讓事件處理常式`StoreData` 按鈕。 下圖顯示的順序：


[![](healthkit-images/image13.png "下圖顯示事件的順序")](healthkit-images/image13.png#lightbox)

按下**記錄** 按鈕。 這會導致`StoreData_TouchUpInside()`處理常式執行時，這將會嘗試剖析的值`heartRate`文字欄位中，轉換成`HKQuantity`透過先前所述`HeartRateModel.HeartRateInBeatsPerMinute()`函式，並傳遞至該數量`HeartRateModel.StoreHeartRate()`。 如先前所述，這會嘗試儲存資料，並會引發`HeartRateStored`或`ErrorMessageChanged`事件。

按兩下**首頁**您裝置上的按鈕，然後開啟健全狀況的應用程式。 按一下 **來源**索引標籤，您會看到列出的範例應用程式。 選擇它並不允許更新核心比率資料的權限。 按兩下**首頁**按鈕並切換回您的應用程式。 同樣地，`ReactToHealthCarePermissions()`將會呼叫，但這次，因為存取被拒， **StoreData**按鈕將會變成停用 （請注意，這在以非同步方式進行，並在使用者介面中的變更可能會顯示給使用者）。

## <a name="advanced-topics"></a>進階主題

讀取資料從健全狀況套件資料庫是非常類似於將資料寫入： 其中一個指定的其中一個嘗試存取，要求授權的資料類型，授與該授權時，資料是否可用，且自動轉換為相容單位量值。

有數個更複雜的查詢函數可讓述詞為基礎的查詢和更新相關資料時，執行更新的查詢。 

健全狀況套件應用程式開發人員應檢閱的健全狀況套件一節 Apple[應用程式檢閱的指導方針](https://developer.apple.com/app-store/review/guidelines/#healthkit)。

一旦可了解安全性和型別系統模型、 儲存和讀取共用的健全狀況套件資料庫中的資料是很簡單。 許多健康套件內的函式以非同步方式運作，應用程式開發人員必須適當地撰寫其程式。

在撰寫這篇文章，目前在 Android 或 Windows Phone 的健全狀況套件沒有對等用法。

## <a name="summary"></a>總結

在本文中我們已了解健全狀況套件如何讓應用程式來儲存、 擷取、 以及共用健全狀況相關的資訊，同時也提供標準的健康狀態應用程式，可讓使用者存取及控制這項資料。 

我們也看到了如何隱私權、 安全性和資料的完整性會覆寫的健全狀況的相關資訊的考量，並使用健全狀況套件的應用程式必須處理增加的複雜性，在應用程式管理層面 （佈建），撰寫程式碼 （健全狀況的套件類型系統），和使用者體驗 （透過系統對話方塊和健全狀況的應用程式的權限的使用者控制項）。 

最後，我們看看使用可寫入的健全狀況套件存放區中的活動訊號資料，以及具有非同步感知的設計包含的範例應用程式的健全狀況套件的簡單實作。

## <a name="related-links"></a>相關連結

- [HKWork （範例）](https://developer.xamarin.com/samples/monotouch/ios8/IntroToHealthKit/)
- [iOS 8 簡介](~/ios/platform/introduction-to-ios8.md)
