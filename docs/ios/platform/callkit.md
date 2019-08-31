---
title: 在 Xamarin 中 CallKit
description: 本文涵蓋 Apple 在 iOS 10 中發行的新 CallKit API, 以及如何在 Xamarin iOS VOIP 應用程式中執行。
ms.prod: xamarin
ms.assetid: 738A142D-FFD2-4738-B3ED-57C273179848
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/15/2017
ms.openlocfilehash: 92e4cd45a7fe49a7a78a8922bf70ac87870db095
ms.sourcegitcommit: 1e3a0d853669dcc57d5dee0894d325d40c7d8009
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/31/2019
ms.locfileid: "70200411"
---
# <a name="callkit-in-xamarinios"></a>在 Xamarin 中 CallKit

IOS 10 中新的 CallKit API 可讓 VOIP 應用程式與 iPhone UI 整合, 並為使用者提供熟悉的介面和經驗。 有了這個 API, 使用者就可以從 iOS 裝置的鎖定畫面觀看 VOIP 呼叫並與之互動, 並使用電話應用程式的我的最愛和**最近專案**views 來管理連絡人。

## <a name="about-callkit"></a>關於 CallKit

根據 Apple, CallKit 是新的架構, 可將協力廠商 Voice Over IP (VOIP) 應用程式提升為 iOS 10 上的第一方體驗。 CallKit API 可讓 VOIP 應用程式與 iPhone UI 整合, 並為使用者提供熟悉的介面和體驗。 就像內建的電話應用程式一樣, 使用者可以從 iOS 裝置的鎖定畫面觀看 VOIP 電話並與之互動, 並使用電話應用程式的 [我的最愛 **]** 和 [**最近專案**] 視圖來管理連絡人。

此外, CallKit API 可讓您建立應用程式延伸模組, 以便將電話號碼與名稱 (呼叫者識別碼) 建立關聯, 或告知系統應封鎖某個數位 (呼叫封鎖)。

### <a name="the-existing-voip-app-experience"></a>現有的 VOIP 應用程式體驗

在討論新的 CallKit API 和其功能之前, 請先參閱 iOS 9 (和更少) 的協力廠商 VOIP 應用程式的目前使用者體驗, 並使用稱為 MonkeyCall 的虛構 VOIP 應用程式。 MonkeyCall 是一個簡單的應用程式, 可讓使用者使用現有的 iOS Api 傳送和接收 VOIP 呼叫。

目前, 如果使用者在 MonkeyCall 上收到來電, 而其 iPhone 已鎖定, 則鎖定畫面上收到的通知與任何其他類型的通知 (例如, 來自訊息或郵件應用程式的通知) 無法區分。

如果使用者想要接聽來電, 他們必須滑動 MonkeyCall 通知來開啟應用程式, 並輸入其密碼 (或使用者 Touch ID) 來解除鎖定電話, 然後才接受通話並開始交談。

如果手機已解除鎖定, 體驗就會同樣繁瑣。 同樣地, 傳入的 MonkeyCall 呼叫會顯示為從螢幕頂端滑入的標準通知橫幅。 由於通知是暫時性的, 因此使用者可以輕鬆地將其遺失, 讓他們開啟 [通知中心] 並尋找要回答的特定通知, 然後再次呼叫或尋找並啟動 MonkeyCall 應用程式。

### <a name="the-callkit-voip-app-experience"></a>CallKit VOIP 應用程式體驗

藉由在 MonkeyCall 應用程式中執行新的 CallKit Api, 可以在 iOS 10 中大幅改善使用者的傳入 VOIP 呼叫體驗。 當使用者的電話已從上方鎖定時, 請使用接收 VOIP 電話的使用者範例。 藉由執行 CallKit, 呼叫將會出現在 iPhone 的鎖定畫面上, 就像是從內建的電話應用程式收到呼叫一樣, 還有全螢幕、原生 UI 和標準的輕量回應功能。

同樣地, 如果在收到 MonkeyCall 的 VOIP 電話時, 將 iPhone 解除鎖定, 則會顯示相同的全螢幕、原生 UI 以及內建電話應用程式的標準輕量與降低功能, 而且 MonkeyCall 可以選擇播放自訂鈴聲.

CallKit 提供額外的功能來 MonkeyCall, 讓 VOIP 呼叫能夠與其他類型的呼叫互動, 以顯示在內建的最近專案和我的最愛清單中, 以使用內建的 [不幹擾] 和 [封鎖] 功能, 從 Siri 開始 MonkeyCall 呼叫提供使用者將 MonkeyCall 呼叫指派給 Contacts 應用程式中人員的能力。

下列各節將詳細說明 CallKit 架構、傳入和撥出電話流程, 以及 CallKit API。

## <a name="the-callkit-architecture"></a>CallKit 架構

在 iOS 10 中, Apple 已採用所有系統服務中的 CallKit, 例如, 在了 carplay 上所進行的呼叫是透過 CallKit 得知的系統 UI。 在下面的範例中, 由於 MonkeyCall 會採用 CallKit, 因此系統會以與這些內建系統服務相同的方式來得知它, 並取得所有相同的功能:

