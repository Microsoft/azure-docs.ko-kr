---
title: Azure Network Watcher 및 오픈 소스 도구를 사용하여 네트워크 트래픽 패턴 시각화 | Microsoft Docs
description: 이 페이지에서는 Capanalysis와 함께 Network Watcher 패킷 캡처를 사용하여 VM과 주고 받는 트래픽을 시각화하는 방법을 설명합니다.
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.assetid: 936d881b-49f9-4798-8e45-d7185ec9fe89
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: kumud
ms.openlocfilehash: 7c239bbf577645ddb8ab12c525d1b3a8832421df
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60680535"
---
# <a name="visualize-network-traffic-patterns-to-and-from-your-vms-using-open-source-tools"></a>오픈 소스 도구를 사용하여 VM과 주고 받는 네트워크 트래픽 패턴 시각화

패킷 캡처에는 네트워크 과학 수사 및 상세한 패킷 검사를 수행할 수 있는 네트워크 데이터가 포함됩니다. 패킷 캡처를 분석하여 네트워크에 대한 정보를 파악할 수 있는 여러 오픈 소스 도구가 있습니다. 이러한 도구 중 하나는 오픈 소스 패킷 캡처 시각화 도구인 CapAnalysis입니다. 패킷 캡처 데이터 시각화는 네트워크의 패턴 및 오류에 대한 정보를 신속하게 얻을 수 있는 유용한 방법입니다. 또한 시각화는 이러한 정보를 손쉽게 공유할 수 있는 수단을 제공합니다.

Azure의 Network Watcher는 네트워크에서 패킷 캡처를 수행하여 데이터를 캡처하는 기능을 제공합니다. 이 문서에서는 CapAnalysis를 Network Watcher와 함께 사용하여 패킷 캡처를 시각화하고 정보를 얻는 방법을 연습합니다.

## <a name="scenario"></a>시나리오

Azure VM에 간단한 웹 애플리케이션을 배포했으며 오픈 소스 도구를 통해 네트워크 트래픽을 시각화하여 흐름 패턴과 오류를 신속하게 식별하고자 합니다. Network Watcher를 사용하면 네트워크 환경의 패킷 캡처를 획득하여 저장소 계정에 바로 저장할 수 있습니다. 그러면 CapAnalysis가 저장소 BLOB에서 직접 패킷 캡처를 수집하고 해당 콘텐츠를 시각화할 수 있습니다.

![시나리오][1]

## <a name="steps"></a>단계

### <a name="install-capanalysis"></a>CapAnalysis 설치

