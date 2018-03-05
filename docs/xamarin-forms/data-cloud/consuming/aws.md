---
title: "使用 Amazon SimpleDB 服務"
description: "Amazon SimpleDB 是 web 服務，提供儲存和查詢 Amazon 雲端中的資料的能力。 本文說明如何使用查詢、 建立、 取代和刪除儲存在 SimpleDB 服務中的資料在 AWS SDK for.NET。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 823819AA-15F9-4144-B355-78A10AD37513
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/20/2016
ms.openlocfilehash: 590e39deb7972df9e45064bb1a96e533a1fc9856
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="consuming-an-amazon-simpledb-service"></a>使用 Amazon SimpleDB 服務

_Amazon SimpleDB 是 web 服務，提供儲存和查詢 Amazon 雲端中的資料的能力。本文說明如何使用查詢、 建立、 取代和刪除儲存在 SimpleDB 服務中的資料在 AWS SDK for.NET。_

SimpleDB 服務是使用熟悉的 REST 服務取用者要求和回應模式。 作業會叫用 SimpleDB 服務傳送要求，其中可能包含資料。 在處理要求之後, SimpleDB 服務會傳回含有任何結果的回應。 SimpleDB 服務必須以程式設計方式建立，而且無法透過建立[AWS 主控台](https://aws.amazon.com)。 不過，則需要建立及存取任何 Amazon web 服務 AWS 帳戶。

在 SimpleDB 服務中，資料會組織成網域、 在其中可放置資料和對資料執行查詢。 網域是由所描述的屬性名稱 / 值組的項目所組成。 網域可以想像視為資料表，類似以資料行和資料列類似的項目類似的屬性。 如需 SimpleDB 資料模型的詳細資訊，請參閱[資料模型](http://docs.aws.amazon.com/AmazonSimpleDB/latest/DeveloperGuide/DataModel.html)Amazon 網站上。

設定所需的 Amazon 服務指示可以找到範例應用程式隨附的讀我檔案。 執行範例應用程式時，它會連線到 Amazon Cognito 身分識別集區，以授權存取 SimpleDB 服務，如下列螢幕擷取畫面所示：

![](aws-images/portal.png "範例應用程式")

> [!NOTE]
> 在 iOS 9 和更新版本中，應用程式傳輸安全性 (ATS) 會強制執行安全連線 （例如應用程式的後端伺服器） 的網際網路資源與應用程式，因此可以防止意外洩漏機密資訊。 由於 AT 預設會在 ios 9 建置的應用程式啟用時，所有連線將都會受限於 AT 安全性需求。 如果連線不符合這些需求，它們將會失敗並發生例外狀況。
> 如果無法使用，可以超出選擇 AT`HTTPS`通訊協定和安全的網際網路資源的通訊。 這可以藉由更新應用程式的**Info.plist**檔案。 如需詳細資訊，請參閱[應用程式的傳輸安全性](~/ios/app-fundamentals/ats.md)。

## <a name="consuming-a-simpledb-service"></a>使用 SimpleDB 服務

Amazon Cognito 身分識別可讓 AWS 服務，例如 SimpleDB，從應用程式不含硬式編碼 AWS 認證將應用程式叫用。 唯一識別集區中建立的相反地， [Amazon Cognito 主控台](https://console.aws.amazon.com/cognito/home)。 身分識別集區包含用來指定資源，例如 SimpleDB 身分識別可以存取的角色的身分識別。

[AWS SDK for.NET](https://www.nuget.org/packages?q=Tags%3A%22aws-sdk-v3%22)提供`CognitoAWSCredentials`和`AmazonSimpleDBClient`類別，用於 Xamarin.Forms 應用程式所存取 SimpleDB 服務，如下列程式碼範例所示：

```csharp
AmazonSimpleDBClient client;
...

public SimpleDBStorage ()
{
  var credentials = new CognitoAWSCredentials (
                      Constants.CognitoIdentityPoolId,
                      RegionEndpoint.USEast1);
  var config = new AmazonSimpleDBConfig ();
  config.RegionEndpoint = RegionEndpoint.USWest2;
  client = new AmazonSimpleDBClient (credentials, config);
  ...
}
```

新執行個體`CognitoAWSCredentials`類別由提供唯一識別集區識別碼和 Cognito 識別帳戶的區域。 在撰寫本文時，Cognito 身分識別功能僅適用於為 USEast1 和 EUWest1 地區。 不過，它可以與外部區域的 Amazon 服務通訊。

當`AmazonSimpleDBClient`建立執行個體時，`CognitoAWSCredentials`執行個體必須提供，以及與`AmazonSimpleDBConfig`指定 SimpleDB 服務所在的地理區域的執行個體。 `CognitoAWSCredentials`執行個體可確保存取 SimpleDB 服務是一個識別集區所建立中，同時避免需要將應用程式中內嵌 AWS 存取金鑰和秘密金鑰 AWS 帳戶相關聯。

藉由呼叫建立 SimpleDB 服務網域`AmazonSimpleDBClient.CreateDomainAsync`方法，如下列程式碼範例所示：

```csharp
string tableName = "Todo";
...

async Task CreateDomain ()
{
  ...
  await client.CreateDomainAsync (new CreateDomainRequest { DomainName = tableName });
  ...
}
```

`CreateDomainAsync`方法需要`CreateDomainRequest`做為參數的執行個體。 `CreateDomainRequest`執行個體初始化`DomainName`內容用來識別該定義域的值。 若要建立網域，此值必須是在 AWS 帳戶相關聯的網域之間唯一的。 否則將不會建立網域，而且沒有錯誤回應會傳送至表示。 對網域名稱的任何作業則會發生對現有的網域，而不是新建立的網域。

### <a name="creating-simpledb-objects"></a>建立 SimpleDB 物件

範例應用程式使用`TodoItem`模型資料的類別。 若要儲存`TodoItem`必須先將它轉換成 SimpleDB 服務中的執行個體`List`的`ReplaceableAttribute`物件。 這會透過`ToSimpleDBReplaceableAttributes`方法，如下列程式碼範例所示：

```csharp
List<ReplaceableAttribute> ToSimpleDBReplaceableAttributes (TodoItem item)
{
  return new List<ReplaceableAttribute> () {
    new ReplaceableAttribute () {
      Name = "Name",
      Value = item.Name,
      Replace = true
    },
    new ReplaceableAttribute () {
      Name = "Notes",
      Value = item.Notes,
      Replace = true
    },
    new ReplaceableAttribute () {
      Name = "Done",
      Value = item.Done.ToString (),
      Replace = true
    }
  };
}
```

這個方法會建立`List`的新`ReplaceableAttribute`執行個體，與`List`代表單一`TodoItem`執行個體。 每個`ReplaceableAttribute`的執行個體表示的單一屬性從`TodoItem`執行個體。 如需有關`ReplaceableAttribute`類別，請參閱[ReplaceableAttribute 類別](http://docs.aws.amazon.com/sdkfornet1/latest/apidocs/html/T_Amazon_SimpleDB_Model_ReplaceableAttribute.htm)Amazon 網站上。

同樣地，從 SimpleDB 服務擷取資料時，必須將它從轉換`List`的`Attribute`執行個體來`TodoItem`執行個體。 這是與`FromSimpleDBAttributes`方法，如下列程式碼範例所示：

```csharp
TodoItem FromSimpleDBAttributes (List<Amazon.SimpleDB.Model.Attribute> attributeList, string id)
{
  var todoItem = new TodoItem ();
  todoItem.ID = id;
  todoItem.Name = attributeList.Where (attr => attr.Name == "Name").FirstOrDefault ().Value;
  todoItem.Notes = attributeList.Where (attr => attr.Name == "Notes").FirstOrDefault ().Value;
  todoItem.Done = Convert.ToBoolean (attributeList.Where (attr => attr.Name == "Done").FirstOrDefault ().Value);
  return todoItem;
}
```

這個方法只會擷取每個`Attribute`的執行個體`List`並將它設定在新建立`TodoItem`執行個體。

如需有關`Attribute`類別，請參閱[屬性類別](http://docs.aws.amazon.com/sdkfornet1/latest/apidocs/html/T_Amazon_SimpleDB_Model_Attribute.htm)Amazon 網站上。

### <a name="querying-data"></a>查詢資料

網域的內容可以藉由呼叫擷取`AmazonSimpleDBClient.SelectAsync`方法，如下列程式碼範例所示：

```csharp
public async Task<List<TodoItem>> RefreshDataAsync ()
{
  ...
  var request = new SelectRequest () {
      SelectExpression = string.Format ("SELECT * from {0}", tableName)
  };
  var response = await client.SelectAsync (request);
  foreach (var item in response.Items) {
    Items.Add (FromSimpleDBAttributes (item.Attributes, item.Name));
  }
  ...
}
```

`SelectAsync`方法會接受`SelectRequest`做為參數，指定執行個體`Select`查詢運算式中的`SelectExpression`屬性。 查詢運算式的格式會類似於標準 SQL 格式`SELECT`陳述式。 如需查詢運算式的詳細資訊，請參閱[建立 Amazon SimpleDB 查詢使用 選取](http://docs.aws.amazon.com/AmazonSimpleDB/latest/DeveloperGuide/UsingSelect.html)Amazon 網站上。

> [!NOTE]
> **請注意**： 請小心建構查詢運算式時，請依照下列引號的規則。 如需詳細資訊，請參閱[選取用引號括住規則](http://docs.aws.amazon.com/AmazonSimpleDB/latest/DeveloperGuide/QuotingRulesSelect.html)Amazon 網站上。

`SelectAsync`方法會傳回包含集合的項目和相關聯的屬性符合之查詢運算式的回應。 這個集合接著會轉換為`List`的`TodoItem`顯示執行個體。

### <a name="creating-and-replacing-data"></a>建立和取代資料

`AmazonSimpleDBClient.PutAttributesAsync`方法用來建立，並取代在 SimpleDB 服務網域中的資料，如下列程式碼範例所示：

```csharp
public async Task SaveTodoItemAsync (TodoItem todoItem)
{
  ...
  var attributeList = ToSimpleDBReplaceableAttributes (todoItem);
  var request = new PutAttributesRequest () {
      DomainName = tableName,
      ItemName = todoItem.ID,
      Attributes = attributeList
  };
  await client.PutAttributesAsync (request);
  ...
}
```

`PutAttributesAsync`方法會接受`PutAttributesRequest`做為參數的執行個體。 `PutAttributesRequest`執行個體會指定要建立新的項目，或取代現有的項目中的屬性名稱 / 值組。 `List`的`ReplaceableAttribute`建置執行個體`ToSimpleDBReplaceableAttributes`方法。 這個方法也會設定`Replace`每個屬性`ReplaceableAttribute`至`true`。 這會導致新的屬性值，以取代現有的屬性值，如果資料已被取代。 不過，嘗試取代不存在的屬性值不會導致錯誤回應。

值`PutAttributesRequest.ItemName`屬性控制是否將新的項目加入至網域，或是否會取代現有的項目。 當應用程式建立新的項目時，它會設定`TodoItem.ID`屬性至新`Guid`。 這可確保每個`TodoItem`執行個體都有唯一識別碼。 因此，如果`PutAttributesRequest.ItemName`屬性設為不存在的值在網域中，SimpleDB 服務將會建立新的項目包含指定的屬性名稱 / 值組。 如果`PutAttributesRequest.ItemName`屬性設為值已經存在，網域中，SimpleDB 服務將會更新與指定的屬性名稱 / 值組的項目。

### <a name="deleting-data"></a>刪除資料

`AmazonSimpleDBClient.DeleteAttributesAsync`方法用來從 SimpleDB 服務網域中，刪除資料，如下列程式碼範例所示：

```csharp
public async Task DeleteTodoItemAsync (TodoItem todoItem)
{
  ...
  var attributeList = ToSimpleDBAttributes (todoItem);
  var request = new DeleteAttributesRequest () {
      DomainName = tableName,
      ItemName = todoItem.ID,
      Attributes = attributeList
  };
  await client.DeleteAttributesAsync (request);
  ...
}
```

`DeleteAttributesAsync`方法會接受`DeleteAttributesRequest`做為參數的執行個體。  `DeleteAttributesRequest`執行個體會指定要從項目，刪除與屬性`List`的`Attribute`正在建立所要刪除執行個體`ToSimpleDBAttributes`方法。 前提是項目的所有屬性都已都刪除，則會都刪除項目。

## <a name="summary"></a>總結

本文說明如何使用 AWS SDK for.NET 進行查詢、 建立和取代，並刪除儲存在 SimpleDB 服務中的資料。 這個 SDK 提供`CognitoAWSCredentials`和`AmazonSimpleDBClient`Xamarin.Forms 應用程式用來存取 SimpleDB 服務類別。


## <a name="related-links"></a>相關連結

- [TodoAWS （範例）](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoAWS/)
- [Amazon Web 服務 SDK Xamarin 開發人員指南](http://docs.aws.amazon.com/mobile/sdkforxamarin/developerguide/)
- [Amazon Cognito 身分識別](http://docs.aws.amazon.com/cognito/devguide/identity/)
- [Amazon SimpleDB 開發人員文件](http://docs.aws.amazon.com/AmazonSimpleDB/latest/DeveloperGuide/Welcome.html)
- [AmazonSimpleDBClient 類別](http://docs.aws.amazon.com/sdkfornet1/latest/apidocs/html/T_Amazon_SimpleDB_AmazonSimpleDBClient.htm)
- [Amazon Web Services SDK for .NET](https://www.nuget.org/packages?q=Tags%3A%22aws-sdk-v3%22)
