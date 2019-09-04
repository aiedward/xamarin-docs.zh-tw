---
title: Xamarin 中的語音辨識
description: 本文提供新的語音 API, 並示範如何在 Xamarin iOS 應用程式中執行它, 以支援連續語音辨識和轉譯語音 (從即時或錄製的音訊串流) 到文字。
ms.prod: xamarin
ms.assetid: 64FED50A-6A28-4833-BEAE-63CEC9A09010
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: 40aa36fa8a89eacd8be7914020c06f3fec75baff
ms.sourcegitcommit: c9651cad80c2865bc628349d30e82721c01ddb4a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/03/2019
ms.locfileid: "70227335"
---
# <a name="speech-recognition-in-xamarinios"></a>Xamarin 中的語音辨識

_本文提供新的語音 API, 並示範如何在 Xamarin iOS 應用程式中執行它, 以支援連續語音辨識和轉譯語音 (從即時或錄製的音訊串流) 到文字。_

Apple 的新手已發行語音辨識 API, 可讓 iOS 應用程式支援連續語音辨識和轉譯語音 (從即時或錄製的音訊串流) 到文字。

根據 Apple, 語音辨識 API 具有下列功能和優點:

- 高度精確
- 美工圖案的狀態
- 便於使用
- 快速
- 支援多種語言
- 尊重使用者隱私權

## <a name="how-speech-recognition-works"></a>語音辨識的運作方式

語音辨識會在 iOS 應用程式中執行, 方法是取得即時或預先錄製的音訊 (以 API 支援的任何語音語言), 並將它傳遞給語音辨識器, 這會傳回說話單字的純文字轉譯。

