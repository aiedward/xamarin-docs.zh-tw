---
title: 在 Xamarin 中 HealthKit
description: 本檔說明 HealthKit, 這是 iOS 8 中引進的架構, 可為健康相關的資訊提供集中、協調及安全的資料存放區。 其中討論如何布建 HealthKit 應用程式, 以及如何撰寫使用 HealthKit 架構的程式碼。
ms.prod: xamarin
ms.assetid: E3927A21-507C-43BA-A2AD-957716BA9B52
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/19/2017
ms.openlocfilehash: 245c406a1b62b454ff9935ebb7cd3531a624aea3
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2019
ms.locfileid: "68656591"
---
# <a name="healthkit-in-xamarinios"></a>在 Xamarin 中 HealthKit

健康情況套件為使用者的健康狀態相關資訊提供安全的資料存放區。 健康情況套件應用程式可能會有使用者的明確許可權、讀取和寫入此資料存放區, 並在新增相關資料時接收通知。 應用程式可以呈現資料, 或使用者可以使用 Apple 提供的健康情況應用程式來查看其所有資料的儀表板。

由於健全狀況相關資料十分敏感且非常重要, 因此健康情況套件是強型別, 並具有測量單位與所記錄之資訊類型的明確關聯 (例如, 血糖層級或心形速率)。 此外, 健康情況套件應用程式必須使用明確權利、必須要求存取特定類型的資訊, 而且使用者必須明確授與應用程式存取這些類型的資料。

本文將介紹:

- 健康情況套件的安全性需求, 包括應用程式布建和要求使用者存取健康情況套件資料庫的許可權;
- 健康情況套件的類型系統, 可將錯誤套用或錯誤解譯資料的可能性降到最低;
- 寫入共用、全系統的健全狀況套件資料存放區。

本文不會涵蓋更多的進一步主題, 例如查詢資料庫、在測量單位之間轉換, 或接收新資料的通知。

在本文中, 我們將建立範例應用程式來記錄使用者的核心速度:

