---
title: Azure에서 고객 데이터 보호
description: 이 문서에서는 Azure에서 고객 데이터를 보호하는 방법을 다룹니다.
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
ms.openlocfilehash: 49615dcb2f077d2e1d8b93a4bb900b435e4c87bf
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60589071"
---
# <a name="azure-customer-data-protection"></a>Azure 고객 데이터 보호   
Microsoft 운영 및 지원 담당자의 고객 데이터 액세스는 기본적으로 거부됩니다. 고객 데이터에 대한 액세스 권한이 부여되면 권한 승인이 필요하며 액세스가 신중하게 관리되고 기록됩니다. 액세스 제어 요구 사항은 다음 Azure 보안 정책으로 설정됩니다.

- 기본적으로 고객 데이터에 대한 액세스 권한이 없습니다.
- 고객 VM에는 사용자 또는 관리자 계정이 없습니다.
- 작업(액세스 요청 감사 및 로깅)을 완료하는 데 필요한 최소 권한만 부여합니다.

Azure 지원 담당자에게는 Microsoft의 고유한 회사 Active Directory 계정이 할당됩니다. Azure는 MSIT(Microsoft Information Technology)에서 관리하는 Microsoft Corporate Active Directory를 사용하여 주요 정보 시스템에 대한 액세스를 제어합니다. 다단계 인증이 필요하며, 액세스는 보안 콘솔에서만 허용됩니다.

모든 액세스 시도가 모니터링되고, 기본 보고서 집합을 통해 표시될 수 있습니다.

## <a name="data-protection"></a>데이터 보호
Azure는 고객에게 강력한 데이터 보안을 기본적으로 그리고 고객 옵션으로 제공합니다.

**데이터 분리**: Azure는 다중 테넌트 서비스입니다. 즉, 여러 고객 배포 및 VM이 동일한 물리적 하드웨어에 저장됩니다. Azure는 논리적 격리를 사용하여 각 고객의 데이터를 다른 고객의 데이터와 분리합니다. 분리는 다중 테넌트 서비스의 규모상의 이익과 경제적 이익을 제공하는 한편, 고객이 서로의 데이터에 액세스하지 못하도록 엄격하게 방지합니다.

**저장 데이터 보호**: 고객은 Azure에 저장된 데이터가 표준에 따라 암호화되도록 할 책임이 있습니다. Azure는 다양한 암호화 기능을 제공하여 고객의 필요에 가장 부합하는 솔루션을 선택할 수 있는 유연성을 제공합니다. Azure Key Vault를 사용하면 고객이 클라우드 애플리케이션과 서비스에서 데이터를 암호화하는 데 사용되는 키를 쉽게 제어할 수 있습니다. Azure Disk Encryption을 사용하면 고객이 VM을 암호화할 수 있습니다. Azure 저장소 서비스 암호화를 사용하면 고객의 저장소 계정에 배치된 모든 데이터를 암호화하는 것이 가능합니다.

**전송 중인 데이터 보호**: 고객은 자신의 VM과 최종 사용자 간의 트래픽을 암호화할 수 있습니다. Azure는 두 개의 가상 네트워크 사이와 같이 외부 구성 요소 간에 전송 중인 데이터와 내부적으로 전송 중인 데이터를 보호합니다. Azure는 CESG/NCSC에서 권장하는 2,048비트 RSA/SHA256 암호화 키를 사용하는 업계 표준 TLS(전송 계층 보안) 1.2 이상의 프로토콜을 사용하여 다음과 같은 통신을 암호화합니다.

- 고객과 클라우드 간 통신
- Azure 시스템과 데이터 센터 간 내부 통신

**암호화**: 고객은 스토리지 데이터와 전송 중인 데이터의 암호화를 데이터의 기밀성과 무결성을 보장하기 위한 모범 사례로 배포할 수 있습니다. 고객이 SSL을 통해 인터넷 통신 및 Azure에서 호스팅된 VM 간의 통신을 보호하도록 Azure 클라우드 서비스를 간단하게 구성할 수 있습니다.

**데이터 중복성**: Microsoft는 데이터 센터에 사이버 공격이나 물리적 손상이 있는 경우 데이터를 보호합니다. 고객이 선택할 수 있는 항목은 다음과 감습니다.

- 규정 준수 또는 대기 시간 고려 사항을 위한 국내 저장소
- 보안 또는 재해 복구를 위한 국외 저장소

데이터는 중복성을 위해 선택한 지리적 영역 내에 복제하는 것은 가능하지만 외부로 전송할 수는 없습니다. 고객에게는 복사본 수, 복제 데이터 센터의 수와 위치를 포함하여 데이터를 복제하기 위한 여러 옵션이 제공됩니다.