가상 머신에 CapAnalysis를 설치하는 방법은 https://www.capanalysis.net/ca/how-to-install-capanalysis에서 공식 절차를 참조하세요.
CapAnalysis에 원격으로 액세스하려면 새 인바운드 보안 규칙을 추가하여 VM에서 9877 포트를 열어야 합니다. 네트워크 보안 그룹에서 규칙을 만드는 방법에 대한 자세한 내용은 [기존 NSG에서 규칙 만들기](../virtual-network/manage-network-security-group.md#create-a-security-rule)를 참조하세요. 규칙이 추가되면 `http://<PublicIP>:9877`에서 CapAnalysis에 액세스할 수 있습니다.

### <a name="use-azure-network-watcher-to-start-a-packet-capture-session"></a>Azure Network Watcher를 사용하여 패킷 캡처 세션 시작

Network Watcher를 사용하면 가상 컴퓨터가 주고 받는 트래픽을 추적하는 패킷을 캡처할 수 있습니다. 패킷 캡처 세션을 시작하는 방법은 [Network Watcher를 사용하여 패킷 캡처 관리](network-watcher-packet-capture-manage-portal.md)의 지침을 참조하세요. 패킷 캡처는 CapAnalysis가 액세스할 수 있도록 저장소 BLOB에 저장할 수 있습니다.

### <a name="upload-a-packet-capture-to-capanalysis"></a>CapAnalysis에 패킷 캡처 업로드
"URL에서 가져오기" 탭을 사용하여 패킷 캡처가 저장된 저장소 BLOB의 링크를 제공하는 방법을 통해 Network Watcher가 만든 패킷 캡처를 직접 업로드할 수 있습니다.

CapAnalysis에 대한 링크를 제공할 때 저장소 BLOB URL에 SAS 토큰을 추가해야 합니다.  이렇게 하려면 저장소 계정에서 공유 액세스 서명으로 이동하여 허용되는 권한을 지정하고 SAS 생성 단추를 눌러 토큰을 만들어야 합니다. 그런 다음, SAS 토큰을 패킷 캡처 저장소 BLOB URL에 추가할 수 있습니다.

결과 URL은 다음 URL과 같습니다. http://storageaccount.blob.core.windows.net/container/location?addSASkeyhere


### <a name="analyzing-packet-captures"></a>패킷 캡처 분석

CapAnalysis는 패킷 캡처를 시각화하는 다양한 옵션을 제공하며, 각 옵션은 서로 다른 관점에서 패킷 캡처를 분석합니다. 이러한 시각적 요약 정보를 통해 네트워크 트래픽 추세를 이해하고 신속하게 비정상적인 활동을 발견할 수 있습니다. 이러한 기능 중 일부는 다음 목록에 표시됩니다.

1. 흐름 테이블

    이 테이블은 패킷 데이터의 흐름 목록, 흐름과 연결된 타임 스탬프 및 흐름과 연결된 다양한 프로토콜 그리고 원본 및 대상 IP를 제공합니다.

    ![capanalysis 흐름 페이지][5]

1. 프로토콜 개요

    이 창에서는 다양한 프로토콜 및 지리적 위치의 네트워크 트래픽 분산을 신속하게 확인할 수 있습니다.

    ![capanalysis 프로토콜 개요][6]

1. 통계

    이 창에서는 원본 및 대상 IP와 주고 받은 바이트, 각 원본 및 대상 IP의 흐름, 다양한 흐름에 사용된 프로토콜, 흐름의 기간 등 네트워크 트래픽 통계를 볼 수 있습니다.

    ![capanalysis 통계][7]

1. 지역 지도

    이 창에서는 각 국가의 트래픽 볼륨 크기에 따라 색깔이 표시된 네트워크 트래픽의 지도 보기를 제공합니다. 강조 표시된 국가를 선택하면 해당 국가의 IP에서 주고 받은 데이터 비율 같은 추가 흐름 통계를 볼 수 있습니다.

    ![지역 지도][8]

1. 필터

    CapAnalysis는 특정 패킷을 신속하게 분석할 수 있는 필터 집합을 제공합니다. 예를 들어 프로토콜을 기준으로 데이터를 필터링하여 해당 트래픽 하위 집합에 대한 구체적인 정보를 얻을 수 있습니다.

    ![filters][11]

    모든 CapAnalysis의 기능에 대해 자세히 알아보려면 [https://www.capanalysis.net/ca/#about](https://www.capanalysis.net/ca/#about)을 참조하세요.

## <a name="conclusion"></a>결론

Network Watcher의 패킷 캡처 기능을 사용하면 네트워크 과학 수사를 수행하고 네트워크 트래픽을 보다 정확하게 이해하는 데 필요한 데이터를 캡처할 수 있습니다. 이 시나리오에서는 Network Watcher의 패킷 캡처를 오픈 소스 시각화 도구와 간단하게 통합하는 방법을 살펴보았습니다. CapAnalysis 같은 오픈 소스 도구를 사용하여 패킷 캡처를 시각화하면 패킷을 자세히 검사하여 네트워크 트래픽의 추세를 신속하게 파악할 수 있습니다.

## <a name="next-steps"></a>다음 단계

[NSG 흐름 기록](network-watcher-nsg-flow-logging-overview.md)을 방문하여 NSG 흐름 로그에 대해 자세히 알아보기

[Power BI에서 NSG 흐름 로그 시각화](network-watcher-visualize-nsg-flow-logs-power-bi.md)를 방문하여 Power BI로 NSG 흐름 로그를 시각화하는 방법 알아보기
<!--Image references-->

[1]: ./media/network-watcher-using-open-source-tools/figure1.png
[2]: ./media/network-watcher-using-open-source-tools/figure2.png
[3]: ./media/network-watcher-using-open-source-tools/figure3.png
[4]: ./media/network-watcher-using-open-source-tools/figure4.png
[5]: ./media/network-watcher-using-open-source-tools/figure5.png
[6]: ./media/network-watcher-using-open-source-tools/figure6.png
[7]: ./media/network-watcher-using-open-source-tools/figure7.png
[8]: ./media/network-watcher-using-open-source-tools/figure8.png
[9]: ./media/network-watcher-using-open-source-tools/figure9.png
[10]: ./media/network-watcher-using-open-source-tools/figure10.png
[11]: ./media/network-watcher-using-open-source-tools/figure11.png
