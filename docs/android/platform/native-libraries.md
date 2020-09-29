---
title: 使用原生程式庫
ms.prod: xamarin
ms.assetid: 7AA6CEC8-C09E-BBDA-FDD6-E40559143548
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/09/2018
ms.openlocfilehash: 1b24648f70041abb092c93f41e999b68e8f26a26
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91453255"
---
# <a name="using-native-libraries"></a>使用原生程式庫

Xamarin 支援透過標準 PInvoke 機制使用原生程式庫。 您也可以將不屬於作業系統一部分的其他原生程式庫組合到 apk 中。

若要使用 Xamarin Android 應用程式部署原生程式庫，請將程式庫二進位檔新增至專案，並將其 **組建動作** 設為 **AndroidNativeLibrary**。

若要使用 Xamarin. Android 程式庫專案部署原生程式庫，請將程式庫二進位檔新增至專案，並將其 **組建動作** 設為 **EmbeddedNativeLibrary**。

請注意，由於 Android 支援多個應用程式二進位介面 (Abi) ，因此 Xamarin 必須知道要建立原生程式庫的 ABI。
方法有二：

1. 路徑「探查」
1. 藉由使用  `AndroidNativeLibrary/Abi` 專案檔中的元素

路徑探查會使用原生程式庫的父目錄名稱來指定程式庫的目標 ABI。 因此，如果您將加入 `lib/armeabi/libfoo.so` 至專案，則 ABI 將會「探查」為 `armeabi` 。

或者，您可以編輯專案檔，以明確指定要使用的 ABI：

```xml
<ItemGroup>
    <AndroidNativeLibrary Include="path/to/libfoo.so">
        <Abi>armeabi</Abi>
    </AndroidNativeLibrary>
</ItemGroup>
```

如需使用原生程式庫的詳細資訊，請參閱 [使用原生程式庫的 Interop](https://www.mono-project.com/docs/advanced/pinvoke/)。

## <a name="debugging-native-code-with-visual-studio"></a>使用 Visual Studio 來調試機器碼

如果您是使用 *Visual Studio 2019* 或 *Visual Studio 2017*，則不需要修改您的專案檔，如上所述。
您可以將專案參考新增至 c + + **動態共用程式庫 (Android) ** 專案，以在您的 Xamarin 方案內建立和偵錯工具。

若要在您的專案中調試原生 c + + 程式碼，請遵循下列步驟：

1. 按兩下 [專案 **屬性** ]，然後選取 [ **Android 選項** ] 頁面。
2. 向下滾動至 **調試選項**。
3. 在 **偵錯工具** 下拉式功能表中，選取 **c + +** (，而不是預設的 **.net (Xamarin) **) 。

Visual Studio c + + 開發人員可以看到 [SanAngeles_NativeDebug](/samples/xamarin/monodroid-samples/sanangeles-ndk) 範例，以嘗試使用 Xamarin 從 Visual Studio 2019 或 Visual Studio 2017 進行 c + + 的調試;如需詳細資訊，請參閱我們的 [blog 文章](https://blog.xamarin.com/build-and-debug-c-libraries-in-xamarin-android-apps-with-visual-studio-2015/) 。

## <a name="related-links"></a>相關連結

- [SanAngeles_NativeDebug (範例) ](/samples/xamarin/monodroid-samples/sanangeles-ndk)
- [開發 Xamarin Android 原生應用程式](https://blogs.msdn.microsoft.com/vcblog/2015/02/23/developing-xamarin-android-native-applications/)