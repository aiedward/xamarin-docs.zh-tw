---
title: 安卓佈局診斷分析儀
description: 本指南列出了 Xamarin.Android 上當前支援的所有 Android 佈局診斷分析儀。
ms.prod: xamarin
ms.assetid: BD252EA7-7E69-4DB4-96AB-D52CC0510C8F
ms.technology: xamarin-android
author: decriptor
ms.author: stepsha
ms.date: 04/07/2020
ms.openlocfilehash: 6203ce444bb97fa453a912a724f7f5724558b32a
ms.sourcegitcommit: 765b69ed451a0f48625ea597c3f39de95f3ae693
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/09/2020
ms.locfileid: "80987603"
---
# <a name="android-designer-diagnostic-analyzers"></a>Android 設計器診斷分析儀

本指南列出了當前支援的所有 Android 佈局診斷分析器。

## <a name="accessibility"></a>Accessibility

以下分析器有助於改進輔助功能支援:

| ID | Title | Severity | 描述 |
|----|-------|----------|-------------|
| ContentDescription | 沒有影像`contentDescription` | 警告 | 影像`contentDescription`上缺少屬性 |

## <a name="correctness"></a>正確性

以下分析器有助於修復佈局中的正確性問題:

| ID | Title | Severity | 描述 | 説明 |
|----|-------|----------|-------------|------|
| 配接器檢視子級 | 配接器檢視與子檢視 | 警告 | 配接器檢視不能在 XML 中具有子級 | [連結](xref:Android.Widget.AdapterView) |
| 缺少代碼 | 片段應指定或`id``tag` | 警告 |此`<fragment>`標記應`id`指定`tag`或 ,以在作用重新啟動時保留狀態 | [連結](xref:Android.App.Fragment) |
| 巢狀垂直 | 巢狀垂直捲動元素 | 警告 | 巢狀捲元件 |
| 巢狀捲動 | 巢狀層捲動元素 | 警告 | 巢狀捲元件 |
| 捲動檢視大小 | 捲動檢視fill_parent/match_parent大小錯誤的子級 | 警告 | 捲動檢視fill_parent/match_parent大小錯誤的子級 |
| 捲動檢視計數 | 捲動檢視只能有一個子級 | 警告 | 捲動檢視只能有一個子級 |
| 缺少 Android 命名空間 | 有屬性上的 Android 命名空間 | 錯誤 | 缺少 Android XML 命名空間;您的屬性將被解釋為自訂屬性 |
| 重複的識別碼 | 重複的代用品 | 錯誤 | 單一佈局的重複代碼 |
| 包含佈局參數的長寬度和高度 | 缺少寬度和高度 | 錯誤 | 在包括時忽略的佈局參數 | [連結](https://stackoverflow.com/questions/2631614/does-android-xml-layouts-include-tag-really-work) |
| 包含佈局參數的長寬度 | 缺少寬度 | 錯誤 | 在包括時忽略的佈局參數 | [連結](https://stackoverflow.com/questions/2631614/does-android-xml-layouts-include-tag-really-work) |
| 包含佈局參數的遺失高度 | 缺少高度 | 錯誤 | 在包括時忽略的佈局參數 | [連結](https://stackoverflow.com/questions/2631614/does-android-xml-layouts-include-tag-really-work) |
| 方向 | 遺失法方向 | 錯誤 | 遺失法方向 |
| 可疑0dp | 可疑 0dp 尺寸 | 錯誤 | 可疑 0dp 尺寸 |
| 所需的 Size 寬度 | 缺少寬度屬性 | 錯誤 | 缺少屬性:layout_width |
| 所需的 Size Height | 遺失高度屬性 | 錯誤 | 缺少屬性:layout_height |
| 網頁檢視佈局 | 家長wrap_content Web 檢視 | 錯誤 |
| 錯誤案例 | 檢視標記的案例錯誤 | 錯誤 | 檢視標記的案例錯誤 | [連結](xref:Android.App.Fragment) |

## <a name="design"></a>設計

以下分析器有助於改進加入佈局檔案的方式:

| ID | Title | Severity | 描述 |
|----|-------|----------|-------------|
| 硬編碼顏色 | 硬編碼顏色 | 資訊 | 硬編碼顏色通常會導致不一致 |
| 硬編碼大小  | 硬編碼大小  | 資訊 | 硬編碼大小通常會導致不一致  |
| 硬編碼文字  | 硬編碼文字  | 警告 | 硬編碼文字 |
| 未解決的資源 | 未解析的資源網址 | 警告 | 無法剖析此資源網址 |
| Xml 錯誤 | XML 語法錯誤 | 錯誤 | XML 語法錯誤 |

## <a name="performance"></a>效能

以下分析器有助於提高佈局的效能:

| ID | Title | Severity | 描述 |
|----|-------|----------|-------------|
| 巢狀 | 巢狀佈局權重 | 警告 | 巢狀性功能重對性能不利 |
| 太多檢視 | 佈局的檢視太多 | 警告 | 佈局的檢視太多 |
| 太深的佈局 | 佈局層次結構太深 | 警告 | 佈局層次結構太深 |
| 無用的父母 | 沒有用的父佈局 | 警告 | 沒有用的父佈局 |
| 無用的葉子 | 無用的葉子佈局 | 警告 | 這種`%1$s`觀點是無用的(沒有孩子,`background`不`id`,不`style`,不 ) |

## <a name="usability"></a>可用性

以下分析儀有助於提高客戶的佈局可用性:

| ID | Title | Severity | 描述 |
|----|-------|----------|-------------|
| 負利潤 | 負邊距 | 警告 | 負邊距 |
| 遺失輸入類型 | 編輯文字,沒有輸入類型 | 警告 | 沒有指定輸入類型 |
| 輸入型態電話 | 編輯文字似乎是一個電話號碼 | 警告 | 視圖名稱表明這是一個電話號碼,但它不包括`phone`在`inputType` |
| 輸入類型代碼 | 編輯文字似乎是一個數字 | 警告 | 檢視名稱表示這是一個數位,但它不包括數位`inputType`(如`numberDecimal`) |
| 輸入類型密碼 | 編輯文字似乎是密碼 | 警告 | 檢視名稱表示這是密碼,但它不包括`password`在`inputType`(`textVisiblePassword`如 ) |
| 輸入類型 | 編輯文字似乎是 PIN | 警告 | 檢視名稱顯示這是密碼 (PIN),但它不包括`numberPassword`在`inputType` |
| 輸入類型電子郵件 | 編輯文字似乎是一封電子郵件 | 警告 | 檢視名稱表示這是電子郵件地址,但它不包括`email`在`inputType`(如`textEmailAddress`) |
| 輸入類型 | 編輯文字似乎是一個 URI | 警告 | 檢視名稱表明這是一個 URI,但它不包括`textUri`在`inputType` |
| 輸入類型日期 | 編輯文字似乎是日期 | 警告 | 檢視名稱表示這是一個日期,但它不包括`date`在`inputType`(`datetime`如 ) |
