---
title: 在 Xamarin 中 CallKit
description: 本文涵蓋 Apple 在 iOS 10 中發行的新 CallKit API，以及如何在 Xamarin 的 VOIP 應用程式中加以執行。
ms.prod: xamarin
ms.assetid: 738A142D-FFD2-4738-B3ED-57C273179848
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/15/2017
ms.openlocfilehash: 0dd7736d37f6db17ad794258d9dff852b2745053
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91431853"
---
# <a name="callkit-in-xamarinios"></a>在 Xamarin 中 CallKit

IOS 10 中的新 CallKit API 提供了一種方法，可讓 VOIP 應用程式與 iPhone UI 整合，並提供熟悉的介面和體驗給終端使用者。 使用此 API 時，使用者可以從 iOS 裝置的鎖定畫面來查看並與 VOIP 通話進行互動，並使用手機應用程式的 [我的最愛] 和 [**最近專案** **]** 視圖來管理連絡人。

## <a name="about-callkit"></a>關於 CallKit

根據 Apple，CallKit 是新的架構，可將協力廠商語音透過 IP (VOIP) 應用程式提升至 iOS 10 上的第一方體驗。 CallKit API 可讓 VOIP 應用程式與 iPhone UI 整合，並為終端使用者提供熟悉的介面和體驗。 就像內建的電話應用程式，使用者可以從 iOS 裝置的鎖定畫面中查看 VOIP 電話並進行互動，並使用手機應用程式的 [我的最愛 **]** 和 [ **最近專案** ] 視圖來管理連絡人。

此外，CallKit API 可讓您建立應用程式延伸模組，以將電話號碼與名稱 (呼叫者識別碼產生關聯) 或告知系統 (呼叫封鎖) 時應封鎖的數位。

### <a name="the-existing-voip-app-experience"></a>現有的 VOIP 應用程式體驗

在討論新的 CallKit API 及其能力之前，請先看看 iOS 9 (中的協力廠商 VOIP 應用程式目前的使用者體驗，並使用稱為 MonkeyCall 的虛構 VOIP 應用程式來執行較少的) 。 MonkeyCall 是一個簡單的應用程式，可讓使用者使用現有的 iOS Api 來傳送和接收 VOIP 呼叫。

目前，如果使用者在 MonkeyCall 上收到來電，而其 iPhone 已鎖定，則鎖定畫面上收到的通知會與任何其他類型的通知（例如來自訊息或郵件應用程式的 (通知）有所區別，例如) 。

如果使用者想要接聽通話，他們必須滑動 MonkeyCall 通知以開啟應用程式，並輸入密碼 (或使用者觸控識別碼) 來解除鎖定電話，然後才能接受電話並開始交談。

如果電話已解除鎖定，此體驗也相當繁瑣。 同樣地，傳入的 MonkeyCall 呼叫會顯示為從畫面頂端滑入的標準通知橫幅。 由於通知是暫時性的，因此使用者可以輕鬆地將其遺失，以強制他們開啟通知中心，並尋找要回答的特定通知，然後手動呼叫或尋找並啟動 MonkeyCall 應用程式。

### <a name="the-callkit-voip-app-experience"></a>CallKit VOIP 應用程式體驗

藉由在 MonkeyCall 應用程式中執行新的 CallKit Api，您可以在 iOS 10 中大幅改善使用者的連入 VOIP 通話體驗。 當使用者的電話已從上方鎖定時，請使用收到 VOIP 通話的使用者範例。 藉由執行 CallKit，此呼叫將會出現在 iPhone 的鎖定畫面上，就像是從內建的電話應用程式收到通話時一樣，其具有全螢幕、原生 UI 和標準的輕量回應功能。

同樣地，如果在收到 MonkeyCall VOIP 通話時將 iPhone 解除鎖定，則會顯示內建 Phone 應用程式的相同全螢幕、原生 UI 和標準的滑動點對回應功能，而 MonkeyCall 可選擇播放自訂鈴聲。

