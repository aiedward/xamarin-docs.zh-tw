---
title: "語音辨識"
description: "本文章提供新的語音應用程式開發介面，並示範如何實作在 Xamarin.iOS 應用程式中支援連續的語音辨識和語音 （從即時或錄製音訊資料流） 同時成文字。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 64FED50A-6A28-4833-BEAE-63CEC9A09010
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/17/2017
ms.openlocfilehash: 00980c0038ac3ae51d0444e6f71dde9dfef25831
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="speech-recognition"></a>語音辨識

_本文章提供新的語音應用程式開發介面，並示範如何實作在 Xamarin.iOS 應用程式中支援連續的語音辨識和語音 （從即時或錄製音訊資料流） 同時成文字。_

新增 ios 10，Apple 已釋放語音辨識應用程式開發介面，可讓文字中的 iOS 應用程式支援連續的語音辨識，並同時語音 （從即時或錄製音訊資料流）。

根據 Apple，語音辨識應用程式開發介面有下列功能和優點：

- 高度精確
- 先進
- 容易使用
- 快速
- 支援多國語言
- 尊重使用者隱私權

## <a name="how-speech-recognition-works"></a>語音辨識的運作方式

語音辨識被藉由在 iOS 應用程式取得實況或預錄音訊 （在任何 API 支援的語音語言），並將其傳遞到語音辨識器會傳回純文字講出口語的文字。

[ ![](speech-images/speech01.png "語音辨識的運作方式")](speech-images/speech01.png)

### <a name="keyboard-dictation"></a>鍵盤聽寫

當大部分使用者認為語音辨識的 iOS 裝置上時，它們將內建的 Siri 語音助理，以及鍵盤聽寫 iOS 5 與 iPhone 4S 中發行。

鍵盤聽寫受到支援 TextKit 任何介面項目 (例如`UITextField`或`UITextArea`) 是由和啟動使用者按一下 iOS 虛擬鍵盤在聽寫 按鈕 （直接以空格鍵的左邊）。

Apple 已發行下列鍵盤聽寫統計資料 （收集自 2011年）：

- 在 iOS 5 發行後已廣泛使用鍵盤聽寫。
- 大約 65000 應用程式每日使用它。
- 所有 iOS 的第三個有關聽寫是在第 3 個合作對象應用程式。

鍵盤聽寫是使用相當簡單，因為它需要沒有投入時間集中用於開發人員的一部分，在應用程式的 UI 設計使用 TextKit 介面項目以外。 鍵盤聽寫也的優點是不需要任何特殊權限要求，從應用程式，才能使用。

使用新的語音辨識 Api 的應用程式將會需要特殊權限可授與使用者，因為語音辨識要求的傳輸和 Apple 的伺服器上的資料暫存儲存位置。 請參閱我們[安全性和隱私權增強功能](~/ios/app-fundamentals/security-privacy.md)文件以取得詳細資料。

鍵盤聽寫容易實作時，它會帶來幾個限制和缺點：

- 需要文字輸入欄位的使用和顯示鍵盤。
- 它適用於即時音訊輸入只有和應用程式有任何控制音訊錄製程序。
- 它提供用來解譯使用者的語音語言無法控制。
- 沒有任何要知道是否聽寫按鈕甚至可供使用者應用程式的方式。
- 應用程式不能自訂音訊錄製程序。
- 它提供非常淺層的結果集缺少計時以及信心等資訊。

### <a name="speech-recognition-api"></a>語音辨識應用程式開發介面

新增 ios 10，Apple 已發行可提供更強大的方式，針對 iOS 應用程式實作 語音辨識語音辨識 API。 這個 API 已同一個 Apple 使用 Siri 和鍵盤聽寫的電源，而且能夠快速轉譯提供先進的精確度。

個別的使用者，而不需要收集或存取任何私用的使用者資料的應用程式以透明的方式自訂語音辨識 API 所提供的結果。

語音辨識應用程式開發介面會提供回到呼叫應用程式中的結果接近即時的因為使用者說，並提供比只是文字的轉譯結果的詳細資訊。 它們包括：

- 多重使用者所說的解譯。
- 個別翻譯的信賴等級。
- 計時資訊。

如前所述，是由即時的摘要，或從預先錄製的來源和中的任何超過 50 種語言和方言支援 iOS 10 可提供翻譯的音訊。

語音辨識應用程式開發介面可用在任何執行 iOS 10 的 iOS 裝置上，而且在大部分情況下，需要即時網際網路連線，因為大部分的轉譯會在 Apple 的伺服器上進行。 話雖如此，某些較新的 iOS 裝置支援永遠開啟功能，裝置上的特定語言的翻譯。

Apple，並包含可用性 API，以判斷給定的語言是否適用於在目前時間的轉譯。 應用程式應該使用此應用程式開發介面，而不是直接測試本身的網際網路連線。

