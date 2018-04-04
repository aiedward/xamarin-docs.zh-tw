---
title: DependencyService 簡介
description: 了解 DependencyService 存取原生平台功能的運作方式
ms.prod: xamarin
ms.assetid: 5d019604-4f6f-4932-9b26-1fce3b4d88f8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/06/2017
ms.openlocfilehash: 01953d55a104a70b0451c9b796c732254afb081e
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="introduction-to-dependencyservice"></a>DependencyService 簡介

## <a name="overview"></a>總覽

[`DependencyService`](https://developer.xamarin.com/api/type/Xamarin.Forms.DependencyService/) 允許從共用的程式碼呼叫特定平台功能的應用程式。 這項功能可讓 Xamarin.Forms 應用程式執行原生應用程式可執行的任何動作。

`DependencyService` 是相依性解析程式。 在實務上，定義介面和`DependencyService`尋找正確實作該介面，從各種不同平台專案。

## <a name="how-dependencyservice-works"></a>DependencyService 的運作方式

Xamarin.Forms 應用程式都必須使用三個元件`DependencyService`:

- **介面**&ndash;所需的功能由共用程式碼中的介面所定義。
- **每個平台實作**&ndash;實作介面的類別必須新增至每個平台專案。
- **註冊**&ndash;每個實作的類別必須向`DependencyService`透過中繼資料屬性。 註冊可讓`DependencyService`尋找實作類別，並在執行階段提供取代介面。
- **呼叫以 DependencyService** &ndash;共用程式碼必須明確地呼叫`DependencyService`要求介面的實作。

請注意，必須提供實作，每個平台專案方案中。 平台專案中的不含實作將會在執行階段失敗。

下列圖表所說明的應用程式的結構：

![](introduction-images/overview-diagram.png "DependencyService 應用程式結構")

### <a name="interface"></a>介面

您在設計介面會定義互動平台特有的功能。 請格外小心，如果您正在開發元件或 Nuget 封裝共用的元件。 應用程式開發介面的設計可以進行，或中斷封裝。 下列範例會指定簡單的就文字，要說出允許指定文字的彈性，但會保留每個平台自訂的實作介面：

```csharp
public interface ITextToSpeech {
    void Speak ( string text ); //note that interface members are public by default
}
```

### <a name="implementation-per-platform"></a>每個平台的實作

一旦已設計適當的介面，必須實作該介面中每個平台所設定的目標專案。 例如，下列類別會實作`ITextToSpeech`Windows Phone 上的介面：

```csharp
namespace TextToSpeech.WinPhone
{
  public class TextToSpeechImplementation : ITextToSpeech
  {
      public TextToSpeechImplementation() {}

      public async void Speak(string text)
      {
          SpeechSynthesizer synth = new SpeechSynthesizer();
          await synth.SpeakTextAsync(text);
      }
  }
}
```

請注意，每個實作必須擁有預設 （無參數） 建構函式為了讓`DependencyService`能夠將它執行個體化。 無參數建構函式不能定義介面。

### <a name="registration"></a>註冊

每個介面的實作必須與註冊`DependencyService`與中繼資料屬性。 下列程式碼會註冊 Windows Phone 的實作：

```csharp
using TextToSpeech.WinPhone;

[assembly: Xamarin.Forms.Dependency (typeof (TextToSpeechImplementation))]
namespace TextToSpeech.WinPhone {
  ...
```

融會貫通，平台特定實作看起來像這樣：

```csharp
[assembly: Xamarin.Forms.Dependency (typeof (TextToSpeechImplementation))]
namespace TextToSpeech.WinPhone {
  public class TextToSpeechImplementation : ITextToSpeech
  {
      public TextToSpeechImplementation() {}

      public async void Speak(string text)
      {
          SpeechSynthesizer synth = new SpeechSynthesizer();
          await synth.SpeakTextAsync(text);
      }
  }
}
```

注意： 在命名空間層級，而不是類別層級執行登錄。

#### <a name="universal-windows-platform-net-native-compilation"></a>通用 Windows 平台.NET 原生編譯

使用.NET Native 編譯選項的 UWP 專案應遵循[稍有不同的組態](~/xamarin-forms/platform/windows/installation/universal.md#target-invocation-exception)初始化 Xamarin.Forms 時。 .NET 原生編譯也需要稍微不同的註冊相依性服務。

在**App.xaml.cs**檔案中，手動註冊每個定義在 UWP 專案中使用的相依性服務`Register<T>`方法，如下所示：

```csharp
Xamarin.Forms.Forms.Init(e, assembliesToInclude);
// register the dependencies in the same
Xamarin.Forms.DependencyService.Register<TextToSpeechImplementation>();
```

請注意： 手動註冊使用`Register<T>`是唯一有效的版本中建立使用.NET 原生編譯。 如果您省略這一行，還是可以使用偵錯組建，但發行組建將無法載入相依性服務。

### <a name="call-to-dependencyservice"></a>呼叫以 DependencyService

一旦專案已設定使用的通用介面和實作的每個平台，使用`DependencyService`在執行階段取得正確的實作：

```csharp
DependencyService.Get<ITextToSpeech>().Speak("Hello from Xamarin Forms");
```

`DependencyService.Get<T>` 會尋找正確的介面實作`T`。

### <a name="solution-structure"></a>方案結構

[範例 UsingDependencyService 方案](https://developer.xamarin.com/samples/UsingDependencyService/)是適用於 iOS 和 Android，如下所示，上述程式碼變更反白顯示。

 [![iOS 和 Android 的方案](introduction-images/solution-sml.png "DependencyService 範例方案結構")](introduction-images/solution.png#lightbox "DependencyService 範例方案結構")

> [!NOTE]
> 您**必須**提供每個平台專案中的實作。 如果介面未不註冊任何實作，則`DependencyService`無法解析`Get<T>()`方法在執行階段。


## <a name="related-links"></a>相關連結

- [DependencyServiceSample](https://developer.xamarin.com/samples/xamarin-forms/UsingDependencyService/)
- [Xamarin.Forms 範例](https://developer.xamarin.com/samples/xamarin-forms/all/)
