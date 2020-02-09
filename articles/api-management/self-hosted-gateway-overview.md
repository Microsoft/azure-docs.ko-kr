---
title: 자체 호스팅 Azure API Management gateway 개요 | Microsoft Docs
description: 조직이 하이브리드 및 다중 클라우드 환경에서 Api를 관리 하는 데 도움이 되는 자체 호스팅 Azure API Management gateway에 대해 알아봅니다.
services: api-management
documentationcenter: ''
author: vlvinogr
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/31/2019
ms.author: apimpm
ms.openlocfilehash: 415f0e209e607a863d715b1a66a2435603a662f0
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73513719"
---
# <a name="self-hosted-api-management-gateway-overview"></a>자체 호스팅 API Management 게이트웨이 개요

이 문서에서는 자체 호스팅 게이트웨이 기능을 통해 하이브리드 및 다중 클라우드 API를 관리 하 고, 개략적인 아키텍처를 소개 하 고, 기본적인 기능을 강조 하는 방법을 설명 합니다.

> [!NOTE]
> 자체 호스팅 게이트웨이 기능은 미리 보기 상태입니다. 미리 보기 중에 자체 호스팅 게이트웨이는 개발자 및 프리미엄 계층 에서만 추가 비용 없이 사용할 수 있습니다. 개발자 계층은 단일 자체 호스팅 게이트웨이 배포로 제한 됩니다.

## <a name="hybrid-and-multi-cloud-api-management"></a>하이브리드 및 다중 클라우드 API management

자체 호스팅 게이트웨이 기능은 하이브리드 및 다중 클라우드 환경에 대한 API Management 지원을 확장 하 고, 조직에서 Azure의 단일 API Management 서비스에서 온-프레미스 및 클라우드 간에 호스트 되는 Api를 효율적이 고 안전 하 게 관리할 수 있도록 합니다.

자체 호스팅 게이트웨이를 사용 하는 경우 고객은 Api를 호스트 하는 동일한 환경에 API Management gateway 구성 요소의 컨테이너 화 된 버전을 유연 하 게 배포할 수 있습니다. 모든 자체 호스팅 게이트웨이는 페더레이션 된 API Management 서비스에서 관리 되므로, 모든 내부 및 외부 Api에 대한 가시성 및 통합 관리 환경을 고객에 게 제공 합니다. 게이트웨이를 Api 가까이 배치 하면 고객이 API 트래픽 흐름을 최적화 하 고 보안 및 규정 준수 요구 사항을 해결할 수 있습니다.

각 API Management 서비스는 다음과 같은 주요 구성 요소로 구성 됩니다.

-   API로 노출 되는 관리 평면은 Azure Portal, PowerShell 및 기타 지원 되는 메커니즘을 통해 서비스를 구성 하는 데 사용 됩니다.
-   게이트웨이 (또는 데이터 평면)는 API 요청을 프록시 하 고 정책을 적용 하 고 원격 분석을 수집 하는 일을 담당 합니다.
-   개발자가 Api를 사용 하기 위해 검색, 학습 및 등록 하는 데 사용 하는 개발자 포털

기본적으로 이러한 모든 구성 요소는 Azure에 배포 되므로 api를 구현 하는 백 엔드가 호스트 되는 위치에 관계 없이 모든 API 트래픽 (아래 그림의 검정 실선으로 표시 됨)이 Azure를 통해 전달 됩니다. 이 모델의 운영 단순성은 대기 시간, 규정 준수 문제 및 경우에 따라 추가 데이터 전송 요금을 절감 하는 것입니다.

![자체 호스팅 게이트웨이가 없는 API 트래픽 흐름](media/self-hosted-gateway-overview/without-gateways.png)

백 호스팅 게이트웨이를 백 엔드 API 구현과 동일한 환경에 배포 하 고이를 API Management 서비스에 추가 하면 API 트래픽이 백 엔드 Api로 직접 이동 하 여 대기 시간을 개선 하 고 데이터 전송 비용을 최적화 하 고 사용할 수 있습니다. 규정 준수는 구현 호스트 되는 위치에 관계 없이 조직 내 모든 Api의 단일 관리 및 검색 지점을 유지 하는 이점을 유지 하는 데 도움이 됩니다.