[![](healthkit-images/image01.png "記錄使用者核心速率的範例應用程式")](healthkit-images/image01.png#lightbox)

## <a name="requirements"></a>需求

需要下列專案, 才能完成本文中顯示的步驟:

- **Xcode 7 和 ios 8 (或更新版本)** –必須在開發人員的電腦上安裝及設定 Apple 的最新 Xcode 和 ios api。
- **Visual Studio for Mac 或 Visual Studio** –應該在開發人員的電腦上安裝並設定最新版本的 Visual Studio for Mac。
- **ios 8 (或更大) 裝置**–執行最新版 ios 8 或更新版本的 ios 裝置以進行測試。

> [!IMPORTANT]
> 健康情況套件是在 iOS 8 中引進。 目前, 健康情況套件無法在 iOS 模擬器上使用, 而且需要與實體 iOS 裝置的連線進行調試。




## <a name="creating-and-provisioning-a-health-kit-app"></a>建立和布建健康情況套件應用程式
在 Xamarin iOS 8 應用程式可以使用 HealthKit API 之前, 必須妥善設定和布建。 本節將涵蓋正確設定 Xamarin 應用程式所需的步驟。

健康情況套件應用程式需要:

- 明確的**應用程式識別碼**。
- 與該明確**應用程式識別碼**和**健全狀況套件**許可權相關聯的布建**設定檔**。
- , 其`Boolean`屬性的類型設定為`Yes`。 `com.apple.developer.healthkit` `Entitlements.plist`
- `String` `healthkit`, 其索引`UIRequiredDeviceCapabilities`鍵包含具有值的專案。 `Info.plist`
- `NSHealthShareUsageDescription` `String` `NSHealthUpdateUsageDescription` `String`也必須有適當的隱私權說明專案: 如果應用程式即將寫入資料時的金鑰說明, 以及應用程式即將讀取健康情況套件時的金鑰說明`Info.plist`data.

如需有關布建 iOS 應用程式的詳細資訊, Xamarin 的**消費者入門**系列中的[裝置](~/ios/get-started/installation/device-provisioning/index.md)布建文章會說明開發人員憑證、應用程式識別碼、布建設定檔和應用程式權利之間的關聯性。

<a name="explicit-appid" />

### <a name="explicit-app-id-and-provisioning-profile"></a>明確的應用程式識別碼和布建設定檔

建立明確的**應用程式識別碼**和適當的布建**設定檔**, 會在 Apple 的[iOS 開發人員中心](https://developer.apple.com/devcenter/ios/index.action)內完成。 

您目前的**應用程式**識別碼會列在開發人員中心的 [[憑證]、[識別碼 & 設定檔](https://developer.apple.com/account/ios/identifiers/bundle/bundleList.action)] 區段內。 通常, 此清單會顯示  的`*`識別碼值, 表示**應用程式識別碼** - **名稱**可以與任意數目的尾碼搭配使用。 這類*萬用字元應用程式識別碼*無法與健康情況套件搭配使用。
 
若要建立明確 **應用程式識別碼**，按一下 **+** 帶您到右上方中的按鈕 **註冊 iOS 應用程式識別碼** 頁面：


[![](healthkit-images/image02.png "在 Apple 開發人員入口網站上註冊應用程式")](healthkit-images/image02.png#lightbox)

如上圖所示, 在建立應用程式描述之後, 請使用 [**明確應用程式識別碼**] 區段來建立應用程式的識別碼。 在 [**應用程式服務**] 區段中, 勾選 [**啟用服務**] 區段中的 [**健康情況套件**]。

當您完成時, 請按 [**繼續**] 按鈕, 在您的帳戶中註冊**應用程式識別碼**。 您將會回到 [憑證] **、[識別碼] 和 [設定檔**] 頁面。 按一下 **佈建設定檔** 帶您前往您目前的佈建設定檔的清單，然後按一下 **+** 帶您前往右上角的按鈕 **Add iOS佈建設定檔** 頁面。 選取 [ **IOS 應用程式開發**] 選項, 然後按一下 [**繼續**] 以進入 [**選取應用程式識別碼**] 頁面。 在這裡, 選取您先前指定的明確**應用程式識別碼**:


[![](healthkit-images/image03.png "選取明確的應用程式識別碼")](healthkit-images/image03.png#lightbox)

按一下 [**繼續**] 並流覽其餘畫面, 您將在其中指定您的**開發人員憑證**、**裝置**和此布建**設定檔**的**名稱**:

[![](healthkit-images/image04.png "產生布建設定檔")](healthkit-images/image04.png#lightbox)

按一下 [**產生**], 並等待建立您的設定檔。 下載檔案, 然後按兩下以安裝在 Xcode 中。 您可以在**Xcode > 喜好設定 > 帳戶 > 視圖詳細資料** 下確認它是否已安裝。 您應該會看到剛安裝的布建設定檔, 而且其**權利**列中應該有健康情況套件和任何其他特殊服務的圖示:

[![](healthkit-images/image05.png "在 Xcode 中檢視設定檔")](healthkit-images/image05.png#lightbox)

<a name="associating-appid" />

### <a name="associating-the-app-id-and-provisioning-profile-with-your-xamarinios-app"></a>將應用程式識別碼和布建設定檔與您的 Xamarin iOS 應用程式產生關聯

如所述, 建立並安裝適當的布建**設定檔**之後, 通常是在 Visual Studio for Mac 或 Visual Studio 中建立解決方案的時候。 健康情況套件存取適用于任何 iOS C#或F#專案。

請改為開啟附加至本文的範例應用程式 (其中包含預先建立的腳本和程式碼), 而不是手動建立 Xamarin iOS 8 專案的過程。 若要將範例應用程式與已啟用健康情況套件的布建**設定檔**產生關聯, 請在  **Solution Pad**中, 以滑鼠右鍵按一下您的專案, 並顯示其 **選項** 對話方塊。 切換至 [ **IOS 應用程式**] 面板, 並輸入您先前建立的應用程式套件組合**識別碼**的明確**應用程式**識別碼:

[![](healthkit-images/image06.png "輸入明確的應用程式識別碼")](healthkit-images/image06.png#lightbox)

現在, 切換至 [IOS 套件組合**簽署**] 面板。 您最近安裝的布建**設定檔**及其與明確**應用程式識別碼**的關聯, 現在將可作為布建**設定檔**:

[![](healthkit-images/image07.png "選取布建設定檔")](healthkit-images/image07.png#lightbox)

如果無法使用布建**設定檔**, 請在 [ **ios 應用程式**] 面板中再次檢查 [套件組合**識別碼**], 而不是在**ios 開發人員中心**內指定, 並且已安裝布建**設定檔**(**Xcode> 喜好設定 > 帳戶 > 視圖詳細資料 ...** )。

選取 [已啟用健全狀況套件的布建**設定檔**] 時, 按一下 **[確定]** 以關閉 [專案選項] 對話方塊。

### <a name="entitlementsplist-and-infoplist-values"></a>Plist 和 Info. plist 值

範例應用程式包含`Entitlements.plist`檔案 (這是啟用健康套件的應用程式所需的檔案), 不包含在每個專案範本中。 如果您的專案未包含權利, 請以滑鼠右鍵按一下您的專案, 選擇 [檔案] **> [新增檔案 ...]> iOS > plist** , 以手動方式加入一個。

最後, 您`Entitlements.plist`必須具有下列索引鍵和值組:

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

同樣地, `Info.plist`應用程式的`healthkit`值必須具有與`UIRequiredDeviceCapabilities`索引鍵相關聯的值:

```xml
<key>UIRequiredDeviceCapabilities</key>
<array>
<string>armv7</string>
    <string>healthkit</string>
</array>

```

本文所提供的範例應用程式包含預先`Entitlements.plist`設定的, 其中包含所有必要的金鑰。

<a name="programming" />

## <a name="programming-health-kit"></a>程式設計健全狀況套件

健康情況套件資料存放區是私人的使用者特定資料存放區, 可在應用程式之間共用。 由於健全狀況資訊是敏感性的, 因此使用者必須採取正面的步驟來允許資料存取。 這項存取可能是部分 (寫入但不讀取、某些資料類型的存取權, 而不是其他等), 而且可以隨時撤銷。 健康情況套件應用程式應該以依舊撰寫, 撰寫, 並瞭解許多使用者將遲疑儲存其健康狀態相關資訊。

健康情況套件資料僅限於 Apple 指定的類型。 這些類型是嚴格定義的: 某些部分 (例如, 血壓類型) 僅限於 Apple 提供之列舉的特定值, 其他則結合量值與測量單位 (例如, 字母、卡路里和升)。 即使共用相容單位的量值的資料, 也可以透過`HKObjectType`其區分, 例如, 型別系統會攔截到將`HKQuantityTypeIdentifier.NumberOfTimesFallen`值儲存`HKQuantityTypeIdentifier.FlightsClimbed`至預期為的欄位, 但同時使用`HKUnit.Count`測量單位。

健康情況套件資料存放區中的 [類型] 可存放`HKObjectType`是的子類別。 `HKCharacteristicType`物件會儲存生物的性別、血壓類型和出生日期。 不過, 更常見的`HKSampleType`是物件, 代表在特定時間或一段時間內取樣的資料。 

[![](healthkit-images/image08.png "HKSampleType 物件圖表")](healthkit-images/image08.png#lightbox)

`HKSampleType`是抽象的, 而且有四個具體的子類別。 目前只有一種`HKCategoryType`資料類型, 也就是睡眠分析。 健康情況套件中的大部分資料都屬於類型`HKQuantityType` , 並會將其資料儲存在`HKQuantitySample`物件中, 而這些物件是使用熟悉的工廠設計模式所建立:

[![](healthkit-images/image09.png "健康情況套件中的大部分資料都屬於 HKQuantityType 類型, 並將其資料儲存在 HKQuantitySample 物件中")](healthkit-images/image09.png#lightbox)

`HKQuantityType`類型的範圍`HKQuantityTypeIdentifier.ActiveEnergyBurned`從`HKQuantityTypeIdentifier.StepCount`到。 

<a name="requesting-permission" />

### <a name="requesting-permission-from-the-user"></a>正在向使用者要求許可權

終端使用者必須採取正面步驟, 才能讓應用程式讀取或寫入健康情況套件資料。 這是透過預先安裝在 iOS 8 裝置上的健全狀況應用程式來完成。 第一次執行健康情況套件應用程式時, 使用者會看到系統控制的**健全狀況存取**對話方塊:

[![](healthkit-images/image10.png "使用者會看到 [系統控制的健全狀況存取] 對話方塊")](healthkit-images/image10.png#lightbox)

之後, 使用者可以使用健全狀況應用程式的 [**來源**] 對話方塊來變更許可權:

[![](healthkit-images/image11.png "使用者可以使用 [健全狀況應用程式來源] 對話方塊變更許可權")](healthkit-images/image11.png#lightbox)

由於健全狀況資訊非常敏感, 因此應用程式開發人員應該會依舊撰寫, 撰寫其程式, 並預期在應用程式執行時將會拒絕和變更許可權。 最常見的用法是在`UIApplicationDelegate.OnActivated`方法中要求許可權, 然後適當地修改使用者介面。

### <a name="permissions-walkthrough"></a>許可權逐步解說

在您的健康情況套件-已布建`AppDelegate.cs`專案中, 開啟檔案。 請注意, 使用`HealthKit`的語句, 位於檔案的頂端。


下列程式碼與健康情況套件許可權相關:

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

這些方法中的所有程式碼都可以在中以`OnActivated`內嵌方式完成, 但範例應用程式會使用不同的方法, `ValidateAuthorization()`讓其意圖更清楚: 具有必要的步驟來要求對所要寫入之特定類型的存取權 (如果需要應用程式, 則讀取)和`ReactToHealthCarePermissions()`是一種回呼, 會在使用者與健康情況應用程式中的 [許可權] 對話方塊互動之後啟動。

的工作`ValidateAuthorization()`是建立應用程式將寫入的`HKObjectTypes`集合, 並要求授權以更新該資料。 在範例應用程式中, `HKObjectType`是索引鍵`KHQuantityTypeIdentifierKey.HeartRate`的。 此類型會加入至集合`typesToWrite`, 而集合`typesToRead`會保留空白。 這些集合和`ReactToHealthCarePermissions()`回呼的參考會傳遞至`HKHealthStore.RequestAuthorizationToShare()`。

回呼會在使用者與 [許可權] 對話方塊互動並傳遞兩項資訊後呼叫: `true`如果使用者與 [許可權`bool` ] 對話方塊互動, 則會是一個值, 而`ReactToHealthCarePermissions()` `NSError`如果不是 null, 則表示與呈現許可權對話方塊相關聯的某種錯誤。

> [!IMPORTANT]
> 若要清楚瞭解此函式的引數:_成功_和_錯誤_參數不會指出使用者是否已授與存取健康情況套件資料的許可權! 它們只會指出使用者已獲得允許存取資料的機會。

若要確認應用程式是否可存取資料, `HKHealthStore.GetAuthorizationStatus()`請使用, `HKQuantityTypeIdentifierKey.HeartRate`傳入。 根據傳回的狀態, 應用程式會啟用或停用輸入資料的能力。 沒有任何標準使用者經驗可以處理拒絕存取, 而且有許多可能的選項。 在範例應用程式中, 會在`HeartRateModel`單一物件上設定狀態, 而後者會引發相關事件。

## <a name="model-view-and-controller"></a>模型、視圖和控制器

若要檢查`HeartRateModel`單一物件, 請`HeartRateModel.cs`開啟檔案:

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

第一個區段是用來建立一般事件和處理常式的「未定案」程式碼。 `HeartRateModel`類別的初始部分也是用來建立安全線程單一物件的樣板。

接著會`HeartRateModel`公開3個事件: 

- `EnabledChanged`-指出已啟用或停用核心速率存放裝置 (請注意, 一開始會停用儲存體)。 
- `ErrorMessageChanged`-針對此範例應用程式, 我們有一個非常簡單的錯誤處理模型: 包含最後一個錯誤的字串。 
- `HeartRateStored`-當核心速率儲存在健康情況套件資料庫中時引發。

請注意, 每當引發這些事件時, 就會透過來`NSObject.InvokeOnMainThread()`完成, 讓訂閱者可以更新 UI。 或者, 這些事件可能會記錄為在背景執行緒上引發, 並負責確保相容性可以留給其處理常式。 執行緒考慮在健康情況套件應用程式中很重要, 因為許多函式 (例如許可權要求) 都是非同步, 並且會在非主要執行緒上執行其回呼。

中`HeartRateModel`的 Heath 套件特定程式碼位於`HeartRateInBeatsPerMinute()`兩個函式`StoreHeartRate()`和中。 

`HeartRateInBeatsPerMinute()`將其引數轉換為強型別的健康`HKQuantity`情況套件。 數量`HKQuantityTypeIdentifierKey.HeartRate`的類型是由指定, 而`HKUnit.Count`數量的單位是除以`HKUnit.Minute` (換句話說, 單位是*每分鐘的節拍*)。 

函式`HKQuantity`會接受 (在範例應用程式中, 由`HeartRateInBeatsPerMinute()`所建立)。 `StoreHeartRate()` 若要驗證其資料, 它會`HKQuantity.IsCompatible()`使用方法, `true`如果物件的單位可以轉換為引數中的單位, 則會傳回。 如果以這種`HeartRateInBeatsPerMinute()`方式建立的數量很`true`明顯地傳回, `true`但如果建立數量為 (例如,*每小時的節拍*), 它也會傳回。 更常見的`HKQuantity.IsCompatible()`情況是, 可以用來驗證使用者或裝置可能輸入或顯示在一種測量系統中的大量、距離和能源 (例如英制單位), 但可能會儲存在另一個系統中 (例如度量單位)。 

一旦已驗證數量的相容性, `HKQuantitySample.FromType()` factory 方法就會用來建立強`heartRateSample`型別物件。 `HKSample`物件具有開始和結束日期;針對瞬間讀取, 這些值應該相同, 如範例中所示。 此範例也不會在其`HKMetadata`引數中設定任何索引鍵值資料, 而是可以使用程式碼 (例如下列程式碼) 來指定感應器位置:

```csharp
var hkm = new HKMetadata();
hkm.HeartRateSensorLocation = HKHeartRateSensorLocation.Chest;

```

`heartRateSample`一旦建立之後, 程式碼就會使用 using 區塊來建立與資料庫的新連接。 在該區塊內, `HKHealthStore.SaveObject()`方法會嘗試對資料庫進行非同步寫入。 對 lambda 運算式產生的呼叫會觸發相關事件, 也`HeartRateStored`就`ErrorMessageChanged`是或。

既然模型已經過程式設計, 就可以查看控制器如何反映模型的狀態。 `HKWorkViewController.cs`開啟檔案。 此函式只`HeartRateModel`會將 singleton 連線到事件處理方法 (同樣地, 這可能會以 lambda 運算式來內嵌, 但是不同的方法讓意圖變得更明顯):

```csharp
public HKWorkViewController (IntPtr handle) : base (handle)
{
     HeartRateModel.Instance.EnabledChanged += OnEnabledChanged;
     HeartRateModel.Instance.ErrorMessageChanged += OnErrorMessageChanged;
     HeartRateModel.Instance.HeartRateStored += OnHeartBeatStored;
}

```

以下是相關的處理常式:

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

很明顯地, 在具有單一控制器的應用程式中, 可以避免建立個別的模型物件, 並使用事件來控制流程, 但使用模型物件更適合實際的應用程式。

## <a name="running-the-sample-app"></a>執行範例應用程式

IOS 模擬器不支援健康情況套件。 必須在執行 iOS 8 的實體裝置上進行調試。

將已正確布建的 iOS 8 開發裝置附加至您的系統。 在 Visual Studio for Mac 中選取它作為部署目標, 然後從功能表中選擇 [**執行 > Debug**]。

> [!IMPORTANT]
> 此時將會呈現與布建相關的錯誤。 若要對錯誤進行疑難排解, 請參閱上方的建立和布建健康情況套件應用程式一節。 元件包括: 
>
> - **IOS 開發人員中心**-明確的應用程式識別碼 & 健康情況套件已啟用布建設定檔。 
> - **專案選項**-組合識別碼 (明確應用程式識別碼) & 布建設定檔。
> - **原始碼**-Plist & Info. plist

假設布建已正確設定, 您的應用程式就會啟動。 當它到達其`OnActivated`方法時, 它會要求健康情況套件授權。 第一次在作業系統遇到這種情況時, 您的使用者將會看到下列對話方塊:


[![](healthkit-images/image12.png "使用者將會看到此對話方塊")](healthkit-images/image12.png#lightbox)

讓您的應用程式可以更新核心速率資料, 您的應用程式將會重新出現。 `ReactToHealthCarePermissions`回呼會以非同步方式啟動。 `HeartRateModel’s`這會導致`StoreData` `HKPermissionsViewController.OnEnabledChanged()` `EnabledChanged`屬性變更, 這會引發事件, 這會導致事件處理常式執行, 以啟用按鈕。 `Enabled` 下圖顯示順序:


[![](healthkit-images/image13.png "此圖表顯示事件的順序")](healthkit-images/image13.png#lightbox)

按下 [**錄製**] 按鈕。 `StoreData_TouchUpInside()`這會導致處理常式執行, 這將會嘗試剖析`HKQuantity` `heartRate`文字欄位的值、透過先前討論`HeartRateModel.HeartRateInBeatsPerMinute()`的函式轉換成, 並將該數量傳遞給`HeartRateModel.StoreHeartRate()`。 如先前所述, 這會嘗試儲存資料, 並將引發`HeartRateStored`或`ErrorMessageChanged`事件。

按兩下裝置上的 [**首頁**] 按鈕, 然後開啟 [健康情況應用程式]。 按一下 [**來源**] 索引標籤, 您會看到列出的範例應用程式。 選擇它, 並不允許更新核心速率資料的許可權。 按兩下 [**首頁**] 按鈕, 並切換回您的應用程式。 同樣地, `ReactToHealthCarePermissions()`將會呼叫, 但這次, 因為存取被拒, 所以 [ **StoreData** ] 按鈕將會停用 (請注意, 這會以非同步方式發生, 使用者介面中的變更可能會對終端使用者顯示)。

## <a name="advanced-topics"></a>進階主題

從健康情況套件資料庫讀取資料非常類似于寫入資料: 一個指定嘗試存取的資料類型, 要求授權, 如果授與該授權, 則資料可供使用, 並自動轉換成相容的單位量值.

有一些更精密的查詢函式, 可在更新相關資料時, 允許以述詞為基礎的查詢和執行更新的查詢。 

健康情況套件應用程式的開發人員應該參閱 Apple[應用程式審查指導方針](https://developer.apple.com/app-store/review/guidelines/#healthkit)的健全狀況套件一節。

瞭解安全性和類型系統模型之後, 儲存和讀取共用健康套件資料庫中的資料相當簡單。 健康情況套件中的許多函式會以非同步方式運作, 而應用程式開發人員必須適當地撰寫程式。

在撰寫本文時, 目前與 Android 或 Windows Phone 中的健康情況套件並沒有同等的功能。

## <a name="summary"></a>總結

在本文中, 我們已瞭解健全狀況套件如何讓應用程式儲存、抓取和共用健康狀態相關資訊, 同時提供標準的健全狀況應用程式, 讓使用者能夠存取及控制此資料。 

我們也已瞭解隱私權、安全性及資料完整性如何覆寫與健康情況相關的資訊, 以及使用健康情況套件的應用程式, 必須處理應用程式管理方面的複雜性增加 (布建)、編碼 (健全狀況套件的類型系統) 和使用者經驗 (透過系統對話方塊和健全狀況應用程式來控制許可權的使用者)。 

最後, 我們將使用內含的範例應用程式 (可將心跳資料寫入健康情況套件存放區, 並具有非同步感知設計), 來查看健康情況套件的簡單執行。

## <a name="related-links"></a>相關連結

- [HKWork (範例)](https://docs.microsoft.com/samples/xamarin/ios-samples/ios8-introtohealthkit)
- [iOS 8 簡介](~/ios/platform/introduction-to-ios8.md)
