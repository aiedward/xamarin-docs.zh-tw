---
title: CallKit
description: 本文涵蓋新 CallKit API 的 Apple iOS 10 以及如何實作 Xamarin.iOS VOIP 應用程式中發行。
ms.prod: xamarin
ms.assetid: 738A142D-FFD2-4738-B3ED-57C273179848
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/15/2017
ms.openlocfilehash: 67c761aa6656b571f16632dd1a076ff11737a424
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="callkit"></a>CallKit

_本文涵蓋新 CallKit API 的 Apple iOS 10 以及如何實作 Xamarin.iOS VOIP 應用程式中發行。_


在 iOS 10 中新的 CallKit API 提供 VOIP 與 iPhone UI 整合提供了熟悉的介面和終端使用者體驗的應用程式的方式。 透過此 API 使用者可以檢視和互動的 iOS 裝置鎖定畫面的 VOIP 呼叫和管理使用電話應用程式的連絡人**我的最愛**和**最近**檢視。

## <a name="about-callkit"></a>關於 CallKit

根據 Apple，CallKit 是會提升至第 1 合作對象 iOS 10 上的體驗的第 3 個合作對象透過 IP 語音 (VOIP) 應用程式的新架構。 CallKit API 允許 VOIP 應用程式與 iPhone UI 整合提供了熟悉的介面和終端使用者體驗。 如同內建的電話應用程式，使用者可以檢視和互動 VOIP 呼叫從 iOS 裝置鎖定畫面與管理連絡人使用的手機應用程式**我的最愛**和**最近**檢視。

此外，CallKit API 會提供建立應用程式擴充功能，可以將電話號碼 （呼叫者識別碼） 名稱產生關聯，或告知時應該是數字，系統封鎖 （呼叫封鎖） 的能力。

### <a name="the-existing-voip-app-experience"></a>現有的 VOIP 應用程式體驗

之前討論新 CallKit API，其功能時，看看目前的使用者體驗，以第 3 合作對象 VOIP 應用程式在 iOS 9 （和較小者） 會使用稱為 MonkeyCall 虛構 VOIP 應用程式。 MonkeyCall 是簡單的應用程式可讓使用者傳送及接收 VOIP 呼叫使用現有的 iOS 應用程式開發介面。

目前，使用者會接收 MonkeyCall 上的傳入呼叫，且其 iPhone 已鎖定，鎖定螢幕上收到通知是否與任何其他類型的通知 (例如從訊息或例如郵件應用程式)。

如果使用者想要接聽電話，他們必須在投影片 MonkeyCall 通知，以開啟應用程式並輸入其密碼 （或使用者 Touch ID） 無法接受呼叫和啟動交談之前解除鎖定手機。

經驗將非常累贅如果電話已解除鎖定。 同樣地，MonkeyCall 來電顯示為投影片中從畫面頂端的標準通知橫幅。 通知是暫時性的因為它可以輕鬆地遺失迫使他們開啟通知中心，並尋找回答接著呼叫，或找出並以手動方式啟動 MonkeyCall 應用程式特定的通知使用者。

### <a name="the-callkit-voip-app-experience"></a>CallKit VOIP 應用程式體驗

藉由實作新的 CallKit Api MonkeyCall 應用程式中，使用者體驗與 VOIP 來電可以大幅改善在 iOS 10 中。 需要使用者會接收 VOIP 呼叫上述鎖定其電話時的範例。 藉由實作 CallKit，呼叫會出現在 iPhone 的鎖定畫面上，就像呼叫來自內建的電話應用程式，以全螢幕、 原生 UI 和標準撥動至回應的功能一樣。

同樣地，如果收到 MonkeyCall VOIP 呼叫時，在 iPhone 已解除鎖定，相同的全螢幕、 原生 UI 和標準撥動至回應的功能和點選-拒絕功能內建應用程式會顯示的電話和 MonkeyCall 已播放自訂鈴聲選項.

