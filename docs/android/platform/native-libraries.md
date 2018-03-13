---
title: "使用原生程式庫"
ms.topic: article
ms.prod: xamarin
ms.assetid: 7AA6CEC8-C09E-BBDA-FDD6-E40559143548
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/09/2018
ms.openlocfilehash: 7bd9a64ab7ea775688225ff5496773647174ebf8
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/12/2018
---
# <a name="using-native-libraries"></a>使用原生程式庫

Xamarin.Android 支援原生程式庫，透過標準的 PInvoke 機制使用。 您也可以使配套其他原生程式庫不到您的.apk 作業系統的一部分。

若要部署與 Xamarin.Android 應用程式的原生程式庫，二進位文件庫加入至專案，並設定其**建置動作**至**AndroidNativeLibrary**。

若要部署的 Xamarin.Android 程式庫專案的原生程式庫，二進位文件庫加入至專案，並設定其**建置動作**至**EmbeddedNativeLibrary**。

請注意，由於 Android 支援多個應用程式二進位介面 (ABIs)，Xamarin.Android 必須知道哪一個建置原生程式庫的 ABI。
方法有二：

1.  路徑 「 探測 」
1.  使用`AndroidNativeLibrary/Abi`專案檔內的項目


路徑探查會使用原生程式庫的父目錄名稱來指定程式庫的目標 ABI。 因此，如果您將加入`lib/armeabi/libfoo.so`至專案，然後 ABI 會 「 探查 」 做為`armeabi`。

或者，您可以編輯專案檔，才能明確地指定要使用的 ABI:

```xml
<ItemGroup>
    <AndroidNativeLibrary Include="path/to/libfoo.so">
        <Abi>armeabi</Abi>
    </AndroidNativeLibrary>
</ItemGroup>
```

如需有關如何使用原生程式庫的詳細資訊，請參閱[+ 原生程式庫與](http://www.mono-project.com/docs/advanced/pinvoke/)。

## <a name="debugging-native-code-with-visual-studio-2015"></a>使用 Visual Studio 2015 偵錯原生程式碼

如果您使用*Visual Studio 2015*，您不需要修改您的專案檔案 （如上面所述）。
您可以建置和偵錯 c + + 內 Xamarin.Android 方案時，只需將專案參考加入 c + +**動態共用程式庫 (Android)**專案。

Visual Studio c + + 開發人員可查看[SanAngeles_NativeDebug](https://developer.xamarin.com/samples/monodroid/SanAngeles_NDK/)範例來嘗試偵錯 c + +，Visual Studio 2015 和 Xamarin; 與參照我們[部落格文章](https://blog.xamarin.com/build-and-debug-c-libraries-in-xamarin-android-apps-with-visual-studio-2015/)如需詳細資訊。



## <a name="related-links"></a>相關連結

- [SanAngeles_NativeDebug （範例）](https://developer.xamarin.com/samples/monodroid/SanAngeles_NDK/)
