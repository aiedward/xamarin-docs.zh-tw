---
title: 管理片段
ms.prod: xamarin
ms.assetid: 02C5E8F0-32EF-4FD9-DC8B-04650E20722C
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/07/2018
ms.openlocfilehash: 3e0430b8ed9c42030441021e71c3b08b1ddccc57
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61022194"
---
# <a name="managing-fragments"></a>管理片段

為了協助管理片段，提供 Android`FragmentManager`類別。 每個活動都有的執行個體`Android.App.FragmentManager`，會尋找，或以動態方式變更其片段。 每一組這些變更就所謂*交易*，並使用其中一個類別中包含的 Api 來執行`Android.App.FragmentTransation`，由管理`FragmentManager`。 活動可能會啟動交易，就像這樣：

```csharp
FragmentTransaction fragmentTx = this.FragmentManager.BeginTransaction();
```

片段的這些變更將會在`FragmentTransaction`藉由使用下列方法的執行個體`Add()`，`Remove(),`並`Replace().`所做的變更會套用使用`Commit()`。 在交易中的變更不會立即執行。
相反地，它們被排程儘速在活動的 UI 執行緒上執行。

下列範例示範如何將片段新增至現有的容器：

```csharp
// Create a new fragment and a transaction.
FragmentTransaction fragmentTx = this.FragmentManager.BeginTransaction();
DetailsFragment aDifferentDetailsFrag = new DetailsFragment();

// The fragment will have the ID of Resource.Id.fragment_container.
fragmentTx.Add(Resource.Id.fragment_container, aDifferentDetailsFrag);

// Commit the transaction.
fragmentTx.Commit();
```

如果交易被認可之後`Activity.OnSaveInstanceState()`是呼叫，將會擲回例外狀況。 這是因為活動會儲存其狀態，當 Android 也會儲存任何裝載的片段的狀態。 如果任何片段的交易認可之後，還原活動時，將會遺失這些交易的狀態。

可儲存活動的片段交易[上一頁堆疊](https://developer.android.com/guide/topics/fundamentals/tasks-and-back-stack.html)藉由呼叫`FragmentTransaction.AddToBackStack()`。 這可讓使用者透過向後巡覽片段時變更**回**按下按鈕時。 呼叫這個方法時，會移除的片段會終結而如果使用者巡覽回到活動將無法。

下列範例示範如何使用`AddToBackStack`方法的`FragmentTransaction`來取代一個片段中，同時保留在上一頁堆疊上的第一個片段的狀態：

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


## <a name="communicating-with-fragments"></a>片段與通訊

*FragmentManager*知道所有附加至活動的片段，並提供兩種方法可以協助您尋找這些片段：

-   **FindFragmentById** &ndash;這個方法會尋找片段片段已新增為交易的一部分時，使用版面配置檔案中指定的識別碼或容器的識別碼。

-   **FindFragmentByTag** &ndash;這個方法用來尋找有的標記的配置檔案中所提供，或已加入在交易中的片段。

片段和活動的參考`FragmentManager`，因此相同的技巧來彼此之間來回通訊。 應用程式可能使用這兩種方法之一來尋找片段的參考、 轉型為適當的型別，該參考，然後直接片段上呼叫方法。 下列程式碼片段提供一個範例：

此外，也可以使用活動`FragmentManager`尋找片段：

```csharp
var emailList = FragmentManager.FindFragmentById<EmailListFragment>(Resource.Id.email_list_fragment);
emailList.SomeCustomMethod(parameter1, parameter2);
```


### <a name="communicating-with-the-activity"></a>與活動通訊

若要使用的片段可能會`Fragment.Activity`屬性來參考它的主機。 轉型為更特定類型的活動，就可以為活動中呼叫方法與屬性，其在主機上，如下列範例所示：

```csharp
var myActivity = (MyActivity) this.Activity;
myActivity.SomeCustomMethod();
```