CallKit 提供額外的 MonkeyCall 功能，可讓其 VOIP 呼叫與其他類型的呼叫互動，以顯示在內建的最近專案和我的最愛清單中，以使用內建的「不打擾」和「封鎖」功能、從 Siri 開始 MonkeyCall 呼叫，以及提供使用者將 MonkeyCall 呼叫指派給「連絡人」應用程式中人員的能力。

下列各節將詳細說明 CallKit 架構、傳入和撥出電話流程以及 CallKit API。

## <a name="the-callkit-architecture"></a>CallKit 架構

在 iOS 10 中，Apple 已在所有系統服務中採用 CallKit，例如，在 CarPlay 上進行的呼叫會透過 CallKit 已知于系統 UI 中。 在下面提供的範例中，因為 MonkeyCall 採用 CallKit，所以系統的已知方式與這些內建的系統服務相同，而且會取得所有相同的功能：

[![CallKit 服務堆疊](callkit-images/callkit01.png)](callkit-images/callkit01.png#lightbox)

請仔細查看上圖中的 MonkeyCall 應用程式。 應用程式會包含其所有程式碼，以與其自己的網路通訊，並包含其本身的使用者介面。 它會在 CallKit 中連結以與系統通訊：

[![MonkeyCall 應用程式架構](callkit-images/callkit02.png)](callkit-images/callkit02.png#lightbox)

應用程式使用的 CallKit 中有兩個主要介面：

- `CXProvider` -這可讓 MonkeyCall 代理程式更新系統有任何可能發生的頻外通知。
- `CXCallController` -允許 MonkeyCall 代理程式更新系統本機使用者動作。

### <a name="the-cxprovider"></a>CXProvider

如上所述， `CXProvider` 可讓代理程式更新系統有任何可能發生的頻外通知。 這些是由於本機使用者動作而不會發生的通知，但會因為連入呼叫之類的外來事件而發生。

應用程式應該使用下列各項 `CXProvider` ：

- 報告系統的連入呼叫。
- 報告撥出電話已連接至系統。
- 報告遠端使用者結束對系統的呼叫。

當應用程式想要與系統通訊時，它會使用 `CXCallUpdate` 類別，當系統需要與應用程式通訊時，它會使用 `CXAction` 類別：

[![透過 CXProvider 與系統通訊](callkit-images/callkit03.png)](callkit-images/callkit03.png#lightbox)

### <a name="the-cxcallcontroller"></a>CXCallController

`CXCallController`可讓代理程式更新系統本機使用者動作，例如使用者啟動 VOIP 通話。 藉由執行 `CXCallController` 應用程式，即可與系統中的其他呼叫類型相互作用。 例如，如果已有作用中的電話語音通話進行中， `CXCallController` 則可允許 voip 應用程式將該呼叫放置在待命上，並啟動或回答 VOIP 通話。

應用程式應該使用下列各項 `CXCallController` ：

- 報告使用者已開始對系統發出的呼叫。
- 當使用者回答系統的連入呼叫時回報。
- 當使用者結束對系統的呼叫時報告。

當應用程式想要將本機使用者動作傳達給系統時，它會使用 `CXTransaction` 類別：

[![使用 CXCallController 向系統報告](callkit-images/callkit04.png)](callkit-images/callkit04.png#lightbox)

## <a name="implementing-callkit"></a>執行 CallKit

下列各節將示範如何在 Xamarin iOS VOIP 應用程式中執行 CallKit。 舉例而言，本檔將使用來自虛構 MonkeyCall VOIP 應用程式的程式碼。 此處所呈現的程式碼代表數個支援類別，CallKit 的特定部分將在下列各節中詳細說明。

### <a name="the-activecall-class"></a>ActiveCall 類別

`ActiveCall`MonkeyCall 應用程式會使用類別來保存目前作用中之 VOIP 呼叫的所有資訊，如下所示：

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

`ActiveCall` 保存數個定義撥號狀態的屬性，以及在撥號狀態變更時可以引發的兩個事件。 因為這只是一個範例，所以有三種方法可用來模擬啟動、接聽和結束通話。

### <a name="the-startcallrequest-class"></a>StartCallRequest 類別

`StartCallRequest`靜態類別提供一些 helper 方法，這些方法會在使用撥出電話時使用：

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

`CallHandleFromURL`和 `CallHandleFromActivity` 類別是在 AppDelegate 中用來取得在外寄呼叫中所呼叫之人員的 contact 控制碼。 如需詳細資訊，請參閱下面的 [處理外寄呼叫](#handling-outgoing-calls) 一節。

### <a name="the-activecallmanager-class"></a>ActiveCallManager 類別

`ActiveCallManager`類別會在 MonkeyCall 應用程式中處理所有開啟的呼叫。

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

同樣地，因為這只是模擬，所以 `ActiveCallManager` 只會維護物件的集合， `ActiveCall` 並具有常式，可透過其屬性來尋找指定的呼叫 `UUID` 。 它也包含可啟動、結束和變更外寄呼叫之保存狀態的方法。 如需詳細資訊，請參閱下面的 [處理外寄呼叫](#handling-outgoing-calls) 一節。

### <a name="the-providerdelegate-class"></a>ProviderDelegate 類別

如上所述，會 `CXProvider` 提供應用程式和系統之間的雙向通訊，以進行頻外通知。 開發人員必須提供自訂 `CXProviderDelegate` ，並將它附加至， `CXProvider` 應用程式才能處理頻外 CallKit 事件。 MonkeyCall 會使用下列各項 `CXProviderDelegate` ：

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

建立這個委派的實例時，會將它 `ActiveCallManager` 將用來處理任何呼叫活動的傳遞給它。 接下來，它會定義要回應的控制碼類型 (`CXHandleType`) `CXProvider` ：

```csharp
// Define handle types
var handleTypes = new [] { (NSNumber)(int)CXHandleType.PhoneNumber };
```

它會取得在進行呼叫時，將套用至應用程式圖示的範本映射：

```csharp
// Get Image Template
var templateImage = UIImage.FromFile ("telephone_receiver.png");
```

這些值會配套至 `CXProviderConfiguration` ，以用來設定 `CXProvider` ：

```csharp
// Setup the initial configurations
Configuration = new CXProviderConfiguration ("MonkeyCall") {
    MaximumCallsPerCallGroup = 1,
    SupportedHandleTypes = new NSSet<NSNumber> (handleTypes),
    IconTemplateImageData = templateImage.AsPNG(),
    RingtoneSound = "musicloop01.wav"
};
```

然後，委派 `CXProvider` 會使用這些設定來建立新的，並將其本身附加至它：

```csharp
// Create a new provider
Provider = new CXProvider (Configuration);

// Attach this delegate
Provider.SetDelegate (this, null);
```

使用 CallKit 時，應用程式不會再建立並處理自己的音訊會話，而是必須設定並使用系統將為其建立並處理的音訊會話。

如果這是實際的應用程式，則 `DidActivateAudioSession` 會使用該方法來以系統提供的預先設定開始呼叫 `AVAudioSession` ：

```csharp
public override void DidActivateAudioSession (CXProvider provider, AVFoundation.AVAudioSession audioSession)
{
    // Start the call's audio session here...
}
```

它也會使用 `DidDeactivateAudioSession` 方法來完成和釋放與系統提供之音訊會話的連接：

```csharp
public override void DidDeactivateAudioSession (CXProvider provider, AVFoundation.AVAudioSession audioSession)
{
    // End the calls audio session and restart any non-call
    // releated audio
}
```

其餘的程式碼將會在後續各節中詳細討論。

### <a name="the-appdelegate-class"></a>AppDelegate 類別

MonkeyCall 會使用 AppDelegate 來保存的實例， `ActiveCallManager` 而且 `CXProviderDelegate` 會在整個應用程式中使用：

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

`OpenUrl` `ContinueUserActivity` 當應用程式正在處理外寄呼叫時，會使用和覆寫方法。 如需詳細資訊，請參閱下面的 [處理外寄呼叫](#handling-outgoing-calls) 一節。

## <a name="handling-incoming-calls"></a>處理撥入電話

在典型的撥入電話工作流程中，有數個狀態和程式可供連入的 VOIP 呼叫，例如：

- 通知使用者 (，且系統) 內送通話存在。
- 當使用者想要接聽通話，並將呼叫與其他使用者進行初始化時，收到通知。
- 當使用者想要結束目前的呼叫時，通知系統和通訊網路。

下列各節將詳細探討應用程式如何使用 CallKit 來處理傳入的呼叫工作流程，並再次使用 MonkeyCall VOIP 應用程式作為範例。

### <a name="informing-user-of-incoming-call"></a>通知使用者撥入電話

當遠端使用者開始與本機使用者進行 VOIP 對話時，會發生下列情況：

[![遠端使用者已啟動 VOIP 對話](callkit-images/callkit05.png)](callkit-images/callkit05.png#lightbox)

1. 應用程式會從其通訊網路取得通知，其中有連入的 VOIP 通話。
2. 應用程式會使用將 `CXProvider` 傳送 `CXCallUpdate` 給系統，通知它呼叫。
3. 系統會使用 CallKit，發佈系統 UI、系統服務和任何其他 VOIP 應用程式的呼叫。

例如，在中 `CXProviderDelegate` ：

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

此程式碼會建立新的 `CXCallUpdate` 實例，並將它的控制碼附加至該實例，以識別呼叫者。 接下來，它會使用 `ReportNewIncomingCall` 類別的方法 `CXProvider` 來通知系統的呼叫。 如果成功，則會將呼叫加入至應用程式的使用中呼叫集合，如果不是，則必須向使用者回報錯誤。

### <a name="user-answering-incoming-call"></a>使用者接聽撥入電話

如果使用者想要接聽傳入的 VOIP 通話，則會發生下列情況：

[![使用者回答了傳入的 VOIP 通話](callkit-images/callkit06.png)](callkit-images/callkit06.png#lightbox)

1. 系統 UI 會通知系統，使用者想要接聽 VOIP 通話。
2. 系統會將傳送 `CXAnswerCallAction` 給應用程式， `CXProvider` 告知其答案意圖。
3. 應用程式會通知其通訊網路，指出使用者正在接聽通話，而 VOIP 通話會如往常般繼續進行。

例如，在中 `CXProviderDelegate` ：

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

此程式碼會先在其作用中呼叫清單中搜尋指定的呼叫。 如果找不到呼叫，系統就會收到通知，並結束方法。 如果找到，就 `AnswerCall` `ActiveCall` 會呼叫類別的方法來啟動呼叫，而如果成功或失敗，系統就會是資訊。

### <a name="user-ending-incoming-call"></a>使用者結束來電

如果使用者想要在應用程式的 UI 內結束通話，就會發生下列情況：

[![使用者在應用程式的 UI 中結束通話](callkit-images/callkit07.png)](callkit-images/callkit07.png#lightbox)

1. 應用程式 `CXEndCallAction` 會建立，並將其配套至 `CXTransaction` 傳送至系統的，以通知該呼叫即將結束。
2. 系統會驗證結束呼叫意圖，並透過 `CXEndCallAction` 將傳回給應用程式 `CXProvider` 。
3. 然後，應用程式會通知其通訊網路正在結束呼叫。

例如，在中 `CXProviderDelegate` ：

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

此程式碼會先在其作用中呼叫清單中搜尋指定的呼叫。 如果找不到呼叫，系統就會收到通知，並結束方法。 如果找到，就 `EndCall` `ActiveCall` 會呼叫類別的方法來結束呼叫，而如果成功或失敗，系統就會是資訊。 如果成功，則會從使用中呼叫的集合中移除呼叫。

## <a name="managing-multiple-calls"></a>管理多個呼叫

大部分的 VOIP 應用程式都可以一次處理多個呼叫。 例如，如果目前有作用中的 VOIP 呼叫，而應用程式收到有新來電的通知，則使用者可以在第一次呼叫時暫停或掛斷，以回答第二個通話。

在上述情況中，系統會將傳送 `CXTransaction` 至應用程式，該應用程式會包含多個動作的清單 (例如 `CXEndCallAction` 和 `CXAnswerCallAction`) 。 所有這些動作都需要個別完成，以便系統可以適當地更新 UI。

## <a name="handling-outgoing-calls"></a>處理外寄呼叫

如果使用者從電話應用程式的 [最近專案] 清單中， (的專案，) （例如，來自應用程式的呼叫），系統就會傳送 _起始呼叫意圖_ 給系統：

[![接收開始呼叫意圖](callkit-images/callkit08.png)](callkit-images/callkit08.png#lightbox)

1. 應用程式會根據從系統接收到的起始呼叫意圖來建立 _起始呼叫動作_ 。
2. 應用程式會使用 `CXCallController` 從系統要求開始呼叫動作。
3. 如果系統接受此動作，則會透過委派將其傳回給應用程式 `XCProvider` 。
4. 應用程式會使用其通訊網路來啟動外寄通話。

如需意圖的詳細資訊，請參閱我們的 [意圖和意圖 UI 延伸](~/ios/platform/sirikit/understanding-sirikit.md) 模組檔。

### <a name="the-outgoing-call-lifecycle"></a>撥出電話生命週期

使用 CallKit 和外寄呼叫時，應用程式必須通知系統下列生命週期事件：

1. **啟動** -通知系統，撥出電話即將開始。
2. **已啟動** -通知系統已開始撥出電話。
3. **連接** -通知系統輸出呼叫正在連接。
4. **已連線** -通知外寄通話已連線，而且雙方都可以立即進行交談。

例如，下列程式碼將會開始撥出電話：

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

它會建立 `CXHandle` ，並使用它來設定，而該 `CXStartCallAction` 類別會 `CXTransaction` 使用類別的方法傳送至系統 `RequestTransaction` `CXCallController` 。 藉由呼叫 `RequestTransaction` 方法，系統可以在新的呼叫開始之前，隨時放置任何現有的呼叫，不論來源 (Phone 應用程式、FaceTime、VOIP 等 ) 。

啟動連出 VOIP 通話的要求可能來自數個不同的來源，例如 Siri、Contact app 中的連絡人卡片 (專案) 或電話應用程式) 中的最近專案清單 (。 在這些情況下，應用程式會在中傳送起始呼叫意圖， `NSUserActivity` 而 AppDelegate 將需要處理它：

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

在這裡， `CallHandleFromActivity` helper 類別的方法 `StartCallRequest` 是用來取得所呼叫人員的控制碼 (請參閱) 上面 [的 StartCallRequest 類別](#the-startcallrequest-class) 。

`PerformStartCallAction` [ProviderDelegate 類別](#the-providerdelegate-class)的方法是用來最後啟動實際的外寄呼叫，並通知系統其生命週期：

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

它會建立類別的實例 `ActiveCall` ， (保存正在進行之呼叫的相關資訊) 並填入要呼叫的人員。 `StartingConnectionChanged`和 `ConnectedChanged` 事件可用來監視和報告外寄呼叫生命週期。 此呼叫會啟動，且系統會通知該動作已完成。

### <a name="ending-an-outgoing-call"></a>結束撥出電話

當使用者完成撥出來電並希望結束時，可以使用下列程式碼：

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

如果 `CXEndCallAction` 以 end 的呼叫的 UUID 建立，則會使用類別的方法，將它與 `CXTransaction` 傳送至系統的進行組合 `RequestTransaction` `CXCallController` 。

## <a name="additional-callkit-details"></a>其他 CallKit 詳細資料

本節將討論在使用 CallKit 時，開發人員需要考慮的一些額外詳細資料，例如：

- 提供者設定
- 動作錯誤
- 系統限制
- VOIP 音訊

### <a name="provider-configuration"></a>提供者設定

提供者設定可讓 iOS 10 VOIP 應用程式自訂在使用 CallKit 時，在原生的呼叫中 UI) 內 (的使用者體驗。

應用程式可以進行下列自訂類型：

- 顯示當地語系化的名稱。
- 啟用影片通話支援。
- 藉由呈現自己的範本影像圖示，在呼叫中 UI 自訂按鈕。 使用者與自訂按鈕的互動會直接傳送至要處理的應用程式。

### <a name="action-errors"></a>動作錯誤

使用 CallKit 的 iOS 10 VOIP 應用程式需要適當地處理動作失敗，並隨時讓使用者知道動作狀態。

請考慮下列範例：

1. 應用程式已收到開始通話動作，並已開始使用其通訊網路初始化新的 VOIP 通話的程式。
2. 因為有受限或沒有網路通訊功能，所以此連接會失敗。
3. 應用程式 *必須* 將 **失敗** 訊息傳回給 [開始呼叫] 動作， (`Action.Fail()`) 通知系統失敗。
4. 這可讓系統通知使用者呼叫的狀態。 例如，顯示呼叫失敗的 UI。

此外，當預期的動作不能在指定的一段時間內處理時，iOS 10 VOIP 應用程式必須回應可能發生的 _逾時錯誤_ 。 CallKit 提供的每個動作類型都有與其相關聯的最大超時值。 這些超時值可確保使用者要求的任何 CallKit 動作都是以回應的方式處理，因此也能讓作業系統保持流暢和回應性。

提供者委派上有幾種方法 (`CXProviderDelegate`) 應該加以覆寫，才能正常地處理這個超時狀況。

### <a name="system-restrictions"></a>系統限制

根據執行 iOS 10 VOIP 應用程式之 iOS 裝置的目前狀態，可能會強制執行特定的系統限制。

例如，在下列情況下，系統可能會限制傳入的 VOIP 呼叫：

1. 呼叫的人員位於使用者的封鎖呼叫端清單。
2. 使用者的 iOS 裝置處於「不打擾」模式。

如果 VOIP 呼叫受限於任何一種情況，請使用下列程式碼來處理它：

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

CallKit 提供數個優點，可處理 iOS 10 VOIP 應用程式在即時 VOIP 通話期間所需的音訊資源。 其中一項最大的優點是，在 iOS 10 中執行時，應用程式的音訊會話會有更高的優先順序。 這與內建的電話和 FaceTime 應用程式具有相同的優先順序層級，而此增強的優先權層級會防止其他執行中的應用程式中斷 VOIP 應用程式的音訊會話。

此外，CallKit 也可以存取其他音訊路由提示，以增強效能，並根據使用者喜好設定和裝置狀態，以智慧方式將 VOIP 音訊路由傳送至特定輸出裝置。 例如，以連接的裝置（例如藍牙耳機）為基礎，即時 CarPlay 連接或協助工具設定。

在使用 CallKit 的一般 VOIP 呼叫生命週期期間，應用程式將需要設定 CallKit 將提供它的音訊串流。 請看下列範例：

[![開始通話動作順序](callkit-images/callkit09.png)](callkit-images/callkit09.png#lightbox)

1. 應用程式會收到開始呼叫動作，以回應傳入的呼叫。
2. 應用程式在完成此動作之前，會提供其所需的設定 `AVAudioSession` 。
3. 應用程式會通知系統該動作已完成。
4. 在呼叫連接之前，CallKit 會提供高優先順序的，以 `AVAudioSession` 符合應用程式所要求的設定。 應用程式會透過其的方法收到通知 `DidActivateAudioSession` `CXProviderDelegate` 。

## <a name="working-with-call-directory-extensions"></a>使用呼叫目錄延伸模組

使用 CallKit 時， _呼叫目錄延伸_ 模組可讓您新增封鎖的電話號碼，並將特定 VOIP 應用程式專屬的數位，識別為 iOS 裝置上 Contact 應用程式中的連絡人。

### <a name="implementing-a-call-directory-extension"></a>執行呼叫目錄延伸模組

若要在 Xamarin iOS 應用程式中執行呼叫目錄延伸，請執行下列動作：

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

1. 在 Visual Studio for Mac 中開啟應用程式的解決方案。
2. 以滑鼠右鍵按一下**方案總管**中的方案名稱，然後選取 [**加入**  >  **新專案**]。
3. 選取 [ **iOS**  >  **延伸**模組  >  **呼叫目錄擴充**功能]，然後按 [**下一步]** 按鈕：

    [![建立新的呼叫目錄延伸模組](callkit-images/calldir01.png)](callkit-images/calldir01.png#lightbox)
4. 輸入擴充功能的 **名稱** ，然後按 [ **下一步]** 按鈕：

    [![輸入擴充功能的名稱](callkit-images/calldir02.png)](callkit-images/calldir02.png#lightbox)
5. 視需要調整 **專案名稱** 和（或） **方案名稱** ，然後按一下 [ **建立** ] 按鈕：

    [![建立專案](callkit-images/calldir03.png)](callkit-images/calldir03.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. 在 Visual Studio 中開啟應用程式的解決方案。
2. 以滑鼠右鍵按一下**方案總管**中的方案名稱，然後選取 [**加入**  >  **新專案**]。
3. 選取 [ **iOS**  >  **延伸**模組  >  **呼叫目錄擴充**功能]，然後按 [**下一步]** 按鈕：

    [![建立新的呼叫目錄延伸模組](callkit-images/calldir01w.png)](callkit-images/calldir01.png#lightbox)
4. 輸入擴充功能的 **名稱** ，然後按一下 [ **確定]** 按鈕

-----

這會將 `CallDirectoryHandler.cs` 類別新增至專案，如下所示：

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

您必須 `BeginRequest` 修改呼叫目錄處理常式中的方法，以提供所需的功能。 在上述範例的案例中，它會嘗試設定 VOIP 應用程式連絡人資料庫中已封鎖和可用數位的清單。 如果任一要求因任何原因而失敗，請建立 `NSError` 以描述失敗，並將類別的方法傳遞給它 `CancelRequest` `CXCallDirectoryExtensionContext` 。

若要設定封鎖的數位，請使用 `AddBlockingEntry` 類別的方法 `CXCallDirectoryExtensionContext` 。 提供給方法的數位 _必須_ 以數位遞增順序。 若要在有許多電話號碼時取得最佳效能和記憶體使用量，請考慮只在指定時間載入數位子集，並使用 autorelease 集區 (s) 來釋放在載入的每個數位批次中所配置的物件。

若要通知與 VOIP 應用程式已知的連絡人電話號碼，請使用類別的 `AddIdentificationEntry` 方法， `CXCallDirectoryExtensionContext` 並同時提供數位和識別標籤。 同樣地，提供給方法的數位 _必須_ 以數位遞增順序。 若要在有許多電話號碼時取得最佳效能和記憶體使用量，請考慮只在指定時間載入數位子集，並使用 autorelease 集區 (s) 來釋放在載入的每個數位批次中所配置的物件。

## <a name="summary"></a>摘要

本文涵蓋了 Apple 在 iOS 10 中發行的新 CallKit API，以及如何在 Xamarin 和 Xamarin 應用程式中加以執行。 它已示範 CallKit 如何讓應用程式整合到 iOS 系統、如何提供與內建應用程式的功能同位， (例如電話) ，以及它如何透過 Siri 互動和透過「連絡人」應用程式，在整個 iOS 中增加應用程式的可見度，例如鎖定和主畫面。

## <a name="related-links"></a>相關連結

- [iOS 10 範例](/samples/browse/?products=xamarin&term=Xamarin.iOS%2biOS10)