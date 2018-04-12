---
title: 驗證使用者與 Amazon SimpleDB 服務
description: Amazon SimpleDB 並未提供它自己的資源為基礎的權限系統。 相反地，身分識別提供者的驗證可以用來確保使用者只能 SimpleDB 網域中擁有自己的資料存取。 本文說明如何限制使用者存取他們自己的 SimpleDB 資料。
ms.prod: xamarin
ms.assetid: 797C91A5-9720-4DAC-89D8-5C85996584C8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/20/2016
ms.openlocfilehash: 592e957e0c64e7189d6f01f1ba0f23da074c4bec
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="authenticating-users-with-an-amazon-simpledb-service"></a>驗證使用者與 Amazon SimpleDB 服務

_Amazon SimpleDB 並未提供它自己的資源為基礎的權限系統。相反地，身分識別提供者的驗證可以用來確保使用者只能 SimpleDB 網域中擁有自己的資料存取。本文說明如何限制使用者存取他們自己的 SimpleDB 資料。_

[Xamarin.Auth](https://github.com/xamarin/Xamarin.Auth)範例應用程式中用來管理使用者驗證程序，並安全地儲存在裝置上的 使用者帳戶詳細資料。 如需詳細資訊，請參閱[驗證使用者身分識別提供者](~/xamarin-forms/data-cloud/authentication/oauth.md)。

## <a name="allowing-an-authenticated-user-access-to-simpledb-domain-data"></a>允許已驗證的使用者存取 SimpleDB 網域資料

範例應用程式使用`TodoItem`模型資料的類別。 若要儲存`TodoItem`必須先將它轉換成 SimpleDB 服務中的執行個體`List`的`ReplaceableAttribute`物件。 如需詳細資訊，請參閱[建立 SimpleDB 物件](~/xamarin-forms/data-cloud/consuming/aws.md)。

> [!NOTE]
> 在 iOS 9 和更新版本中，應用程式傳輸安全性 (ATS) 會強制執行安全連線 （例如應用程式的後端伺服器） 的網際網路資源與應用程式，因此可以防止意外洩漏機密資訊。 由於 AT 預設會在 ios 9 建置的應用程式啟用時，所有連線將都會受限於 AT 安全性需求。 如果連線不符合這些需求，它們將會失敗並發生例外狀況。
> 如果無法使用，可以超出選擇 AT`HTTPS`通訊協定和安全的網際網路資源的通訊。 這可以藉由更新應用程式的**Info.plist**檔案。 如需詳細資訊，請參閱[應用程式的傳輸安全性](~/ios/app-fundamentals/ats.md)。

若要確保使用者在 SimpleDB 網域中擁有自己的資料存取`ToSimpleDBReplaceableAttributes`方法儲存的其他屬性`TodoItem`執行個體，如下列程式碼範例所示：

```csharp
List<ReplaceableAttribute> ToSimpleDBReplaceableAttributes (TodoItem item)
{
  return new List<ReplaceableAttribute> () {
    ...
    new ReplaceableAttribute () {
      Name = "User",
      Value = App.User.Email,
      Replace = true
    },
  };
}
```

此屬性可確保每個項目儲存在 SimpleDB 定義域中具有使用者用來唯一識別資料所屬的使用者相關聯的電子郵件地址。 當網域中的內容藉由呼叫擷取`AmazonSimpleDBClient.SelectAsync`方法，查詢運算式可確保只有已驗證之使用者的項目要擷取的如下列程式碼範例所示：

```csharp
public async Task<List<TodoItem>> RefreshDataAsync ()
{
  ...
  var request = new SelectRequest () {
    SelectExpression = string.Format ("SELECT * from {0} WHERE User = '{1}'", tableName, App.User.Email)
  };
  var response = await client.SelectAsync (request);
  ...
}
```

`SelectAsync`方法會傳回包含集合的項目和相關聯的屬性符合之查詢運算式的回應。 查詢運算式可確保將會擷取符合使用者的電子郵件地址的項目。 如需查詢運算式的詳細資訊，請參閱[建立 Amazon SimpleDB 查詢使用 選取](http://docs.aws.amazon.com/AmazonSimpleDB/latest/DeveloperGuide/UsingSelect.html)Amazon 網站上。

> [!NOTE]
> 請小心建構查詢運算式時，請依照下列引號的規則。 如需詳細資訊，請參閱[選取用引號括住規則](http://docs.aws.amazon.com/AmazonSimpleDB/latest/DeveloperGuide/QuotingRulesSelect.html)Amazon 網站上。

## <a name="summary"></a>總結

這篇文章解釋如何限制使用者存取他們自己的 SimpleDB 資料。 Amazon SimpleDB 並未提供它自己的資源為基礎的權限系統。 相反地，身分識別提供者的驗證可以用來確保使用者 SimpleDB 網域中擁有自己的資料存取。


## <a name="related-links"></a>相關連結

- [TodoAWSAuth （範例）](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoAWSAuth/)
- [使用 Amazon SimpleDB 服務](~/xamarin-forms/data-cloud/consuming/aws.md)
- [驗證使用者身分識別提供者](~/xamarin-forms/data-cloud/authentication/oauth.md)
- [Amazon Cognito 身分識別](http://docs.aws.amazon.com/cognito/devguide/identity/)
- [Amazon SimpleDB 開發人員文件](http://docs.aws.amazon.com/AmazonSimpleDB/latest/DeveloperGuide/Welcome.html)
- [Amazon Web Services SDK for .NET](https://www.nuget.org/packages?q=Tags%3A%22aws-sdk-v3%22)