![자체 호스팅 게이트웨이를 사용 하는 API 트래픽 흐름](media/self-hosted-gateway-overview/with-gateways.png)

## <a name="packaging-and-features"></a>패키징 및 기능

자체 호스팅 게이트웨이는 모든 API Management 서비스의 일부로 Azure에 배포 된 컨테이너 화 된 기능적으로 동일한 버전의 관리 되는 게이트웨이입니다. 자체 호스팅 게이트웨이는 Microsoft Container Registry에서 Linux 기반 Docker 컨테이너로 사용할 수 있습니다. 데스크톱, 서버 클러스터 또는 클라우드 인프라에서 실행 되는 Docker, Kubernetes 또는 다른 컨테이너 오케스트레이션 솔루션에 배포할 수 있습니다.

> [!IMPORTANT]
> 관리 되는 게이트웨이에서 사용할 수 있는 일부 기능은 아직 미리 보기에서 사용할 수 없습니다. 가장 주목할 만한 것은 이벤트 허브 정책, Service Fabric 통합, 다운스트림 HTTP/2에 대한 로그입니다. 자체 호스팅 게이트웨이에서는 기본 제공 캐시를 사용할 수 있도록 할 계획이 없습니다.

## <a name="connectivity-to-azure"></a>Azure에 대한 연결

자체 호스팅 게이트웨이를 사용 하려면 포트 443의 Azure에 대한 아웃 바운드 TCP/IP 연결이 필요 합니다. 자체 호스트 된 각 게이트웨이는 단일 API Management 서비스와 연결 되어야 하며 해당 관리 평면을 통해 구성 됩니다. 자체 호스팅 게이트웨이는 다음을 위해 Azure에 대한 연결을 사용 합니다.

-   1 분 마다 하트 비트 메시지를 전송 하 여 상태 보고
-   정기적으로 (10 초 마다) 확인 및 구성 업데이트를 사용할 수 있을 때마다 적용
-   요청 로그 및 메트릭을 Azure Monitor로 보내기 (구성 된 경우)
-   Application Insights로 설정 되어 있는 경우 이벤트 전송

Azure에 대한 연결이 끊어지면 자체 호스팅 게이트웨이는 구성 업데이트를 받거나, 상태를 보고 하거나, 원격 분석을 업로드할 수 없습니다.

자체 호스팅 게이트웨이는 "정적 오류"로 설계 되었으며 일시적으로 Azure에 대한 연결 손실을 감당할 수 있습니다. 로컬 구성 백업을 설정 하거나 사용 하지 않고 배포할 수 있습니다. 이전 경우 자체 호스팅 게이트웨이는 컨테이너 또는 pod에 연결 된 영구적 볼륨에 구성의 백업 복사본을 정기적으로 저장 합니다.

구성 백업이 꺼져 있고 Azure에 대한 연결이 중단 된 경우:

-   를 실행 하는 자체 호스팅 게이트웨이는 구성의 메모리 내 복사본을 사용 하 여 계속 작동 합니다.
-   중지 된 자체 호스팅 게이트웨이를 시작할 수 없습니다.

구성 백업이 켜져 있고 Azure에 대한 연결이 중단 된 경우:

-   를 실행 하는 자체 호스팅 게이트웨이는 구성의 메모리 내 복사본을 사용 하 여 계속 작동 합니다.
-   중지 된 자체 호스팅 게이트웨이는 구성의 백업 복사본을 사용 하기 시작 합니다.

연결이 복원 되 면 중단의 영향을 받는 자체 호스트 된 각 게이트웨이가 연결 된 API Management 서비스와 자동으로 다시 연결 되 고 게이트웨이가 "오프 라인" 상태일 때 발생 한 모든 구성 업데이트를 다운로드 합니다.

## <a name="next-steps"></a>다음 단계

-   [이 항목에 대한 추가 배경의 백서 읽기](https://aka.ms/hybrid-and-multi-cloud-api-management)
-   [자체 호스팅 게이트웨이를 Docker에 배포](api-management-howto-deploy-self-hosted-gateway-to-docker.md)
-   [Kubernetes에 자체 호스팅 게이트웨이 배포](api-management-howto-deploy-self-hosted-gateway-to-k8s.md)
