---
title: Azure VPN 閘道：安裝點到站台用戶端證書
description: 安裝用於 P2S 憑證驗證的用戶端憑證 - Windows、Mac 或 Linux。
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 01/10/2020
ms.author: cherylmc
ms.openlocfilehash: 787b8a34ed4b232b9e6cc033e67b1a8162c85f6c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75902838"
---
# <a name="install-client-certificates-for-p2s-certificate-authentication-connections"></a>安裝用於 P2S 憑證驗證連線的用戶端憑證

使用 Azure 點對站憑證驗證來連線到虛擬網路的所有用戶端都必須有用戶端憑證。 本文可協助您安裝使用 P2S 連線至 VNet 時要供驗證使用的用戶端憑證。

## <a name="acquire-a-client-certificate"></a><a name="generate"></a>取得用戶端憑證

無論您想要從哪個用戶端作業系統進行連線，都必須一律使用戶端憑證。 您可以透過使用企業 CA 解決方案所產生的根憑證來產生用戶端憑證，也可以透過自我簽署的根憑證來產生。 請參閱 [PowerShell](vpn-gateway-certificates-point-to-site.md)、[MakeCert](vpn-gateway-certificates-point-to-site-makecert.md) 或 [Linux](vpn-gateway-certificates-point-to-site-linux.md) 指示，來取得產生用戶端憑證的步驟。 

## <a name="windows"></a><a name="installwin"></a>Windows

[!INCLUDE [Install on Windows](../../includes/vpn-gateway-certificates-install-client-cert-include.md)]

## <a name="mac"></a><a name="installmac"></a>Mac

>[!NOTE]
>只有 Resource Manager 部署模型能支援 Mac VPN 用戶端。 傳統部署模型則不支援。
>
>

[!INCLUDE [Install on Mac](../../includes/vpn-gateway-certificates-install-mac-client-cert-include.md)]

## <a name="linux"></a><a name="installlinux"></a>Linux

Linux 用戶端憑證會安裝在用戶端，是用戶端組態的一部分。 如需相關指示，請參閱[用戶端組態 - Linux](point-to-site-vpn-client-configuration-azure-cert.md#linuxinstallcli)。

## <a name="next-steps"></a>後續步驟

請繼續進行點對站設定步驟，以[建立和安裝 VPN 用戶端組態檔](point-to-site-vpn-client-configuration-azure-cert.md)。