[![](speech-images/speech01.png "語音辨識的運作方式")](speech-images/speech01.png#lightbox)

### <a name="keyboard-dictation"></a>鍵盤聽寫

當大部分使用者在 iOS 裝置上使用語音辨識時, 他們會將內建的 Siri 語音助理和 iOS 5 中的鍵盤聽寫一併視為 iPhone 4S。

支援 TextKit 的任何介面元素 (例如`UITextField`或`UITextArea`) 都支援鍵盤聽寫, 而使用者會在 iOS 虛擬鍵盤中按一下 [聽寫] 按鈕 (直接在空格鍵的左邊) 來啟用。

Apple 已發行下列鍵盤聽寫統計資料 (從2011開始收集):

- 鍵盤聽寫已廣泛使用, 因為它是在 iOS 5 中發行。
- 大約65000個應用程式每天會使用它。
- 在協力廠商應用程式中, 會完成所有 iOS 聽寫的三分之一。

鍵盤聽寫非常容易使用, 因為它不需要對開發人員的部分進行任何工作, 而是在應用程式的 UI 設計中使用 TextKit 介面元素。 鍵盤聽寫也具有不需要應用程式任何特殊許可權要求即可使用的優點。

使用新語音辨識 Api 的應用程式將需要使用者授與特殊許可權, 因為語音辨識需要在 Apple 伺服器上傳輸和暫時儲存資料。 如需詳細資訊, 請參閱我們的[安全性和隱私權增強功能](~/ios/app-fundamentals/security-privacy.md)檔。

雖然鍵盤聽寫很容易實行, 但有幾項限制和缺點:

- 它需要使用文字輸入欄位和鍵盤顯示。
- 它僅適用于即時音訊輸入, 且應用程式無法控制音訊錄製程式。
- 它不會對用來解讀使用者語音的語言提供任何控制。
- 應用程式沒有辦法知道 [聽寫] 按鈕是否可供使用者使用。
- 應用程式無法自訂音訊錄製程式。
- 它提供了一組非常淺層的結果, 其中缺少時間和信心等資訊。

### <a name="speech-recognition-api"></a>語音辨識 API

Apple 已發行「語音辨識 API」, 這是 iOS 10 的新功能, 可提供更強大的方式讓 iOS 應用程式執行「語音辨識」。 這個 API 與 Apple 用來加強 Siri 和鍵盤聽寫的程式相同, 而且能夠以藝術精確度的狀態提供快速轉譯。

語音辨識 API 所提供的結果會以透明方式自訂給個別使用者, 而不需要應用程式收集或存取任何私用使用者資料。

語音辨識 API 會在使用者說話時, 以近乎即時的方式將結果傳回給呼叫應用程式, 並提供翻譯結果的詳細資訊, 而不只是文字。 它們包括：

- 使用者所說的多項解釋。
- 個別翻譯的信賴等級。
- 計時資訊。

如上所述, 可以透過即時摘要或預先錄製的來源, 以及 iOS 10 所支援的任何超過50語言和方言, 提供翻譯的音訊。

語音辨識 API 可用於任何執行 iOS 10 的 iOS 裝置上, 而且在大部分情況下, 都需要即時網際網路連線, 因為大部分的翻譯都是在 Apple 的伺服器上進行。 話雖如此, 某些較新的 iOS 裝置支援特定語言的 always on、裝置上轉譯。

Apple 已包含可用性 API, 以判斷目前是否有指定的語言可供翻譯。 應用程式應該使用此 API, 而不是直接測試網際網路連線能力本身。

如先前在鍵盤聽寫一節中所述, 語音辨識需要透過網際網路在 Apple 伺服器上傳輸和暫時儲存資料, 因此, 應用程式_必須_要求使用者的許可權來執行辨識, 包括`SFSpeechRecognizer.RequestAuthorization`其`NSSpeechRecognitionUsageDescription` 檔案中的索引鍵,`Info.plist`並呼叫方法。 

根據語音辨識所使用的音訊來源, 可能需要對應用程式`Info.plist`檔案進行其他變更。 如需詳細資訊, 請參閱我們的[安全性和隱私權增強功能](~/ios/app-fundamentals/security-privacy.md)檔。

## <a name="adopting-speech-recognition-in-an-app"></a>在應用程式中採用語音辨識

開發人員必須採取四個主要步驟, 才能在 iOS 應用程式中採用語音辨識:

- 使用金鑰, 在應用程式的`Info.plist`檔案中提供使用方式描述。 `NSSpeechRecognitionUsageDescription` 例如, 相機應用程式可能會包含下列描述: 「_這可讓您只需說出「乳酪」這個字, 就能拍攝相片。_ 」
- 藉由呼叫`SFSpeechRecognizer.RequestAuthorization`方法來要求授權, 以提供應用程式想要`NSSpeechRecognitionUsageDescription`在對話方塊中存取使用者的語音辨識, 並允許他們接受或拒絕的說明 (在上述金鑰中提供)。
- 建立語音辨識要求:
  - 針對磁片上預先錄製的音訊, 請使用`SFSpeechURLRecognitionRequest`類別。
  - 若為即時音訊 (或記憶體中的音訊), `SFSPeechAudioBufferRecognitionRequest`請使用類別。
- 將語音辨識要求傳遞給語音辨識器 (`SFSpeechRecognizer`), 以開始辨識。 應用程式可以選擇性地按住傳回`SFSpeechRecognitionTask`的來監視和追蹤辨識結果。

下面將詳細說明這些步驟。

### <a name="providing-a-usage-description"></a>提供使用方式描述

若要`NSSpeechRecognitionUsageDescription` `Info.plist`在檔案中提供所需的金鑰, 請執行下列動作:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

1. `Info.plist`按兩下檔案將其開啟以進行編輯。
2. 切換至 [**來源**] 視圖: 

    [![](speech-images/speech02.png "來源視圖")](speech-images/speech02.png#lightbox)
3. 按一下 [**新增專案**], 然後`NSSpeechRecognitionUsageDescription` `String`針對 [**類型**] 和 [**使用描述**] 做為**值**, 輸入做為**屬性**。 例如： 

    [![](speech-images/speech03.png "加入 NSSpeechRecognitionUsageDescription")](speech-images/speech03.png#lightbox)
4. 如果應用程式將會處理即時音訊轉譯, 則也會需要麥克風使用方式描述。 按一下 [**新增專案**], 然後`NSMicrophoneUsageDescription` `String`針對 [**類型**] 和 [**使用描述**] 做為**值**, 輸入做為**屬性**。 例如： 

    [![](speech-images/speech04.png "加入 NSMicrophoneUsageDescription")](speech-images/speech04.png#lightbox)
5. 將變更儲存到檔案。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. `Info.plist`按兩下檔案將其開啟以進行編輯。
2. 按一下 [**新增專案**], 然後`NSSpeechRecognitionUsageDescription` `String`針對 [**類型**] 和 [**使用描述**] 做為**值**, 輸入做為**屬性**。 例如： 

    [![](speech-images/speech03w.png "加入 NSSpeechRecognitionUsageDescription")](speech-images/speech03w.png#lightbox)
3. 如果應用程式將會處理即時音訊轉譯, 則也會需要麥克風使用方式描述。 按一下 [**新增專案**], 然後`NSMicrophoneUsageDescription` `String`針對 [**類型**] 和 [**使用描述**] 做為**值**, 輸入做為**屬性**。 例如： 

    [![](speech-images/speech04w.png "加入 NSMicrophoneUsageDescription")](speech-images/speech04w.png#lightbox)
4. 將變更儲存到檔案。

-----

> [!IMPORTANT]
> 無法提供上述`Info.plist`任一金鑰 (`NSSpeechRecognitionUsageDescription`或`NSMicrophoneUsageDescription`), 會在嘗試存取語音辨識或即時音訊的麥克風時, 導致應用程式失敗, 而不發出警告。




### <a name="requesting-authorization"></a>要求授權

若要要求允許應用程式存取語音辨識的必要使用者授權, 請編輯主要視圖控制器類別, 並新增下列程式碼:

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

`SFSpeechRecognizer` `NSSpeechRecognitionUsageDescription`類別的`Info.plist`方法會使用開發人員在檔案金鑰中提供的原因, 向使用者要求許可權來存取語音辨識。 `RequestAuthorization`

結果會傳`RequestAuthorization`回到方法的回呼常式, 此常式可用來根據使用者的許可權採取動作。 `SFSpeechRecognizerAuthorizationStatus` 

> [!IMPORTANT]
> Apple 建議等待, 直到使用者在應用程式中啟動需要語音辨識的動作, 然後再要求此許可權。

### <a name="recognizing-pre-recorded-speech"></a>識別預先錄製的語音

如果應用程式想要從預先錄製的 WAV 或 MP3 檔案辨識語音, 它可以使用下列程式碼:

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

請先仔細查看這段程式碼, 它會嘗試建立語音辨識器 (`SFSpeechRecognizer`)。 如果語音辨識不支援預設語言, 則會`null`傳回, 且函式會結束。

如果語音辨識器適用于預設語言, 應用程式會檢查其目前是否可使用`Available`屬性進行辨識。 例如, 如果裝置沒有作用中的網際網路連線, 則辨識可能無法使用。

會從 iOS 裝置上預先錄製檔案的位置建立,並將它傳遞給語音辨識器以處理回呼常式。`NSUrl` `SFSpeechUrlRecognitionRequest`

呼叫回呼時, 如果`NSError`不`null`存在必須處理的錯誤, 則為。 因為語音辨識是以累加方式完成, 所以回呼常式可以呼叫多次, 因此`SFSpeechRecognitionResult.Final`會測試屬性, 以查看翻譯是否已完成, 以及是否將翻譯的最佳版本寫出 (`BestTranscription`)。

### <a name="recognizing-live-speech"></a>辨識即時語音

如果應用程式想要辨識即時語音, 此流程與識別預先錄製的語音非常類似。 例如：

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

查看這段程式碼的詳細資訊, 它會建立數個私用變數來處理辨識程式:

```csharp
private AVAudioEngine AudioEngine = new AVAudioEngine ();
private SFSpeechRecognizer SpeechRecognizer = new SFSpeechRecognizer ();
private SFSpeechAudioBufferRecognitionRequest LiveSpeechRequest = new SFSpeechAudioBufferRecognitionRequest ();
private SFSpeechRecognitionTask RecognitionTask;
```

它會使用 AV 基礎來記錄將傳遞至的`SFSpeechAudioBufferRecognitionRequest`音訊, 以處理辨識要求:

```csharp
var node = AudioEngine.InputNode;
var recordingFormat = node.GetBusOutputFormat (0);
node.InstallTapOnBus (0, 1024, recordingFormat, (AVAudioPcmBuffer buffer, AVAudioTime when) => {
    // Append buffer to recognition request
    LiveSpeechRequest.Append (buffer);
});
```

如果無法啟動錄製, 應用程式會嘗試開始錄製, 並處理任何錯誤:

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

辨識工作已啟動, 且控制碼會保留給辨識工作 (`SFSpeechRecognitionTask`):

```csharp
RecognitionTask = SpeechRecognizer.GetRecognitionTask (LiveSpeechRequest, (SFSpeechRecognitionResult result, NSError err) => {
    ...
});
```

回呼的使用方式與上述在預先錄製的語音上使用的相同。

如果使用者 stoped 錄製, 則會通知音訊引擎和語音辨識要求:

```csharp
AudioEngine.Stop ();
LiveSpeechRequest.EndAudio ();
```

如果使用者取消辨識, 則會通知「音訊引擎」和「辨識」工作:

```csharp
AudioEngine.Stop ();
RecognitionTask.Cancel ();
```

如果使用者取消翻譯以`RecognitionTask.Cancel`釋放記憶體和裝置的處理器, 請務必呼叫。

> [!IMPORTANT]
> 無法提供`NSSpeechRecognitionUsageDescription`或`NSMicrophoneUsageDescription` 金鑰`Info.plist`可能會導致應用程式在嘗試存取語音辨識或麥克風以進行即時音訊 (`var node = AudioEngine.InputNode;`) 時失敗, 而不會發出警告。 如需詳細資訊, 請參閱上面的**提供使用方式描述**一節。

## <a name="speech-recognition-limits"></a>語音辨識限制

在 iOS 應用程式中使用語音辨識時, Apple 會強加下列限制:

- 所有應用程式都可以免費使用「語音辨識」, 但其使用量不受限制:
  - 個別的 iOS 裝置每日可執行檔辨識數目有限。
  - 應用程式將會以每日要求為基礎進行全域節流。
- 應用程式必須準備好處理語音辨識網路連線和使用速率限制失敗。
- 語音辨識在使用者的 iOS 裝置上, 電池耗盡和高網路流量的成本可能會很高, 因此 Apple 會將嚴格的音訊持續時間限制 (大約一分鐘的語音上限) 強加。

如果應用程式定期達到其速率節流限制, Apple 會要求開發人員與他們聯繫。

## <a name="privacy-and-usability-considerations"></a>隱私權和使用性考慮

Apple 提供下列建議, 以在 iOS 應用程式中包含語音辨識時, 透明化並尊重使用者的隱私權:

- 記錄使用者的語音時, 請務必清楚指出該錄製正在應用程式的使用者介面中進行。 例如, 應用程式可能會播放「錄製」音效, 並顯示錄製指示器。
- 請勿將語音辨識用於機密的使用者資訊, 例如密碼、健康情況資料或財務資訊。
- 在對其採取行動_之前, 先_顯示辨識結果。 這不僅會提供應用程式執行內容的意見反應, 還可讓使用者在辨識錯誤發生時加以處理。

## <a name="summary"></a>總結

本文介紹了新的語音 API, 並示範如何在 Xamarin iOS 應用程式中執行它, 以支援連續語音辨識和轉譯語音 (從即時或錄製的音訊串流) 到文字。 



## <a name="related-links"></a>相關連結

- [SpeakToMe (範例)](https://docs.microsoft.com/samples/xamarin/ios-samples/ios10-speaktome)
