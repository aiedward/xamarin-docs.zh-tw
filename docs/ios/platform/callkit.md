---
title: 在 Xamarin.iOS 中 CallKit
description: 本文章涵蓋新的 CallKit API，Apple 在 iOS 10 和如何實作 VOIP Xamarin.iOS 應用程式中發行。
ms.prod: xamarin
ms.assetid: 738A142D-FFD2-4738-B3ED-57C273179848
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/15/2017
ms.openlocfilehash: 6db9ff0085c17f07d07a7591f5d735793bfbc5f9
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61390156"
---
# <a name="callkit-in-xamarinios"></a>在 Xamarin.iOS 中 CallKit

在 iOS 10 中新的 CallKit API 提供 VOIP 與 iPhone UI 整合並提供熟悉的介面給終端使用者體驗的應用程式的方式。 使用此 API 使用者可以檢視和互動 VOIP 電話從 iOS 裝置鎖定畫面及管理使用的手機應用程式的連絡人**我的最愛**並**最近**檢視。

## <a name="about-callkit"></a>關於 CallKit

根據 Apple CallKit 會是新的架構，將會提升至第 1 方 iOS 10 上的體驗的第 3 個合作對象移轉 IP 語音 (VOIP) 應用程式。 CallKit API 允許 VOIP 應用程式整合與 iPhone UI 並提供熟悉的介面與體驗給使用者。 如同內建的手機應用程式，使用者可以檢視和互動 VOIP 電話從 iOS 裝置鎖定畫面和管理使用的手機應用程式的連絡人**我的最愛**並**最近**檢視。

此外，CallKit API 會提供建立應用程式延伸模組，其可以 （呼叫者識別碼） 的名稱相關聯的電話號碼，或告知系統應該是數字，可讓您封鎖 （封鎖呼叫） 的能力。

### <a name="the-existing-voip-app-experience"></a>現有的 VOIP 應用程式體驗

之前討論過新的 CallKit API 和其功能，看看目前的使用者經驗，使用第 3 方 VOIP 應用程式在 iOS 中的 9 （和較小者） 使用虛構的 VOIP 應用程式，稱為 MonkeyCall。 MonkeyCall 是簡單的應用程式，可讓使用者使用現有的 iOS Api 的 VOIP 撥打與接聽。

如果使用者收到 MonkeyCall 的傳入呼叫，而且他們的 iPhone 已被鎖定，鎖定畫面上接收到通知目前與任何其他類型的通知 (例如從訊息或例如郵件應用程式)。

如果使用者想要接聽電話，他們必須投影片 MonkeyCall 通知以開啟應用程式，並輸入他們的密碼 （或使用者 Touch ID） 或解除鎖定裝置，才能無法接受呼叫並啟動交談。

會解除鎖定行動電話是否一樣麻煩的體驗。 同樣地，連入 MonkeyCall 呼叫會顯示為投影片中，從畫面頂端的標準通知橫幅。 通知是暫時的因為它可以很容易忽略強迫使用者，開啟通知中心，並尋找特定的通知以回應呼叫或尋找然後手動啟動 MonkeyCall 應用程式。

### <a name="the-callkit-voip-app-experience"></a>CallKit VOIP 應用程式體驗

藉由實作新的 CallKit Api MonkeyCall 應用程式中，使用者體驗與 VOIP 來電可以大幅提升在 iOS 10。 採取上述鎖定他們的電話號碼時，接收 VOIP 通話之使用者的範例。 藉由實作 CallKit，呼叫會出現在 iPhone 的鎖定畫面上，就像呼叫來自內建的手機應用程式，以全螢幕、 原生 UI 和標準的撥動以回應功能一樣。

同樣地，如果收到 MonkeyCall VOIP 呼叫時，已解除鎖定 iPhone，相同的全螢幕、 原生 UI 和標準的撥動-回應 」 和 「 點選-拒絕功能內建的應用程式會顯示的電話和 MonkeyCall 可以選擇播放自訂的鈴聲.

