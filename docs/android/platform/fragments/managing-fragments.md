---
title: 管理片段
ms.prod: xamarin
ms.assetid: 02C5E8F0-32EF-4FD9-DC8B-04650E20722C
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/07/2018
ms.openlocfilehash: 3733ed37abe9604e77529db4864f601d2b473280
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2020
ms.locfileid: "73027324"
---
# <a name="managing-fragments"></a>管理片段

為了協助管理片段，Android 提供了 `FragmentManager` 類別。 每個活動都有 `Android.App.FragmentManager` 的實例，會尋找或動態變更其片段。 這些變更的每一組稱為「交易」（ *transaction*），其執行方式是使用類別所包含的其中一個 api `Android.App.FragmentTransation`（由 `FragmentManager`所管理）。 活動可能會啟動如下所示的交易：

```csharp
FragmentTransaction fragmentTx = this.FragmentManager.BeginTransaction();
```

這些片段的變更會使用 `Add()`之類的方法在 `FragmentTransaction` 實例中執行，`Remove(),` 和 `Replace().` 接著會使用 `Commit()`來套用變更。 交易中的變更不會立即執行。
相反地，它們會排程在活動的 UI 執行緒上儘快執行。

下列範例顯示如何將片段新增至現有的容器：

```csharp
// Create a new fragment and a transaction.
FragmentTransaction fragmentTx = this.FragmentManager.BeginTransaction();
DetailsFragment aDifferentDetailsFrag = new DetailsFragment();

// The fragment will have the ID of Resource.Id.fragment_container.
fragmentTx.Add(Resource.Id.fragment_container, aDifferentDetailsFrag);

// Commit the transaction.
fragmentTx.Commit();
```

如果在呼叫 `Activity.OnSaveInstanceState()` 之後認可交易，則會擲回例外狀況。 發生這種情況的原因是，當活動儲存其狀態時，Android 也會儲存任何託管片段的狀態。 如果在此時間點之後認可任何片段交易，則在還原活動時，這些交易的狀態將會遺失。

藉由呼叫 `FragmentTransaction.AddToBackStack()`，可以將片段交易儲存至活動的[後端堆疊](https://developer.android.com/guide/topics/fundamentals/tasks-and-back-stack.html)。 這可讓使用者在按下 [**上一頁**] 按鈕時，透過片段變更來回溯導覽。 如果沒有呼叫這個方法，移除的片段將會被終結，而且如果使用者導覽回到活動中，將無法使用。

下列範例顯示如何使用 `FragmentTransaction` 的 `AddToBackStack` 方法來取代一個片段，同時保留後端堆疊上第一個片段的狀態：

```csharp
// Create a new fragment and a transaction.
FragmentTransaction fragmentTx = this.FragmentManager.BeginTransaction();
DetailsFragment aDifferentDetailsFrag = new DetailsFragment();

// Replace the fragment that is in the View fragment_container (if applicable).
fragmentTx.Replace(Resource.Id.fragment_container, aDifferentDetailsFrag);

// Add the transaction to the back stack.
fragmentTx.AddToBackStack(null);

// Commit the transaction.
fragmentTx.Commit();
```

## <a name="communicating-with-fragments"></a>與片段通訊

*FragmentManager*知道附加至活動的所有片段，並提供兩種方法來協助尋找這些片段：

- **FindFragmentById** &ndash; 此方法會使用配置檔案中所指定的識別碼，或當片段加入為交易一部分時的容器識別碼，來尋找片段。

- **FindFragmentByTag** &ndash; 此方法可用來尋找具有配置檔案中所提供之標記或在交易中加入的片段。

片段和活動都會參考 `FragmentManager`，因此會使用相同的技巧，在兩者之間來回通訊。 應用程式可以使用這兩種方法的其中一個來尋找參考片段，將該參考轉換為適當的類型，然後直接在片段上呼叫方法。 下列程式碼片段提供範例：

活動也可能會使用 `FragmentManager` 來尋找片段：

```csharp
var emailList = FragmentManager.FindFragmentById<EmailListFragment>(Resource.Id.email_list_fragment);
emailList.SomeCustomMethod(parameter1, parameter2);
```

### <a name="communicating-with-the-activity"></a>與活動通訊

片段可能會使用 `Fragment.Activity` 屬性來參考其主機。 藉由將活動轉換成更特定的類型，活動可以呼叫其主機上的方法和屬性，如下列範例所示：

```csharp
var myActivity = (MyActivity) this.Activity;
myActivity.SomeCustomMethod();
```
