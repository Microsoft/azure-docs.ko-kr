---
title: Azure Network Watcher로 연결 문제 해결 - Azure Portal | Microsoft Docs
description: Azure Portal을 사용하여 Azure Network Watcher의 연결 문제 해결 기능을 사용하는 방법을 알아봅니다.
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/03/2017
ms.author: kumud
ms.openlocfilehash: 65afc3374a306cf2bb1b80baa2aa0949240a674a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60681590"
---
# <a name="troubleshoot-connections-with-azure-network-watcher-using-the-azure-portal"></a>Azure Portal을 사용하여 Azure Network Watcher로 연결 문제 해결

> [!div class="op_single_selector"]
> - [포털](network-watcher-connectivity-portal.md)
> - [PowerShell](network-watcher-connectivity-powershell.md)
> - [Azure CLI](network-watcher-connectivity-cli.md)
> - [Azure REST API](network-watcher-connectivity-rest.md)

연결 문제 해결을 사용하여 가상 머신에서 지정된 엔드포인트로 직접 TCP 연결을 설정하는 방법을 알아봅니다.

## <a name="before-you-begin"></a>시작하기 전에

이 문서에서는 사용자에게 다음 리소스가 있는 것으로 가정합니다.

* 연결 문제를 해결하려는 지역의 Network Watcher 인스턴스
* 연결 문제를 해결할 가상 머신

> [!IMPORTANT]
> 연결 문제 해결은 문제를 해결하는 VM에 `AzureNetworkWatcherExtension` VM 확장이 설치되어 있어야 합니다. Windows VM에서 확장을 설치하려면 [Windows용 Azure Network Watcher 에이전트 가상 머신 확장](../virtual-machines/windows/extensions-nwa.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)을 방문하고 Linux VM인 경우 [Linux용 Azure Network Watcher 에이전트 가상 머신 확장](../virtual-machines/linux/extensions-nwa.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)을 방문하세요. 확장은 대상 엔드포인트에서 필요하지 않습니다.

## <a name="check-connectivity-to-a-virtual-machine"></a>가상 머신에 대한 연결 확인

이 예제에서는 포트 80을 통해 대상 가상 머신에 대한 연결을 확인합니다.

Network Watcher로 이동하여 **연결 문제 해결**을 클릭합니다. 가상 머신을 선택하여 연결을 확인합니다. **대상** 섹션에서 **가상 머신 선택**을 선택하고 테스트할 가상 머신과 포트를 정확히 선택합니다.

**확인**을 클릭하면 지정된 포트의 가상 머신 간 연결이 확인됩니다. 이 예에서는 대상 VM이 연결될 수 없으며 홉 목록이 표시됩니다.

![가상 머신에 대한 연결 결과 확인][1]

## <a name="check-remote-endpoint-connectivity"></a>원격 엔드포인트 연결 확인

원격 엔드포인트에 대한 연결 및 대기 시간을 확인하려면 **대상** 섹션에서 **수동으로 지정** 라디오 단추를 선택하고 URL 및 포트를 입력한 다음 **확인**을 클릭합니다.  이 방법은 웹 사이트 및 저장소 엔드포인트와 같은 원격 엔드포인트에 사용됩니다.

![웹 사이트에 대한 연결 확인 결과][2]

## <a name="next-steps"></a>다음 단계

[경고로 트리거된 패킷 캡처 만들기](network-watcher-alert-triggered-packet-capture.md)를 확인하여 가상 머신 경고로 패킷 캡처를 자동화하는 방법을 알아봅니다.

[IP 흐름 확인 확인](diagnose-vm-network-traffic-filtering-problem.md)을 방문하여 특정 트래픽이 VM에서 허용되는지 알아봅니다.

[1]: ./media/network-watcher-connectivity-portal/figure1.png
[2]: ./media/network-watcher-connectivity-portal/figure2.png