---
title: HealthKit
description: "HealthKit 是 iOS 8 的健全狀況的相關資訊提供集中式、 協調，且安全的資料存放區中導入的架構。 作業系統可確保隱私權和安全性的健全狀況資訊和健全狀況應用程式時，使用者的儀表板。 使用使用者的權限、 應用程式可以讀取並寫入各式各樣的健全狀況資訊。"
ms.topic: article
ms.prod: xamarin
ms.assetid: E3927A21-507C-43BA-A2AD-957716BA9B52
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/19/2017
ms.openlocfilehash: e7075b67db94b6bf603bd96c637c9f7724ae1519
ms.sourcegitcommit: 73bd0c7e5f237f0a1be70a6c1384309bb26609d5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/22/2018
---
# <a name="healthkit"></a>HealthKit

_HealthKit 是 iOS 8 的健全狀況的相關資訊提供集中式、 協調，且安全的資料存放區中導入的架構。作業系統可確保隱私權和安全性的健全狀況資訊和健全狀況應用程式時，使用者的儀表板。使用使用者的權限、 應用程式可以讀取並寫入各式各樣的健全狀況資訊。_

健全狀況套件提供使用者的健全狀況的相關資訊的安全資料存放區。 健全狀況套件應用程式，使用使用者的明確權限、 讀取和寫入這個資料存放區並加入相關的資料時收到通知。 應用程式可以呈現資料，或使用者可以使用 Apple 的提供健全狀況應用程式，以檢視其所有資料的儀表板。

由於健全狀況相關的資料是如此敏感性及具決定性，健全狀況套件強型別，與量值和明確的 （比方說，捐血 glucose 層級或核心的比率） 記錄的資訊類型與相關聯的單位。 此外，健全狀況套件的應用程式必須使用明確的權利，必須要求的存取權的特定類型的資訊，且使用者必須明確授與應用程式存取這些類型的資料。

本文將介紹：

- 健全狀況套件的安全性需求，包括應用程式佈建，且要求使用者的權限存取套件健全狀況資料庫;
- 健全狀況套件的型別系統的正確套用或解譯資料; 可能性降到最低
- 寫入至共用的全系統健全狀況套件資料存放區。

本文會包含更進階的主題，例如查詢資料庫、 量值單位之間的轉換或接收新資料的通知。

在本文中，我們將建立範例應用程式，來記錄使用者的核心率：

