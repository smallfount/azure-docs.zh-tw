---
title: Azure 事件方格中的事件網域
description: 本文說明如何使用事件網域，來管理自訂事件到您各種不同商業組織、客戶或應用程式的流程。
services: event-grid
author: banisadr
ms.service: event-grid
ms.author: babanisa
ms.topic: conceptual
ms.date: 01/21/2020
ms.openlocfilehash: f6698f91d7659f9fc2c314a9291380301146f8ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78898873"
---
# <a name="understand-event-domains-for-managing-event-grid-topics"></a>了解用於管理事件方格主題的事件網域

本文說明如何使用事件網域，來管理自訂事件到您各種不同商業組織、客戶或應用程式的流程。 使用事件網域：

* 大規模管理多租用戶事件架構。
* 管理您的驗證和授權。
* 分割您的主題，而不個別管理每個主題。
* 避免個別發行到您的每個主題端點。

## <a name="event-domain-overview"></a>事件網域概觀

事件網域是適用於與同一個應用程式相關之大量事件方格主題的管理工具。 您可以將它視為可含有數千個個別主題的中繼主題。

事件網域可讓您使用 Azure 服務 (例如，儲存體和 IoT 中樞) 所使用的相同架構來發行它們的事件。 它們讓您能夠將事件發行到數千個主題。 網域也會為您提供對每個主題的授權和驗證控制，讓您能夠分割租用戶。

### <a name="example-use-case"></a>使用案例範例

說明事件網域最容易的方式是使用範例。 假設您運行 Contoso 工程機械，在那裡您製造拖拉機、挖掘設備和其他重型機械。 在業務營運的過程中，您會為客戶推送有關設備維護、系統健康情況、合約更新等即時資訊。 此資訊全都會傳送到各種端點，包括您的應用程式、客戶端點，以及客戶已設定的其他基礎結構。

事件網域可讓您為 Contoso Construction Machinery 建立模型以作為單一事件實體。 您的每位客戶都會表示為網域內的一個主題。 驗證和授權都會使用 Azure Active Directory 來處理。 每位客戶都可訂閱他們的主題，並將他們的事件傳遞給他們。 透過事件網域的受控存取，確保他們只能存取自己的主題。

它也會為您提供單一端點，讓您可將所有客戶事件發行到其中。 事件方格將負責確保每個主題只知道範圍為其租用戶的事件。

![Contoso Construction 範例](./media/event-domains/contoso-construction-example.png)

## <a name="access-management"></a>存取管理

使用網域，您就能透過 Azure 角色型存取控制 (RBAC) 來微調對每個主題的授權和驗證控制。 您可以使用這些角色，將應用程式中的每個租用戶限制為只有您想要為其授與存取權的主題。

事件網域中 RBAC 的運作方式，與[受控存取控制](security-authorization.md)在事件方格和 Azure 其餘部分中運作的方式相同。 使用 RBAC，在事件網域中建立和強制執行自訂角色定義。

### <a name="built-in-roles"></a>內建角色

事件方格具有兩個內建角色定義，可使 RBAC 更容易與事件網域搭配運作。 這些角色為 **EventGrid EventSubscription 參與者 (預覽)** 和 **EventGrid EventSubscription 讀者 (預覽)**。 您可以將這些角色指派給需要訂閱您事件網域中之主題的使用者。 將角色指派範圍限定為使用者需要訂閱的主題。

如需這些角色的詳細資訊，請參閱[適用於事件方格的內建角色](security-authorization.md#built-in-roles)。

## <a name="subscribing-to-topics"></a>訂閱主題

在事件網域內的主題上訂用事件，與[在自訂主題上建立事件訂閱](./custom-event-quickstart.md)或訂閱來自 Azure 服務的事件一樣。

### <a name="domain-scope-subscriptions"></a>網域範圍訂用帳戶

事件網域也允許用於網域範圍訂用帳戶。 不論事件是否會傳送到該主題，事件網域上的事件訂閱將會接收已傳送到網域的所有事件。 網域範圍訂用帳戶對於管理和稽核用途非常實用。

## <a name="publishing-to-an-event-domain"></a>發行到事件網域

當您建立事件網域時，您會收到類似於您是否已在事件方格中建立主題的發行端點。 

若要將事件發行到事件網域中的任何主題，請[以您針對自訂主題所使用的相同方式](./post-to-custom-topic.md)來將事件推送到網域端點。 唯一的差別是必須指定您想要將事件傳遞到其中的主題。

例如，發行下列事件陣列會將 `"id": "1111"` 的事件傳送到主題 `foo`，同時將 `"id": "2222"` 的事件傳送到主題 `bar`：

```json
[{
  "topic": "foo",
  "id": "1111",
  "eventType": "maintenanceRequested",
  "subject": "myapp/vehicles/diggers",
  "eventTime": "2018-10-30T21:03:07+00:00",
  "data": {
    "make": "Contoso",
    "model": "Small Digger"
  },
  "dataVersion": "1.0"
},
{
  "topic": "bar",
  "id": "2222",
  "eventType": "maintenanceCompleted",
  "subject": "myapp/vehicles/tractors",
  "eventTime": "2018-10-30T21:04:12+00:00",
  "data": {
    "make": "Contoso",
    "model": "Big Tractor"
  },
  "dataVersion": "1.0"
}]
```

事件網域會為您處理發行到主題。 不要將事件個別發行到您所管理的每個主題，您可以改為將所有事件發行到網域端點。 事件方格負責確保會將每個事件傳送到正確的主題。

## <a name="limits-and-quotas"></a>限制和配額
以下是與事件域相關的限制和配額：

- 每個事件域 100，000 個主題 
- 每個 Azure 訂閱有 100 個事件域 
- 事件域中每個主題 500 個事件訂閱
- 50 個域範圍訂閱 
- 每秒 5，000 個事件引入率（進入域）

如果這些限制不適合您，請通過打開支援票證或發送電子郵件到[askgrid@microsoft.com](mailto:askgrid@microsoft.com)聯繫產品團隊。 

## <a name="pricing"></a>定價
事件域使用的操作定價與事件網格中所有其他功能使用的操作[定價](https://azure.microsoft.com/pricing/details/event-grid/)相同。

作業在事件網域中的運作方式，與它們在自訂主題中所做的一樣。 每個對事件網域的事件輸入都是一項作業，而每次對於事件的傳遞嘗試都是一項作業。

## <a name="next-steps"></a>後續步驟

* 若要了解如何設定事件網域、建立主題、建立事件訂閱，以及發行事件，請參閱[管理事件網域](./how-to-event-domains.md)。
