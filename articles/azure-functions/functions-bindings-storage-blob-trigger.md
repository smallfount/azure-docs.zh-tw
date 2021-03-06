---
title: Azure 函數的 Azure Blob 存儲觸發器
description: 瞭解如何在 Azure Blob 存儲資料更改時運行 Azure 函數。
author: craigshoemaker
ms.topic: reference
ms.date: 02/13/2020
ms.author: cshoe
ms.openlocfilehash: 39e3521339947263161979033406fb39e397373f
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "80348963"
---
# <a name="azure-blob-storage-trigger-for-azure-functions"></a>Azure 函數的 Azure Blob 存儲觸發器

偵測到新的或已更新的 Blob 時，Blob 儲存體觸發程序會啟動函式。 blob 內容作為輸入提供給[函數](./functions-bindings-storage-blob-input.md)。

Azure Blob 存儲觸發器需要通用存儲帳戶。 要使用僅 Blob 帳戶，或者應用程式有專門需要，請查看使用此觸發器的替代方法。

有關設置和配置詳細資訊的資訊，請參閱[概述](./functions-bindings-storage-blob.md)。

## <a name="alternatives"></a>替代方案

### <a name="event-grid-trigger"></a>事件網格觸發器

[事件網格觸發器](functions-bindings-event-grid.md)還內置了對[blob 事件](../storage/blobs/storage-blob-event-overview.md)的支援。 請使用 Event Grid 來因應以下情節的需求，避免使用 Blob 儲存體觸發程序：

