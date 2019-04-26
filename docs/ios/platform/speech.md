---
title: 在 Xamarin.iOS 中的語音辨識
description: 本文章提供新的語音 API，並示範如何實作在 Xamarin.iOS 應用程式中支援持續的語音辨識，並轉譯成文字的語音功能 （即時或錄製音訊資料流）。
ms.prod: xamarin
ms.assetid: 64FED50A-6A28-4833-BEAE-63CEC9A09010
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: c1f488213f9b3be945fd98e09f630c243d0b0d62
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61382798"
---
# <a name="speech-recognition-in-xamarinios"></a>在 Xamarin.iOS 中的語音辨識

_本文章提供新的語音 API，並示範如何實作在 Xamarin.iOS 應用程式中支援持續的語音辨識，並轉譯成文字的語音功能 （即時或錄製音訊資料流）。_

新增至 iOS 10，Apple 已發行讓 iOS 應用程式支援持續的語音辨識和轉譯 （從即時或錄製音訊資料流） 的語音文字到語音辨識 API。

根據 Apple，語音辨識 API 具有下列功能和優點：

- 高準確率
- 先進
- 容易使用
- 快速
- 支援多國語言
- 尊重使用者隱私權

## <a name="how-speech-recognition-works"></a>語音辨識的運作方式

IOS 應用程式中實作語音辨識取得 （以任何語言，此 API 支援） 的即時或預先錄製音訊，並將它傳遞至語音辨識器傳回純文字的文字稿的讀出的文字。

