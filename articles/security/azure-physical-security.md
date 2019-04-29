---
title: Azure 시설, 프레미스 및 물리적 보안 | Microsoft Docs
description: 이 문서에서는 물리적 인프라, 보안 및 규정 준수 제안을 포함하여 Azure 데이터 센터에 대해 설명합니다.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2018
ms.author: terrylan
ms.openlocfilehash: 5f8378de18796fd679354c88880942df83399ad5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60586805"
---
# <a name="azure-facilities-premises-and-physical-security"></a>Azure 시설, 구역 및 물리적 보안
Azure는 전 세계적으로 수천 개의 온라인 서비스를 지원하고 100개가 넘는 고도의 보안 시설에 걸쳐 있는 [글로벌로 분산된 데이터 센터 인프라](https://azure.microsoft.com/global-infrastructure/)로 구성되어 있습니다.

이 인프라는 전 세계 사용자에게 애플리케이션을 더 가까이 제공하는 데 필요한 확장성을 제공하고, 데이터 상주를 유지하고, 고객에게 포괄적인 준수 및 복원력 옵션을 제공하도록 디자인되어 있습니다. Azure는 전 세계 52개 지역을 포함하며 140개 국가에서 사용할 수 있습니다.

지역은 대규모의 복원력 있는 네트워크를 통해 상호 연결된 데이터 센터 집합입니다. 네트워크에는 기본적으로 콘텐츠 배포, 부하 분산, 중복성 및 암호화가 포함되어 있습니다. 다른 클라우드 공급자보다 많은 글로벌 지역을 제공하는 Azure는 필요한 곳에 응용 프로그램을 유연하게 배포할 수 있습니다.

Azure 지역은 지리적 위치로 구성됩니다. Azure 지리적 위치는 지리적 경계 내에서 데이터 상주, 주권, 준수 및 복원력 요구 사항이 적용되도록 합니다.

지리적 위치를 통해 특정 데이터 상주 및 준수 요구 사항이 있는 고객은 데이터와 애플리케이션을 가깝게 유지할 수 있습니다. 지리는 전용 대용량 네트워킹 인프라와 연결을 통해 전체 지역 오류를 견디는 내결함성이 있습니다.

가용성 영역은 Azure 지역 내에서 물리적으로 떨어진 위치입니다. 각 가용성 영역은 독립된 전원, 냉각 및 네트워킹을 갖춘 하나 이상의 데이터 센터로 구성됩니다. 가용성 영역을 통해 고가용성과 짧은 대기 시간 복제로 중요 업무용 애플리케이션을 실행할 수 있습니다.

다음 그림은 Azure 글로벌 인프라가 고가용성, 재해 복구 및 백업을 위해 동일한 데이터 상주 경계 내에서 지역과 가용성 영역을 연결하는 방법을 보여줍니다.

![데이터 상주 경계를 보여주는 다이어그램][1]

지리적으로 분산된 데이터 센터는 Microsoft가 네트워크 대기 시간을 줄이고 지역 중복 백업 및 장애 조치(failover)를 허용하기 위해 고객과 가깝게 있을 수 있습니다.

## <a name="physical-security"></a>물리적 보안
Microsoft는 고객 데이터가 저장되어 있는 영역에 대한 물리적 액세스를 엄격하게 제어하는 방식으로 데이터 센터를 디자인, 구축 및 운영합니다. Microsoft는 고객 데이터 보호의 중요성을 이해하며 데이터를 포함하는 데이터 센터의 보안을 유지하도록 노력합니다. Microsoft에는 Azure를 지원하는 물리적 시설을 디자인, 구축 및 운영하는 일을 전담하는 부서가 있습니다. 이 팀은 최첨단의 물리적 보안을 유지 관리하는 데 투입되었습니다.

Microsoft는 허가되지 않은 사용자가 데이터 및 데이터 센터 리소스에 대한 실제 액세스 권한을 얻을 위험을 줄이기 위해 물리적 보안에 대해 계층화된 접근 방식을 사용합니다. Microsoft에서 관리하는 데이터 센터는 시설 경계, 건물 경계, 건물 내부 및 데이터 센터 층에서 액세스 승인을 허용하는 광범위한 보호 계층을 포함합니다. 물리적 보안 계층은 다음과 같습니다.

- **액세스 요청 및 승인.** 데이터 센터에 도착하기 전에 액세스를 요청해야 합니다. 규정 준수 또는 감사 목적과 같이, 방문에 대해 정당한 비즈니스 근거를 제공하도록 요구됩니다. 모든 요청은 Microsoft 직원의 액세스 필요성에 따라 승인됩니다. 액세스 필요성 기준은 데이터 센터에서 작업을 완료해야 하는 개인의 수를 최소한으로 유지하는 데 도움이 됩니다. Microsoft가 권한을 부여하면 개인만이 승인된 비즈니스 근거에 따라 필요한 데이터 센터의 개별 영역에 액세스할 수 있습니다. 권한은 특정 기간으로 제한되며, 기간 후에는 만료됩니다.

- **시설의 경계.** 데이터 센터에 도착하면 잘 정의된 액세스 지점을 통과해야 합니다. 일반적으로 강철과 콘크리트로 만들어진 높은 울타리가 경계를 1인치 간격으로 둘러싸고 있습니다. 데이터 센터 주변에는 카메라가 있고, 보안 팀이 항상 비디오를 모니터링합니다.

- **건물 입구.** 데이터 센터 입구에는 엄격한 훈련 및 신원 조사를 거친 전문 보안 책임자가 배치됩니다. 또한 이러한 보안 책임자는 데이터 센터를 일상적으로 순찰하고, 데이터 센터 내부의 카메라 비디오를 항상 모니터링합니다.

- **건물 내부.** 건물에 들어간 후에 데이터 센터로 계속 이동하려면 생체 인식 방법을 통한 2단계 인증을 통과해야 합니다. ID가 유효한 것으로 확인되면 데이터 센터 내에서 액세스가 승인된 곳에만 들어갈 수 있습니다. 승인된 기간 동안만 데이터 센터에 머무를 수 있습니다.

- **데이터 센터 층.** 출입이 승인된 층만 출입할 수 있습니다. 전신 금속 탐지기를 통과해야 합니다. Microsoft에서 알지 못하게 데이터 센터에서 무단으로 데이터를 입출력할 위험을 줄이기 위해 승인된 디바이스만 데이터 센터 층에 유입되도록 할 수 있습니다. 또한 감시 카메라가 모든 서버 랙의 앞뒷면을 모니터링합니다. 데이터 센터 층을 나갈 때도 전신 금속 탐지기를 통과해야 합니다. 데이터 센터를 벗어나기 위해 추가 보안 검사를 통과해야 합니다.

방문자는 Microsoft 시설에서 떠날 때 배지를 반납해야 합니다.

## <a name="physical-security-reviews"></a>물리적 보안 검토
데이터 센터에서 Azure 보안 요구 사항을 적절히 처리할 수 있도록, 시설에 대한 물리적 보안 검토가 정기적으로 수행됩니다. 데이터 센터 호스팅 공급자 담당자는 Azure 서비스 관리를 제공하지 않습니다. 직원은 Azure 시스템에 로그인할 수 없고 Azure 배치실 및 운전실에 물리적으로 액세스할 수 없습니다.

## <a name="data-bearing-devices"></a>데이터 포함 디바이스
Microsoft는 [NIST 800-88 규격](https://csrc.nist.gov/publications/detail/sp/800-88/archive/2006-09-01)의 모범 사례 절차 및 초기화 솔루션을 사용합니다. 초기화할 수 없는 하드 드라이브의 경우 하드 드라이브를 파기하여 정보를 복구할 수 없게 렌더링하는 파기 프로세스가 사용됩니다. 파기 프로세스로는 분해, 파쇄, 분쇄 또는 소각 등이 있습니다. 폐기 방법은 자산 유형에 따라 결정됩니다. 파기 기록은 유지됩니다.  

## <a name="equipment-disposal"></a>장비 폐기
시스템의 수명이 다하면 Microsoft 운영 담당자가 엄격한 데이터 처리 및 하드웨어 폐기 절차에 따라 고객 데이터를 포함하는 하드웨어를 신뢰할 수 없는 사용자가 사용할 수 없도록 합니다. 하드 드라이브에는 지원되는 안전한 지우기 방법이 사용됩니다. 초기화할 수 없는 하드 드라이브의 경우 드라이브를 파기하여 정보를 복구할 수 없게 렌더링하는 파기 프로세스가 사용됩니다. 파기 프로세스로는 분해, 파쇄, 분쇄 또는 소각 등이 있습니다. 폐기 방법은 자산 유형에 따라 결정됩니다. 파기 기록은 유지됩니다. 모든 Azure 서비스는 승인된 미디어 저장소 및 폐기 관리 서비스를 사용합니다.

## <a name="compliance"></a>준수
Microsoft는 광범위한 국제 및 산업별 준수 표준(예: ISO 27001, HIPAA, FedRAMP, SOC 1 및 SOC 2)을 충족하도록 Azure 인프라를 디자인하고 관리합니다. 호주 IRAP, UK G-Cloud 및 싱가포르 MTCS를 비롯한 국가별 표준도 준수합니다. 영국 표준 협회 등의 엄격한 타사 감사를 통해 이러한 표준에 규정된 엄격한 보안 제어 정책의 준수 여부를 검사합니다.

Azure가 준수하는 전체 규정 준수 표준 목록은 [규정 준수 제안](https://www.microsoft.com/trustcenter/compliance/complianceofferings)을 참조하세요. 

## <a name="next-steps"></a>다음 단계
Azure 인프라를 보호하기 위해 Microsoft에서 수행하는 작업에 대해 자세히 알아보려면 다음을 참조하세요.

- [Azure 인프라 가용성](azure-infrastructure-availability.md)
- [Azure 정보 시스템 구성 요소 및 경계](azure-infrastructure-components.md)
- [Azure 네트워크 아키텍처](azure-infrastructure-network.md)
- [Azure 프로덕션 네트워크](azure-production-network.md)
- [Azure SQL Database 보안 기능](azure-infrastructure-sql.md)
- [Azure 프로덕션 운영 및 관리](azure-infrastructure-operations.md)
- [심층 인프라 모니터링](azure-infrastructure-monitoring.md)
- [Azure 인프라 무결성](azure-infrastructure-integrity.md)
- [Azure 고객 데이터 보호](azure-protection-of-customer-data.md)

<!--Image references-->
[1]: ./media/azure-physical-security/data-residency-boundary.png