如先前所述的鍵盤聽寫區段中，語音辨識需要傳輸和 Apple 的伺服器上的資料暫存儲存體透過網際網路，因此應用程式_必須_要求使用者的權限，才能執行藉由辨識`NSSpeechRecognitionUsageDescription`中的索引鍵及其`Info.plist`檔案，並呼叫`SFSpeechRecognizer.RequestAuthorization`方法。 

根據用於語音辨識音訊的來源，其他變更應用程式的`Info.plist`可能需要的檔案。 請參閱我們[安全性和隱私權增強功能](~/ios/app-fundamentals/security-privacy.md)文件以取得詳細資料。

## <a name="adopting-speech-recognition-in-an-app"></a>採用的應用程式中的語音辨識

有開發人員必須取得採用 iOS 應用程式中的語音辨識的四個主要步驟：

- 提供的應用程式中的使用方式描述`Info.plist`檔案使用`NSSpeechRecognitionUsageDescription`索引鍵。 例如，相機應用程式可能會包含下列的說明， _"這可讓您採取相片只依所說這個字 'cheese'。 」_
- 藉由呼叫要求授權`SFSpeechRecognizer.RequestAuthorization`呈現說明的方法 (中提供`NSSpeechRecognitionUsageDescription`上方的索引鍵) 的應用程式為何想語音辨識 對話方塊中的使用者存取，以及允許其接受或拒絕。
- 建立語音辨識要求：
    * 對於預先錄製的音訊磁碟上，使用`SFSpeechURLRecognitionRequest`類別。
    * 即時音訊 （或從記憶體音訊），請使用`SFSPeechAudioBufferRecognitionRequest`類別。
- 傳遞至語音辨識器的 語音辨識要求 (`SFSpeechRecognizer`) 開始辨識。 應用程式可以選擇性地保留傳回`SFSpeechRecognitionTask`來監視和追蹤辨識結果。

將下面將詳細討論這些步驟。

### <a name="providing-a-usage-description"></a>提供使用方式描述

若要提供必要`NSSpeechRecognitionUsageDescription`中的索引鍵`Info.plist`檔案中，執行下列動作：

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

1. 按兩下`Info.plist`檔案，以開啟它進行編輯。
2. 切換至**來源**檢視： 

    [ ![](speech-images/speech02.png "原始碼檢視")](speech-images/speech02.png)
3. 按一下**加入新項目**，輸入`NSSpeechRecognitionUsageDescription`如**屬性**，`String`如**類型**和**使用方式描述**做為**值**。 例如:  

    [ ![](speech-images/speech03.png "加入 NSSpeechRecognitionUsageDescription")](speech-images/speech03.png)
4. 如果應用程式會處理即時音訊轉譯，它也需要麥克風的使用方式描述。 按一下**加入新項目**，輸入`NSMicrophoneUsageDescription`如**屬性**，`String`如**類型**和**使用方式描述**做為**值**。 例如:  

    [ ![](speech-images/speech04.png "加入 NSMicrophoneUsageDescription")](speech-images/speech04.png)
4. 將變更儲存到檔案。

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. 按兩下`Info.plist`檔案，以開啟它進行編輯。
3. 按一下**加入新項目**，輸入`NSSpeechRecognitionUsageDescription`如**屬性**，`String`如**類型**和**使用方式描述**做為**值**。 例如:  

    [ ![](speech-images/speech03w.png "加入 NSSpeechRecognitionUsageDescription")](speech-images/speech03w.png)
4. 如果應用程式會處理即時音訊轉譯，它也需要麥克風的使用方式描述。 按一下**加入新項目**，輸入`NSMicrophoneUsageDescription`如**屬性**，`String`如**類型**和**使用方式描述**做為**值**。 例如:  

    [ ![](speech-images/speech04w.png "加入 NSMicrophoneUsageDescription")](speech-images/speech04w.png)
4. 將變更儲存到檔案。

-----

> [!IMPORTANT]
> **注意：**無法提供上述其中一項`Info.plist`索引鍵 (`NSSpeechRecognitionUsageDescription`或`NSMicrophoneUsageDescription`) 可能會導致應用程式嘗試存取即時音訊麥克風或語音辨識時失敗而不發出警告。




### <a name="requesting-authorization"></a>要求的授權

若要要求必要的使用者授權，可讓應用程式存取語音辨識，編輯的主要檢視控制器類別並加入下列程式碼：

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

`RequestAuthorization`方法`SFSpeechRecognizer`類別會要求使用者權限來存取語音辨識使用中的開發人員所提供的原因`NSSpeechRecognitionUsageDescription`索引鍵`Info.plist`檔案。

A`SFSpeechRecognizerAuthorizationStatus`結果會傳回到`RequestAuthorization`可以用來採取的動作方法的回呼常式根據使用者的權限。 

> [!IMPORTANT]
> **注意：** Apple 建議等待，直到使用者已要求此權限之前需要語音辨識的應用程式中啟動動作。

### <a name="recognizing-pre-recorded-speech"></a>可預先錄製的語音辨識