- **僅限 Blob 的存儲帳戶**：[僅 Blob 存儲帳戶](../storage/common/storage-account-overview.md#types-of-storage-accounts)支援 Blob 輸入和輸出綁定，但不支援 Blob 觸發器。

- **高比例**：可鬆散地定義為包含 100，000 個以上 blob 的容器或每秒具有 100 個以上 blob 更新的存儲帳戶。

- **最小化延遲**：如果函數應用位於消耗計畫上，則如果函數應用已空閒，則處理新 Blob 的延遲可能長達 10 分鐘。 若要避免這類延遲，可以切換到 App Service 方案並啟用 Always On。 您也可以透過 Blob 儲存體帳戶使用 [Event Grid 觸發程序](functions-bindings-event-grid.md)。 如需範例，請參閱[ Event Grid 教學課程](../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2Fazure%2Fazure-functions%2Ftoc.json)。

請參閱[事件網格示例的事件網格教程的圖像調整大小](../event-grid/resize-images-on-storage-blob-upload-event.md)。

### <a name="queue-storage-trigger"></a>佇列儲存體觸發程序

處理 Blob 的另一種方法是編寫與正在創建或修改的 Blob 對應的佇列消息，然後使用[佇列存儲觸發器](./functions-bindings-storage-queue.md)開始處理。

## <a name="example"></a>範例

# <a name="c"></a>[C#](#tab/csharp)

下列範例示範在 `samples-workitems` 容器中新增或更新 Blob 時，寫入記錄的 [C# 函式](functions-dotnet-class-library.md)。

```csharp
[FunctionName("BlobTriggerCSharp")]        
public static void Run([BlobTrigger("samples-workitems/{name}")] Stream myBlob, string name, ILogger log)
{
    log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

Blob 觸發程序路徑 `samples-workitems/{name}` 中的字串 `{name}` 會建立[繫結運算式](./functions-bindings-expressions-patterns.md)，您可以在函式程式碼中用來存取觸發 Blob 的檔案名稱。 如需詳細資訊，請參閱本文稍後的 [Blob 名稱模式](#blob-name-patterns)。

有關屬性的詳細資訊，`BlobTrigger`請參閱[屬性和注釋](#attributes-and-annotations)。

# <a name="c-script"></a>[C# 腳本](#tab/csharp-script)

下面的示例顯示了*函數.json*檔中的 blob 觸發器綁定和使用綁定的代碼。 當在`samples-workitems`[容器](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources)中添加或更新 Blob 時，函數將寫入日誌。

以下是 *function.json* 檔案中的繫結資料：

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myBlob",
            "type": "blobTrigger",
            "direction": "in",
            "path": "samples-workitems/{name}",
            "connection":"MyStorageAccountAppSetting"
        }
    ]
}
```

Blob 觸發程序路徑 `samples-workitems/{name}` 中的字串 `{name}` 會建立[繫結運算式](./functions-bindings-expressions-patterns.md)，您可以在函式程式碼中用來存取觸發 Blob 的檔案名稱。 如需詳細資訊，請參閱本文稍後的 [Blob 名稱模式](#blob-name-patterns)。

如需 *function.json* 檔案屬性的詳細資訊，請參閱[設定](#configuration)一節中這些屬性的說明。

以下是繫結至 `Stream` 的 C# 指令碼：

```cs
public static void Run(Stream myBlob, string name, ILogger log)
{
   log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

以下是繫結至 `CloudBlockBlob` 的 C# 指令碼：

```cs
#r "Microsoft.WindowsAzure.Storage"

using Microsoft.WindowsAzure.Storage.Blob;

public static void Run(CloudBlockBlob myBlob, string name, ILogger log)
{
    log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name}\nURI:{myBlob.StorageUri}");
}
```

# <a name="javascript"></a>[JAVAscript](#tab/javascript)

下列範例所示範的是使用繫結之 *function.json* 檔案和 [JavaScript 程式碼](functions-reference-node.md)中的 Blob 觸發程序繫結。 在 `samples-workitems` 容器中新增或更新 Blob 時，函數會寫入記錄。

以下是 *function.json* 檔案：

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myBlob",
            "type": "blobTrigger",
            "direction": "in",
            "path": "samples-workitems/{name}",
            "connection":"MyStorageAccountAppSetting"
        }
    ]
}
```

Blob 觸發程序路徑 `samples-workitems/{name}` 中的字串 `{name}` 會建立[繫結運算式](./functions-bindings-expressions-patterns.md)，您可以在函式程式碼中用來存取觸發 Blob 的檔案名稱。 如需詳細資訊，請參閱本文稍後的 [Blob 名稱模式](#blob-name-patterns)。

如需 *function.json* 檔案屬性的詳細資訊，請參閱[設定](#configuration)一節中這些屬性的說明。

以下是 JavaScript 程式碼：

```javascript
module.exports = function(context) {
    context.log('Node.js Blob trigger function processed', context.bindings.myBlob);
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

下列範例示範的是使用繫結的 *function.json* 檔案和 [Python 程式碼](functions-reference-python.md)中的 Blob 觸發程序繫結。 當在`samples-workitems`[容器](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources)中添加或更新 Blob 時，函數將寫入日誌。

以下是 *function.json* 檔案：

```json
{
    "scriptFile": "__init__.py",
    "disabled": false,
    "bindings": [
        {
            "name": "myblob",
            "type": "blobTrigger",
            "direction": "in",
            "path": "samples-workitems/{name}",
            "connection":"MyStorageAccountAppSetting"
        }
    ]
}
```

Blob 觸發程序路徑 `samples-workitems/{name}` 中的字串 `{name}` 會建立[繫結運算式](./functions-bindings-expressions-patterns.md)，您可以在函式程式碼中用來存取觸發 Blob 的檔案名稱。 如需詳細資訊，請參閱本文稍後的 [Blob 名稱模式](#blob-name-patterns)。

如需 *function.json* 檔案屬性的詳細資訊，請參閱[設定](#configuration)一節中這些屬性的說明。

以下是 Python 程式碼：

```python
import logging
import azure.functions as func


def main(myblob: func.InputStream):
    logging.info('Python Blob trigger function processed %s', myblob.name)
```

# <a name="java"></a>[JAVA](#tab/java)

當在容器中添加或更新 Blob 時，`myblob`此函數將寫入日誌。

```java
@FunctionName("blobprocessor")
public void run(
  @BlobTrigger(name = "file",
               dataType = "binary",
               path = "myblob/{name}",
               connection = "MyStorageAccountAppSetting") byte[] content,
  @BindingName("name") String filename,
  final ExecutionContext context
) {
  context.getLogger().info("Name: " + filename + " Size: " + content.length + " bytes");
}
```

---

## <a name="attributes-and-annotations"></a>屬性和注釋

# <a name="c"></a>[C#](#tab/csharp)

在 [C# 類別庫](functions-dotnet-class-library.md)中，使用下列屬性以設定 Blob 觸發程序：

* [BlobTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobTriggerAttribute.cs)

  該屬性的建構函式採用路徑字串，指示要監看的容器以及可選的 [Blob 名稱模式](#blob-name-patterns)。 以下是範例：

  ```csharp
  [FunctionName("ResizeImage")]
  public static void Run(
      [BlobTrigger("sample-images/{name}")] Stream image,
      [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
  {
      ....
  }
  ```

  您可以設定 `Connection` 屬性來指定要使用的儲存體帳戶，如下列範例所示：

   ```csharp
  [FunctionName("ResizeImage")]
  public static void Run(
      [BlobTrigger("sample-images/{name}", Connection = "StorageConnectionAppSetting")] Stream image,
      [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
  {
      ....
  }
   ```

  有關完整示例，請參閱[觸發器示例](#example)。

* [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)

  提供另一種方式來指定要使用的儲存體帳戶。 建構函式採用的是內含儲存體連接字串的應用程式設定名稱。 屬性可以套用在參數、方法或類別層級。 下列範例所示範的是類別層級與方法層級：

  ```csharp
  [StorageAccount("ClassLevelStorageAppSetting")]
  public static class AzureFunctions
  {
      [FunctionName("BlobTrigger")]
      [StorageAccount("FunctionLevelStorageAppSetting")]
      public static void Run( //...
  {
      ....
  }
  ```

要使用的儲存體帳戶按以下順序決定：

* `BlobTrigger` 屬性的 `Connection` 內容。
* `StorageAccount` 屬性套用至與 `BlobTrigger` 屬性相同的參數。
* `StorageAccount` 屬性套用至該函式。
* `StorageAccount` 屬性套用至該類別。
* 函數應用程式 (「AzureWebJobsStorage」應用程式設定) 的預設儲存體帳戶。

# <a name="c-script"></a>[C# 腳本](#tab/csharp-script)

C# 腳本不支援屬性。

# <a name="javascript"></a>[JAVAscript](#tab/javascript)

JavaScript 不支援屬性。

# <a name="python"></a>[Python](#tab/python)

Python 不支援屬性。

# <a name="java"></a>[JAVA](#tab/java)

該`@BlobTrigger`屬性用於授予您對觸發函數的 blob 的存取權限。 有關詳細資訊，請參閱[觸發器示例](#example)。

---

## <a name="configuration"></a>組態

下表介紹了您在*函數.json*檔和`BlobTrigger`屬性中設置的綁定配置屬性。

|function.json 屬性 | 屬性內容 |描述|
|---------|---------|----------------------|
|**型別** | n/a | 必須設為 `blobTrigger`。 當您在 Azure 入口網站中建立觸發程序時，會自動設定此屬性。|
|**direction** | n/a | 必須設為 `in`。 當您在 Azure 入口網站中建立觸發程序時，會自動設定此屬性。 例外狀況在[使用方式](#usage)一節中會加以說明。 |
|**名稱** | n/a | 表示函式程式碼中 Blob 的變數名稱。 |
|**路徑** | **BlobPath** |要監視的[容器](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources)。  可能是 [Blob 名稱模式](#blob-name-patterns)。 |
|**連接** | **連接** | 應用程式設定的名稱包含要用於此繫結的儲存體連接字串。 如果應用程式設定名稱是以「AzureWebJobs」開頭，於此僅能指定名稱的其餘部分。 例如，如果您將 `connection` 設定為「MyStorage」，則函式執行階段會尋找名稱為「AzureWebJobsMyStorage」的應用程式設定。 如果您將 `connection` 保留空白，則函式執行階段會使用應用程式設定中名稱為 `AzureWebJobsStorage` 的預設儲存體連接字串。<br><br>連接字串必須是用於一般用途的儲存體帳戶，而不是[Blob 儲存體帳戶](../storage/common/storage-account-overview.md#types-of-storage-accounts)。|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>使用量

# <a name="c"></a>[C#](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-trigger.md)]

# <a name="c-script"></a>[C# 腳本](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-trigger.md)]

# <a name="javascript"></a>[JAVAscript](#tab/javascript)

使用`context.bindings.<NAME>`與`<NAME>`*函數.json*中定義的值匹配的位置訪問 blob 資料。

# <a name="python"></a>[Python](#tab/python)

通過鍵入為[InputStream](https://docs.microsoft.com/python/api/azure-functions/azure.functions.inputstream?view=azure-python)的參數訪問 blob 資料。 有關詳細資訊，請參閱[觸發器示例](#example)。

# <a name="java"></a>[JAVA](#tab/java)

該`@BlobTrigger`屬性用於授予您對觸發函數的 blob 的存取權限。 有關詳細資訊，請參閱[觸發器示例](#example)。

---

## <a name="blob-name-patterns"></a>Blob 名稱模式

您可以在 *function.json* 中的 `path` 屬性或 `BlobTrigger` 屬性建構函式中，指定 Blob 名稱模式。 名稱模式可以是[篩選條件或繫結運算式](./functions-bindings-expressions-patterns.md)。 下列各節提供相關範例。

### <a name="get-file-name-and-extension"></a>取得檔案名稱和副檔名

下列範例示範如何分別繫結至 Blob 檔案名稱和副檔名：

```json
"path": "input/{blobname}.{blobextension}",
```

如果 Blob 名稱為 *original-Blob1.txt*，則函式程式碼中的 `blobname` 和 `blobextension` 變數值為 *original-Blob1* 和 *txt*。

### <a name="filter-on-blob-name"></a>Blob 名稱上的篩選條件

下列範例只會觸發 `input` 容器中以字串 "original-" 開頭的 Blob：

```json
"path": "input/original-{name}",
```

如果 Blob 名稱為 *original-Blob1.txt*，則函式程式碼中 `name` 變數的值為 `Blob1`。

### <a name="filter-on-file-type"></a>檔案類型上的篩選條件

下列範例只會在 *.png* 檔案上觸發：

```json
"path": "samples/{name}.png",
```

### <a name="filter-on-curly-braces-in-file-names"></a>檔案名稱中大括號上的篩選條件

若要尋找檔案名稱中的大括號，請使用兩個括號逸出括號。 下列範例篩選名稱中有大括號的 Blob：

```json
"path": "images/{{20140101}}-{name}",
```

如果 blob 名為*{20140101}-soundfile.mp3，* 則函數代碼中的`name`變數值為*soundfile.mp3*。

## <a name="metadata"></a>中繼資料

# <a name="c"></a>[C#](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-metadata.md)]

# <a name="c-script"></a>[C# 腳本](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-metadata.md)]

# <a name="javascript"></a>[JAVAscript](#tab/javascript)

```javascript
module.exports = function (context, myBlob) {
    context.log("Full blob path:", context.bindingData.blobTrigger);
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

中繼資料在 Python 中不可用。

# <a name="java"></a>[JAVA](#tab/java)

中繼資料在 JAVA 中不可用。

---

## <a name="blob-receipts"></a>Blob 回條

Azure Functions 執行階段可確保不會針對一樣新或更新的 blob 多次呼叫 blob 觸發程序函式。 為了判斷指定的 blob 版本是否已處理過，它會維護 *blob 回條*。

Azure Functions 會將 blob 回條儲存在您函數應用程式 (`AzureWebJobsStorage` 應用程式設定所定義) 的 Azure 儲存體帳戶中名為 *azure-webjobs-hosts*的容器中。 Blob 回條具有下列資訊：

* 觸發的函數（"*&lt;函數應用名稱>*。功能。函數名稱>"，例如："My功能應用程式.函數.CopyBlob"） * &lt; *
* 容器名稱
* Blob 類型 ("BlockBlob" 或 "PageBlob")
* Blob 名稱
* ETag (Blob 版本識別碼，例如："0x8D1DC6E70A277EF")

要強制重新處理某個 Blob，可以從 *azure-webjobs-hosts* 容器中手動刪除該 Blob 的 Blob 回條。 雖然後處理可能不會立即發生，但保證在以後的時間點發生。

## <a name="poison-blobs"></a>毒物斑點

當指定 blob 的 blob 觸發程序函式失敗時，Azure Functions 預設一共會重試該函式 5 次。

如果 5 次嘗試全都失敗，Azure Functions 會將訊息新增至名為 *webjobs-blobtrigger-poison* 的儲存體佇列。 您可以設定重試次數上限。 相同的 MaxDequeueCount 設定可用於處理有害的 Blob 和處理有害的佇列訊息。 適用於有害 Blob 的佇列訊息是一個 JSON 物件，其中包含下列屬性：

* 函數 Id（在格式*&lt;函數應用名稱>。* 功能。函數名稱>） * &lt; *
* BlobType ("BlockBlob" 或 "PageBlob")
* ContainerName
* BlobName
* ETag (Blob 版本識別碼，例如："0x8D1DC6E70A277EF")

## <a name="concurrency-and-memory-usage"></a>併發性和記憶體使用

Blob 觸發程序會在內部使用佇列，因此並行函式叫用數上限由 [host.json 中的佇列組態](functions-host-json.md#queues)所控制。 預設設定會將並行存取限制為 24 個叫用。 這項限制會個別套用至使用 Blob 觸發程序的每個函式。

[消耗計畫](functions-scale.md#how-the-consumption-and-premium-plans-work)將一個虛擬機器 （VM） 上的功能應用限制為 1.5 GB 記憶體。 每個並行執行的函式執行個體和函式執行階段本身都會使用記憶體。 如果 Blob 觸發的函式將整個 Blob 載入記憶體中，則該函式用於 Blob 的記憶體上限為 24 * Blob 大小上限。 例如，若某個函式應用程式有三個 Blob 觸發的函式，則預設的每一 VM 並行存取上限將是 3 * 24 = 72 個函式叫用。

JavaScript 和 Java 函式會將整個 Blob 載入記憶體中，而 C# 函式則會在您繫結至 `string`、`Byte[]` 或 POCO 時有此行為。

## <a name="polling"></a>輪詢

輪詢在檢查日誌和運行定期容器掃描之間作為混合方式。 Blob 一次以 10，000 個組進行掃描，並在間隔之間使用延續權杖。

> [!WARNING]
> 此外，[會以「最大努力」建立儲存體記錄](/rest/api/storageservices/About-Storage-Analytics-Logging)。 並不保證會擷取所有事件。 在某些情況下可能會遺失記錄。
> 
> 如果您需要更快或更可靠的 blob 處理，請考慮在建立 blob 時建立[佇列訊息](../storage/queues/storage-dotnet-how-to-use-queues.md)。 然後，使用[佇列觸發程序](functions-bindings-storage-queue.md) (而不是 Blob 觸發程序) 處理該 Blob。 另一個選項是使用 Event Grid；請參閱教學課程[使用 Event Grid 自動調整已上傳映像的大小](../event-grid/resize-images-on-storage-blob-upload-event.md)。
>

## <a name="next-steps"></a>後續步驟

- [運行函數時讀取 Blob 存儲資料](./functions-bindings-storage-blob-input.md)
- [從函數寫入 blob 存儲資料](./functions-bindings-storage-blob-output.md)
