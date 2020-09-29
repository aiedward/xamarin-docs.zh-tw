---
title: Xamarin 中的語音辨識
description: 本文將介紹新的語音 API，並示範如何在 Xamarin iOS 應用程式中加以執行，以支援從即時或錄製的音訊串流) 至文字的連續語音辨識和轉譯語音 (。
ms.prod: xamarin
ms.assetid: 64FED50A-6A28-4833-BEAE-63CEC9A09010
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/17/2017
ms.openlocfilehash: 98cbcd333d223d741602786643ef1948915d7dfc
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91436469"
---
# <a name="speech-recognition-in-xamarinios"></a>Xamarin 中的語音辨識

_本文將介紹新的語音 API，並示範如何在 Xamarin iOS 應用程式中加以執行，以支援從即時或錄製的音訊串流) 至文字的連續語音辨識和轉譯語音 (。_

Apple 的新功能已發行語音辨識 API，可讓 iOS 應用程式從即時或錄製的音訊串流 () 為文字，以支援連續的語音辨識和轉譯語音。

根據 Apple，語音辨識 API 具有下列功能和優點：

- 高度精確
- 藝術的狀態
- 容易使用
- 快速
- 支援多種語言
- 尊重使用者隱私權

## <a name="how-speech-recognition-works"></a>語音辨識的運作方式

語音辨識會在 iOS 應用程式中執行，方法是取得即時或預先錄製的音訊 (以 API 所支援的任何語言) ，並將它傳遞給語音辨識器，以傳回說出單字的純文字轉譯。

[![語音辨識的運作方式](speech-images/speech01.png)](speech-images/speech01.png#lightbox)

### <a name="keyboard-dictation"></a>鍵盤聽寫

當大部分的使用者在 iOS 裝置上想要使用語音辨識時，他們會考慮使用內建的 Siri 語音助理，此助理是與 iOS 5 中的鍵盤聽寫搭配 iPhone 4S 一起發行。

任何支援 TextKit (的介面元素（例如或) ）都支援鍵盤聽寫 `UITextField` `UITextArea` ，而使用者可以按一下 [聽寫] 按鈕 (直接到 iOS 虛擬鍵盤中的空格鍵左方) 。

Apple 已發行下列鍵盤聽寫統計資料， (在 2011) 之後收集：

- 鍵盤聽寫已廣泛使用，因為它是在 iOS 5 中發行。
- 大約65000的應用程式每天使用它。
- 關於所有 iOS 聽寫的協力廠商是在協力廠商應用程式中完成。

鍵盤聽寫很容易使用，因為它不需要投入開發人員的一部分，而是在應用程式的 UI 設計中使用 TextKit 介面元素。 鍵盤聽寫也有一個優點，就是不需要從應用程式要求任何特殊許可權要求，就可以使用它。

使用新語音辨識 Api 的應用程式將需要使用者授與特殊許可權，因為語音辨識需要在 Apple 伺服器上傳輸和暫存資料。 請參閱我們的 [安全性和隱私權增強](~/ios/app-fundamentals/security-privacy.md) 檔以取得詳細資料。

雖然鍵盤聽寫很容易執行，但有幾項限制和缺點：

- 它需要使用文字輸入欄位並顯示鍵盤。
- 它只適用于實況音訊輸入，應用程式無法控制音訊錄製程式。
- 它不會提供用來解讀使用者語音之語言的控制權。
- 應用程式沒有辦法知道聽寫按鈕是否可供使用者使用。
- 應用程式無法自訂音訊錄製流程。
- 它提供一組非常淺層的結果，這些結果缺少時間和信賴的資訊。

### <a name="speech-recognition-api"></a>語音辨識 API

Apple 已發行語音辨識 API，這是 iOS 10 的新功能，可提供更強大的方式讓 iOS 應用程式執行語音辨識。 此 API 與 Apple 用來同時提供 Siri 和鍵盤聽寫的相同，而且能夠以最尖端的精確度來提供快速轉譯。

語音辨識 API 提供的結果會以透明的方式自訂給個別使用者，而不需要應用程式收集或存取任何私用使用者資料。

語音辨識 API 會在使用者說話時，以近乎即時的方式將結果提供給呼叫的應用程式，並且提供有關翻譯結果的詳細資訊，而不只是文字。 這些包括：

- 使用者所說的多項解讀。
- 個別翻譯的信賴等級。
- 計時資訊。

如上所述，可透過即時摘要或從預先錄製的來源，以及 iOS 10 所支援的任何50語言和方言，來提供翻譯的音訊。

語音辨識 API 可用於任何執行 iOS 10 的 iOS 裝置，在大部分情況下，都需要即時網際網路連線，因為大部分的翻譯都是在 Apple 的伺服器上進行。 話雖如此，某些較新的 iOS 裝置也支援特定語言的 always on 裝置轉譯。

Apple 已包含可用性 API，以判斷目前是否有指定的語言可供翻譯。 應用程式應該使用此 API，而不是直接測試網際網路連線能力。

如上所述，在鍵盤聽寫區段中，語音辨識需要透過網際網路傳輸和暫存 Apple 伺服器上的資料，因此，應用程式 _必須_ `NSSpeechRecognitionUsageDescription` 在其檔案中包含金鑰 `Info.plist` 並呼叫方法，以要求使用者執行辨識的許可權 `SFSpeechRecognizer.RequestAuthorization` 。 

根據用於語音辨識的音訊來源，可能需要對應用程式的檔案進行其他變更 `Info.plist` 。 請參閱我們的 [安全性和隱私權增強](~/ios/app-fundamentals/security-privacy.md) 檔以取得詳細資料。

## <a name="adopting-speech-recognition-in-an-app"></a>在應用程式中採用語音辨識

在 iOS 應用程式中採用語音辨識時，開發人員必須採取四個主要步驟：

- 使用金鑰在應用程式的檔案中提供使用方式描述 `Info.plist` `NSSpeechRecognitionUsageDescription` 。 例如，相機應用程式可能會包含下列描述：「 _這讓您只要說出「乳酪」這個字，就能拍攝相片」。_
- 藉由呼叫 `SFSpeechRecognizer.RequestAuthorization` 方法來要求授權，以提供上述索引鍵中所提供的說明 (`NSSpeechRecognitionUsageDescription`) 瞭解應用程式希望使用者在對話方塊中的語音辨識存取權，並允許他們接受或拒絕。
- 建立語音辨識要求：
  - 對於磁片上預先錄製的音訊，請使用 `SFSpeechURLRecognitionRequest` 類別。
  - 若為即時音訊 (或記憶體) 的音訊，請使用 `SFSPeechAudioBufferRecognitionRequest` 類別。
- 將語音辨識要求傳遞給語音辨識器 (`SFSpeechRecognizer`) 以開始辨識。 應用程式可以選擇性地保留在傳回的上， `SFSpeechRecognitionTask` 以監視及追蹤辨識結果。

以下將詳細說明這些步驟。

### <a name="providing-a-usage-description"></a>提供使用方式描述

若要在檔案中提供必要的 `NSSpeechRecognitionUsageDescription` 金鑰 `Info.plist` ，請執行下列動作：

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

1. 按兩下檔案 `Info.plist` 以開啟它進行編輯。
2. 切換至 **來源** view： 

    [![來源視圖](speech-images/speech02.png)](speech-images/speech02.png#lightbox)
3. 按一下 [ **加入新專案**]、輸入 `NSSpeechRecognitionUsageDescription` **屬性**、輸入 `String` **類型** 和使用方式 **描述** 作為 **值**。 例如： 

    [![新增 NSSpeechRecognitionUsageDescription](speech-images/speech03.png)](speech-images/speech03.png#lightbox)
4. 如果應用程式將會處理即時音訊轉譯，則也會需要麥克風使用方式描述。 按一下 [ **加入新專案**]、輸入 `NSMicrophoneUsageDescription` **屬性**、輸入 `String` **類型** 和使用方式 **描述** 作為 **值**。 例如： 

    [![新增 NSMicrophoneUsageDescription](speech-images/speech04.png)](speech-images/speech04.png#lightbox)
5. 將變更儲存至檔案。

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. 按兩下檔案 `Info.plist` 以開啟它進行編輯。
2. 按一下 [ **加入新專案**]、輸入 `NSSpeechRecognitionUsageDescription` **屬性**、輸入 `String` **類型** 和使用方式 **描述** 作為 **值**。 例如： 

    [![新增 NSSpeechRecognitionUsageDescription](speech-images/speech03w.png)](speech-images/speech03w.png#lightbox)
3. 如果應用程式將會處理即時音訊轉譯，則也會需要麥克風使用方式描述。 按一下 [ **加入新專案**]、輸入 `NSMicrophoneUsageDescription` **屬性**、輸入 `String` **類型** 和使用方式 **描述** 作為 **值**。 例如： 

    [![新增 NSMicrophoneUsageDescription](speech-images/speech04w.png)](speech-images/speech04w.png#lightbox)
4. 將變更儲存至檔案。

-----

> [!IMPORTANT]
> 無法提供上述任何一個 `Info.plist` 金鑰 (`NSSpeechRecognitionUsageDescription` 或 `NSMicrophoneUsageDescription`) 可能會導致應用程式在嘗試存取語音辨識或麥克風以進行實況音訊時失敗，而不會發出任何警告。

### <a name="requesting-authorization"></a>要求授權

若要要求允許應用程式存取語音辨識的必要使用者授權，請編輯主要視圖控制器類別，然後新增下列程式碼：

```csharp
using System;
using UIKit;
using Speech;

namespace MonkeyTalk
{
    public partial class ViewController : UIViewController
    {
        protected ViewController (IntPtr handle) : base (handle)
        {
            // Note: this .ctor should not contain any initialization logic.
        }

        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Request user authorization
            SFSpeechRecognizer.RequestAuthorization ((SFSpeechRecognizerAuthorizationStatus status) => {
                // Take action based on status
                switch (status) {
                case SFSpeechRecognizerAuthorizationStatus.Authorized:
                    // User has approved speech recognition
                    ...
                    break;
                case SFSpeechRecognizerAuthorizationStatus.Denied:
                    // User has declined speech recognition
                    ...
                    break;
                case SFSpeechRecognizerAuthorizationStatus.NotDetermined:
                    // Waiting on approval
                    ...
                    break;
                case SFSpeechRecognizerAuthorizationStatus.Restricted:
                    // The device is not permitted
                    ...
                    break;
                }
            });
        }
    }
}
```

`RequestAuthorization`類別的方法 `SFSpeechRecognizer` 會向使用者要求許可權，以利用開發人員在檔案的索引鍵中提供的原因來存取語音辨識 `NSSpeechRecognitionUsageDescription` `Info.plist` 。

`SFSpeechRecognizerAuthorizationStatus`結果會傳回給 `RequestAuthorization` 方法的回呼常式，可用來根據使用者的許可權來採取動作。 

> [!IMPORTANT]
> Apple 建議等候，直到使用者在應用程式中開始需要語音辨識的動作，然後才要求此許可權。

### <a name="recognizing-pre-recorded-speech"></a>辨識預先錄製的語音

如果應用程式想要從預先錄製的 WAV 或 MP3 檔案辨識語音，則可以使用下列程式碼：

```csharp
using System;
using UIKit;
using Speech;
using Foundation;
...

public void RecognizeFile (NSUrl url)
{
    // Access new recognizer
    var recognizer = new SFSpeechRecognizer ();

    // Is the default language supported?
    if (recognizer == null) {
        // No, return to caller
        return;
    }

    // Is recognition available?
    if (!recognizer.Available) {
        // No, return to caller
        return;
    }

    // Create recognition task and start recognition
    var request = new SFSpeechUrlRecognitionRequest (url);
    recognizer.GetRecognitionTask (request, (SFSpeechRecognitionResult result, NSError err) => {
        // Was there an error?
        if (err != null) {
            // Handle error
            ...
        } else {
            // Is this the final translation?
            if (result.Final) {
                Console.WriteLine ("You said, \"{0}\".", result.BestTranscription.FormattedString);
            }
        }
    });
}
```

首先詳細探討此程式碼，它會嘗試建立 () 的語音辨識器 `SFSpeechRecognizer` 。 如果語音辨識不支援預設語言， `null` 則會傳回且函式會結束。

如果語音辨識器可供預設語言使用，則應用程式會檢查目前是否可使用屬性進行辨識 `Available` 。 例如，如果裝置沒有作用中的網際網路連線，則可能無法使用辨識。

`SFSpeechUrlRecognitionRequest`從 `NSUrl` iOS 裝置上預先錄製檔案的位置建立，並將它遞交給語音辨識器，以使用回呼常式進行處理。

呼叫回呼時，如果沒有必須處理的錯誤，則為 `NSError` `null` 。 由於語音辨識是以累加方式完成，因此回呼常式可能會被呼叫一次以上，因此會 `SFSpeechRecognitionResult.Final` 測試屬性以查看轉譯是否完整，以及將翻譯的最佳版本寫出 (`BestTranscription`) 。

### <a name="recognizing-live-speech"></a>辨識即時語音

如果應用程式想要辨識即時語音，此程式非常類似于辨識預先錄製的語音。 例如：

```csharp
using System;
using UIKit;
using Speech;
using Foundation;
using AVFoundation;
...

#region Private Variables
private AVAudioEngine AudioEngine = new AVAudioEngine ();
private SFSpeechRecognizer SpeechRecognizer = new SFSpeechRecognizer ();
private SFSpeechAudioBufferRecognitionRequest LiveSpeechRequest = new SFSpeechAudioBufferRecognitionRequest ();
private SFSpeechRecognitionTask RecognitionTask;
#endregion
...

public void StartRecording ()
{
    // Setup audio session
    var node = AudioEngine.InputNode;
    var recordingFormat = node.GetBusOutputFormat (0);
    node.InstallTapOnBus (0, 1024, recordingFormat, (AVAudioPcmBuffer buffer, AVAudioTime when) => {
        // Append buffer to recognition request
        LiveSpeechRequest.Append (buffer);
    });

    // Start recording
    AudioEngine.Prepare ();
    NSError error;
    AudioEngine.StartAndReturnError (out error);

    // Did recording start?
    if (error != null) {
        // Handle error and return
        ...
        return;
    }

    // Start recognition
    RecognitionTask = SpeechRecognizer.GetRecognitionTask (LiveSpeechRequest, (SFSpeechRecognitionResult result, NSError err) => {
        // Was there an error?
        if (err != null) {
            // Handle error
            ...
        } else {
            // Is this the final translation?
            if (result.Final) {
                Console.WriteLine ("You said \"{0}\".", result.BestTranscription.FormattedString);
            }
        }
    });
}

public void StopRecording ()
{
    AudioEngine.Stop ();
    LiveSpeechRequest.EndAudio ();
}

public void CancelRecording ()
{
    AudioEngine.Stop ();
    RecognitionTask.Cancel ();
}
```

詳細查看此程式碼，它會建立數個私用變數來處理辨識流程：

```csharp
private AVAudioEngine AudioEngine = new AVAudioEngine ();
private SFSpeechRecognizer SpeechRecognizer = new SFSpeechRecognizer ();
private SFSpeechAudioBufferRecognitionRequest LiveSpeechRequest = new SFSpeechAudioBufferRecognitionRequest ();
private SFSpeechRecognitionTask RecognitionTask;
```

它會使用 AV 基礎來記錄將傳遞給的音訊， `SFSpeechAudioBufferRecognitionRequest` 以處理辨識要求：

```csharp
var node = AudioEngine.InputNode;
var recordingFormat = node.GetBusOutputFormat (0);
node.InstallTapOnBus (0, 1024, recordingFormat, (AVAudioPcmBuffer buffer, AVAudioTime when) => {
    // Append buffer to recognition request
    LiveSpeechRequest.Append (buffer);
});
```

如果無法開機記錄，應用程式會嘗試開始錄製，並處理任何錯誤：

```csharp
AudioEngine.Prepare ();
NSError error;
AudioEngine.StartAndReturnError (out error);

// Did recording start?
if (error != null) {
    // Handle error and return
    ...
    return;
}
```

辨識工作會啟動，並將控制碼保留給 () 的辨識工作 `SFSpeechRecognitionTask` ：

```csharp
RecognitionTask = SpeechRecognizer.GetRecognitionTask (LiveSpeechRequest, (SFSpeechRecognitionResult result, NSError err) => {
    ...
});
```

回呼的使用方式與上述在預先錄製語音上使用的方式相似。

如果使用者 stoped 錄製，則會通知音訊引擎和語音辨識要求：

```csharp
AudioEngine.Stop ();
LiveSpeechRequest.EndAudio ();
```

如果使用者取消辨識，則會通知音訊引擎和辨識工作：

```csharp
AudioEngine.Stop ();
RecognitionTask.Cancel ();
```

`RecognitionTask.Cancel`如果使用者取消翻譯以釋出記憶體和裝置的處理器，請務必呼叫。

> [!IMPORTANT]
> `NSSpeechRecognitionUsageDescription`無法提供或 `NSMicrophoneUsageDescription` `Info.plist` 金鑰可能會導致應用程式在嘗試存取語音辨識或麥克風以進行即時音訊 () 時，不會發出警告 `var node = AudioEngine.InputNode;` 。 如需詳細資訊，請參閱上面的「 **提供使用方式描述** 」一節。

## <a name="speech-recognition-limits"></a>語音辨識限制

在 iOS 應用程式中使用語音辨識時，Apple 會施加下列限制：

- 語音辨識適用于所有應用程式，但其使用方式不是無限制：
  - 個別的 iOS 裝置每日可執行檔表彰數目有限。
  - 應用程式會根據每日要求進行全域節流。
- 應用程式必須準備好處理語音辨識網路連接和使用速率限制失敗。
- 語音辨識在使用者的 iOS 裝置上的電池耗盡和高網路流量中可能會有高成本，因此 Apple 會將嚴格的音訊持續時間限制為大約一分鐘的語音最大值。

如果應用程式定期達到其速率節流限制，則 Apple 會要求開發人員聯絡。

## <a name="privacy-and-usability-considerations"></a>隱私權和可用性考慮

在 iOS 應用程式中包含語音辨識時，Apple 提供下列建議：透明且尊重使用者的隱私權：

- 錄製使用者的語音時，請務必清楚地指出正在應用程式消費者介面中進行錄製。 例如，應用程式可能會播放「錄製」音效，並顯示錄製指標。
- 請勿針對機密使用者資訊（例如密碼、健康情況資料或財務資訊）使用語音辨識。
- 先顯示辨識結果， _再_ 對其採取行動。 這並不只會提供應用程式執行內容的意見反應，還可讓使用者在辨識錯誤發生時加以處理。

## <a name="summary"></a>摘要

本文已介紹新的語音 API，並示範如何在 Xamarin iOS 應用程式中加以執行，以支援從即時或錄製的音訊串流) 至文字的連續語音辨識和轉譯語音 (。 

## <a name="related-links"></a>相關連結

- [SpeakToMe (範例) ](/samples/xamarin/ios-samples/ios10-speaktome)