如果應用程式想要辨識語音從預先錄製的 WAV 或 MP3 檔案時，它可以使用下列程式碼：

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

查看這個程式碼的詳細資料，首先，它會嘗試建立語音辨識器 (`SFSpeechRecognizer`)。 如果預設的語言不支援語音辨識`null`會傳回並函式會結束。

如果語音辨識器的預設語言，則應用程式會檢查以查看是否目前可供辨識使用`Available`屬性。 比方說，辨識可能會無法使用，如果裝置沒有作用中的網際網路連線。

A`SFSpeechUrlRecognitionRequest`建立從`NSUrl`預先錄製的 iOS 裝置，且該檔案的位置交給語音辨識器進行回呼常式。

當回呼呼叫時，如果`NSError`不`null`有必須處理的錯誤。 語音辨識以累加方式進行設定，因為回呼常式可能會呼叫超過一次，所以`SFSpeechRecognitionResult.Final`屬性測試以查看轉譯已完成，翻譯的最佳版本會寫出 (`BestTranscription`)。

### <a name="recognizing-live-speech"></a>可辨識的即時語音

如果應用程式想要辨識即時語音，處理程序是非常類似，以及預先錄製的語音辨識。 例如: 

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

查看這個程式碼的詳細資料，它會建立數個處理辨識程序的私用變數：

```csharp
private AVAudioEngine AudioEngine = new AVAudioEngine ();
private SFSpeechRecognizer SpeechRecognizer = new SFSpeechRecognizer ();
private SFSpeechAudioBufferRecognitionRequest LiveSpeechRequest = new SFSpeechAudioBufferRecognitionRequest ();
private SFSpeechRecognitionTask RecognitionTask;
```

它會使用錄製音訊會傳遞至 AV Foundation`SFSpeechAudioBufferRecognitionRequest`處理辨識要求：

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

辨識工作啟動和控制代碼保留辨識工作 (`SFSpeechRecognitionTask`):

```csharp
RecognitionTask = SpeechRecognizer.GetRecognitionTask (LiveSpeechRequest, (SFSpeechRecognitionResult result, NSError err) => {
    ...
});
```

回呼用於預先錄製語音在上面使用的一個類似的方式。

如果記錄由使用者停止，就會接到通知音訊引擎和語音辨識要求：

```csharp
AudioEngine.Stop ();
LiveSpeechRequest.EndAudio ();
```

如果使用者取消辨識，然後為音訊引擎和辨識工作就會接到通知：

```csharp
AudioEngine.Stop ();
RecognitionTask.Cancel ();
```

請務必呼叫`RecognitionTask.Cancel`如果使用者取消的翻譯，以釋出記憶體和裝置的處理器。

> [!IMPORTANT]
> **注意：**無法提供`NSSpeechRecognitionUsageDescription`或`NSMicrophoneUsageDescription``Info.plist`金鑰可能會導致應用程式嘗試存取即時音訊麥克風或語音辨識時，而不發出警告失敗 (`var node = AudioEngine.InputNode;`)。 請參閱**提供使用方式描述**節，如需詳細資訊。

## <a name="speech-recognition-limits"></a>語音辨識限制

Apple 在 iOS 應用程式中使用 語音辨識時沒有以下限制：

- 語音辨識可用所有的應用程式，但其使用方式不是無限制：
    - 個別的 iOS 裝置數目有限制 recognitions 可以每日執行。
    - 應用程式會要求每日為基礎的全域調整。
- 應用程式必須準備好處理語音辨識網路連線和使用速率限制失敗。
- 語音辨識可能會在電池清空和高的網路流量成本最高使用者的 iOS 裝置，因為這個緣故，Apple 對於強加約一分鐘的最大的語音嚴格音訊的持續時間限制。

如果應用程式會定期達到其速度節流設定的限制，系統會要求 Apple 開發人員連絡他們。

## <a name="privacy-and-usability-considerations"></a>隱私權和可用性考量

Apple 已被透明和尊重使用者隱私權，在 iOS 應用程式中包含語音辨識時的下列建議：

- 記錄使用者的語音時, 務必清楚指出，錄製正在進行中的應用程式使用者介面。 例如，應用程式可能音效 「 記錄 」，並顯示錄製指標。
- 請勿使用語音辨識敏感使用者資訊，例如密碼、 健全狀況資料或財務資訊。
- 顯示辨識結果_之前_對它們執行動作。 這不只會提供功能的應用程式正在進行，但可讓使用者處理辨識錯誤，他們所做的回應。

## <a name="summary"></a>總結

本文章有顯示新的語音 API，並示範如何實作在 Xamarin.iOS 應用程式中支援連續的語音辨識和語音 （從即時或錄製音訊資料流） 同時成文字。 



## <a name="related-links"></a>相關連結

- [SpeakToMe （範例）](https://developer.xamarin.com/samples/monotouch/ios10/SpeakToMe/)
