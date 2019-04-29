---
title: Azure Service Fabric에서 지원 되는 클러스터 버전 | Microsoft Docs
description: Azure Service Fabric 클러스터 버전에 알아봅니다.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chakdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: troubleshooting
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 9/24/2018
ms.author: aljo
ms.openlocfilehash: 75e95737eecb9407a80103d1cad00d4987fe7091
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60716084"
---
# <a name="supported-service-fabric-versions"></a>지원되는 Service Fabric 버전

클러스터는 지원 되는 Azure Service Fabric 버전을 항상 실행 해야 합니다. Service Fabric에서 이전 버전에 대 한 지원의 새 버전의 릴리스를 발표 후 60 일의 최소 종료 됩니다. 새 릴리스 발표에 찾을 수 있습니다 합니다 [Service Fabric 팀 블로그](https://blogs.msdn.microsoft.com/azureservicefabric/)합니다.

지원 되는 Service Fabric 버전을 실행 하 여 클러스터를 유지 하는 방법에 대 한 내용은 다음 문서를 참조 하세요.

- [Azure Service Fabric 클러스터 업그레이드](service-fabric-cluster-upgrade.md)
- [독립 실행형 Windows Server 클러스터에서 실행 되는 Service Fabric 버전 업그레이드](service-fabric-cluster-upgrade-windows-server.md)

## <a name="supported-versions"></a>지원되는 버전

다음 표에서 Service Fabric 및 지원 종료 날짜의 버전을 나열 합니다.

| 클러스터의 service Fabric 런타임 | 클러스터 버전에서 직접 업그레이드할 수 있습니다. |호환 되는 SDK 또는 NuGet 패키지 버전 | 지원 종료 |
| --- | --- |--- | --- |
| 5.3.121 이전의 모든 클러스터 버전 | 5.1.158.* |버전 2.3보다 작거나 같음 |2017년 1월 20일 |
| 5.3.* | 5.1.158.* |버전 2.3보다 작거나 같음 |2017년 2월 24일 |
| 5.4.* | 5.1.158.* |버전 2.4보다 작거나 같음 |2017년 5월 10일       |
| 5.5.* | 5.4.164.* |버전 2.5보다 작거나 같음 |2017년 8월 10일    |
| 5.6.* | 5.4.164.* |버전 2.6보다 작거나 같음 |2017년 10월 13일   |
| 5.7.* | 5.4.164.* |버전 2.7보다 작거나 같음 |2017 년 12 월 15 일  |
| 6.0.* | 5.6.205.* |버전 2.8보다 작거나 같음 |2018 년 3 월 30 일     |
| 6.1.* | 5.7.221.* |버전 3.0보다 작거나 같음 |2018년 7월 15일      |
| 6.2.* | 6.0.232.* |버전 3.1보다 작거나 같음 |2018 년 10 월 26 일   |
| 6.3.* | 6.1.480.* |버전 3.2보다 작거나 같음 |2019 년 3 월 31 일  |
| 6.4.* | 6.2.301.* |버전 3.3보다 작거나 같음 |현재 버전, 종료 날짜 |

## <a name="supported-operating-systems"></a>지원되는 운영 체제

다음 표에서 지원 되는 Service Fabric 버전에 대 한 지원 되는 운영 체제를 나열합니다.

| 운영 체제 | 가장 오래 된 지원 되는 Service Fabric 버전 |
| --- | --- |
| Windows Server 2012 R2 | 모든 버전 |
| Windows Server 2016 | 모든 버전 |
| Windows Server 1709 | 6.0 |
| Windows Server 1803 | 6.4 |
| Windows Server 1809 | 6.4.654.9590 |
| Windows Server 2019 | 6.4.654.9590 |
| Linux Ubuntu 16.04 | 6.0 |

