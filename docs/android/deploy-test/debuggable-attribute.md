---
title: 可偵錯的屬性
ms.prod: xamarin
ms.assetid: 1ABF90F1-6A70-45AE-9271-D90DC42807D0
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 12/13/2019
ms.openlocfilehash: 6e54dba0c832596818c5163dc4e14ad1e659e040
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "75487967"
---
# <a name="debuggable-attribute"></a>可偵錯的屬性

為了進行偵錯，Android 支援 Java 偵錯連線通訊協定 (Java Debug Wire Protocol, JDWP, JDWP)。 這是一項允許如 ADB 的工具與 JVM 進行通訊的技術。 雖然 JDWP 在開發過程中很重要,但應在應用程式發佈之前禁用它。

JDWP 可以按 Android`android:debuggable`應用程式中 的屬性值進行配置。 選擇以下三種方法_之一_,在 Xamarin.Android 中設置此屬性:

## <a name="androidmanifestxml"></a>AndroidManifest.xml

建立或打開`AndroidManifext.xml`檔案,並在那裡`android:debuggable`設置 屬性。 在啟用調試後,不要提供發佈版本,請格外小心。

## <a name="add-an-application-class-attribute"></a>新增應用程式類別屬性

如果您的 Xamarin.Android 應用具有具有`[Application]`屬性的類別,請將`[Application(Debuggable = true)]`該屬性更新為 。 將其設置為`false`禁用。

## <a name="add-an-assembly-attribute"></a>新增程式集屬性

如果您的 Xamarin.Android 應用`[Application]`尚未具有 類屬性,請在 c# 檔中添加`[assembly: Application(Debuggable=true)]`程式集級屬性。 將其設置為`false`禁用。

## <a name="summary"></a>總結

若同時具有 `AndroidManifest.xml` 及 `ApplicationAttribute`，則 `AndroidManifest.xml` 的內容會優先於 `ApplicationAttribute` 所指定的內容。

如果同時新增類別屬性_與_程式集屬性,則存在編譯器錯誤:

```error
"Error The "GenerateJavaStubs" task failed unexpectedly.
System.InvalidOperationException: Application cannot have both a type with an [Application] attribute and an [assembly:Application] attribute."
```

預設情況下,如果`AndroidManifest.xml`不存在 ,`ApplicationAttribute``android:debuggable`則屬性的值取決於是否生成調試符號。 如果存在調試符號,則 Xamarin.Android`android:debuggable``true`將為您設置屬性。

> [!WARNING]
> `android:debuggable`屬性的值不一定取決於生成配置。 發行組建的 `android:debuggable` 屬性也可設為 true。 如果使用屬性設定這個值,則可以選擇在編譯器指令中換行該屬性:
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
