---
title: Xamarin.iOS 的 Info.plist 參考
description: 本文件描述各種可在 Xamarin.iOS 應用程式之 Info.plist 檔案中設定的索引鍵/值組。 如果您的應用程式執行特定工作，例如存取位置、相片、麥克風或相機，這些索引鍵即為必要。
ms.prod: xamarin
ms.assetid: 944DFDB5-ADBA-4D6E-984C-5AEC19A1CC57
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 01/18/2017
ms.openlocfilehash: d94a647583539ac6af603a9074e1966c1c41d587
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "73028454"
---
# <a name="infoplist-reference-for-xamarinios"></a>Xamarin.iOS 的 Info.plist 參考

如需有關使用 Info.Plist 機碼的詳細資訊，請參閱[使用安全性與隱私權](~/ios/app-fundamentals/security-privacy.md)指南。 

## <a name="location"></a>Location 

存取使用者的位置也需要修改 Info.plist。 以下是應該設定的位置資料相關機碼： 

- **NSLocationWhenInUseUsageDescription** - 用於當您在使用者與應用程式互動期間存取使用者位置時。 
- **NSLocationAlwaysUsageDescription** - 用於您的應用程式在背景中存取使用者的位置時。

## <a name="photos"></a>相片 

NSPhotoLibraryUsageDescription  

## <a name="contacts"></a>連絡人 

NSContactsUsageDescription 

## <a name="calendar-data"></a>行事曆資料 
    
NSCalendarsUsageDescription 

## <a name="reminder-data"></a>提醒資料 
    
NSRemindersUsageDescription 

## <a name="bluetooth-peripherals"></a>藍牙周邊裝置 
    
NSBluetoothPeripheralUsageDescription 

## <a name="microphone"></a>麥克風 

NSMicrophoneUsageDescription 

## <a name="camera"></a>相機 
    
NSCameraUsageDescription 

## <a name="reading-healthkit"></a>讀取 HealthKit  

NSHealthShareUsageDescription 

NSHealthUpdateUsageDescription 

## <a name="background-modes"></a>背景模式 
    
UIBackgroundModes 

## <a name="homekit"></a>HomeKit 

NSHomeKitUsageDescription 

## <a name="related-links"></a>相關連結

- [Apple 參考指南。](https://developer.apple.com/library/content/documentation/General/Reference/InfoPlistKeyReference/Articles/iPhoneOSKeys.html#//apple_ref/doc/uid/TP40009252-SW10)
