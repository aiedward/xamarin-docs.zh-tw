---
title: 可偵錯的屬性
ms.prod: xamarin
ms.assetid: 1ABF90F1-6A70-45AE-9271-D90DC42807D0
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 12/13/2019
ms.openlocfilehash: 6e54dba0c832596818c5163dc4e14ad1e659e040
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/25/2019
ms.locfileid: "75487967"
---
# <a name="debuggable-attribute"></a>可偵錯的屬性

為了進行偵錯，Android 支援 Java 偵錯連線通訊協定 (Java Debug Wire Protocol, JDWP, JDWP)。 這是一項允許如 ADB 的工具與 JVM 進行通訊的技術。 雖然 JDWP 在開發期間很重要，但在發行應用程式之前，應該先將它停用。

JDWP 可以透過 Android 應用程式中 `android:debuggable` 屬性的值來設定。 選擇下列三種方式的_其中一_種，在 Xamarin 中設定此屬性：

## <a name="androidmanifestxml"></a>AndroidManifest.xml

建立或開啟 `AndroidManifext.xml` 檔案，並在該處設定 `android:debuggable` 屬性。 請特別小心，不要在啟用偵測功能的情況下寄送發行組建。

## <a name="add-an-application-class-attribute"></a>新增應用程式類別屬性

如果您的 Xamarin Android 應用程式具有具有 `[Application]` 屬性的類別，請將屬性更新為 `[Application(Debuggable = true)]`。 將它設定為 `false` 以停用。

## <a name="add-an-assembly-attribute"></a>加入元件屬性

如果您的 Xamarin Android 應用程式還沒有 `[Application]` 類別屬性，請在 c # 檔案中加入元件層級屬性 `[assembly: Application(Debuggable=true)]`。 將它設定為 `false` 以停用。

## <a name="summary"></a>總結

若同時具有 `AndroidManifest.xml` 及 `ApplicationAttribute`，則 `AndroidManifest.xml` 的內容會優先於 `ApplicationAttribute` 所指定的內容。

如果您同時加入類別屬性_和_元件屬性，將會發生編譯器錯誤：

```error
"Error The "GenerateJavaStubs" task failed unexpectedly.
System.InvalidOperationException: Application cannot have both a type with an [Application] attribute and an [assembly:Application] attribute."
```

根據預設，如果 `AndroidManifest.xml` 或 `ApplicationAttribute` 都不存在，`android:debuggable` 屬性的值將取決於是否產生 debug 符號。 如果有偵錯工具符號，則 Xamarin 會將 `android:debuggable` 屬性設定為您 `true`。

> [!WARNING]
> `android:debuggable` 屬性的值不一定會相依于組建設定。 發行組建的 `android:debuggable` 屬性也可設為 true。 如果您使用屬性來設定此值，您可以選擇將屬性包裝在編譯器指示詞中：
> 
> ```csharp
> #if DEBUG
> [Application(Debuggable = true)]
> #else
> [Application(Debuggable = false)]
> #endif
> ```

## <a name="related-links"></a>相關連結

- [Debuggable apps in the Android market](https://labs.f-secure.com/archive/debuggable-apps-in-android-market/) (Android Market 中的可偵錯應用程式)
