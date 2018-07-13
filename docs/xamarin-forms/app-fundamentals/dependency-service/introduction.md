---
title: DependencyService 簡介
description: 這篇文章說明 Xamarin.Forms DependencyService 類別存取原生平台功能的運作方式。
ms.prod: xamarin
ms.assetid: 5d019604-4f6f-4932-9b26-1fce3b4d88f8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/06/2017
ms.openlocfilehash: 558a05b5fdc4c4f08194b708de886bca342dd860
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/12/2018
ms.locfileid: "38995410"
---
# <a name="introduction-to-dependencyservice"></a>DependencyService 簡介

## <a name="overview"></a>總覽

[`DependencyService`](xref:Xamarin.Forms.DependencyService) 允許呼叫共用的程式碼的平台專屬功能的應用程式。 這項功能可讓您執行原生應用程式可執行的任何動作的 Xamarin.Forms 應用程式。

`DependencyService` 是相依性解析程式。 在實務上，定義介面和`DependencyService`找到正確實作該介面從各種平台專案。

## <a name="how-dependencyservice-works"></a>DependencyService 的運作方式

Xamarin.Forms 應用程式需要使用四個元件`DependencyService`:

- **介面**&ndash;所共用的程式碼中的介面定義必要的功能。
- **實作每個平台**&ndash;實作介面的類別必須加入至每個平台專案。
- **註冊**&ndash;每個實作的類別必須向`DependencyService`透過中繼資料屬性。 註冊啟用`DependencyService`尋找實作的類別，並在執行階段提供介面取代。
- **呼叫以 DependencyService** &ndash;共用程式碼必須明確地呼叫`DependencyService`要求介面的實作。

請注意，必須提供實作，每個平台專案方案中。 不含實作的平台專案會在執行階段失敗。

下圖說明應用程式的結構：

![](introduction-images/overview-diagram.png "DependencyService 應用程式結構")

### <a name="interface"></a>介面

您在設計介面會定義您與平台專屬功能的互動。 要小心，如果您正在開發共用為元件或 Nuget 套件的元件。 API 設計的成敗封裝。 下列範例會指定簡單介面說到要讀出允許為指定文字的彈性，但會保留自訂每個平台實作的文字：

```csharp
public interface ITextToSpeech {
    void Speak ( string text ); //note that interface members are public by default
}
```

### <a name="implementation-per-platform"></a>每個平台的實作

一旦已設計適當的介面，該介面必須實作您的目標每個平台專案中。 例如，下列類別會實作`ITextToSpeech`在 iOS 上的介面：

```csharp
namespace UsingDependencyService.iOS
{
    public class TextToSpeech_iOS : ITextToSpeech
    {
        public void Speak (string text)
        {
            var speechSynthesizer = new AVSpeechSynthesizer ();

            var speechUtterance = new AVSpeechUtterance (text) {
                Rate = AVSpeechUtterance.MaximumSpeechRate/4,
                Voice = AVSpeechSynthesisVoice.FromLanguage ("en-US"),
                Volume = 0.5f,
                PitchMultiplier = 1.0f
            };

            speechSynthesizer.SpeakUtterance (speechUtterance);
        }
    }
}
```

### <a name="registration"></a>註冊

每個介面的實作必須向`DependencyService`與中繼資料屬性。 下列程式碼會註冊適用於 iOS 的實作：

```csharp
[assembly: Dependency (typeof (TextToSpeech_iOS))]
namespace UsingDependencyService.iOS
{
  ...
}
```

總結，平台特定實作看起來像這樣：

```csharp
[assembly: Dependency (typeof (TextToSpeech_iOS))]
namespace UsingDependencyService.iOS
{
    public class TextToSpeech_iOS : ITextToSpeech
    {
        public void Speak (string text)
        {
            var speechSynthesizer = new AVSpeechSynthesizer ();

            var speechUtterance = new AVSpeechUtterance (text) {
                Rate = AVSpeechUtterance.MaximumSpeechRate/4,
                Voice = AVSpeechSynthesisVoice.FromLanguage ("en-US"),
                Volume = 0.5f,
                PitchMultiplier = 1.0f
            };

            speechSynthesizer.SpeakUtterance (speechUtterance);
        }
    }
}
```

注意： 在命名空間層級，而不是類別層級執行註冊。

#### <a name="universal-windows-platform-net-native-compilation"></a>通用 Windows 平台.NET 原生編譯

使用.NET Native 編譯選項的 UWP 專案應該遵循[稍有不同的組態](~/xamarin-forms/platform/windows/installation/index.md#target-invocation-exception)初始化 Xamarin.Forms 時。 .NET native 編譯也會需要稍微不同的註冊相依性服務。

在  **App.xaml.cs**檔案中，手動註冊每個定義在 UWP 專案中使用的相依性服務`Register<T>`方法，如下所示：

```csharp
Xamarin.Forms.Forms.Init(e, assembliesToInclude);
// register the dependencies in the same
Xamarin.Forms.DependencyService.Register<TextToSpeechImplementation>();
```

注意： 手動註冊使用`Register<T>`是使用.NET Native 編譯時，組建版本中的唯一有效。 如果您省略此線路，還是可以使用偵錯組建，但發行組建將無法載入相依性服務。

### <a name="call-to-dependencyservice"></a>呼叫以 DependencyService

一旦專案已設定的通用介面與實作每個平台，使用`DependencyService`在執行階段取得右實作：

```csharp
DependencyService.Get<ITextToSpeech>().Speak("Hello from Xamarin Forms");
```

`DependencyService.Get<T>` 將會找到正確的實作介面`T`。

### <a name="solution-structure"></a>方案結構

[範例 UsingDependencyService 方案](https://developer.xamarin.com/samples/UsingDependencyService/)是適用於 iOS 和 Android，如下所示，使用上述的程式碼變更反白顯示。

 [![iOS 和 Android 的解決方案](introduction-images/solution-sml.png "DependencyService 範例的方案結構")](introduction-images/solution.png#lightbox "DependencyService 範例的方案結構")

> [!NOTE]
> 您**必須**提供每個平台專案中的實作。 如果沒有介面實作已註冊，則`DependencyService`無法解析`Get<T>()`方法在執行階段。


## <a name="related-links"></a>相關連結

- [DependencyServiceSample](https://developer.xamarin.com/samples/xamarin-forms/UsingDependencyService/)
- [Xamarin.Forms 範例](https://developer.xamarin.com/samples/xamarin-forms/all/)