[![](callkit-images/callkit01.png "CallKit 服務堆疊")](callkit-images/callkit01.png#lightbox)

請仔細查看上圖中的 MonkeyCall 應用程式。 應用程式包含其所有的程式碼, 以與自己的網路進行通訊, 並包含自己的使用者介面。 它會在 CallKit 中連結以與系統進行通訊:

[![](callkit-images/callkit02.png "MonkeyCall 應用程式架構")](callkit-images/callkit02.png#lightbox)

應用程式會使用 CallKit 中的兩個主要介面:

- `CXProvider`-這可讓 MonkeyCall 代理程式更新系統可能會發生的任何頻外通知。
- `CXCallController`-允許 MonkeyCall 代理程式更新系統本機使用者動作。

### <a name="the-cxprovider"></a>CXProvider

如上所述, `CXProvider`可讓代理程式更新系統可能發生的任何頻外通知。 這些是因本機使用者動作而不會發生的通知, 但由於連入呼叫之類的外來事件而發生。

應用程式應`CXProvider`針對下列各項使用:

- 報告系統的撥入電話。
- 報告撥出電話已連接到系統。
- 報告遠端使用者結束對系統的呼叫。

當應用程式想要與系統通訊時, 它會使用`CXCallUpdate`類別, 而當系統需要與應用程式通訊時, 它會`CXAction`使用類別:

[![](callkit-images/callkit03.png "透過 CXProvider 與系統通訊")](callkit-images/callkit03.png#lightbox)

### <a name="the-cxcallcontroller"></a>CXCallController

`CXCallController`可讓代理程式更新系統本機使用者動作, 例如啟動 VOIP 呼叫的使用者。 藉由執行`CXCallController` , 應用程式就會與系統中的其他呼叫類型相互作用。 例如, 如果已經有作用中的電話語音通話, `CXCallController`可以讓 VOIP 應用程式在按住並啟動或接聽 VOIP 電話時, 放置該呼叫。

應用程式應`CXCallController`針對下列各項使用:

- 當使用者開始對系統進行撥出電話時回報。
- 當使用者回應系統的撥入電話時回報。
- 當使用者結束對系統的呼叫時報告。

當應用程式想要將本機使用者動作傳達給系統時, 它會`CXTransaction`使用類別:

[![](callkit-images/callkit04.png "使用 CXCallController 向系統報告")](callkit-images/callkit04.png#lightbox)

## <a name="implementing-callkit"></a>執行 CallKit

下列各節將示範如何在 Xamarin iOS VOIP 應用程式中執行 CallKit。 例如, 這份檔將使用來自虛構 MonkeyCall VOIP 應用程式的程式碼。 此處顯示的程式碼代表數個支援的類別, CallKit 特定元件將在下列各節中詳細說明。

### <a name="the-activecall-class"></a>ActiveCall 類別

MonkeyCall 應用程式會使用類別來保存目前作用中之VOIP呼叫的所有相關資訊,如下所示:`ActiveCall`

```csharp
using System;
using CoreFoundation;
using Foundation;

namespace MonkeyCall
{
    public class ActiveCall
    {
        #region Private Variables
        private bool isConnecting;
        private bool isConnected;
        private bool isOnhold;
        #endregion

        #region Computed Properties
        public NSUuid UUID { get; set; }
        public bool isOutgoing { get; set; }
        public string Handle { get; set; }
        public DateTime StartedConnectingOn { get; set;}
        public DateTime ConnectedOn { get; set;}
        public DateTime EndedOn { get; set; }

        public bool IsConnecting {
            get { return isConnecting; }
            set {
                isConnecting = value;
                if (isConnecting) StartedConnectingOn = DateTime.Now;
                RaiseStartingConnectionChanged ();
            }
        }

        public bool IsConnected {
            get { return isConnected; }
            set {
                isConnected = value;
                if (isConnected) {
                    ConnectedOn = DateTime.Now;
                } else {
                    EndedOn = DateTime.Now;
                }
                RaiseConnectedChanged ();
            }
        }

        public bool IsOnHold {
            get { return isOnhold; }
            set {
                isOnhold = value;
            }
        }
        #endregion

        #region Constructors
        public ActiveCall ()
        {
        }

        public ActiveCall (NSUuid uuid, string handle, bool outgoing)
        {
            // Initialize
            this.UUID = uuid;
            this.Handle = handle;
            this.isOutgoing = outgoing;
        }
        #endregion

        #region Public Methods
        public void StartCall (ActiveCallbackDelegate completionHandler)
        {
            // Simulate the call starting successfully
            completionHandler (true);

            // Simulate making a starting and completing a connection
            DispatchQueue.MainQueue.DispatchAfter (new DispatchTime(DispatchTime.Now, 3000), () => {
                // Note that the call is starting
                IsConnecting = true;

                // Simulate pause before connecting
                DispatchQueue.MainQueue.DispatchAfter (new DispatchTime (DispatchTime.Now, 1500), () => {
                    // Note that the call has connected
                    IsConnecting = false;
                    IsConnected = true;
                });
            });
        }

        public void AnswerCall (ActiveCallbackDelegate completionHandler)
        {
            // Simulate the call being answered
            IsConnected = true;
            completionHandler (true);
        }

        public void EndCall (ActiveCallbackDelegate completionHandler)
        {
            // Simulate the call ending
            IsConnected = false;
            completionHandler (true);
        }
        #endregion

        #region Events
        public delegate void ActiveCallbackDelegate (bool successful);
        public delegate void ActiveCallStateChangedDelegate (ActiveCall call);

        public event ActiveCallStateChangedDelegate StartingConnectionChanged;
        internal void RaiseStartingConnectionChanged ()
        {
            if (this.StartingConnectionChanged != null) this.StartingConnectionChanged (this);
        }

        public event ActiveCallStateChangedDelegate ConnectedChanged;
        internal void RaiseConnectedChanged ()
        {
            if (this.ConnectedChanged != null) this.ConnectedChanged (this);
        }
        #endregion
    }
}
```

`ActiveCall`包含數個定義撥號狀態的屬性, 以及撥號狀態變更時可以引發的兩個事件。 因為這只是範例, 所以有三種方法可用來模擬啟動、接聽和結束呼叫。

### <a name="the-startcallrequest-class"></a>StartCallRequest 類別

`StartCallRequest`靜態類別提供一些 helper 方法, 在使用撥出電話時將會用到:

```csharp
using System;
using Foundation;
using Intents;

namespace MonkeyCall
{
    public static class StartCallRequest
    {
        public static string URLScheme {
            get { return "monkeycall"; }
        }

        public static string ActivityType {
            get { return INIntentIdentifier.StartAudioCall.GetConstant ().ToString (); }
        }

        public static string CallHandleFromURL (NSUrl url)
        {
            // Is this a MonkeyCall handle?
            if (url.Scheme == URLScheme) {
                // Yes, return host
                return url.Host;
            } else {
                // Not handled
                return null;
            }
        }

        public static string CallHandleFromActivity (NSUserActivity activity)
        {
            // Is this a start call activity?
            if (activity.ActivityType == ActivityType) {
                // Yes, trap any errors
                try {
                    // Get first contact
                    var interaction = activity.GetInteraction ();
                    var startAudioCallIntent = interaction.Intent as INStartAudioCallIntent;
                    var contact = startAudioCallIntent.Contacts [0];

                    // Get the person handle
                    return contact.PersonHandle.Value;
                } catch {
                    // Error, report null
                    return null;
                }
            } else {
                // Not handled
                return null;
            }
        }
    }
}
```

`CallHandleFromURL` 和`CallHandleFromActivity`類別會在 AppDelegate 中用來取得在撥出電話中呼叫之人員的連絡人控制碼。 如需詳細資訊, 請參閱下面的[處理外寄呼叫](#handling-outgoing-calls)一節。

### <a name="the-activecallmanager-class"></a>ActiveCallManager 類別

`ActiveCallManager`類別會處理 MonkeyCall 應用程式中所有開啟的呼叫。

```csharp
using System;
using System.Collections.Generic;
using Foundation;
using CallKit;

namespace MonkeyCall
{
    public class ActiveCallManager
    {
        #region Private Variables
        private CXCallController CallController = new CXCallController ();
        #endregion

        #region Computed Properties
        public List<ActiveCall> Calls { get; set; }
        #endregion

        #region Constructors
        public ActiveCallManager ()
        {
            // Initialize
            this.Calls = new List<ActiveCall> ();
        }
        #endregion

        #region Private Methods
        private void SendTransactionRequest (CXTransaction transaction)
        {
            // Send request to call controller
            CallController.RequestTransaction (transaction, (error) => {
                // Was there an error?
                if (error == null) {
                    // No, report success
                    Console.WriteLine ("Transaction request sent successfully.");
                } else {
                    // Yes, report error
                    Console.WriteLine ("Error requesting transaction: {0}", error);
                }
            });
        }
        #endregion

        #region Public Methods
        public ActiveCall FindCall (NSUuid uuid)
        {
            // Scan for requested call
            foreach (ActiveCall call in Calls) {
                if (call.UUID.Equals(uuid)) return call;
            }

            // Not found
            return null;
        }

        public void StartCall (string contact)
        {
            // Build call action
            var handle = new CXHandle (CXHandleType.Generic, contact);
            var startCallAction = new CXStartCallAction (new NSUuid (), handle);

            // Create transaction
            var transaction = new CXTransaction (startCallAction);

            // Inform system of call request
            SendTransactionRequest (transaction);
        }

        public void EndCall (ActiveCall call)
        {
            // Build action
            var endCallAction = new CXEndCallAction (call.UUID);

            // Create transaction
            var transaction = new CXTransaction (endCallAction);

            // Inform system of call request
            SendTransactionRequest (transaction);
        }

        public void PlaceCallOnHold (ActiveCall call)
        {
            // Build action
            var holdCallAction = new CXSetHeldCallAction (call.UUID, true);

            // Create transaction
            var transaction = new CXTransaction (holdCallAction);

            // Inform system of call request
            SendTransactionRequest (transaction);
        }

        public void RemoveCallFromOnHold (ActiveCall call)
        {
            // Build action
            var holdCallAction = new CXSetHeldCallAction (call.UUID, false);

            // Create transaction
            var transaction = new CXTransaction (holdCallAction);

            // Inform system of call request
            SendTransactionRequest (transaction);
        }
        #endregion
    }
}
```

同樣地, 因為這只是模擬, `ActiveCallManager`所以只會維護`ActiveCall`物件的集合, 並具有一個常式來尋找其`UUID`屬性所提供的呼叫。 它也包含可啟動、結束和變更外寄呼叫之暫停狀態的方法。 如需詳細資訊, 請參閱下面的[處理外寄呼叫](#handling-outgoing-calls)一節。

### <a name="the-providerdelegate-class"></a>ProviderDelegate 類別

如上所述, `CXProvider`會提供應用程式與系統之間的雙向通訊, 以進行頻外通知。 開發人員必須提供自訂`CXProviderDelegate` , 並將它附加`CXProvider`至, 應用程式才能處理頻外 CallKit 事件。 MonkeyCall 會使用下列`CXProviderDelegate`各項:

```csharp
using System;
using Foundation;
using CallKit;
using UIKit;

namespace MonkeyCall
{
    public class ProviderDelegate : CXProviderDelegate
    {
        #region Computed Properties
        public ActiveCallManager CallManager { get; set;}
        public CXProviderConfiguration Configuration { get; set; }
        public CXProvider Provider { get; set; }
        #endregion

        #region Constructors
        public ProviderDelegate (ActiveCallManager callManager)
        {
            // Save connection to call manager
            CallManager = callManager;

            // Define handle types
            var handleTypes = new [] { (NSNumber)(int)CXHandleType.PhoneNumber };

            // Get Image Template
            var templateImage = UIImage.FromFile ("telephone_receiver.png");

            // Setup the initial configurations
            Configuration = new CXProviderConfiguration ("MonkeyCall") {
                MaximumCallsPerCallGroup = 1,
                SupportedHandleTypes = new NSSet<NSNumber> (handleTypes),
                IconTemplateImageData = templateImage.AsPNG(),
                RingtoneSound = "musicloop01.wav"
            };

            // Create a new provider
            Provider = new CXProvider (Configuration);

            // Attach this delegate
            Provider.SetDelegate (this, null);

        }
        #endregion

        #region Override Methods
        public override void DidReset (CXProvider provider)
        {
            // Remove all calls
            CallManager.Calls.Clear ();
        }

        public override void PerformStartCallAction (CXProvider provider, CXStartCallAction action)
        {
            // Create new call record
            var activeCall = new ActiveCall (action.CallUuid, action.CallHandle.Value, true);

            // Monitor state changes
            activeCall.StartingConnectionChanged += (call) => {
                if (call.isConnecting) {
                    // Inform system that the call is starting
                    Provider.ReportConnectingOutgoingCall (call.UUID, call.StartedConnectingOn.ToNSDate());
                }
            };

            activeCall.ConnectedChanged += (call) => {
                if (call.isConnected) {
                    // Inform system that the call has connected
                    provider.ReportConnectedOutgoingCall (call.UUID, call.ConnectedOn.ToNSDate ());
                }
            };

            // Start call
            activeCall.StartCall ((successful) => {
                // Was the call able to be started?
                if (successful) {
                    // Yes, inform the system
                    action.Fulfill ();

                    // Add call to manager
                    CallManager.Calls.Add (activeCall);
                } else {
                    // No, inform system
                    action.Fail ();
                }
            });
        }

        public override void PerformAnswerCallAction (CXProvider provider, CXAnswerCallAction action)
        {
            // Find requested call
            var call = CallManager.FindCall (action.CallUuid);

            // Found?
            if (call == null) {
                // No, inform system and exit
                action.Fail ();
                return;
            }

            // Attempt to answer call
            call.AnswerCall ((successful) => {
                // Was the call successfully answered?
                if (successful) {
                    // Yes, inform system
                    action.Fulfill ();
                } else {
                    // No, inform system
                    action.Fail ();
                }
            });
        }

        public override void PerformEndCallAction (CXProvider provider, CXEndCallAction action)
        {
            // Find requested call
            var call = CallManager.FindCall (action.CallUuid);

            // Found?
            if (call == null) {
                // No, inform system and exit
                action.Fail ();
                return;
            }

            // Attempt to answer call
            call.EndCall ((successful) => {
                // Was the call successfully answered?
                if (successful) {
                    // Remove call from manager's queue
                    CallManager.Calls.Remove (call);

                    // Yes, inform system
                    action.Fulfill ();
                } else {
                    // No, inform system
                    action.Fail ();
                }
            });
        }

        public override void PerformSetHeldCallAction (CXProvider provider, CXSetHeldCallAction action)
        {
            // Find requested call
            var call = CallManager.FindCall (action.CallUuid);

            // Found?
            if (call == null) {
                // No, inform system and exit
                action.Fail ();
                return;
            }

            // Update hold status
            call.isOnHold = action.OnHold;

            // Inform system of success
            action.Fulfill ();
        }

        public override void TimedOutPerformingAction (CXProvider provider, CXAction action)
        {
            // Inform user that the action has timed out
        }

        public override void DidActivateAudioSession (CXProvider provider, AVFoundation.AVAudioSession audioSession)
        {
            // Start the calls audio session here
        }

        public override void DidDeactivateAudioSession (CXProvider provider, AVFoundation.AVAudioSession audioSession)
        {
            // End the calls audio session and restart any non-call
            // related audio
        }
        #endregion

        #region Public Methods
        public void ReportIncomingCall (NSUuid uuid, string handle)
        {
            // Create update to describe the incoming call and caller
            var update = new CXCallUpdate ();
            update.RemoteHandle = new CXHandle (CXHandleType.Generic, handle);

            // Report incoming call to system
            Provider.ReportNewIncomingCall (uuid, update, (error) => {
                // Was the call accepted
                if (error == null) {
                    // Yes, report to call manager
                    CallManager.Calls.Add (new ActiveCall (uuid, handle, false));
                } else {
                    // Report error to user here
                    Console.WriteLine ("Error: {0}", error);
                }
            });
        }
        #endregion
    }
}
```

建立此委派的實例時, 會將它傳遞`ActiveCallManager`給它, 以用來處理任何呼叫活動。 接下來, 它`CXHandleType` `CXProvider`會定義將會回應的控制碼類型 ():

```csharp
// Define handle types
var handleTypes = new [] { (NSNumber)(int)CXHandleType.PhoneNumber };
```

它會取得範本映射, 以在進行呼叫時套用至應用程式的圖示:

```csharp
// Get Image Template
var templateImage = UIImage.FromFile ("telephone_receiver.png");
```

這些值會組合成`CXProviderConfiguration` , 將用來`CXProvider`設定:

```csharp
// Setup the initial configurations
Configuration = new CXProviderConfiguration ("MonkeyCall") {
    MaximumCallsPerCallGroup = 1,
    SupportedHandleTypes = new NSSet<NSNumber> (handleTypes),
    IconTemplateImageData = templateImage.AsPNG(),
    RingtoneSound = "musicloop01.wav"
};
```

然後, 委派會使用這些`CXProvider`設定來建立新的, 並將其本身附加至該設定:

```csharp
// Create a new provider
Provider = new CXProvider (Configuration);

// Attach this delegate
Provider.SetDelegate (this, null);
```

使用 CallKit 時, 應用程式將不再建立和處理自己的音訊會話, 而是必須設定並使用系統將為其建立及處理的音訊會話。 

如果這是實際的應用程式, `DidActivateAudioSession`則會使用方法, 以系統提供的預先設定`AVAudioSession`來啟動呼叫:

```csharp
public override void DidActivateAudioSession (CXProvider provider, AVFoundation.AVAudioSession audioSession)
{
    // Start the call's audio session here...
}
```

它也會使用`DidDeactivateAudioSession`方法, 來完成與系統提供之音訊會話的連線:

```csharp
public override void DidDeactivateAudioSession (CXProvider provider, AVFoundation.AVAudioSession audioSession)
{
    // End the calls audio session and restart any non-call
    // releated audio
}
```

接下來的各節將詳細說明程式碼的其餘部分。

### <a name="the-appdelegate-class"></a>AppDelegate 類別

MonkeyCall 會使用 AppDelegate 來保存的實例`ActiveCallManager` , 並`CXProviderDelegate`在整個應用程式中使用:

```csharp
using Foundation;
using UIKit;
using Intents;
using System;

namespace MonkeyCall
{
    [Register ("AppDelegate")]
    public class AppDelegate : UIApplicationDelegate
    {
        #region Constructors
        public override UIWindow Window { get; set; }
        public ActiveCallManager CallManager { get; set; }
        public ProviderDelegate CallProviderDelegate { get; set; }
        #endregion

        #region Override Methods
        public override bool FinishedLaunching (UIApplication application, NSDictionary launchOptions)
        {
            // Initialize the call handlers
            CallManager = new ActiveCallManager ();
            CallProviderDelegate = new ProviderDelegate (CallManager);

            return true;
        }

        public override bool OpenUrl (UIApplication app, NSUrl url, NSDictionary options)
        {
            // Get handle from url
            var handle = StartCallRequest.CallHandleFromURL (url);

            // Found?
            if (handle == null) {
                // No, report to system
                Console.WriteLine ("Unable to get call handle from URL: {0}", url); 
                return false;
            } else {
                // Yes, start call and inform system
                CallManager.StartCall (handle);
                return true;
            }
        }

        public override bool ContinueUserActivity (UIApplication application, NSUserActivity userActivity, UIApplicationRestorationHandler completionHandler)
        {
            var handle = StartCallRequest.CallHandleFromActivity (userActivity);

            // Found?
            if (handle == null) {
                // No, report to system
                Console.WriteLine ("Unable to get call handle from User Activity: {0}", userActivity);
                return false;
            } else {
                // Yes, start call and inform system
                CallManager.StartCall (handle);
                return true;
            }
        }

        ...
        #endregion
    }
}
```

當應用`ContinueUserActivity`程式正在處理外寄呼叫時, 會使用和覆寫方法。`OpenUrl` 如需詳細資訊, 請參閱下面的[處理外寄呼叫](#handling-outgoing-calls)一節。

## <a name="handling-incoming-calls"></a>處理傳入的呼叫

在一般的撥入電話工作流程期間, 傳入 VOIP 呼叫可以進行數個狀態和進程, 例如:

- 通知使用者 (和系統) 傳入的呼叫存在。
- 當使用者想要接聽呼叫, 並將呼叫與其他使用者初始化時接收通知。
- 當使用者想要結束目前的呼叫時, 通知系統和通訊網路。

下列各節將詳細說明應用程式如何使用 CallKit 來處理撥入電話工作流程, 再次使用 MonkeyCall VOIP 應用程式做為範例。

### <a name="informing-user-of-incoming-call"></a>通知使用者撥入電話

當遠端使用者已開始與本機使用者進行 VOIP 交談時, 將會發生下列情況:

[![](callkit-images/callkit05.png "遠端使用者已開始進行 VOIP 交談")](callkit-images/callkit05.png#lightbox)

1. 應用程式會從其通訊網路取得通知, 其中包含傳入的 VOIP 呼叫。
2. 應用程式會使用`CXProvider`將`CXCallUpdate`傳送給系統, 通知它有呼叫。
3. 系統會使用 CallKit 將呼叫發佈至系統 UI、系統服務和任何其他 VOIP 應用程式。

例如, 在中`CXProviderDelegate`:

```csharp
public void ReportIncomingCall (NSUuid uuid, string handle)
{
    // Create update to describe the incoming call and caller
    var update = new CXCallUpdate ();
    update.RemoteHandle = new CXHandle (CXHandleType.Generic, handle);

    // Report incoming call to system
    Provider.ReportNewIncomingCall (uuid, update, (error) => {
        // Was the call accepted
        if (error == null) {
            // Yes, report to call manager
            CallManager.Calls.Add (new ActiveCall (uuid, handle, false));
        } else {
            // Report error to user here
            Console.WriteLine ("Error: {0}", error);
        }
    });
}
```

此程式碼會建立`CXCallUpdate`新的實例, 並附加將識別呼叫端的控制碼。 接下來, 它會`ReportNewIncomingCall`使用`CXProvider`類別的方法來通知系統呼叫。 如果成功, 則會將呼叫新增至應用程式的作用中呼叫集合, 如果不是, 則必須向使用者回報錯誤。

### <a name="user-answering-incoming-call"></a>使用者接聽撥入電話

如果使用者想要接聽傳入的 VOIP 電話, 則會發生下列情況:

[![](callkit-images/callkit06.png "使用者接聽傳入的 VOIP 電話")](callkit-images/callkit06.png#lightbox)

1. 系統 UI 會通知系統使用者想要接聽 VOIP 呼叫。
2. 系統會將`CXAnswerCallAction`傳送給`CXProvider`應用程式, 以通知其回應意圖。
3. 應用程式會通知其通訊網路, 使用者正在接聽來電, 而 VOIP 呼叫會如往常般繼續進行。

例如, 在中`CXProviderDelegate`:

```csharp
public override void PerformAnswerCallAction (CXProvider provider, CXAnswerCallAction action)
{
    // Find requested call
    var call = CallManager.FindCall (action.CallUuid);

    // Found?
    if (call == null) {
        // No, inform system and exit
        action.Fail ();
        return;
    }

    // Attempt to answer call
    call.AnswerCall ((successful) => {
        // Was the call successfully answered?
        if (successful) {
            // Yes, inform system
            action.Fulfill ();
        } else {
            // No, inform system
            action.Fail ();
        }
    });
}
```

此程式碼會先在其使用中呼叫的清單中搜尋指定的呼叫。 如果找不到呼叫, 系統就會收到通知, 而且方法會結束。 如果找到, `AnswerCall`則會呼叫`ActiveCall`類別的方法來啟動呼叫, 而如果系統成功或失敗, 則會是資訊。

### <a name="user-ending-incoming-call"></a>使用者結束撥入電話

如果使用者希望在應用程式的 UI 中結束通話, 則會發生下列情況:

[![](callkit-images/callkit07.png "使用者在應用程式的 UI 中結束通話")](callkit-images/callkit07.png#lightbox)

1. 應用程式會`CXEndCallAction`建立, 並將其`CXTransaction`捆綁至傳送至系統的, 以通知它該呼叫正在結束。
2. 系統會驗證「結束呼叫」意圖, 並`CXEndCallAction`透過將傳回給應用程式`CXProvider`。
3. 然後, 應用程式會通知其通訊網路正在結束呼叫。

例如, 在中`CXProviderDelegate`:

```csharp
public override void PerformEndCallAction (CXProvider provider, CXEndCallAction action)
{
    // Find requested call
    var call = CallManager.FindCall (action.CallUuid);

    // Found?
    if (call == null) {
        // No, inform system and exit
        action.Fail ();
        return;
    }

    // Attempt to answer call
    call.EndCall ((successful) => {
        // Was the call successfully answered?
        if (successful) {
            // Remove call from manager's queue
            CallManager.Calls.Remove (call);

            // Yes, inform system
            action.Fulfill ();
        } else {
            // No, inform system
            action.Fail ();
        }
    });
}
```

此程式碼會先在其使用中呼叫的清單中搜尋指定的呼叫。 如果找不到呼叫, 系統就會收到通知, 而且方法會結束。 如果找到, `EndCall`則會呼叫`ActiveCall`類別的方法來結束呼叫, 而如果系統成功或失敗, 則會是資訊。 如果成功, 則會從使用中呼叫的集合中移除呼叫。

## <a name="managing-multiple-calls"></a>管理多個呼叫

大部分的 VOIP 應用程式可以一次處理多個呼叫。 例如, 如果目前有作用中的 VOIP 呼叫, 而應用程式收到通知, 指出有新的撥入電話, 使用者可以在第一次呼叫時暫停或掛斷, 以回應第二個呼叫。

在上述情況下, 系統會將傳送`CXTransaction`至應用程式, 其中會包含多個動作 (例如`CXEndCallAction`和`CXAnswerCallAction`) 的清單。 所有這些動作都必須個別完成, 讓系統可以適當地更新 UI。

## <a name="handling-outgoing-calls"></a>處理外寄呼叫

例如, 如果使用者從最近專案清單中 (在電話應用程式中) 按下某個專案, 這就是來自屬於應用程式的呼叫, 系統會將其傳送給 _「開始呼叫」意圖_:

[![](callkit-images/callkit08.png "接收開始呼叫意圖")](callkit-images/callkit08.png#lightbox)

1. 應用程式會根據其從系統收到的開始呼叫意圖, 建立_開始呼叫動作_。 
2. 應用程式會使用`CXCallController`來向系統要求開始呼叫動作。
3. 如果系統接受此動作, 則會透過`XCProvider`委派將其傳回到應用程式。
4. 應用程式會以其通訊網路啟動連出呼叫。

如需意圖的詳細資訊, 請參閱我們的[意圖和意圖 UI 延伸](~/ios/platform/sirikit/understanding-sirikit.md)模組檔。 

### <a name="the-outgoing-call-lifecycle"></a>外寄呼叫生命週期

使用 CallKit 和撥出電話時, 應用程式必須通知系統下列生命週期事件:

1. **啟動**-通知系統即將啟動外寄呼叫。
2. **已啟動**-通知系統已開始撥出電話。
3. **連接**-通知系統傳出的呼叫正在連線。
4. **已連線**-通知外寄呼叫已連線, 而且雙方都可以立即交談。

例如, 下列程式碼會啟動外寄呼叫:

```csharp
private CXCallController CallController = new CXCallController ();
...

private void SendTransactionRequest (CXTransaction transaction)
{
    // Send request to call controller
    CallController.RequestTransaction (transaction, (error) => {
        // Was there an error?
        if (error == null) {
            // No, report success
            Console.WriteLine ("Transaction request sent successfully.");
        } else {
            // Yes, report error
            Console.WriteLine ("Error requesting transaction: {0}", error);
        }
    });
}

public void StartCall (string contact)
{
    // Build call action
    var handle = new CXHandle (CXHandleType.Generic, contact);
    var startCallAction = new CXStartCallAction (new NSUuid (), handle);

    // Create transaction
    var transaction = new CXTransaction (startCallAction);

    // Inform system of call request
    SendTransactionRequest (transaction);
}
```

它會建立`CXHandle` , 並使用它來`CXStartCallAction`設定, `CXTransaction`其會使用`RequestTransaction` `CXCallController`類別的方法, 將其配套到傳送至系統的。 藉由呼叫`RequestTransaction`方法, 系統可以在新的呼叫開始之前, 隨時將任何現有的呼叫放在原處, 不論來源 (電話應用程式、FaceTime、VOIP 等等)。

啟動外寄 VOIP 呼叫的要求可能來自數個不同的來源, 例如 Siri、連絡人卡片上的專案 (在連絡人應用程式中), 或從最近專案清單 (在 Phone 應用程式中)。 在這些情況下, 應用程式將會在內`NSUserActivity`傳送起始呼叫意圖, 而 AppDelegate 將需要處理它:

```csharp
public override bool ContinueUserActivity (UIApplication application, NSUserActivity userActivity, UIApplicationRestorationHandler completionHandler)
{
    var handle = StartCallRequest.CallHandleFromActivity (userActivity);

    // Found?
    if (handle == null) {
        // No, report to system
        Console.WriteLine ("Unable to get call handle from User Activity: {0}", userActivity);
        return false;
    } else {
        // Yes, start call and inform system
        CallManager.StartCall (handle);
        return true;
    }
}
```

在這裡, helper 類別`StartCallRequest`的`CallHandleFromActivity`方法是用來取得所呼叫人員的控制碼(請參閱上述的[StartCallRequest類別](#the-startcallrequest-class))。

[ProviderDelegate 類別](#the-providerdelegate-class)的方法是用來最後啟動實際的撥出電話,並通知系統其生命週期:`PerformStartCallAction`

```csharp
public override void PerformStartCallAction (CXProvider provider, CXStartCallAction action)
{
    // Create new call record
    var activeCall = new ActiveCall (action.CallUuid, action.CallHandle.Value, true);

    // Monitor state changes
    activeCall.StartingConnectionChanged += (call) => {
        if (call.IsConnecting) {
            // Inform system that the call is starting
            Provider.ReportConnectingOutgoingCall (call.UUID, call.StartedConnectingOn.ToNSDate());
        }
    };

    activeCall.ConnectedChanged += (call) => {
        if (call.IsConnected) {
            // Inform system that the call has connected
            Provider.ReportConnectedOutgoingCall (call.UUID, call.ConnectedOn.ToNSDate ());
        }
    };

    // Start call
    activeCall.StartCall ((successful) => {
        // Was the call able to be started?
        if (successful) {
            // Yes, inform the system
            action.Fulfill ();

            // Add call to manager
            CallManager.Calls.Add (activeCall);
        } else {
            // No, inform system
            action.Fail ();
        }
    });
}
```

它會建立`ActiveCall`類別的實例 (以保存進行中的呼叫相關資訊), 並填入所呼叫的人員。 `StartingConnectionChanged` 和`ConnectedChanged`事件是用來監視和報告傳出的呼叫生命週期。 呼叫隨即啟動, 系統會通知您已完成此動作。

### <a name="ending-an-outgoing-call"></a>結束撥出電話

當使用者完成外寄呼叫, 並希望結束時, 可以使用下列程式碼:

```csharp
private CXCallController CallController = new CXCallController ();
...

private void SendTransactionRequest (CXTransaction transaction)
{
    // Send request to call controller
    CallController.RequestTransaction (transaction, (error) => {
        // Was there an error?
        if (error == null) {
            // No, report success
            Console.WriteLine ("Transaction request sent successfully.");
        } else {
            // Yes, report error
            Console.WriteLine ("Error requesting transaction: {0}", error);
        }
    });
}

public void EndCall (ActiveCall call)
{
    // Build action
    var endCallAction = new CXEndCallAction (call.UUID);

    // Create transaction
    var transaction = new CXTransaction (endCallAction);

    // Inform system of call request
    SendTransactionRequest (transaction);
}
```

如果建立`CXEndCallAction`具有 end 呼叫之 UUID 的, 則會將它組合`CXTransaction`在`CXCallController`使用類別的`RequestTransaction`方法傳送到系統的中。 

## <a name="additional-callkit-details"></a>其他 CallKit 詳細資料

本節將討論開發人員在使用 CallKit 時需要考慮的一些其他詳細資料, 例如:

- 提供者設定
- 動作錯誤
- 系統限制
- VOIP 音訊

### <a name="provider-configuration"></a>提供者設定

提供者設定可讓 iOS 10 VOIP 應用程式在使用 CallKit 時自訂使用者體驗 (在原生的呼叫 UI 內)。

應用程式可以進行下列類型的自訂:

- 顯示當地語系化的名稱。
- 啟用 video 通話支援。
- 藉由呈現自己的範本影像圖示來自訂呼叫中 UI 上的按鈕。 使用者與自訂按鈕的互動會直接傳送至要處理的應用程式。 

### <a name="action-errors"></a>動作錯誤

使用 CallKit 的 iOS 10 VOIP 應用程式需要處理正常失敗的動作, 並讓使用者隨時掌握動作狀態的通知。 

請考慮下列範例:

1. 應用程式已收到開始呼叫動作, 並已開始使用其通訊網路將新的 VOIP 呼叫初始化的進程。
2. 因為網路通訊功能受限或不存在, 所以此連線會失敗。
3. 應用程式*必須*將**失敗**訊息傳回給開始呼叫動作 (`Action.Fail()`), 以通知系統失敗。
4. 這可讓系統通知使用者該呼叫的狀態。 例如, 顯示呼叫失敗 UI。

此外, iOS 10 VOIP 應用程式必須回應在指定的時間內無法處理預期的動作時, 可能會發生的_逾時錯誤_。 CallKit 所提供的每個動作類型都有相關聯的最大超時值。 這些超時值可確保使用者所要求的任何 CallKit 動作都會以回應的方式處理, 因此也會讓作業系統更流暢且快速回應。

提供者委派 (`CXProviderDelegate`) 上有數個方法, 應該加以覆寫, 以正常處理此超時狀況。

### <a name="system-restrictions"></a>系統限制

根據執行 iOS 10 VOIP 應用程式的 iOS 裝置目前狀態, 可能會強制執行某些系統限制。

例如, 傳入的 VOIP 呼叫可能會受到系統的限制, 如下所示:

1. 呼叫者位於使用者的封鎖呼叫者清單上。
2. 使用者的 iOS 裝置處於「不打擾」模式。

如果 VOIP 呼叫受到任何一種情況的限制, 請使用下列程式碼來處理它:

```csharp
public class ProviderDelegate : CXProviderDelegate
{
...

    public void ReportIncomingCall (NSUuid uuid, string handle)
    {
        // Create update to describe the incoming call and caller
        var update = new CXCallUpdate ();
        update.RemoteHandle = new CXHandle (CXHandleType.Generic, handle);
    
        // Report incoming call to system
        Provider.ReportNewIncomingCall (uuid, update, (error) => {
            // Was the call accepted
            if (error == null) {
                // Yes, report to call manager
                CallManager.Calls.Add (new ActiveCall (uuid, handle, false));
            } else {
                // Report error to user here
                if (error.Code == (int)CXErrorCodeIncomingCallError.CallUuidAlreadyExists) {
                    // Handle duplicate call ID
                } else if (error.Code == (int)CXErrorCodeIncomingCallError.FilteredByBlockList) {
                    // Handle call from blocked user
                } else if (error.Code == (int)CXErrorCodeIncomingCallError.FilteredByDoNotDisturb) {
                    // Handle call while in do-not-disturb mode
                } else {
                    // Handle unknown error
                }
            }
        });
    }

}
```

### <a name="voip-audio"></a>VOIP 音訊

CallKit 提供數個優點, 可處理 iOS 10 VOIP 應用程式在即時 VOIP 呼叫期間所需的音訊資源。 其中一個最大的優點是在 iOS 10 中執行應用程式的音訊會話時, 會有更高的優先順序。 這是與內建電話和 FaceTime 應用程式相同的優先順序層級, 而此增強的優先權層級可防止其他正在執行的應用程式中斷 VOIP 應用程式的音訊會話。

此外, CallKit 也可以存取其他的音訊路由提示, 以根據使用者喜好設定和裝置狀態, 在即時呼叫期間, 增強效能並以智慧方式將 VOIP 音訊路由至特定的輸出裝置。 例如, 根據連接的裝置 (例如藍牙耳機), 即時了 carplay 連接或協助工具設定。

在使用 CallKit 進行一般 VOIP 呼叫的生命週期期間, 應用程式必須設定 CallKit 將提供的音訊串流。 請看下列範例:

[![](callkit-images/callkit09.png "開始呼叫動作順序")](callkit-images/callkit09.png#lightbox)

1. 應用程式會收到開始呼叫動作, 以回應傳入的呼叫。
2. 在應用程式完成此動作之前, 它會提供所需`AVAudioSession`的設定。
3. 應用程式會通知系統該動作已完成。
4. 在呼叫連接之前, CallKit 會提供高優先順序`AVAudioSession`的, 以符合應用程式所要求的設定。 應用程式會透過其`DidActivateAudioSession` `CXProviderDelegate`的方法收到通知。

## <a name="working-with-call-directory-extensions"></a>使用呼叫目錄延伸模組

使用 CallKit 時,_呼叫目錄延伸_提供了一種方法, 可在 iOS 裝置上的 contact 應用程式中新增封鎖的電話號碼, 並將特定 VOIP 應用程式專屬的數位識別為連絡人。

### <a name="implementing-a-call-directory-extension"></a>執行呼叫目錄延伸模組

若要在 Xamarin iOS 應用程式中執行呼叫目錄延伸模組, 請執行下列動作:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

1. 在 Visual Studio for Mac 中開啟應用程式的解決方案。
2. 以滑鼠右鍵按一下**方案總管**中的方案名稱, 然後選取  > [新增] [**新增專案**]。
3. 選取 [ **iOS**  > 擴充 > 功能] [**呼叫目錄延伸**], 然後按 [**下一步]** 按鈕 

    [![](callkit-images/calldir01.png "建立新的呼叫目錄延伸模組")](callkit-images/calldir01.png#lightbox)
4. 輸入擴充功能的**名稱**, 然後按 [**下一步]** 按鈕: 

    [![](callkit-images/calldir02.png "輸入延伸模組的名稱")](callkit-images/calldir02.png#lightbox)
5. 視需要調整 [**專案名稱**] 和/或 [**方案名稱**], 然後按一下 [**建立**] 按鈕: 

    [![](callkit-images/calldir03.png "建立專案")](callkit-images/calldir03.png#lightbox) 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. 在 Visual Studio 中開啟應用程式的解決方案。
2. 以滑鼠右鍵按一下**方案總管**中的方案名稱, 然後選取  > [新增] [**新增專案**]。
3. 選取 [ **iOS**  > 擴充 > 功能] [**呼叫目錄延伸**], 然後按 [**下一步]** 按鈕 

    [![](callkit-images/calldir01w.png "建立新的呼叫目錄延伸模組")](callkit-images/calldir01.png#lightbox)
4. 輸入擴充功能的**名稱**, 然後按一下 [**確定]** 按鈕

-----

這會將`CallDirectoryHandler.cs`類別新增至專案, 如下所示:

```csharp
using System;

using Foundation;
using CallKit;

namespace MonkeyCallDirExtension
{
    [Register ("CallDirectoryHandler")]
    public class CallDirectoryHandler : CXCallDirectoryProvider, ICXCallDirectoryExtensionContextDelegate
    {
        #region Constructors
        protected CallDirectoryHandler (IntPtr handle) : base (handle)
        {
            // Note: this .ctor should not contain any initialization logic.
        }
        #endregion

        #region Override Methods
        public override void BeginRequest (CXCallDirectoryExtensionContext context)
        {
            context.Delegate = this;

            if (!AddBlockingPhoneNumbers (context)) {
                Console.WriteLine ("Unable to add blocking phone numbers");
                var error = new NSError (new NSString ("CallDirectoryHandler"), 1, null);
                context.CancelRequest (error);
                return;
            }

            if (!AddIdentificationPhoneNumbers (context)) {
                Console.WriteLine ("Unable to add identification phone numbers");
                var error = new NSError (new NSString ("CallDirectoryHandler"), 2, null);
                context.CancelRequest (error);
                return;
            }

            context.CompleteRequest (null);
        }
        #endregion

        #region Private Methods
        private bool AddBlockingPhoneNumbers (CXCallDirectoryExtensionContext context)
        {
            // Retrieve phone numbers to block from data store. For optimal performance and memory usage when there are many phone numbers,
            // consider only loading a subset of numbers at a given time and using autorelease pool(s) to release objects allocated during each batch of numbers which are loaded.
            //
            // Numbers must be provided in numerically ascending order.

            long [] phoneNumbers = { 14085555555, 18005555555 };

            foreach (var phoneNumber in phoneNumbers)
                context.AddBlockingEntry (phoneNumber);

            return true;
        }

        private bool AddIdentificationPhoneNumbers (CXCallDirectoryExtensionContext context)
        {
            // Retrieve phone numbers to identify and their identification labels from data store. For optimal performance and memory usage when there are many phone numbers,
            // consider only loading a subset of numbers at a given time and using autorelease pool(s) to release objects allocated during each batch of numbers which are loaded.
            //
            // Numbers must be provided in numerically ascending order.

            long [] phoneNumbers = { 18775555555, 18885555555 };
            string [] labels = { "Telemarketer", "Local business" };

            for (var i = 0; i < phoneNumbers.Length; i++) {
                long phoneNumber = phoneNumbers [i];
                string label = labels [i];
                context.AddIdentificationEntry (phoneNumber, label);
            }

            return true;
        }
        #endregion

        #region Public Methods
        public void RequestFailed (CXCallDirectoryExtensionContext extensionContext, NSError error)
        {
            // An error occurred while adding blocking or identification entries, check the NSError for details.
            // For Call Directory error codes, see the CXErrorCodeCallDirectoryManagerError enum.
            //
            // This may be used to store the error details in a location accessible by the extension's containing app, so that the
            // app may be notified about errors which occurred while loading data even if the request to load data was initiated by
            // the user in Settings instead of via the app itself.
        }
        #endregion
    }
}
```

呼叫`BeginRequest`目錄處理常式中的方法將需要修改, 才能提供必要的功能。 在上述範例中, 它會嘗試在 VOIP 應用程式的 [連絡人] 資料庫中設定封鎖和可用號碼的清單。 如果其中一個要求因任何原因而失敗, `NSError`請建立來描述失敗, 並將`CancelRequest` `CXCallDirectoryExtensionContext`類別的方法傳遞給它。

若要設定封鎖的數位, `AddBlockingEntry`請使用`CXCallDirectoryExtensionContext`類別的方法。 提供給方法的數位_必須_以數位的遞增順序。 若要在有許多電話號碼時獲得最佳效能和記憶體使用量, 請考慮只在指定的時間載入數位子集, 並使用 autorelease 集區釋放載入的每個數位批次期間所配置的物件。

若要通知連絡人應用程式是否有 VOIP 應用程式已知的連絡人號碼, 請`AddIdentificationEntry`使用`CXCallDirectoryExtensionContext`類別的方法, 並同時提供數位和識別標籤。 同樣地, 提供給方法的數位_必須_以數位的遞增順序。 若要在有許多電話號碼時獲得最佳效能和記憶體使用量, 請考慮只在指定的時間載入數位子集, 並使用 autorelease 集區釋放載入的每個數位批次期間所配置的物件。

## <a name="summary"></a>總結

本文涵蓋 Apple 在 iOS 10 中發行的新 CallKit API, 以及如何在 Xamarin iOS VOIP 應用程式中執行。 其中已示範 CallKit 如何讓應用程式整合到 iOS 系統中、如何提供與內建應用程式 (例如電話) 的功能同位檢查, 以及如何透過 Siri 互動和透過來增加整個 iOS 的應用程式可見度, 例如鎖定和主畫面連絡人應用程式。

## <a name="related-links"></a>相關連結

- [iOS 10 範例](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS10)
