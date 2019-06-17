---
title: 使用原生程式庫
ms.prod: xamarin
ms.assetid: 7AA6CEC8-C09E-BBDA-FDD6-E40559143548
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/09/2018
ms.openlocfilehash: 1b0771a0ccc2597ebd800468b82044e4020d9d94
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61153306"
---
# <a name="using-native-libraries"></a>使用原生程式庫

Xamarin.Android 支援原生程式庫，透過標準的 PInvoke 機制使用。 您也可以組合不到您的.apk 作業系統的一部分的其他原生程式庫。

若要部署 Xamarin.Android 應用程式的原生程式庫，將二進位文件庫加入至專案，並設定其**建置動作**要**AndroidNativeLibrary**。

若要部署的 Xamarin.Android 程式庫專案的原生程式庫，將二進位文件庫加入至專案，並設定其**建置動作**要**EmbeddedNativeLibrary**。

請注意，由於 Android 支援多個應用程式二進位介面 (Abi)，Xamarin.Android，必須知道的建置原生程式庫的 ABI。
方法有二：

1.  路徑 「 探查 」
1.  使用`AndroidNativeLibrary/Abi`專案檔內的項目


路徑探查會使用原生程式庫的父目錄名稱來指定程式庫的目標 ABI。 因此，如果您加入`lib/armeabi/libfoo.so`至專案，然後 ABI 會 「 探查 」 為`armeabi`。

或者，您可以編輯您的專案檔，來明確指定要使用 ABI:

```xml
<ItemGroup>
    <AndroidNativeLibrary Include="path/to/libfoo.so">
        <Abi>armeabi</Abi>
    </AndroidNativeLibrary>
</ItemGroup>
```

如需使用原生程式庫的詳細資訊，請參閱 <<c0> [ 原生程式庫與 Interop](https://www.mono-project.com/docs/advanced/pinvoke/)。

## <a name="debugging-native-code-with-visual-studio"></a>使用 Visual Studio 偵錯原生程式碼

如果您使用*Visual Studio 2019*或是*Visual Studio 2017*，您不需要修改您的專案檔，如上面所述。
您可以建置和偵錯C++在您的 Xamarin.Android 解決方案，藉由新增的專案參考C++**動態共用程式庫 (Android)** 專案。

若要偵錯原生C++您的專案中的程式碼，請遵循下列步驟：

1. 按兩下 [專案]**屬性**，然後選取**Android 選項**頁面。
2. 向下捲動至**偵錯選項**。
3. 在 **偵錯工具**下拉式功能表中，選取**C++** (而非預設 **/.net (Xamarin)**)。

Visual StudioC++開發人員可以看到[SanAngeles_NativeDebug](https://developer.xamarin.com/samples/monodroid/SanAngeles_NDK/)嘗試偵錯範例C++從 Visual Studio 2019 或 Visual Studio 2017 with Xamarin，並參考我們[部落格文章](https://blog.xamarin.com/build-and-debug-c-libraries-in-xamarin-android-apps-with-visual-studio-2015/)如需詳細資訊資訊。



## <a name="related-links"></a>相關連結

- [SanAngeles_NativeDebug (sample)](https://developer.xamarin.com/samples/monodroid/SanAngeles_NDK/)
- [開發 Xamarin Android 原生應用程式](https://blogs.msdn.microsoft.com/vcblog/2015/02/23/developing-xamarin-android-native-applications/)
