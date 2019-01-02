---
title: DependencyService 簡介
description: 本文說明 Xamarin.Forms DependencyService 類別如何存取原生平台的功能。
ms.prod: xamarin
ms.assetid: 5d019604-4f6f-4932-9b26-1fce3b4d88f8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/15/2018
ms.openlocfilehash: 8f32255b6451b5b672293c8db42bb8b1ab38a7fd
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/07/2018
ms.locfileid: "53061834"
---
# <a name="introduction-to-dependencyservice"></a>DependencyService 簡介

[![下載範例](~/media/shared/download.png) 下載範例](https://developer.xamarin.com/samples/xamarin-forms/UsingDependencyService/)

## <a name="overview"></a>總覽

[`DependencyService`](xref:Xamarin.Forms.DependencyService) 允許應用程式透過共用程式碼呼叫平台特定功能。 這項功能可讓 Xamarin.Forms 應用程式執行原生應用程式可執行的任何動作。

`DependencyService` 是一種服務定位器。 在實務上，只要有定義介面，`DependencyService` 就會從各種平台專案找到該介面的正確實作。

> [!NOTE]
> 根據預設，[`DependencyService`](xref:Xamarin.Forms.DependencyService) 僅會解析具有無參數建構函式的平台實作。 不過，您可以將相依性解析方法插入 Xamarin.Forms，其會使用相依性插入容器或 Factory 方法來解析平台實作。 這種方法可用來解析具有參數建構函式的平台實作。 如需詳細資訊，請參閱 [Xamarin.Forms 中的相依性解析](~/xamarin-forms/internals/dependency-resolution.md)。

## <a name="how-dependencyservice-works"></a>DependencyService 的運作方式

Xamarin.Forms 應用程式需要下列四個元件以使用 `DependencyService`：

- **介面** &ndash; 必要功能由共用程式碼中的介面來定義。
- **每個平台的實作** &ndash; 實作介面的類別必須新增至每個平台專案。
- **註冊** &ndash; 每個實作類別必須透過中繼資料屬性向 `DependencyService` 註冊。 註冊可讓 `DependencyService` 尋找實作類別，並提供此類別以在執行階段取代介面。
- **呼叫 DependencyService** &ndash; 共用程式碼必須明確地呼叫 `DependencyService` 以要求介面的實作。

請注意，您必須為方案中每個平台專案提供實作。 如果平台專案不含實作，則會在執行階段失敗。

下圖說明應用程式的結構：

![](introduction-images/overview-diagram.png "DependencyService 應用程式結構")

### <a name="interface"></a>介面

您與平台特定功能的互動方式是由您設計的介面來定義。 如果您開發的元件會以元件或 NuGet 套件形式共用，則必須小心。 API 設計是套件的成敗關鍵。 下列範例是用來指定朗讀文字的簡單介面，其保障指定朗讀文字的作業靈活性，但仍保留每個平台的實作自訂性：

```csharp
public interface ITextToSpeech {
    void Speak ( string text ); //note that interface members are public by default
}
```

### <a name="implementation-per-platform"></a>每個平台的實作

一旦已設計適當的介面，則必須在您設為目標的每個平台專案中實作該介面。 例如，下列類別會在 iOS 上實作 `ITextToSpeech` 介面：

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

介面的每一個實作必須使用中繼資料屬性向 `DependencyService` 註冊。 下列程式碼會註冊 iOS 實作：

```csharp
[assembly: Dependency (typeof (TextToSpeech_iOS))]
namespace UsingDependencyService.iOS
{
  ...
}
```

綜合來看，平台特定實作看起來像這樣：

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

注意：註冊是在命名空間層級執行，而不是在類別層級執行。

#### <a name="universal-windows-platform-net-native-compilation"></a>通用 Windows 平台的 .NET Native 編譯

若是使用 .NET Native 編譯選項的 UWP 專案，在初始化 Xamarin.Forms 時應該採用[略有不同的組態](~/xamarin-forms/platform/windows/installation/index.md#target-invocation-exception)。 .NET Native 編譯也會需要針對相依性服務使用稍微不同的註冊方式。

在 **App.XAML.cs** 檔案中，使用 `Register<T>` 方法手動註冊 UWP 專案中定義的每個相依性服務，如下所示：

```csharp
Xamarin.Forms.Forms.Init(e, assembliesToInclude);
// register the dependencies in the same
Xamarin.Forms.DependencyService.Register<TextToSpeechImplementation>();
```

注意：使用 `Register<T>` 手動註冊只有在使用 .NET Native 編譯的組建版本中才有效。 如果您省略這一行，偵錯組建仍然可以運作，但發行組建將無法載入相依性服務。

### <a name="call-to-dependencyservice"></a>呼叫 DependencyService

一旦專案已設定好通用介面與每個平台的實作，即可使用 `DependencyService` 在執行階段取得適當的實作：

```csharp
DependencyService.Get<ITextToSpeech>().Speak("Hello from Xamarin Forms");
```

`DependencyService.Get<T>` 將會找到介面 `T` 的正確實作。

### <a name="solution-structure"></a>方案結構

下方顯示適用於 iOS 和 Android 的[範例 UsingDependencyService 方案](https://developer.xamarin.com/samples/UsingDependencyService/)，並醒目提示上述的程式碼變更。

 [![iOS 和 Android 方案](introduction-images/solution-sml.png "DependencyService 範例方案結構")](introduction-images/solution.png#lightbox "DependencyService 範例方案結構")

> [!NOTE]
> 您**必須**在每個平台專案中提供實作。 如果未註冊任何介面實作，則 `DependencyService` 將無法在執行階段解析 `Get<T>()` 方法。

## <a name="related-links"></a>相關連結

- [DependencyServiceSample](https://developer.xamarin.com/samples/xamarin-forms/UsingDependencyService/)
- [Xamarin.Forms 範例](https://developer.xamarin.com/samples/xamarin-forms/all/)
