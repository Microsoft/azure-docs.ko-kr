---
title: Azure Network Watcher에서 패킷 캡처 소개 | Microsoft Docs
description: 이 페이지는 Network Watcher 패킷 캡처 기능에 대한 개요를 제공합니다.
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.assetid: 3a81afaa-ecd9-4004-b68e-69ab56913356
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: kumud
ms.openlocfilehash: 3ca9619dcfe9054ab69d248e20f6d8e59324d308
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60727032"
---
# <a name="introduction-to-variable-packet-capture-in-azure-network-watcher"></a>Azure Network Watcher에서 변수 패킷 캡처 소개

Network Watcher 변수 패킷을 사용하면 가상 머신 간에 트래픽을 추적하는 패킷 캡처 세션을 만들 수 있습니다. 패킷 캡처를 통해 사후 및 사전 대응적으로 네트워크 예외를 진단할 수 있습니다. 또한 네트워크 침입에 대한 정보를 가져오는 네트워크 통계를 수집하는 것을 포함하여 클라이언트 서버 간 통신을 디버깅할 수 있습니다.

패킷 캡처는 Network Watcher를 통해 원격으로 시작되는 가상 머신 확장입니다. 이 기능은 원하는 가상 머신에서 수동으로 패킷 캡처를 실행하는 부담을 줄이고 시간을 단축합니다. 포털, PowerShell, CLI 또는 REST API를 통해 패킷 캡처를 트리거할 수 있습니다. 패킷 캡처를 트리거하는 방식에 대한 한 가지 예는 Virtual Machine 경고를 사용하는 것입니다. 모니터링할 트래픽을 캡처할 수 있도록 캡처 세션에 대한 필터가 제공됩니다. 필터는 5개 튜플(프로토콜, 로컬 IP 주소, 원격 IP 주소, 로컬 포트 및 원격 포트) 정보를 기반으로 합니다. 캡처된 데이터는 로컬 디스크 또는 저장소 BLOB에 저장됩니다. 구독당 하위 지역별로 패킷 캡처 세션 수가 10개로 제한됩니다. 이 제한은 세션에만 적용되며 VM에 로컬로 또는 저장소 계정에 저장된 패킷 캡처 파일에는 적용되지 않습니다.

> [!IMPORTANT]
> 패킷 캡처에는 가상 머신 확장 `AzureNetworkWatcherExtension`이 필요합니다. Windows VM에서 확장을 설치하려면 [Windows용 Azure Network Watcher 에이전트 가상 머신 확장](../virtual-machines/windows/extensions-nwa.md)을 방문하고 Linux VM인 경우 [Linux용 Azure Network Watcher 에이전트 가상 머신 확장](../virtual-machines/linux/extensions-nwa.md)을 방문하세요.

캡처할 정보를 원하는 정보로 한정하기 위해 패킷 캡처 세션에 대해 다음 옵션이 제공됩니다.

**캡처 구성**

|자산|설명|
|---|---|
|**패킷당 최대 바이트(bytes)** | 캡처된 각 패킷의 바이트 수이며 비어 있으면 모든 바이트가 캡처됩니다. 캡처된 각 패킷의 바이트 수이며 비어 있으면 모든 바이트가 캡처됩니다. IPv4 헤더만 필요한 경우 여기서 34를 나타냅니다. |
|**세션당 최대 바이트(bytes)** | 값이 세션 끝에 도달할 때까지 캡처된 총 바이트 수입니다.|
|**시간 제한(초)** | 패킷 캡처 세션에서 시간 제약 조건을 설정합니다. 기본값은 18000초 또는 5시간입니다.|

**필터링(선택 사항)**

|자산|설명|
|---|---|
|**프로토콜** | 패킷 캡처에 대해 필터링할 프로토콜입니다. 사용 가능한 값은 TCP, UDP 및 모두입니다.|
|**로컬 IP 주소** | 이 값은 패킷 캡처를 로컬 IP 주소가 이 필터 값과 일치하는 패킷으로 필터링합니다.|
|**로컬 포트** | 이 값은 패킷 캡처를 로컬 포트가 이 필터 값과 일치하는 패킷으로 필터링합니다.|
|**원격 IP 주소** | 이 값은 패킷 캡처를 원격 IP가 이 필터 값과 일치하는 패킷으로 필터링합니다.|
|**원격 포트** | 이 값은 패킷 캡처를 원격 포트가 이 필터 값과 일치하는 패킷으로 필터링합니다.|

### <a name="next-steps"></a>다음 단계

[Azure Portal에서 패킷 캡처 관리](network-watcher-packet-capture-manage-portal.md)를 참조하여 포털을 통해 패킷 캡처를 관리하거나 [PowerShell에서 패킷 캡처 관리](network-watcher-packet-capture-manage-powershell.md)를 참조하여 PowerShell로 패킷 캡처를 관리하는 방법을 알아보세요.

[경고로 트리거된 패킷 캡처 만들기](network-watcher-alert-triggered-packet-capture.md)를 확인하여 가상 머신 경고를 기반으로 사전 패킷 캡처를 만드는 방법을 알아보세요.

<!--Image references-->
[1]: ./media/network-watcher-packet-capture-overview/figure1.png