[![](healthkit-images/image01.png "範例應用程式記錄的使用者核心比率")](healthkit-images/image01.png#lightbox)

## <a name="requirements"></a>需求

下列完成所需呈現在本文中的步驟：

- **Xcode 7 和 iOS 8 （或以上）** – Apple 的最新安裝 Xcode 和 iOS 應用程式開發介面需要安裝和設定開發人員的電腦上。
- **Visual Studio for Mac 或 Visual Studio** – 應安裝及設定開發人員的電腦上最新版本的 Visual Studio for Mac。
- **iOS 8 （或更高） 的裝置**– 8 或更高的測試執行 iOS 的最新版本的 iOS 裝置。

> [!IMPORTANT]
> IOS 8 中引進了健全狀況的套件。 目前健全狀況套件並不適用於在 iOS 模擬器中，而偵錯需要連線至實體 iOS 裝置。




## <a name="creating-and-provisioning-a-health-kit-app"></a>建立並佈建健全狀況套件應用程式
Xamarin iOS 8 應用程式可以使用 HealthKit API 之前，必須正確設定及佈建。 本節將討論正確設定您的 Xamarin 應用程式所需的步驟。

健全狀況套件應用程式需要：

- 明確**應用程式識別碼**。
- A**佈建設定檔**與相關聯的明確**應用程式識別碼**與**健全狀況套件**權限。
- `Entitlements.plist`與`com.apple.developer.healthkit`型別的屬性`Boolean`設`Yes`。
- `Info.plist`其`UIRequiredDeviceCapabilities`索引鍵包含一個項目具有`String`值`healthkit`。
- `Info.plist`也必須擁有適當的隱私權說明項目：`String`金鑰說明`NSHealthUpdateUsageDescription`如果應用程式即將寫入資料和`String`金鑰說明`NSHealthShareUsageDescription`如果應用程式要讀取健全狀況套件資料。

若要進一步了解佈建 iOS 應用程式，[裝置佈建](~/ios/get-started/installation/device-provisioning/index.md)在 Xamarin 的發行項**入門**系列說明開發人員憑證，應用程式識別碼之間的關聯性佈建設定檔，以及應用程式權利。

<a name="explicit-appid" />

### <a name="explicit-app-id-and-provisioning-profile"></a>明確的應用程式識別碼和佈建設定檔

明確建立**應用程式識別碼**和適當**佈建設定檔**Apple 中進行[iOS 開發人員中心](https://developer.apple.com/devcenter/ios/index.action)。 

您目前**應用程式識別碼**中所列[憑證、 識別項與設定檔](https://developer.apple.com/account/ios/identifiers/bundle/bundleList.action)開發人員中心的區段。 通常，這份清單會顯示**識別碼**值`*`，這表示，**應用程式識別碼** - **名稱**可以搭配任何數目的尾碼。 這類*萬用字元應用程式識別碼*不能與健全狀況的套件。
 
若要建立明確**應用程式識別碼**，按一下   **+** 按鈕右上方帶您前往**註冊 iOS 應用程式識別碼**頁面：


[![](healthkit-images/image02.png "註冊 Apple 開發人員入口網站上的應用程式")](healthkit-images/image02.png#lightbox)

中所示的影像上方，建立應用程式描述之後，使用**明確的應用程式識別碼**區段來建立您的應用程式的識別碼。 在**應用程式服務**區段中，按一下**健全狀況套件**中**啟用服務**> 一節。

當您完成之後時，請按**繼續**按鈕，以登錄**應用程式識別碼**在您的帳戶。 您將帶回至**憑證、 識別碼和設定檔**頁面。 按一下**佈建的設定檔**，讓您的目前佈建設定檔清單，並按一下 **+** 帶您前往右上角的按鈕**新增 iOS佈建設定檔**頁面。 選取**iOS 應用程式開發**選項，然後按一下**繼續**前往**選取應用程式識別碼**頁面。 在這裡，選取 明確**應用程式識別碼**您先前指定：


[![](healthkit-images/image03.png "選取明確的應用程式識別碼")](healthkit-images/image03.png#lightbox)

按一下**繼續**與工作其餘的畫面，您將在其中指定您**開發人員憑證**，**裝置**，和**名稱**這個**佈建設定檔**:

[![](healthkit-images/image04.png "產生佈建設定檔")](healthkit-images/image04.png#lightbox)

按一下**產生**和 await 建立您的設定檔。 下載檔案，然後按兩下它以安裝在 Xcode 中。 您可以確認它的安裝下**Xcode > 喜好設定 > 帳戶 > 檢視詳細資料...** 您應該會看到您剛安裝佈建設定檔，而且應該有圖示的健全狀況套件及任何其他特殊服務其**權利**資料列：

[![](healthkit-images/image05.png "在 Xcode 中檢視設定檔")](healthkit-images/image05.png#lightbox)

<a name="associating-appid" />

### <a name="associating-the-app-id-and-provisioning-profile-with-your-xamarinios-app"></a>建立關聯的應用程式識別碼和佈建設定檔 Xamarin.iOS 應用程式

一旦您建立並安裝適當**佈建設定檔**如所述，它通常就是在針對 Mac 或 Visual Studio 的 Visual Studio 中建立方案的時間。 健全狀況套件存取權可供任何 iOS C# 或 F # 專案。

而非逐步解說以手動方式建立 Xamarin iOS 8 專案的程序，請開啟範例應用程式附加至這份文件 （其中包含預先建立的分鏡腳本和程式碼）。 範例應用程式與您啟用的健全狀況套件**佈建設定檔**，請在**方案板**，以滑鼠右鍵按一下您的專案，並顯示其**選項**對話方塊。 切換至**iOS 應用程式** 面板，並輸入明確**應用程式識別碼**您先前建立的應用程式為**配套識別碼**:

[![](healthkit-images/image06.png "輸入明確的應用程式識別碼")](healthkit-images/image06.png#lightbox)

現在請切換到**iOS 套件組合簽署**面板。 您最近若曾安裝**佈建設定檔**，與它關聯到明確**應用程式識別碼**，現在可以為**佈建設定檔**:

[![](healthkit-images/image07.png "選取 佈建設定檔")](healthkit-images/image07.png#lightbox)

如果**佈建設定檔**就無法使用，請仔細檢查**配套識別碼**中**iOS 應用程式**面板中指定與**iOS開發人員中心**而且**佈建設定檔**安裝 (**Xcode > 喜好設定 > 帳戶 > 檢視詳細資料...**).

當健全狀況套件啟用**佈建設定檔**是選取狀態，按一下**確定**關閉專案選項 對話方塊。

### <a name="entitlementsplist-and-infoplist-values"></a>Entitlements.plist 和 Info.plist 值

範例應用程式包含`Entitlements.plist`檔案 （此為必要的啟用應用程式的健全狀況套件），而且未包含在每個專案範本。 如果您的專案不包含權利，以滑鼠右鍵按一下您的專案，請選擇**檔案 > 新檔案 > iOS > Entitlements.plist**手動新增一個。

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

同樣地，`Info.plist`應用程式必須具有值為`healthkit`聯`UIRequiredDeviceCapabilities`機碼：

```xml
<key>UIRequiredDeviceCapabilities</key>
<array>
<string>armv7</string>
    <string>healthkit</string>
</array>

```

本文提供的範例應用程式包括預先設定`Entitlements.plist`，其中包含所有必要的金鑰。

<a name="programming" />

## <a name="programming-health-kit"></a>程式設計健全狀況套件

健全狀況套件資料存放區是在應用程式之間共用的私用的使用者特定資料存放區。 健全狀況資訊非常敏感，因為使用者必須採取正數的步驟，以允許資料存取。 這種存取可以是 partial (寫入但無法讀取、 針對某些類型的資料而非其他電腦的存取等)，隨時都可能會遭撤銷。 健全狀況套件撰寫應用程式應該是就緒，了解許多使用者會遲疑儲存其健全狀況相關資訊。

健全狀況套件資料僅限於 Apple 指定型別。 這些類型嚴格定義： 部分，例如捐血類型受限於特定列舉值的 Apple 提供，而其他人將量級結合 （例如字母組、 卡路里和公升） 的度量單位。 即使共用相容的量值單位的資料的區別在於其`HKObjectType`; 例如，型別系統將會攔截蓄意的嘗試儲存`HKQuantityTypeIdentifier.NumberOfTimesFallen`值欄位必須是`HKQuantityTypeIdentifier.FlightsClimbed`雖然兩者使用` HKUnit.Count`測量單位。

健全狀況套件資料存放區可存放類型是所有的子類別`HKObjectType`。 `HKCharacteristicType` 物件會儲存生物性別、 捐血類型和日期的生日。 不過，都更多的常見`HKSampleType`物件，代表在特定時間或經過一段時間進行取樣的資料。 

[![](healthkit-images/image08.png "HKSampleType 物件圖表")](healthkit-images/image08.png#lightbox)

`HKSampleType` 是抽象的四個具體子類別。 目前沒有一種`HKCategoryType`是睡眠分析的資料。 大部分的健全狀況套件中的資料屬於類型`HKQuantityType`和其資料儲存在`HKQuantitySample`使用熟悉的 Factory 設計模式所建立的物件：

[![](healthkit-images/image09.png "大部分的健全狀況套件中的資料類型 HKQuantityType，並將其資料儲存在 HKQuantitySample 物件")](healthkit-images/image09.png#lightbox)

`HKQuantityType` 類型的範圍從`HKQuantityTypeIdentifier.ActiveEnergyBurned`至`HKQuantityTypeIdentifier.StepCount`。 

<a name="requesting-permission" />

### <a name="requesting-permission-from-the-user"></a>從使用者要求的權限

使用者必須採取正數的步驟，以允許應用程式將讀取或寫入健全狀況套件資料。 這是透過健全狀況應用程式隨附預先安裝在 iOS 8 裝置上。 第一次執行健全狀況套件應用程式時，使用者會看見系統控制**健全狀況存取**對話方塊：

[![](healthkit-images/image10.png "使用者會看到一個系統控制健全狀況存取對話方塊")](healthkit-images/image10.png#lightbox)

更新版本中，使用者可以變更使用應用程式健全狀況的權限**來源**對話方塊：

[![](healthkit-images/image11.png "使用者可以變更權限使用健全狀況的應用程式來源對話方塊")](healthkit-images/image11.png#lightbox)

由於健全狀況資訊非常機密，應用程式開發人員應該撰寫其程式就緒，但卻會拒絕權限，並執行應用程式時變更。 要求中的權限是最常見慣用語`UIApplicationDelegate.OnActivated`方法，然後修改為適當的使用者介面。

### <a name="permissions-walkthrough"></a>逐步解說中的權限

在健全狀況套件佈建專案中，開啟`AppDelegate.cs`檔案。 請注意，陳述式使用`HealthKit`; 在檔案頂端。


下列程式碼相關項目健全狀況套件權限：

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

所有這些方法中的程式碼無法完成內嵌`OnActivated`，但範例應用程式使用不同的方法，以讓其更清楚：`ValidateAuthorization()`有的特定類型所撰寫 （和讀取，視應用程式） 來要求存取所需的步驟和`ReactToHealthCarePermissions()`是使用者已互動的權限中的對話方塊 Health.app 後，會啟用的回呼。

作業的`ValidateAuthorization()`是建立一組`HKObjectTypes`會寫入應用程式，並要求授權，無法更新該資料。 範例應用程式，`HKObjectType`代表金鑰`KHQuantityTypeIdentifierKey.HeartRate`。 這個類型會加入至集`typesToWrite`，而組`typesToRead`為空白。 這些設定，以及參考`ReactToHealthCarePermissions()`回呼，會傳遞至`HKHealthStore.RequestAuthorizationToShare()`。

`ReactToHealthCarePermissions()`使用者已互動的權限 對話方塊，並會傳遞兩項資訊之後，就會呼叫回呼：`bool`值將會`true`使用者已互動的權限對話方塊和`NSError`，如果不是 null，表示某種與呈現的權限對話方塊相關聯的錯誤。

> [!IMPORTANT]
> 若要清楚了解這個函式的引數：_成功_和_錯誤_參數不會指出使用者是否已授與權限可以存取健康情況套件資料 ！ 它們只是表示，使用者具有以允許存取資料的機會。

若要確認應用程式是否具有存取資料，`HKHealthStore.GetAuthorizationStatus()`使用時，傳入`HKQuantityTypeIdentifierKey.HeartRate`。 根據傳回的狀態，應用程式啟用或停用輸入資料的能力。 沒有任何標準使用者經驗，來處理拒絕存取，而且有許多可能的選項。 範例應用程式中的狀態設上`HeartRateModel`單一物件，接著，會引發相關事件。

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

第一個區段是用於建立泛型事件和處理常式的未定案程式碼。 初始的部分`HeartRateModel`類別也是建立安全執行緒的單一物件的未定案。

然後，`HeartRateModel`公開 3 事件： 

- `EnabledChanged` -表示核心率的存放裝置已啟用或停用 （請注意，儲存一開始會停用）。 
- `ErrorMessageChanged` -若為此範例應用程式中，我們有非常簡單的錯誤處理模型： 最後一個錯誤的字串。 
- `HeartRateStored` -核心比率會儲存在健全狀況套件資料庫時引發。

請注意，不會引發這些事件，每當它是透過`NSObject.InvokeOnMainThread()`，可讓 「 訂閱者 」 更新 UI。 或者，事件無法記載為在背景執行緒上引發，並確保相容性的責任，可以保留至其處理常式。 因為許多函式，例如，在權限要求都是非同步，而且在非主要執行緒上執行其回呼，是很重要的健全狀況套件的應用程式執行緒考量。

中的健全狀況套件特定程式碼`HeartRateModel`中兩個函式`HeartRateInBeatsPerMinute()`和`StoreHeartRate()`。 

`HeartRateInBeatsPerMinute()` 將其引數轉換成強類型的健全狀況套件`HKQuantity`。 數量的型別是由指定`HKQuantityTypeIdentifierKey.HeartRate`和單位的數量為`HKUnit.Count`除以`HKUnit.Minute`(換句話說，單位是*每分鐘節拍*)。 

`StoreHeartRate()`函式接受`HKQuantity`(範例應用程式中建立一個由`HeartRateInBeatsPerMinute()`)。 若要驗證其資料，它會使用`HKQuantity.IsCompatible()`方法，這個方法會傳回`true`如果物件的單位可以轉換成引數中的單位。 如果使用的 quantity 建立`HeartRateInBeatsPerMinute()`這會很明顯地傳回`true`，但它也會傳回`true`數量已建立為，比方說，如果*勝過每小時*。 通常，`HKQuantity.IsCompatible()`可以用來驗證大型，距離及能源使用者或裝置可能會輸入或顯示的度量 （例如英制單位） 的一個系統中，但其可能儲存於另一個系統 （例如度量單位）。 

一旦已驗證的相容性的數量， `HKQuantitySample.FromType()` factory 方法，用來建立強型別`heartRateSample`物件。 `HKSample` 物件有開始和結束日期。瞬間讀數，這些值應該相同，因為它們位於此範例。 此範例也不會設定任何索引鍵 / 值的資料其`HKMetadata`引數，但有一位無法使用程式碼，例如下列程式碼來指定感應器位置：

```csharp
var hkm = new HKMetadata();
hkm.HeartRateSensorLocation = HKHeartRateSensorLocation.Chest;

```

一次`heartRateSample`已建立，程式碼會建立新的資料庫連接使用區塊。 在該區塊中，內`HKHealthStore.SaveObject()`方法會嘗試對資料庫的非同步寫入。 Lambda 運算式的結果呼叫觸發程序相關的事件，或是`HeartRateStored`或`ErrorMessageChanged`。

現在已程式設計模型，就以查看如何控制器反應模型狀態的時間。 開啟 `HKWorkViewController.cs`檔案。 建構函式直接繫結在一起`HeartRateModel`單一事件處理方法 （同樣地，這無法完成內部的 lambda 運算式，但不同的方法進行意圖更明顯）：

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

顯然與單一控制器應用程式中，有可能避免建立個別的模型物件和控制流程中，使用事件，但模型物件的使用更適用於真實世界應用程式。

## <a name="running-the-sample-app"></a>執行範例應用程式

IOS 模擬器不支援套件健全狀況。 必須執行 iOS 8 的實體裝置上進行偵錯。

將正確佈建 iOS 8 開發裝置連接到您的系統。 在適用於 Mac 做為部署目標，在 Visual Studio 中選取它，然後從功能表選擇**執行 > 偵錯**。

> [!IMPORTANT]
> 佈建與相關的錯誤會在此時介面。 若要解決錯誤，檢閱建立和佈建的應用程式健全狀況套件上面。 元件如下： 
>
> - **iOS 開發人員中心**-明確的應用程式識別碼和健全狀況套件啟用佈建設定檔。 
> - **專案選項**-組合識別碼 (明確應用程式 ID) 和佈建設定檔。
> - **原始碼**-Entitlements.plist & Info.plist

假設已正確設定佈建，您的應用程式將會啟動。 當到達其`OnActivated`方法，它會要求健康情況套件授權。 這作業系統中，所遇到的第一次您的使用者會看到下列對話方塊：


[![](healthkit-images/image12.png "使用者會看到這個對話方塊")](healthkit-images/image12.png#lightbox)

啟用您的應用程式更新核心比率資料和應用程式將會重新顯示。 `ReactToHealthCarePermissions`回呼將會以非同步方式啟動。 這會導致`HeartRateModel’s``Enabled`屬性設為變更，這將會引發`EnabledChanged`事件，這會導致`HKPermissionsViewController.OnEnabledChanged()`事件處理常式來執行，這可讓`StoreData` 按鈕。 下圖顯示序列：


[![](healthkit-images/image13.png "這個圖表可顯示事件的順序")](healthkit-images/image13.png#lightbox)

按**記錄** 按鈕。 這會導致`StoreData_TouchUpInside()`處理常式執行時，這將會嘗試將值的剖析`heartRate`文字欄位中，轉換成`HKQuantity`透過先前討論`HeartRateModel.HeartRateInBeatsPerMinute()`函式，並傳遞至該數量`HeartRateModel.StoreHeartRate()`。 如先前所討論，這將會嘗試將資料儲存，將會引發`HeartRateStored`或`ErrorMessageChanged`事件。

按兩下**首頁**您的裝置上的按鈕，然後開啟健全狀況的應用程式。 按一下**來源** 索引標籤，您會看到列出範例應用程式。 選擇它，而不允許更新核心比率資料的權限。 按兩下**首頁**按鈕並切換回您的應用程式。 同樣地，`ReactToHealthCarePermissions()`會被呼叫，但這次，因為存取被拒， **StoreData**按鈕將會變成停用 （請注意，這會以非同步方式，而且使用者介面中的變更可能會對使用者顯示）。

## <a name="advanced-topics"></a>進階主題

讀取資料從健全狀況套件資料庫是非常類似於將資料寫入： 其中一個指定的其中一個正在嘗試存取，要求授權的資料類型，並授與該授權時，資料是否可用，會自動轉換為相容的單位量值。

有一些更複雜的查詢函數可讓述詞為基礎的查詢和更新相關的資料時執行更新的查詢。 

健全狀況套件的應用程式的開發人員應檢閱 Apple 的健全狀況套件區段[應用程式檢閱指導方針](https://developer.apple.com/app-store/review/guidelines/#healthkit)。

一旦可了解安全性和型別系統模型、 儲存和讀取共用健全狀況套件資料庫中的資料是很簡單。 健全狀況套件內的函式的許多以非同步方式運作，應用程式開發人員必須適當地撰寫程式。

這篇文章撰寫，目前在 Android 或 Windows Phone 的健全狀況套件沒有對等項目。

## <a name="summary"></a>總結

在本文，我們已看到如何健全狀況套件可讓應用程式存放區中擷取、 以及共用健全狀況相關的資訊，同時也提供標準的健全狀況應用程式，可讓使用者存取，以及控制這項資料。 

我們也已經看到如何隱私權、 安全性和資料的完整性會覆寫健全狀況的相關資訊的考量和應用程式使用健全狀況套件必須處理增加的應用程式的管理層面 （佈建） （健康情況的套件類型撰寫程式碼的複雜度系統） 和使用者經驗 （透過系統對話方塊和健全狀況的應用程式的權限的使用者控制項）。 

最後，我們將討論使用隨附的範例應用程式活動訊號資料寫入的健全狀況套件存放區且具有非同步感知設計的彙總健全狀況套件的簡單實作。

## <a name="related-links"></a>相關連結

- [HKWork （範例）](https://developer.xamarin.com/samples/monotouch/ios8/IntroToHealthKit/)
- [iOS 8 簡介](~/ios/platform/introduction-to-ios8.md)