저장소 계정을 만들 때 다음 복제 옵션 중 하나를 선택합니다.

- **LRS(로컬 중복 스토리지)**:  로컬 중복 저장소는 데이터의 복제본 3개를 유지 관리합니다. LRS는 단일 지역의 단일 시설 내에서 3번 복제됩니다. LRS는 단일 시설의 오류가 아닌 일반적인 하드웨어 오류로부터 데이터를 보호합니다.
- **ZRS(영역 중복 스토리지)**:  영역 중복 저장소는 데이터의 복제본 3개를 유지 관리합니다. ZRS는 LRS보다 더 높은 내구성을 제공하기 위해 2~3개의 시설에 세 번 복제됩니다. 복제는 단일 지역 내에서 또는 두 지역 간에 수행됩니다. ZRS는 데이터를 단일 지역 내에 보관합니다.
- **GRS(지역 중복 스토리지)**: 지역 중복 저장소는 저장소 계정을 만들 때 기본적으로 사용하도록 설정됩니다. GRS는 데이터의 복사본을 6개 유지 관리합니다. GRS를 사용하면 데이터가 주 지역 내에서 세 번 복제됩니다. 또한 데이터는 주 지역에서 수백 마일 떨어진 보조 지역에서 3번 복제되어 최고 수준의 내구성을 제공합니다. 주 지역에서 장애 발생 시 Azure Storage는 보조 지역으로 장애 조치(failover)됩니다. GRS는 데이터를 별도의 두 지역에 보관합니다.

**데이터 파기**: 고객이 데이터를 삭제하거나 Azure를 떠날 때 Microsoft는 다시 사용하기 전에 스토리지 리소스를 덮어쓰는 엄격한 표준을 따르며, 서비스 해제된 하드웨어를 물리적으로 파기합니다. Microsoft는 고객 요청 및 계약 종료 시 데이터의 완전 삭제를 실행합니다.

## <a name="customer-data-ownership"></a>고객 데이터 소유권
Microsoft는 고객이 Azure에 배포하는 애플리케이션을 검사, 승인 또는 모니터링하지 않습니다. 또한 Microsoft는 고객이 어떤 종류의 데이터를 Azure에 저장하도록 선택했는지 알지 못합니다. Microsoft는 Azure에 입력된 고객 정보에 대한 데이터 소유권을 주장하지 않습니다.

## <a name="records-management"></a>레코드 관리
Azure는 백 엔드 데이터에 대한 내부 레코드 보존 요구 사항을 설정했습니다. 고객은 자신의 레코드 보존 요구 사항을 확인해야 할 책임이 있습니다. Azure에 저장된 레코드의 경우 고객은 자신이 지정한 보존 기간 동안 자신의 데이터를 추출하고 Azure 외부에서 콘텐츠를 유지할 책임이 있습니다.

Azure는 고객이 제품에서 데이터를 내보내고 보고서를 감사할 수 있습니다. 내보내기는 로컬로 저장되어 고객이 정의한 보존 기간 동안 정보를 유지합니다.

## <a name="electronic-discovery-e-discovery"></a>e-discovery(Electronic Discovery)
Azure 고객은 Azure 서비스 사용 시 e-discovery 요구 사항을 준수할 책임이 있습니다. Azure 고객이 자신의 고객 데이터를 보존해야 하는 경우 데이터를 로컬로 내보내고 저장할 수 있습니다. 또한 고객은 Azure 고객 지원 부서에서 자신의 데이터에 대한 내보내기를 요청할 수 있습니다. Azure는 고객이 자신의 데이터를 내보낼 수 있도록 허용할 뿐만 아니라 내부적으로 광범위한 로깅과 모니터링을 수행합니다.

## <a name="next-steps"></a>다음 단계
Azure 인프라를 보호하기 위해 Microsoft에서 수행하는 작업에 대해 자세히 알아보려면 다음을 참조하세요.

- [Azure 시설, 구역 및 물리적 보안](azure-physical-security.md)
- [Azure 인프라 가용성](azure-infrastructure-availability.md)
- [Azure 정보 시스템 구성 요소 및 경계](azure-infrastructure-components.md)
- [Azure 네트워크 아키텍처](azure-infrastructure-network.md)
- [Azure 프로덕션 네트워크](azure-production-network.md)
- [Azure SQL Database 보안 기능](azure-infrastructure-sql.md)
- [Azure 프로덕션 운영 및 관리](azure-infrastructure-operations.md)
- [심층 인프라 모니터링](azure-infrastructure-monitoring.md)
- [Azure 인프라 무결성](azure-infrastructure-integrity.md)