[![](speech-images/speech01.png "語音辨識的運作方式")](speech-images/speech01.png#lightbox)

### <a name="keyboard-dictation"></a>鍵盤聽寫

當 iOS 裝置上，將語音辨識大部分的使用者時，他們認為的內建的 Siri 語音助理，以及鍵盤聽寫發行 iOS 5 與 iPhone 4 秒。

鍵盤聽寫受到支援 TextKit 任何介面項目 (例如`UITextField`或`UITextArea`) 是由和啟動使用者按一下 iOS 虛擬鍵盤聽寫 按鈕 （直接向空格鍵的左邊）。

Apple 已發行下列鍵盤聽寫統計資料 （收集自 2011 年）：

- 發行後已在 iOS 5 中已廣泛使用鍵盤聽寫。
- 大約 65,000 應用程式所使用的是其每日。
- 所有 iOS 的第三個有關聽寫是在第 3 個合作對象應用程式。

鍵盤聽寫是使用相當簡單，因為它不需要開發人員的組件，而非使用中應用程式的 UI 設計的 TextKit 介面項目上的任何工作。 鍵盤聽寫也的優點是不需要任何特殊權限要求，從應用程式，才能使用。

使用新的語音辨識 Api 的應用程式會需要特殊權限可授與使用者，因為語音辨識需要傳輸與 Apple 的伺服器上的資料暫存儲存體。 請參閱我們[安全性和隱私權增強功能](~/ios/app-fundamentals/security-privacy.md)文件，如需詳細資訊。

雖然鍵盤聽寫很容易實作，它會隨附數個限制和缺點：

- 它需要文字輸入欄位的使用和鍵盤的顯示方式。
- 它適用於僅限輸入的即時音訊和應用程式有任何控制音訊錄製程序。
- 它提供無法控制用來解譯使用者的語音的語言。
- 沒有任何要知道是否聽寫按鈕，使用者甚至可以使用應用程式的方法。
- 應用程式無法自訂音訊錄製處理程序。
- 它提供非常的結果集，缺少例如計時和信賴的資訊。

### <a name="speech-recognition-api"></a>語音辨識 API

新增至 iOS 10，Apple 已發行語音辨識 API 可提供更強大的方式，來實作語音辨識的 iOS 應用程式。 此 API 是同一個 Apple 使用 Siri 和鍵盤聽寫的電源，而且能夠快速轉譯提供先進的精確度。

個別的使用者，而不需要收集或存取任何私用的使用者資料的應用程式以透明的方式自訂語音辨識 API 所提供的結果。

語音辨識 API 提供結果給呼叫的應用程式中近乎即時的方式，因為使用者說，而且它會提供比只是文字的轉譯結果的詳細資訊。 它們包括：

- 使用者所講的多重解譯。
- 個別翻譯的信心層級。
- 計時資訊。

如上所述，可能是即時摘要，或從預先錄製的來源，在任何超過 50 種語言和方言 iOS 10 支援可提供翻譯的音訊。

語音辨識 API 可用在任何執行 iOS 10 的 iOS 裝置上，而且在大部分情況下，需要使用即時網際網路連線，因為大部分的轉譯會在 Apple 的伺服器。 話雖如此，某些較新的 iOS 裝置支援 alwayson，裝置上的特定語言的翻譯。

Apple 已包含可用性 API，以判斷給定的語言是否可用來在目前時間的翻譯。 應用程式應該使用此 API，而不是直接測試本身的網際網路連線。

如先前所述鍵盤聽寫區段中，語音辨識需要傳輸與 Apple 的伺服器上的資料暫存儲存體，透過網際網路，因此，應用程式_必須_要求使用者的權限，才能執行藉由辨識`NSSpeechRecognitionUsageDescription`中的索引鍵及其`Info.plist`檔案，並呼叫`SFSpeechRecognizer.RequestAuthorization`方法。 

其他變更為應用程式的原始碼基礎使用語音辨識的音訊，`Info.plist`檔案可能需要。 請參閱我們[安全性和隱私權增強功能](~/ios/app-fundamentals/security-privacy.md)文件，如需詳細資訊。

## <a name="adopting-speech-recognition-in-an-app"></a>採用應用程式中的語音辨識

有開發人員必須採用在 iOS 應用程式中的 [語音辨識] 所採取的四個主要步驟：

- 提供在應用程式的使用方式描述`Info.plist`檔案中，使用`NSSpeechRecognitionUsageDescription`索引鍵。 比方說，相機應用程式可能會包含下列描述中， _」 這可讓您拍攝相片，只要說這個字 '起司'。 」_
- 藉由呼叫要求授權`SFSpeechRecognizer.RequestAuthorization`呈現說明的方法 (提供`NSSpeechRecognitionUsageDescription`上述的索引鍵) 的應用程式為何想語音辨識和存取權的對話方塊中的使用者允許其接受或拒絕。
- 建立語音辨識要求：
    * 在磁碟上的預先錄製音訊，使用`SFSpeechURLRecognitionRequest`類別。
    * 即時音訊 （或從記憶體的音訊），請使用`SFSPeechAudioBufferRecognitionRequest`類別。
- 傳遞至語音辨識器的 語音辨識要求 (`SFSpeechRecognizer`) 開始辨識。 應用程式可以選擇性地保留傳回`SFSpeechRecognitionTask`來監視和追蹤的辨識結果。

這些步驟會在下方詳細說明。

### <a name="providing-a-usage-description"></a>提供使用方式描述

若要提供必要`NSSpeechRecognitionUsageDescription`中的索引鍵`Info.plist`檔案中，執行下列動作：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

1. 按兩下`Info.plist`檔案，以開啟它進行編輯。
2. 若要切換**來源**檢視： 

    [![](speech-images/speech02.png "原始碼檢視")](speech-images/speech02.png#lightbox)
3. 按一下**加入新項目**，輸入`NSSpeechRecognitionUsageDescription`如**屬性**，`String`如**類型**和**使用方式描述**作為**值**。 例如:  

    [![](speech-images/speech03.png "新增 NSSpeechRecognitionUsageDescription")](speech-images/speech03.png#lightbox)
4. 如果應用程式將會處理即時音訊轉譯，還需要麥克風使用方式描述。 按一下**加入新項目**，輸入`NSMicrophoneUsageDescription`如**屬性**，`String`如**類型**和**使用方式描述**作為**值**。 例如:  

    [![](speech-images/speech04.png "新增 NSMicrophoneUsageDescription")](speech-images/speech04.png#lightbox)
4. 將變更儲存到檔案。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. 按兩下`Info.plist`檔案，以開啟它進行編輯。
3. 按一下**加入新項目**，輸入`NSSpeechRecognitionUsageDescription`如**屬性**，`String`如**類型**和**使用方式描述**作為**值**。 例如:  

    [![](speech-images/speech03w.png "新增 NSSpeechRecognitionUsageDescription")](speech-images/speech03w.png#lightbox)
4. 如果應用程式將會處理即時音訊轉譯，還需要麥克風使用方式描述。 按一下**加入新項目**，輸入`NSMicrophoneUsageDescription`如**屬性**，`String`如**類型**和**使用方式描述**作為**值**。 例如:  

    [![](speech-images/speech04w.png "新增 NSMicrophoneUsageDescription")](speech-images/speech04w.png#lightbox)
4. 將變更儲存到檔案。

-----

> [!IMPORTANT]
> 無法提供上述任一`Info.plist`機碼 (`NSSpeechRecognitionUsageDescription`或`NSMicrophoneUsageDescription`) 可能會導致應用程式嘗試存取語音辨識或即時音訊麥克風時失敗而不發出警告。




### <a name="requesting-authorization"></a>要求的授權

若要要求所需的使用者授權，可讓應用程式存取語音辨識，編輯主要的檢視控制器類別並新增下列程式碼：

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

`RequestAuthorization`方法`SFSpeechRecognizer`類別會要求使用者權限來存取語音辨識使用中的開發人員提供的原因`NSSpeechRecognitionUsageDescription`索引鍵`Info.plist`檔案。

A`SFSpeechRecognizerAuthorizationStatus`結果會傳回到`RequestAuthorization`可用來採取動作的方法的回呼常式會根據使用者的權限。 

> [!IMPORTANT]
> Apple 建議等待，直到使用者已要求此權限之前需要語音辨識的應用程式中啟動動作。

### <a name="recognizing-pre-recorded-speech"></a>辨識預先錄製的語音

如果應用程式想要辨識語音從預先錄製的 WAV 或 MP3 檔案，它可以使用下列程式碼：

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

查看這個程式碼的詳細資料，首先，它會嘗試建立語音辨識器 (`SFSpeechRecognizer`)。 如果預設的語言不支援語音辨識`null`傳回，函式結束。

如果語音辨識器的預設語言的功能，應用程式會檢查以查看它是否目前可供辨識使用`Available`屬性。 比方說，辨識可能會無法使用，如果裝置沒有網際網路連線。

A`SFSpeechUrlRecognitionRequest`從建立`NSUrl`交給語音辨識器，利用回呼常式來處理預先錄製在 iOS 裝置，且該檔案的位置。

當呼叫回呼，如果`NSError`不是`null`必須處理的錯誤。 因為以累加方式完成語音辨識，可能會回呼常式呼叫超過一次，因此`SFSpeechRecognitionResult.Final`經測試屬性，可查看轉譯是否完整翻譯的最佳版本會寫出 (`BestTranscription`)。

### <a name="recognizing-live-speech"></a>辨識即時語音

如果應用程式想要辨識即時語音，此程序是非常類似，以及預先錄製的語音辨識。 例如: 

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

查看這個程式碼的詳細資料，它會建立數個私用變數，來處理辨識程序：

```csharp
private AVAudioEngine AudioEngine = new AVAudioEngine ();
private SFSpeechRecognizer SpeechRecognizer = new SFSpeechRecognizer ();
private SFSpeechAudioBufferRecognitionRequest LiveSpeechRequest = new SFSpeechAudioBufferRecognitionRequest ();
private SFSpeechRecognitionTask RecognitionTask;
```

它會使用錄製的音訊會傳遞至 AV Foundation`SFSpeechAudioBufferRecognitionRequest`來處理辨識要求：

```csharp
var node = AudioEngine.InputNode;
var recordingFormat = node.GetBusOutputFormat (0);
node.InstallTapOnBus (0, 1024, recordingFormat, (AVAudioPcmBuffer buffer, AVAudioTime when) => {
    // Append buffer to recognition request
    LiveSpeechRequest.Append (buffer);
});
```

若要開始錄製的應用程式嘗試和任何錯誤的處理，如果無法啟動錄製：

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

辨識工作會啟動和控制代碼保留辨識工作 (`SFSpeechRecognitionTask`):

```csharp
RecognitionTask = SpeechRecognizer.GetRecognitionTask (LiveSpeechRequest, (SFSpeechRecognitionResult result, NSError err) => {
    ...
});
```

回呼用在上面使用預先錄製語音上的一個類似的方式。

如果記錄是沒有使用者，則會收到通知，音訊引擎和語音辨識要求：

```csharp
AudioEngine.Stop ();
LiveSpeechRequest.EndAudio ();
```

如果使用者取消辨識，然後音訊引擎和辨識工作，會收到通知：

```csharp
AudioEngine.Stop ();
RecognitionTask.Cancel ();
```

請務必呼叫`RecognitionTask.Cancel`如果使用者取消的翻譯，以釋出記憶體和裝置的處理器。

> [!IMPORTANT]
> 無法提供`NSSpeechRecognitionUsageDescription`或是`NSMicrophoneUsageDescription``Info.plist`金鑰可能會導致應用程式失敗而不發出警告，嘗試存取語音辨識或即時音訊麥克風時 (`var node = AudioEngine.InputNode;`)。 請參閱**提供使用方式描述**上方區段，如需詳細資訊。

## <a name="speech-recognition-limits"></a>語音辨識限制

在 iOS 應用程式中使用語音辨識時，Apple 會施加下列限制：

- 語音辨識所有的應用程式，免費，但其使用方式不是無限制：
    - 個別的 iOS 裝置會有有限的數目的可執行每日項辨識。
    - 應用程式會將全域節流以要求每一天為單位。
- 應用程式必須準備好處理語音辨識的網路連線和使用速率限制失敗。
- 語音辨識會對高的成本相當高的網路流量和電池清空中使用者的 iOS 裝置，因為這個緣故，Apple 限制嚴格音訊的持續時間為大約一分鐘的最大的語音。

如果應用程式會定期達到其速率的節流限制，則 Apple 會要求開發人員與他們連絡。

## <a name="privacy-and-usability-considerations"></a>隱私權和可用性考量

Apple 已當作透明及包括 iOS 應用程式中的語音辨識時尊重使用者隱私權的下列建議：

- 當記錄使用者的語音，請務必清楚指出，錄製發生於應用程式的使用者介面。 例如，應用程式可能會播放 「 記錄 」 的音效，並顯示記錄指標。
- 請勿使用語音辨識敏感性使用者資訊，例如密碼、 健康情況資料或財務資訊。
- 顯示辨識結果_之前_對它們執行動作。 這不只會提供什麼應用程式正在進行，但可讓使用者處理辨識錯誤，因為它們都會回應。

## <a name="summary"></a>總結

本文已出現新的語音 API，並說明如何實作在 Xamarin.iOS 應用程式中支援持續的語音辨識，並轉譯成文字的語音功能 （即時或錄製音訊資料流）。 



## <a name="related-links"></a>相關連結

- [SpeakToMe （範例）](https://developer.xamarin.com/samples/monotouch/ios10/SpeakToMe/)