CallKit 提供其他功能，可 MonkeyCall，允許其 VOIP 呼叫與其他類型的呼叫中，才會出現在 內建的 最近項目互動，以及我的最愛 清單中，若要使用內建的 「 請勿打擾 」 和 「 區塊 」 功能，請從 Siri 中啟動 MonkeyCall 呼叫和提供要指派給連絡人 」 應用程式中的人員 MonkeyCall 呼叫使用者的能力。

下列各節將討論 CallKit 架構，傳入和傳出呼叫流程和 CallKit API 詳細資料。

## <a name="the-callkit-architecture"></a>CallKit 架構

在 iOS 10 中，Apple 已採用 CallKit 中的所有系統服務，使得呼叫，例如對 CarPlay，已知透過 CallKit 系統 UI。 在此範例中，如下所示，因為 MonkeyCall 採用 CallKit，它系統識別為這些內建的系統服務相同的方式，並取得的所有相同功能：

[![](callkit-images/callkit01.png "CallKit 服務堆疊")](callkit-images/callkit01.png#lightbox)

仔細看看 MonkeyCall 應用程式，從上圖。 應用程式包含它的程式碼與它自己的網路通訊，與包含它自己的使用者介面。 它會連結 CallKit 系統與通訊：

[![](callkit-images/callkit02.png "MonkeyCall 應用程式架構")](callkit-images/callkit02.png#lightbox)

在應用程式使用的 CallKit 中有兩個主要的介面：

- `CXProvider` -這可讓 MonkeyCall 應用程式，以通知的系統可能會發生的任何頻外 」 通知。
- `CXCallController` -允許 MonkeyCall 應用程式，以通知系統的本機使用者動作。

### <a name="the-cxprovider"></a>CXProvider

如上所述，`CXProvider`讓應用程式通知的系統可能會發生的任何頻外 」 通知。 這些是本機使用者的動作，因為不會發生，但因為外部事件，例如來電而發生的通知。

應用程式應該使用`CXProvider`下列：

- 報告系統的來電。
- 報告的連出呼叫已連線到系統。
- 報告結束對系統呼叫的遠端使用者。

當應用程式想要對系統進行通訊時，它會使用`CXCallUpdate`類別，並當系統需要進行通訊與應用程式，它會使用`CXAction`類別：

[![](callkit-images/callkit03.png "透過 CXProvider 系統與通訊")](callkit-images/callkit03.png#lightbox)

### <a name="the-cxcallcontroller"></a>CXCallController

`CXCallController`讓應用程式通知系統的本機使用者動作，例如啟動 VOIP 電話的使用者。 藉由實作`CXCallController`應用程式取得系統中顯得與其他類型的呼叫。 例如，如果已經有進行中，使用中的電話語音通話`CXCallController`可以允許 VOIP 應用程式，該呼叫置於暫停並啟動或回答 VOIP 通話。

應用程式應該使用`CXCallController`下列：

- 使用者已開始傳出呼叫至系統時的報表。
- 當使用者接聽連入呼叫至系統的報表。
- 當使用者結束系統呼叫的報表。

當應用程式想要傳達系統的本機使用者動作時，它會使用`CXTransaction`類別：

[![](callkit-images/callkit04.png "報告系統，請使用 CXCallController")](callkit-images/callkit04.png#lightbox)

## <a name="implementing-callkit"></a>實作 CallKit

下列各節將示範如何實作 CallKit Xamarin.iOS VOIP 應用程式中。 作為範例，本文件將會使用虛構的 MonkeyCall VOIP 應用程式的程式碼。 此處所提供的程式碼代表數個支援的類別，下列各節詳細說明特定的組件將 CallKit。

### <a name="the-activecall-class"></a>ActiveCall 類別

`ActiveCall` MonkeyCall 應用程式使用類別來保存所有目前使用如下所示的 VOIP 呼叫的相關資訊：

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

`ActiveCall` 保留幾個屬性，定義呼叫和可呼叫的狀態變更時引發的兩個事件的狀態。 由於這是僅為範例，有三種用來模擬、 回答起訖呼叫的方法。

### <a name="the-startcallrequest-class"></a>StartCallRequest 類別

`StartCallRequest`靜態類別，提供一些使用連出呼叫時，會使用的 helper 方法：

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

`CallHandleFromURL`和`CallHandleFromActivity`類別 AppDelegate 中用來取得所呼叫的連出呼叫中的人員連絡的控制代碼。 如需詳細資訊，請參閱[處理連出呼叫](#handling-outgoing-calls)下一節。

### <a name="the-activecallmanager-class"></a>ActiveCallManager 類別

`ActiveCallManager`類別處理 MonkeyCall 應用程式中所有開啟的呼叫。

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

一次，因為這是模擬僅`ActiveCallManager`只會維護的集合`ActiveCall`物件，並具有常式，以尋找特定的呼叫，藉由其`UUID`屬性。 它也會包含開始、 結束及變更保留狀態的傳出呼叫的方法。 如需詳細資訊，請參閱[處理連出呼叫](#handling-outgoing-calls)下一節。

### <a name="the-providerdelegate-class"></a>ProviderDelegate 類別

如上所述，`CXProvider`提供頻外通知應用程式與系統之間的雙向通訊。 開發人員必須提供自訂`CXProviderDelegate`並將其附加至`CXProvider`來處理外的頻外 CallKit 事件的應用程式。 MonkeyCall 會使用下列`CXProviderDelegate`:

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

建立這個委派的執行個體時，它會傳遞`ActiveCallManager`它將用來處理任何呼叫活動。 接下來，它會定義控制代碼類型 (`CXHandleType`)，`CXProvider`會回應：

```csharp
// Define handle types
var handleTypes = new [] { (NSNumber)(int)CXHandleType.PhoneNumber };
```

然後它會取得範本映像，在進行呼叫時將套用至應用程式的圖示：

```csharp
// Get Image Template
var templateImage = UIImage.FromFile ("telephone_receiver.png");
```

這些值取得打包成`CXProviderConfiguration`會用來設定`CXProvider`:

```csharp
// Setup the initial configurations
Configuration = new CXProviderConfiguration ("MonkeyCall") {
    MaximumCallsPerCallGroup = 1,
    SupportedHandleTypes = new NSSet<NSNumber> (handleTypes),
    IconTemplateImageData = templateImage.AsPNG(),
    RingtoneSound = "musicloop01.wav"
};
```

委派接著會建立新`CXProvider`這種組態並將自己連結到它：

```csharp
// Create a new provider
Provider = new CXProvider (Configuration);

// Attach this delegate
Provider.SetDelegate (this, null);
```

當使用 CallKit，應用程式將不會再建立並處理其本身的音訊工作階段，而是需要設定及使用的音訊工作階段，系統會建立並處理它。 

如果這是真實的應用程式`DidActivateAudioSession`方法會用來開始使用預先設定的呼叫`AVAudioSession`系統提供：

```csharp
public override void DidActivateAudioSession (CXProvider provider, AVFoundation.AVAudioSession audioSession)
{
    // Start the call's audio session here...
}
```

它也會使用`DidDeactivateAudioSession`來完成，並釋放其連線到系統的方法提供音訊工作階段：

```csharp
public override void DidDeactivateAudioSession (CXProvider provider, AVFoundation.AVAudioSession audioSession)
{
    // End the calls audio session and restart any non-call
    // releated audio
}
```

其餘的程式碼將會在下列各節中詳細說明。

### <a name="the-appdelegate-class"></a>AppDelegate 類別

MonkeyCall 用來保留的執行個體的 AppDelegate`ActiveCallManager`和`CXProviderDelegate`，將整個應用程式使用：

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

`OpenUrl`和`ContinueUserActivity`應用程式正在處理的連出呼叫時，會使用方法的覆寫。 如需詳細資訊，請參閱[處理連出呼叫](#handling-outgoing-calls)下一節。

## <a name="handling-incoming-calls"></a>處理傳入的呼叫

有數個狀態和 VOIP 來電可以期間經歷一般的連入呼叫工作流程這類的程序：

- 通知使用者 （和系統） 的傳入呼叫存在。
- 當使用者想要接聽電話時，收到通知，並初始化與其他使用者的呼叫。
- 系統與通訊網路時通知使用者想要結束目前的呼叫。

下列各節會詳細的說明了如何應用程式可以使用 CallKit 來處理連入呼叫工作流程，再使用 MonkeyCall VOIP 應用程式做為範例。

### <a name="informing-user-of-incoming-call"></a>通知使用者的連入呼叫

遠端使用者已開始 VOIP 交談的本機使用者，會發生下列情況：

[![](callkit-images/callkit05.png "遠端使用者已開始 VOIP 交談")](callkit-images/callkit05.png#lightbox)

1. 應用程式取得通知其通訊網路從沒有連入的 VOIP 通話。
2. 應用程式會使用`CXProvider`傳送`CXCallUpdate`通知它呼叫的系統。
3. 系統會發佈到系統 UI、 系統服務和其他任何 VOIP 應用程式使用 CallKit 呼叫。

例如，在`CXProviderDelegate`:

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

此程式碼會建立新`CXCallUpdate`執行個體，並將控制代碼附加至它會識別呼叫端。 接著，它會使用`ReportNewIncomingCall`方法的`CXProvider`類別，以通知呼叫的系統。 如果成功，新增呼叫至作用中呼叫的應用程式的集合，如果沒有，錯誤需要回報給使用者。

### <a name="user-answering-incoming-call"></a>使用者接聽連入呼叫

如果使用者想要回應連入的 VOIP 電話，執行下列動作：

[![](callkit-images/callkit06.png "使用者接聽來電的 VOIP")](callkit-images/callkit06.png#lightbox)

1. 系統 UI 會通知系統使用者想要回答 VOIP 通話。
2. 系統會傳送`CXAnswerCallAction`應用程式的`CXProvider`通知它回應意圖。
3. 使用者已接聽電話，VOIP 通話會繼續如往常一樣，應用程式就會通知它通訊的網路。

例如，在`CXProviderDelegate`:

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

此程式碼首先會搜尋在作用中呼叫其清單中指定的呼叫。 如果找不到呼叫，系統會通知，並在方法結束時。 如果找到它，則`AnswerCall`方法的`ActiveCall`類別稱為以開始呼叫，它成功或失敗時，系統會為資訊。

### <a name="user-ending-incoming-call"></a>結束連入呼叫的使用者

如果使用者想要終止來自應用程式的使用者介面內的呼叫，會發生下列情況：

[![](callkit-images/callkit07.png "使用者終止來自應用程式的使用者介面內的呼叫")](callkit-images/callkit07.png#lightbox)

1. 應用程式建立`CXEndCallAction`，取得其結合成`CXTransaction`，會傳送至系統，通知它結束時呼叫。
2. 系統會驗證結束呼叫意圖，並將傳送`CXEndCallAction`給應用程式透過`CXProvider`。
3. 應用程式接著會通知它通訊的網路呼叫而結束。

例如，在`CXProviderDelegate`:

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

此程式碼首先會搜尋在作用中呼叫其清單中指定的呼叫。 如果找不到呼叫，系統會通知，並在方法結束時。 如果找到它，則`EndCall`方法的`ActiveCall`類別稱為 「 結束通話和它成功或失敗時，系統會為資訊。 如果成功，呼叫會從 作用中呼叫的集合中移除。

## <a name="managing-multiple-calls"></a>管理多個呼叫

大部分的 VOIP 應用程式可以同時處理多個呼叫。 例如，如果目前沒有使用中的 VOIP 撥號和應用程式取得的通知有新的傳入呼叫，使用者可以暫停或掛斷回答第二個第一次呼叫。

在上述的情況下提供，系統會傳送`CXTransaction`應用程式將包含多個動作的清單 (例如`CXEndCallAction`而`CXAnswerCallAction`)。 所有這些動作必須滿足個別，以便系統可以適當地更新 UI。

## <a name="handling-outgoing-calls"></a>處理傳出呼叫

如果在使用者點選 最近項目清單 （依的手機應用程式） 的項目，比方說，這呼叫屬於應用程式，它會傳送_開始呼叫意圖_系統：

[![](callkit-images/callkit08.png "接收的開始呼叫意圖")](callkit-images/callkit08.png#lightbox)

1. 應用程式會建立_開始呼叫動作_根據開始呼叫目的，它從系統接收。 
2. 應用程式會使用`CXCallController`向系統要求起始呼叫動作。
3. 如果系統接受該動作，將傳回至應用程式透過`XCProvider`委派。
4. 應用程式會開始其通訊的網路中的連出呼叫。

如需有關對應方式的詳細資訊，請參閱我們[意圖和 Intents UI 延伸模組](~/ios/platform/sirikit/understanding-sirikit.md)文件。 

### <a name="the-outgoing-call-lifecycle"></a>連出呼叫生命週期

使用 CallKit 和連出呼叫時，應用程式必須通知系統的下列的生命週期事件：

1. **啟動**-通知即將開始撥出的電話系統。
2. **啟動**-通知已開始傳出呼叫的系統。
3. **連接**-通知系統連接連出呼叫。
4. **連接**-通知的傳出呼叫已連線且雙方可以彼此通訊現在。

例如，下列程式碼將會啟動的傳出呼叫：

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

它會建立`CXHandle`並使用它來設定`CXStartCallAction`這會打包成`CXTransaction`也就是傳送至系統使用`RequestTransaction`方法`CXCallController`類別。 藉由呼叫`RequestTransaction`方法，系統可以將任何現有的呼叫保留，不論來源 (電話應用程式、 FaceTime、 VOIP 等)，然後才能在新的呼叫會啟動。

啟動出 VOIP 電話的要求可以來自數個不同來源的詳細資訊，例如 Siri，連絡人卡片 （在連絡人應用程式中） 上的項目或從 最近項目清單 （以的手機應用程式）。 在這些情況下，應用程式會傳送啟動呼叫意圖內`NSUserActivity`AppDelegate 必須加以處理：

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

以下`CallHandleFromActivity`方法的協助程式類別`StartCallRequest`正被呼叫的人員取得的控制代碼 (請參閱[StartCallRequest 類別](#the-startcallrequest-class)上方)。

`PerformStartCallAction`方法[ProviderDelegate 類別](#the-providerdelegate-class)來最後啟動實際的傳出呼叫，並通知系統的生命週期：

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

它會建立的執行個體`ActiveCall`類別 （保存在進行中的呼叫的相關資訊），並填入所呼叫的人員。 `StartingConnectionChanged`和`ConnectedChanged`事件都會用來監控和報告的傳出呼叫生命週期。 呼叫會啟動，系統會通知已完成的動作。

### <a name="ending-an-outgoing-call"></a>結束傳出呼叫

當使用者完成與連出的呼叫，並希望結束時，就可以使用下列程式碼：

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

如果會建立`CXEndCallAction`呼叫來結束的 uuid，組合中`CXTransaction`也就是傳送至系統使用`RequestTransaction`方法`CXCallController`類別。 

## <a name="additional-callkit-details"></a>CallKit 的其他詳細資料

本章節將涵蓋一些開發人員需要納入考量，例如使用 CallKit 時的其他詳細資料：

- 提供者組態
- 動作錯誤
- 系統限制
- VOIP 音訊

### <a name="provider-configuration"></a>提供者組態

提供者組態可讓 iOS 10 VOIP 應用程式時自訂使用者體驗 （在原生的呼叫在 UI 中) 使用 CallKit。

應用程式可以進行下列類型的自訂項目：

- 顯示當地語系化的名稱。
- 啟用視訊通話的支援。
- 自訂中呼叫 UI 上的按鈕會呈現它自己的範本影像圖示。 具有自訂按鈕的使用者互動會直接傳送至應用程式進行處理。 

### <a name="action-errors"></a>動作錯誤

使用 CallKit iOS 10 VOIP 應用程式必須處理失敗的依正常程序的動作，並隨時通知動作狀態的使用者。 

下列範例中列入考量：

1. 應用程式已收到啟動呼叫動作，且已開始初始化新的 VOIP 通話，與它通訊網路的程序。
2. 有限或沒有網路通訊功能，因為此連線會失敗。
3. 應用程式*必須*傳送**失敗**訊息傳回給起始呼叫動作 (`Action.Fail()`) 來通知失敗的系統。
4. 這可讓系統將通知使用者已呼叫的狀態。 例如，若要顯示的呼叫失敗 UI。

此外，iOS 10 VOIP 應用程式必須回應_逾時錯誤_時預期的動作無法處理指定的一段時間內可能發生的。 CallKit 所提供的每個動作類型都有與其相關聯的最大逾時值。 這些逾時值確保使用者所要求的任何 CallKit 動作處理回應的方式，藉此讓作業系統流暢並反應快速以及。

提供者在委派上有數種方法 (`CXProviderDelegate`)，應該要適當地處理這個逾時情況下覆寫。

### <a name="system-restrictions"></a>系統限制

根據執行 iOS 10 VOIP 應用程式的 iOS 裝置的目前狀態，特定系統可能會強制執行限制。

比方說，VOIP 的來電會限制系統上，如果：

1. 呼叫的人員是在使用者的封鎖呼叫端清單上。
2. 使用者的 iOS 裝置處於 Do Not 打擾的模式。

如果 VOIP 呼叫會受到任何這些情況下，使用下列程式碼來處理它：

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

CallKit 處理的音訊的資源，iOS 10 VOIP 應用程式需要即時的 VOIP 通話期間提供多項優點。 最大的好處之一是應用程式的音訊工作階段將會有更高的優先順序在 iOS 10 中執行時。 這是相同的優先順序層級為內建的電話和 FaceTime 應用程式和這個增強的優先權層級會阻礙其他執行的應用程式中斷 VOIP 應用程式的音訊工作階段。

此外，CallKit 可以存取其他的可以加強效能，並根據使用者喜好設定和裝置狀態的即時呼叫期間以智慧方式路由到特定的輸出裝置的 VOIP 音訊音訊路由提示。 例如，根據附加的裝置，例如藍芽耳機，即時 CarPlay 連線或協助工具設定。

在典型的 VOIP 的生命週期期間呼叫使用 CallKit，應用程式必須設定 CallKit 提供音訊 Stream。 看看下列的範例：

[![](callkit-images/callkit09.png "開始呼叫動作順序")](callkit-images/callkit09.png#lightbox)

1. 開始呼叫動作是由接聽連入呼叫的應用程式接收。
2. 應用程式可完成此動作之前，它就會提供之組態的需要來執行其`AVAudioSession`。
3. 應用程式會通知系統的動作已完成。
4. CallKit 呼叫連接之前，提供高優先順序`AVAudioSession`比對應用程式要求的組態。 應用程式將會收到`DidActivateAudioSession`方法及其`CXProviderDelegate`。

## <a name="working-with-call-directory-extensions"></a>使用電話目錄延伸模組

使用 CallKit 時,_呼叫目錄延伸模組_可用來新增封鎖的呼叫數字，並找出指定的 VOIP 應用程式，以在連絡人應用程式在 iOS 裝置上的連絡人專屬的數字。

### <a name="implementing-a-call-directory-extension"></a>實作呼叫目錄延伸模組

若要實作 Call Directory 延伸模組的 Xamarin.iOS 應用程式中，執行下列作業：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

1. 應用程式的方案在 Visual Studio for mac 中開啟
2. 中的方案名稱上按一下滑鼠右鍵**方案總管**，然後選取**新增** > **加入新的專案**。
3. 選取 [ **iOS** > **延伸模組** > **呼叫目錄延伸模組**然後按一下**下一步]** 按鈕： 

    [![](callkit-images/calldir01.png "建立新的 Call Directory 延伸模組")](callkit-images/calldir01.png#lightbox)
4. 請輸入**名稱**延伸模組，然後按一下 [**下一步]** 按鈕： 

    [![](callkit-images/calldir02.png "輸入延伸模組的名稱")](callkit-images/calldir02.png#lightbox)
5. 調整**專案名稱**及/或**方案名稱**如果需要，然後按一下**建立**按鈕： 

    [![](callkit-images/calldir03.png "建立專案")](callkit-images/calldir03.png#lightbox) 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Visual Studio 中開啟應用程式的方案。
2. 中的方案名稱上按一下滑鼠右鍵**方案總管**，然後選取**新增** > **加入新的專案**。
3. 選取 [ **iOS** > **延伸模組** > **呼叫目錄延伸模組**然後按一下**下一步]** 按鈕： 

    [![](callkit-images/calldir01w.png "建立新的 Call Directory 延伸模組")](callkit-images/calldir01.png#lightbox)
4. 請輸入**名稱**延伸模組，然後按一下 **確定**按鈕

-----

這會新增`CallDirectoryHandler.cs`類別至專案，看起來如下所示：

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

`BeginRequest`呼叫目錄處理常式方法需要修改，以提供必要的功能。 在上述範例中，它會嘗試在 VOIP 應用程式的連絡人資料庫中設定的封鎖和可用的數字清單。 如果其中一個要求失敗，因為任何原因，建立`NSError`描述失敗，並將其傳遞`CancelRequest`方法`CXCallDirectoryExtensionContext`類別。

若要設定已封鎖的數字，請使用`AddBlockingEntry`方法的`CXCallDirectoryExtensionContext`類別。 提供給方法的數字_必須_中加上數字遞增的順序。 提供最佳效能和記憶體使用量時有許多電話號碼，請考慮只在指定的時間載入的數字子集，並釋放載入的數字的每個批次期間配置的物件中使用 autorelease 集區。

若要通知連絡人已知 VOIP 應用程式的數字的連絡人應用程式，使用`AddIdentificationEntry`方法的`CXCallDirectoryExtensionContext`類別，並提供數字和識別的標籤。 同樣地，提供給方法的數字_必須_中加上數字遞增的順序。 提供最佳效能和記憶體使用量時有許多電話號碼，請考慮只在指定的時間載入的數字子集，並釋放載入的數字的每個批次期間配置的物件中使用 autorelease 集區。

## <a name="summary"></a>總結

這篇文章已涵蓋新的 CallKit API，Apple 在 iOS 10 和如何實作 VOIP Xamarin.iOS 應用程式中發行。 它具有顯示 CallKit 如何讓應用程式整合至 iOS 系統、 如何使用內建的應用程式 （例如電話） 提供類似的功能，並且它會在 iOS 中位置，例如鎖定和首頁畫面中，透過 Siri 互動以及透過整個應用程式的可見性的增加連絡人的應用程式。

## <a name="related-links"></a>相關連結

- [iOS 10 範例](https://developer.xamarin.com/samples/ios/iOS10/)