CallKit 提供額外的功能來 MonkeyCall，允許其 VOIP 呼叫與其他類型的呼叫，才會出現在最近內建互動，以及列出我的最愛，若要使用的內建的請勿打擾和封鎖功能，請從 Siri 啟動 MonkeyCall 呼叫和提供要指派給連絡人應用程式中的人員 MonkeyCall 呼叫使用者的能力。

下列各節將討論 CallKit 架構，傳入和傳出呼叫流程和 CallKit API 中的詳細資料。


## <a name="the-callkit-architecture"></a>CallKit 架構

在 iOS 10 中，Apple 已採用 CallKit 中的所有系統服務，使呼叫，例如對 CarPlay，已知透過 CallKit 系統 UI。 在此範例中，如下所示，因為 MonkeyCall 採用 CallKit，它系統識別為這些內建的系統服務相同的方式，並取得所有相同功能：

[![](callkit-images/callkit01.png "CallKit 服務堆疊")](callkit-images/callkit01.png#lightbox)

需要進一步 MonkeyCall 應用程式上, 圖中查看。 應用程式包含的程式碼，以與它自己的網路通訊，與包含它自己的使用者介面。 它會連結 CallKit 與系統進行通訊：

[![](callkit-images/callkit02.png "MonkeyCall 應用程式架構")](callkit-images/callkit02.png#lightbox)

應用程式中使用的 CallKit 有兩個主要的介面：

- `CXProvider` -這可讓 MonkeyCall 應用程式對系統可能會發生任何不足的頻外通知的通知。
- `CXCallController` -允許本機使用者動作的系統通知 MonkeyCall 應用程式。

### <a name="the-cxprovider"></a>CXProvider

如上所述，`CXProvider`讓應用程式對系統可能會發生任何不足的頻外通知的通知。 這些是本機使用者的動作，因為不會發生，但等的連入呼叫外部事件，而發生的通知。

應用程式應該使用`CXProvider`下列：

- 報告系統的傳入呼叫。
- 報告之傳出呼叫已連線到系統。
- 報告結束系統呼叫的遠端使用者。

當應用程式想要對系統進行通訊時，它會使用`CXCallUpdate`類別，並在系統需要時與應用程式通訊，它會使用`CXAction`類別：

[![](callkit-images/callkit03.png "與透過 CXProvider 系統通訊")](callkit-images/callkit03.png#lightbox)

### <a name="the-cxcallcontroller"></a>CXCallController

`CXCallController`可讓本機使用者動作，例如使用者啟動 VOIP 電話系統通知的應用程式。 藉由實作`CXCallController`應用程式會取得系統中其他類型的呼叫相互作用。 例如，如果已經有進行中，使用中的電話語音通話`CXCallController`可以允許將該呼叫放保留並啟動或接聽 VOIP 呼叫 VOIP 應用程式。

應用程式應該使用`CXCallController`下列：

- 使用者已開始傳出呼叫系統時，報表。
- 當使用者接聽連入呼叫至系統的報表。
- 當使用者結束系統呼叫的報表。

當應用程式想要傳達系統的本機使用者動作時，它會使用`CXTransaction`類別：

[![](callkit-images/callkit04.png "使用 CXCallController 之系統報告")](callkit-images/callkit04.png#lightbox)

## <a name="implementing-callkit"></a>實作 CallKit

下列各節將示範如何實作 CallKit Xamarin.iOS VOIP 應用程式中。 這是因為範例中，這份文件將會使用從虛構 MonkeyCall VOIP 應用程式的程式碼。 這裡所呈現的程式碼代表數個支援的類別，將特定部分 CallKit 涵蓋下列各節中詳細。

### <a name="the-activecall-class"></a>ActiveCall 類別

`ActiveCall` MonkeyCall 應用程式使用類別來保存所有是目前作用中，如下所示的 VOIP 呼叫的相關資訊：

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

`ActiveCall` 會保留幾個屬性，定義該呼叫，而可能在呼叫狀態變更時引發的兩個事件的狀態。 由於這是僅為範例，有三種用來模擬、 回答起訖呼叫的方法。

### <a name="the-startcallrequest-class"></a>StartCallRequest 類別

`StartCallRequest`靜態類別，提供一些使用連出呼叫時所使用的 helper 方法：

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

`CallHandleFromURL`和`CallHandleFromActivity`類別 AppDelegate 中用來取得在傳出呼叫中被呼叫的人員連絡的控制代碼。 如需詳細資訊，請參閱[處理連出呼叫](#Handling-Outgoing-Calls)下一節。

### <a name="the-activecallmanager-class"></a>ActiveCallManager 類別

`ActiveCallManager`類別處理所有開啟的 MonkeyCall 應用程式中呼叫。

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
                if (call.UUID == uuid) return call;
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

一次，因為這是模擬，`ActiveCallManager`只會維護的集合`ActiveCall`物件且具有常式，以尋找在特定的呼叫由其`UUID`屬性。 它也包含開始、 結束和變更在保留狀態傳出呼叫的方法。 如需詳細資訊，請參閱[處理連出呼叫](#Handling-Outgoing-Calls)下一節。

### <a name="the-providerdelegate-class"></a>ProviderDelegate 類別

如上所述，`CXProvider`提供的頻外通知應用程式和系統之間的雙向通訊。 開發人員必須提供自訂`CXProviderDelegate`並將其附加至`CXProvider`應用程式要處理的頻外 CallKit 事件。 MonkeyCall 會使用下列`CXProviderDelegate`:

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

            // Get Image Mask
            var maskImage = UIImage.FromFile ("telephone_receiver.png");

            // Setup the initial configurations
            Configuration = new CXProviderConfiguration ("MonkeyCall") {
                MaximumCallsPerCallGroup = 1,
                SupportedHandleTypes = new NSSet<NSNumber> (handleTypes),
                IconMaskImageData = maskImage.AsPNG(),
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
                    Provider.ReportConnectingOutgoingCall (call.UUID, call.StartedConnectingOn.ToNsDate());
                }
            };

            activeCall.ConnectedChanged += (call) => {
                if (call.isConnected) {
                    // Inform system that the call has connected
                    provider.ReportConnectedOutgoingCall (call.UUID, call.ConnectedOn.ToNsDate ());
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

建立這個委派的執行個體時，它會傳遞`ActiveCallManager`它會使用來處理任何呼叫活動。 接下來，它會定義控制代碼類型 (`CXHandleType`)，`CXProvider`會回應：

```csharp
// Define handle types
var handleTypes = new [] { (NSNumber)(int)CXHandleType.PhoneNumber };
```

然後它會取得正在進行呼叫時將套用至應用程式的圖示的遮罩：

```csharp
// Get Image Mask
var maskImage = UIImage.FromFile ("telephone_receiver.png");
```

這些值取得配套到`CXProviderConfiguration`會用來設定`CXProvider`:

```csharp
// Setup the initial configurations
Configuration = new CXProviderConfiguration ("MonkeyCall") {
    MaximumCallsPerCallGroup = 1,
    SupportedHandleTypes = new NSSet<NSNumber> (handleTypes),
    IconMaskImageData = maskImage.AsPNG(),
    RingtoneSound = "musicloop01.wav"
};
```

委派接著會建立新`CXProvider`這種組態，並將本身附加它：

```csharp
// Create a new provider
Provider = new CXProvider (Configuration);

// Attach this delegate
Provider.SetDelegate (this, null);
```

當使用 CallKit，應用程式將不會再建立並處理其本身音訊的工作階段，而是需要設定及使用的音訊的工作階段，系統將會建立並處理它。 

如果這是實際的應用程式，`DidActivateAudioSession`方法會用來開始使用預先設定的呼叫`AVAudioSession`系統提供：

```csharp
public override void DidActivateAudioSession (CXProvider provider, AVFoundation.AVAudioSession audioSession)
{
    // Start the call's audio session here...
}
```

它也會使用`DidDeactivateAudioSession`提供音訊的工作階段來完成，並釋放其連線到系統的方法：

```csharp
public override void DidDeactivateAudioSession (CXProvider provider, AVFoundation.AVAudioSession audioSession)
{
    // End the calls audio session and restart any non-call
    // releated audio
}
```

其餘程式碼將在以下各節中詳細討論。

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

`OpenUrl`和`ContinueUserActivity`覆寫應用程式會處理傳出呼叫時，所使用的方法。 如需詳細資訊，請參閱[處理連出呼叫](#Handling-Outgoing-Calls)下一節。

## <a name="handling-incoming-calls"></a>處理傳入的呼叫

有數個狀態和 VOIP 來電可以瀏覽一般的連入呼叫工作流程期間這類的處理程序：

- 通知使用者 （和系統） 的傳入呼叫存在。
- 當使用者想要接聽電話時收到通知，並初始化與其他使用者呼叫。
- 系統與之通訊網路時通知使用者想要結束目前的呼叫。

下列各節會詳細的探討應用程式如何使用 CallKit 來處理連入呼叫工作流程，再使用 MonkeyCall VOIP 應用程式做為範例。

### <a name="informing-user-of-incoming-call"></a>通知使用者的連入呼叫

當遠端使用者已開始 VOIP 交談與本機使用者，則發生下列情況：

[![](callkit-images/callkit05.png "遠端使用者已開始 VOIP 交談")](callkit-images/callkit05.png#lightbox)

1. 應用程式取得通知從其通訊網路是網路電話來電。
2. 應用程式會使用`CXProvider`傳送`CXCallUpdate`系統通知它的呼叫。
3. 系統將發佈系統 UI、 系統服務以及其他任何 VOIP 應用程式使用 CallKit 的呼叫。

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

此程式碼會建立新`CXCallUpdate`執行個體，並將控制代碼附加至它，以識別呼叫端。 接著，它會使用`ReportNewIncomingCall`方法`CXProvider`對系統呼叫的通知類別。 如果成功，呼叫會加入至應用程式的集合中的作用中呼叫，不是，如果錯誤需要向使用者報告。

### <a name="user-answering-incoming-call"></a>使用者接聽來電

如果使用者想要回答 VOIP 來電時，發生下列情況：

[![](callkit-images/callkit06.png "使用者接聽連入 VOIP 電話")](callkit-images/callkit06.png#lightbox)

1. 系統 UI 會通知系統使用者想要回答 VOIP 呼叫。
2. 系統會傳送`CXAnswerCallAction`應用程式的`CXProvider`通知它的回應意圖。
3. 應用程式會在使用者已接聽電話，並如往常般繼續 VOIP 呼叫通知其通訊網路。

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

此程式碼會先搜尋作用中呼叫的清單中指定的呼叫。 如果找不到呼叫，系統會通知，方法會結束。 如果找到，`AnswerCall`方法`ActiveCall`類別稱為以開始呼叫，它成功或失敗時，系統會為資訊。

### <a name="user-ending-incoming-call"></a>結束連入呼叫的使用者

如果使用者想要終止的呼叫應用程式的 UI 中，發生下列情況：

[![](callkit-images/callkit07.png "使用者終止來自應用程式的 UI 中的呼叫")](callkit-images/callkit07.png#lightbox)

1. 應用程式會建立`CXEndCallAction`，取得配套到`CXTransaction`系統傳送通知到結束時呼叫。
2. 系統會驗證結束呼叫意圖，並將傳送`CXEndCallAction`回應用程式透過`CXProvider`。
3. 應用程式接著會通知其通訊網路結束時呼叫。

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

此程式碼會先搜尋作用中呼叫的清單中指定的呼叫。 如果找不到呼叫，系統會通知，方法會結束。 如果找到，`EndCall`方法`ActiveCall`類別稱為 「 結束通話和它成功或失敗時，系統會為資訊。 如果成功的話，呼叫會從作用中呼叫集合中移除。

## <a name="managing-multiple-calls"></a>管理多個呼叫

大部分的 VOIP 應用程式可以處理多個呼叫一次。 例如，如果目前沒有使用中的 VOIP 撥號和應用程式取得通知有新的傳入呼叫，使用者可以暫停或掛斷回答第二個第一次呼叫。

在上述的情況下提供，系統會將傳送`CXTransaction`應用程式包含多個動作的清單 (例如`CXEndCallAction`和`CXAnswerCallAction`)。 所有這些動作必須符合個別，使系統可以適當地更新 UI。

## <a name="handling-outgoing-calls"></a>處理傳出呼叫

如果在使用者點選最近清單 （依電話應用程式） 的項目，例如，而且從呼叫屬於應用程式時，便會傳送_開始呼叫意圖_系統：

[![](callkit-images/callkit08.png "接收開始呼叫意圖")](callkit-images/callkit08.png#lightbox)

1. 應用程式會建立_開始呼叫動作_根據開始呼叫用途它收到來自系統。 
2. 應用程式會使用`CXCallController`向系統要求起始呼叫動作。
3. 如果系統同時接受此動作，將傳回至應用程式透過`XCProvider`委派。
4. 應用程式會啟動其通訊網路傳出呼叫。

如需有關對應方式的詳細資訊，請參閱我們[目的 UI 延伸模組的對應方式和](~/ios/platform/sirikit/understanding-sirikit.md)文件。 

### <a name="the-outgoing-call-lifecycle"></a>傳出呼叫生命週期

當使用 CallKit 和傳出呼叫，應用程式需要下列的生命週期事件系統通知：

1. **啟動**-對傳出呼叫即將開始的系統通知。
2. **啟動**-已開始傳出呼叫系統通知。
3. **連接**-對傳出呼叫連接的系統通知。
4. **連接**-通知傳出呼叫已連線且雙方可以彼此通訊現在。

例如，下列程式碼將會啟動傳出呼叫：

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

它會建立`CXHandle`並使用它來設定`CXStartCallAction`的已包裝成`CXTransaction`也就是傳送給系統使用`RequestTransaction`方法`CXCallController`類別。 藉由呼叫`RequestTransaction`方法時，系統可以將任何現有的呼叫上不放，不論來源 (Phone 應用程式、 FaceTime、 VOIP 等)，然後才能在新的呼叫會啟動。

啟動傳出 VOIP 呼叫要求可以來自數個不同來源的詳細資訊，例如 Siri，連絡人卡片 （在連絡人應用程式中） 上的項目，或是從最近清單 （在電話應用程式）。 在這些情況下，應用程式會傳送啟動呼叫意圖內`NSUserActivity`和 AppDelegate 需要處理它：

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

這裡`CallHandleFromActivity`協助程式類別的方法`StartCallRequest`用來取得控制代碼所呼叫的人員 (請參閱[StartCallRequest 類別](#The-StartCallRequest-Class)上方)。 

`PerformStartCallAction`方法[ProviderDelegate 類別](#The-ProviderDelegate-Class)用於最後啟動實際的傳出呼叫，並對其生命週期的系統通知：

```csharp
public override void PerformStartCallAction (CXProvider provider, CXStartCallAction action)
{
    // Create new call record
    var activeCall = new ActiveCall (action.CallUuid, action.CallHandle.Value, true);

    // Monitor state changes
    activeCall.StartingConnectionChanged += (call) => {
        if (call.IsConnecting) {
            // Inform system that the call is starting
            Provider.ReportConnectingOutgoingCall (call.UUID, call.StartedConnectingOn.ToNsDate());
        }
    };

    activeCall.ConnectedChanged += (call) => {
        if (call.IsConnected) {
            // Inform system that the call has connected
            Provider.ReportConnectedOutgoingCall (call.UUID, call.ConnectedOn.ToNsDate ());
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

它會建立的執行個體`ActiveCall`類別 （保存在進行中的來電的資訊），並填入所呼叫的人員。 `StartingConnectionChanged`和`ConnectedChanged`事件用來監視及報告傳出呼叫生命週期。 呼叫會啟動，且系統會通知已完成的動作。

### <a name="ending-an-outgoing-call"></a>結束傳出呼叫

當使用者完成傳出呼叫與想来結束該處理序時，就可以使用下列程式碼：

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

如果建立`CXEndCallAction`呼叫結束的 uuid，包裝在`CXTransaction`也就是傳送給系統使用`RequestTransaction`方法`CXCallController`類別。 

## <a name="additional-callkit-details"></a>其他 CallKit 詳細資料

本節將討論一些開發人員必須納入考量，例如使用 CallKit 時的其他詳細資料：

- 提供者組態
- 動作的錯誤
- 系統限制
- VOIP 音訊

### <a name="provider-configuration"></a>提供者組態

提供者設定可讓 iOS 10 VOIP 應用程式的自訂使用者經驗 （在原生呼叫中 UI 中) 時使用 CallKit。

應用程式可以進行下列類型的自訂項目：

- 顯示當地語系化的名稱。
- 啟用視訊電話支援。
- 自訂中呼叫 UI 上的按鈕呈現自己的遮罩的影像圖示。 具有自訂按鈕的使用者互動是直接傳送至應用程式進行處理。 

### <a name="action-errors"></a>動作的錯誤

使用 CallKit iOS 10 VOIP 應用程式必須處理失敗的依正常程序的動作，並隨時通知動作狀態的使用者。 

下列範例納入考量：

1. 應用程式收到啟動呼叫動作，而且已開始初始化其通訊網路與新的 VOIP 呼叫的程序。
2. 因為有限或沒有網路通訊功能，而此連線會失敗。
3. 應用程式*必須*傳送**失敗**訊息傳回給起始呼叫動作 (`Action.Fail()`) 對系統失敗的通知。
4. 這可讓系統通知使用者呼叫的狀態。 例如，若要顯示呼叫失敗 UI。

此外，iOS 10 VOIP 應用程式需要回應_逾時錯誤_，可能無法在指定的時間內處理預期的動作。 CallKit 所提供的每個動作類型都有與其相關聯的最大逾時值。 這些逾時值，請確定該使用者要求任何 CallKit 動作會處理以回應的方式，藉此讓作業系統流暢並反應快速以及。

提供者委派中有數種方法 (`CXProviderDelegate`)，應該能夠順暢地處理這個逾時情況下覆寫。

### <a name="system-restrictions"></a>系統限制

根據目前執行的 iOS 10 VOIP 應用程式的 iOS 裝置的狀態，可能會強制某些系統限制。

例如，傳入的 VOIP 呼叫可能會限制系統如果：

1. 呼叫者是在使用者的封鎖呼叫端清單上。
2. 使用者的 iOS 裝置為 Do Not 打擾模式。

如果 VOIP 呼叫受限於上述任一情況下，使用下列程式碼來處理它：

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

CallKit 處理 iOS 10 VOIP 應用程式需要即時 VOIP 呼叫期間的音訊資源提供多項優點。 其中一個最大的優點是應用程式的音訊的工作階段會有更高的優先順序 iOS 10 中執行時。 這是相同的優先權層級為內建的電話，FaceTime 應用程式與這個增強的優先權層級會阻止其他執行中應用程式從干擾 VOIP 應用程式的音訊的工作階段。

此外，CallKit 具有存取權，可以增強效能，並根據使用者喜好設定和裝置狀態的即時呼叫期間有智慧地路由至特定的輸出裝置的 VOIP 音訊其他音訊路由提示。 例如，根據附加的裝置，例如藍芽耳機，即時 CarPlay 連接或協助工具設定。

典型的 VOIP 生命週期期間使用 CallKit 呼叫，應用程式將需要設定 CallKit 會提供它的音訊資料流。 看看下列範例：

[![](callkit-images/callkit09.png "開始呼叫動作順序")](callkit-images/callkit09.png#lightbox)

1. 接聽連入呼叫應用程式收到啟動呼叫動作。
2. 應用程式可完成此動作之前，它提供之組態的需要來執行其`AVAudioSession`。
3. 應用程式會通知系統動作已完成。
4. CallKit 呼叫連線之前，提供高優先順序`AVAudioSession`比對要求的應用程式的組態。 應用程式將會收到通知，透過`DidActivateAudioSession`方法及其`CXProviderDelegate`。

## <a name="working-with-call-directory-extensions"></a>使用呼叫目錄延伸模組

當使用 CallKit，_呼叫目錄延伸模組_提供一個方式來新增封鎖的呼叫數字及識別特定連絡人，請連絡應用程式在 iOS 裝置上的給定 VOIP 應用程式的數字。

### <a name="implementing-a-call-directory-extension"></a>實作呼叫目錄延伸模組

Xamarin.iOS 應用程式中實作呼叫目錄延伸模組，執行下列作業：

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

1. 在 Visual Studio 中開啟應用程式的方案，for mac。
2. 以滑鼠右鍵按一下方案名稱在**方案總管 中**選取**新增** > **加入新的專案**。
3. 選取**iOS** > **延伸** > **呼叫目錄延伸模組**按一下**下一步**按鈕： 

    [![](callkit-images/calldir01.png "建立新的呼叫目錄擴充功能")](callkit-images/calldir01.png#lightbox)
4. 輸入**名稱**延伸模組，然後按一下**下一步**按鈕： 

    [![](callkit-images/calldir02.png "輸入副檔名的名稱")](callkit-images/calldir02.png#lightbox)
5. 調整**專案名稱**及/或**方案名稱**如果需要，然後按一下 **建立**按鈕： 

    [![](callkit-images/calldir03.png "建立專案")](callkit-images/calldir03.png#lightbox) 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. 在 Visual Studio 中開啟應用程式的方案。
2. 以滑鼠右鍵按一下方案名稱在**方案總管 中**選取**新增** > **加入新的專案**。
3. 選取**iOS** > **延伸** > **呼叫目錄延伸模組**按一下**下一步**按鈕： 

    [![](callkit-images/calldir01w.png "建立新的呼叫目錄擴充功能")](callkit-images/calldir01.png#lightbox)
4. 輸入**名稱**延伸模組，然後按一下**確定**按鈕

-----


這會新增`CallDirectoryHandler.cs`類別至專案，看起來如下：

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

`BeginRequest`呼叫目錄處理常式方法必須修改，以提供必要的功能。 在上述範例中，它會嘗試在 VOIP 應用程式的 [連絡人] 資料庫中設定的封鎖和可用的數字清單。 如果是要求失敗，因為任何原因，建立`NSError`描述失敗，並將它傳遞`CancelRequest`方法`CXCallDirectoryExtensionContext`類別。

若要設定已封鎖的編號，請使用`AddBlockingEntry`方法`CXCallDirectoryExtensionContext`類別。 提供給方法的數字_必須_使用數字遞增順序。 獲得最佳效能和記憶體使用量時有許多電話號碼，請考慮只在指定的時間載入的數字子集，並使用 autorelease 集釋放在每個批次的數字罿剢謚檓期間配置的物件。

若要通知的連絡人知道 VOIP 應用程式的數字的連絡人應用程式，使用`AddIdentificationEntry`方法`CXCallDirectoryExtensionContext`類別，並提供數字和識別的標籤。 同樣地，提供給方法的數字_必須_使用數字遞增順序。 獲得最佳效能和記憶體使用量時有許多電話號碼，請考慮只在指定的時間載入的數字子集，並使用 autorelease 集釋放在每個批次的數字罿剢謚檓期間配置的物件。


## <a name="summary"></a>總結

這篇文章已涵蓋新 CallKit API 的 Apple iOS 10 以及如何實作 Xamarin.iOS VOIP 應用程式中發行。 它示範了如何 CallKit 讓整合到系統的 iOS 應用程式、 如何使用內建的應用程式 （例如電話） 提供功能同位檢查和如何增加整個中位置的鎖定和首頁畫面中，例如透過 Siri 互動，並透過 iOS 應用程式的可見性連絡人應用程式中。



## <a name="related-links"></a>相關連結

- [iOS 10 範例](https://developer.xamarin.com/samples/ios/iOS10/)
