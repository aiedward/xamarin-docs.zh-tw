---
title: 使用原生程式庫
ms.prod: xamarin
ms.assetid: 7AA6CEC8-C09E-BBDA-FDD6-E40559143548
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/09/2018
ms.openlocfilehash: d3e5b36f2cbc48dac09b55bfba8c3613db12bbc8
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2019
ms.locfileid: "68643275"
---
# <a name="using-native-libraries"></a>使用原生程式庫

Xamarin 支援透過標準 PInvoke 機制來使用原生程式庫。 您也可以將不屬於作業系統一部分的其他原生程式庫, 組合到您的 apk 中。

若要部署包含 Xamarin 應用程式的原生程式庫, 請將程式庫二進位檔新增至專案, 並將其**組建動作**設定為**AndroidNativeLibrary**。

若要使用 Xamarin 程式庫專案部署原生程式庫, 請將程式庫二進位檔新增至專案, 並將其**組建動作**設定為**EmbeddedNativeLibrary**。

請注意, 因為 Android 支援多個應用程式二進位介面 (Abi), 所以 Xamarin。 Android 必須知道原生程式庫所建立的 ABI。
方法有二：

1.  路徑「探查」
1.  藉由使用`AndroidNativeLibrary/Abi`專案檔中的元素


路徑探查會使用原生程式庫的父目錄名稱來指定程式庫的目標 ABI。 因此, 如果您將`lib/armeabi/libfoo.so`新增至專案, 則 ABI 會是 "探查", 做`armeabi`為。

或者, 您也可以編輯您的專案檔, 以明確指定要使用的 ABI:

```xml
<ItemGroup>
    <AndroidNativeLibrary Include="path/to/libfoo.so">
        <Abi>armeabi</Abi>
    </AndroidNativeLibrary>
</ItemGroup>
```

如需使用原生程式庫的詳細資訊, 請參閱搭配[原生程式庫的 Interop](https://www.mono-project.com/docs/advanced/pinvoke/)。

## <a name="debugging-native-code-with-visual-studio"></a>使用 Visual Studio 來調試機器碼

如果您使用的是*Visual Studio 2019*或*Visual Studio 2017*, 則不需要修改您的專案檔, 如上所述。
您可以藉由將C++專案參考新增至C++ **動態共用程式庫 (Android)** 專案, 在您的 Xamarin Android 方案內建立和偵錯工具。

若要在C++您的專案中對機器碼進行偵錯工具, 請遵循下列步驟:

1. 按兩下 [專案**屬性**], 然後選取 [ **Android 選項**] 頁面。
2. 向下流覽至 [**調試選項**]。
3. 在 [**偵錯工具**] 下拉式功能表中**C++** , 選取 (而不是預設的 **.net (Xamarin)** )。

Visual Studio C++開發人員可以查看[SanAngeles_NativeDebug](https://docs.microsoft.com/samples/xamarin/monodroid-samples/sanangeles-ndk)範例, 以嘗試C++使用 Xamarin 從 Visual Studio 2019 或 Visual Studio 2017 進行調試;如需詳細資訊, 請參閱我們的[blog 文章](https://blog.xamarin.com/build-and-debug-c-libraries-in-xamarin-android-apps-with-visual-studio-2015/)。



## <a name="related-links"></a>相關連結

- [SanAngeles_NativeDebug (範例)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/sanangeles-ndk)
- [開發 Xamarin Android 原生應用程式](https://blogs.msdn.microsoft.com/vcblog/2015/02/23/developing-xamarin-android-native-applications/)
