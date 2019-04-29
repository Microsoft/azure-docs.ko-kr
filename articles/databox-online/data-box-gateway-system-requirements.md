---
title: Microsoft Azure Data Box Gateway 시스템 요구 사항 | Microsoft Docs
description: Azure Data Box Gateway의 소프트웨어 및 네트워킹 요구 사항에 관해 알아봅니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 04/02/2019
ms.author: alkohli
ms.openlocfilehash: cac451634bfa357784f9fd3d3a24e06ef3a4ee19
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60754700"
---
# <a name="azure-data-box-gateway-system-requirements"></a>Azure 데이터 상자 Gateway 시스템 요구 사항

이 문서에서는 Microsoft Azure Data Box Gateway 솔루션 및 Azure Data Box Gateway에 연결하는 클라이언트에 대한 중요한 시스템 요구 사항을 설명합니다. Data Box Gateway를 배포하기 전에 정보를 신중하게 검토하고 배포 및 후속 작업 중 필요에 따라 다시 검토하는 것이 좋습니다.

Data Box Gateway 가상 디바이스의 시스템 요구 사항은 다음과 같습니다.

- **호스트의 소프트웨어 요구 사항** - 지원되는 플랫폼, 로컬 구성 UI용 브라우저, SMB 클라이언트 및 디바이스에 연결하는 호스트에 대한 추가 요구 사항을 설명합니다.
- **디바이스의 네트워킹 요구 사항** - 가상 디바이스가 작동하기 위한 네트워킹 요구 사항에 관한 정보를 제공합니다.


## <a name="specifications-for-the-virtual-device"></a>가상 디바이스의 사양

Data Box Gateway용 기본 호스트 시스템은 다음 리소스를 전용으로 사용하여 다음과 같은 가상 디바이스를 프로비전할 수 있습니다.

| 사양                                          | 설명              |
|---------------------------------------------------------|--------------------------|
| 가상 프로세서(코어)   | 최소 4개 |
| 메모리  | 최소 8GB|
| 가용성|단일 노드|
| 디스크| OS 디스크: 250GB <br> 데이터 디스크: 최소 2TB, 씬 프로비저닝 및 SSD를 통해 지원해야 함|
| 네트워크 인터페이스|가상 네트워크 인터페이스 하나 이상|


## <a name="supported-os-for-clients-connected-to-device"></a>디바이스에 연결된 클라이언트에 지원되는 OS

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-supported-client-os.md)]

## <a name="supported-protocols-for-clients-accessing-device"></a>디바이스에 액세스하는 클라이언트에 지원되는 프로토콜

[!INCLUDE [Supported protocols for clients accessing device](../../includes/data-box-edge-gateway-supported-client-protocols.md)]

## <a name="supported-virtualization-platforms-for-device"></a>디바이스에 지원되는 가상화 플랫폼

| **운영 체제/플랫폼**  |**버전**   |**참고 사항**  |
|---------|---------|---------|
|Hyper-V  |  2012 R2 <br> 2016  |         |
|VMware ESXi     | 6.0 <br> 6.5 <br> 6.7       |VMware 도구는 지원되지 않습니다.         |


## <a name="supported-storage-accounts"></a>지원되는 저장소 계정

[!INCLUDE [Supported storage accounts](../../includes/data-box-edge-gateway-supported-storage-accounts.md)]


## <a name="supported-storage-types"></a>지원되는 저장소 형식

[!INCLUDE [Supported storage types](../../includes/data-box-edge-gateway-supported-storage-types.md)]

## <a name="supported-browsers-for-local-web-ui"></a>로컬 웹 UI에 지원되는 브라우저

[!INCLUDE [Supported browsers for local web UI](../../includes/data-box-edge-gateway-supported-browsers.md)]

## <a name="networking-port-requirements"></a>네트워킹 포트 요구 사항

다음 테이블에는 SMB, 클라우드 또는 관리 트래픽을 고려하여 방화벽에서 열려야 하는 포트가 나열되어 있습니다. 이 테이블에서 *인* 또는 *인바운드*는 디바이스에 대한 들어오는 클라이언트 요청 액세스에서 방향을 참조합니다. *아웃* 또는 *아웃바운드*는 배포 후 데이터를 외부로 보내는 Data Box Gateway 디바이스에서 방향을 참조합니다.

[!INCLUDE [Port configuration for device](../../includes/data-box-edge-gateway-port-config.md)]

## <a name="url-patterns-for-firewall-rules"></a>방화벽 규칙에 대한 URL 패턴

네트워크 관리자는 URL 패턴을 기준으로 하는 고급 방화벽 규칙이 인바운드 및 아웃바운드 트래픽을 필터링하도록 구성할 수 있습니다. Data Box Gateway 장치 및 Data Box Gateway 서비스는 Azure Service Bus, Azure Active Directory Access Control, 저장소 계정 및 Microsoft 업데이트 서버 등의 다른 Microsoft 애플리케이션에 의존합니다. 이러한 애플리케이션과 연결된 URL 패턴을 사용하여 방화벽 규칙을 구성할 수 있습니다. 이러한 애플리케이션과 연결된 URL 패턴은 달라질 수 있습니다. 따라서 네트워크 관리자는 Data Box Gateway에 대한 방화벽 규칙을 모니터링하고 필요에 따라 업데이트해야 합니다.

Data Box Gateway 고정 IP 주소에 따라 대부분의 경우에서 자유롭게 아웃바운드 트래픽에 대한 방화벽 규칙을 설정하는 것이 좋습니다. 그러나 보안 환경을 만드는 데 필요한 고급 방화벽 규칙을 설정하려면 아래 정보를 사용할 수 있습니다.

> [!NOTE]
> - 디바이스(원본) IP는 항상 클라우드를 사용하도록 설정된 네트워크 인터페이스로 설정해야 합니다.
> - 대상 IP는 [Azure 데이터 센터 IP 범위](https://www.microsoft.com/download/confirmation.aspx?id=41653)로 설정해야 합니다.

[!INCLUDE [URL patterns for firewall](../../includes/data-box-edge-gateway-url-patterns-firewall.md)]

### <a name="url-patterns-for-azure-government"></a>Azure Government에 대 한 URL 패턴

[!INCLUDE [Azure Government URL patterns for firewall](../../includes/data-box-edge-gateway-gov-url-patterns-firewall.md)]

## <a name="internet-bandwidth"></a>인터넷 대역폭

[!INCLUDE [Internet bandwidth](../../includes/data-box-edge-gateway-internet-bandwidth.md)]

## <a name="next-step"></a>다음 단계

* [Azure Data Box Gateway 배포](data-box-gateway-deploy-prep.md)

