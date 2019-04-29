---
title: Azure 인프라 무결성
description: 이 문서에서는 Azure 인프라의 무결성을 확인합니다.
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
ms.date: 07/06/2018
ms.author: terrylan
ms.openlocfilehash: 24d54fa7a8985a6af58cddfc969b8023485c73c2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60587124"
---
# <a name="azure-infrastructure-integrity"></a>Azure 인프라 무결성

## <a name="software-installation"></a>소프트웨어 설치
Azure 환경에 설치된 소프트웨어 스택의 모든 구성 요소는 Microsoft SDL(Security Development Lifecycle) 프로세스에 따라 빌드된 사용자 지정입니다. OS 이미지 및 SQL Database를 포함한 모든 소프트웨어 구성 요소는 변경 관리 및 릴리스 관리 프로세스의 일부로 배포됩니다. 모든 노드에서 실행되는 OS는 Windows Server 2008 또는 Windows Server 2012의 사용자 지정된 버전입니다. 정확한 버전은 재생할 OS에 대해 정해진 역할에 따라 FC(패브릭 컨트롤러)에 의해 선택됩니다. 또한 호스트 OS는 승인되지 않은 소프트웨어 구성 요소의 설치를 허용하지 않습니다.

일부 Azure 구성 요소는 게스트 OS에서 실행되는 게스트 VM에 Azure 고객으로 배포됩니다.

## <a name="virus-scans-on-builds"></a>빌드에 대한 바이러스 검사
Azure 소프트웨어 구성 요소(OS 포함) 빌드는 Endpoint Protection 바이러스 백신 도구를 사용하여 바이러스 검사를 거쳐야 합니다. 각 바이러스 검사는 연결된 빌드 디렉터리 내에 검사된 내용 및 검사 결과를 자세히 설명하는 로그를 만듭니다. 바이러스 검사는 Azure 내에서 모든 구성 요소에 대한 빌드 소스 코드의 일부입니다. 명확하고 성공적인 바이러스 검사를 하지 않으면 코드가 프로덕션으로 이동되지 않습니다. 문제가 발견되면 빌드가 중단되고, Microsoft Security 내의 보안 팀으로 보내서 "악의적인" 코드가 빌드의 어디로 침입했는지 파악합니다.

## <a name="closed-and-locked-environment"></a>닫히고 잠긴 환경
기본적으로 Azure 인프라 노드 및 게스트 VM에는 사용자 계정이 생성되지 않습니다. 또한 기본 Windows 관리자 계정이 비활성화됩니다. Azure 라이브 지원의 관리자는 적절한 인증을 사용하여 이러한 머신에 로그인하고 응급 복구 작업에 대한 Azure 프로덕션 네트워크를 관리합니다.

## <a name="azure-sql-database-authentication"></a>Azure SQL Database 인증
SQL Server의 모든 구현과 마찬가지로 사용자 계정 관리는 긴밀하게 제어되어야 합니다. Azure SQL Database는 SQL Server 인증만 지원합니다. 고객의 데이터 보안 모델을 보완하려면 강력한 암호를 사용하고 특정 권한으로 구성된 사용자 계정을 사용해야 합니다.

## <a name="acls-and-firewalls-between-the-microsoft-corporate-network-and-an-azure-cluster"></a>Microsoft 회사 네트워크와 Azure 클러스터 간의 ACL 및 방화벽
서비스 플랫폼과 Microsoft 회사 네트워크 간의 ACL(액세스 제어 목록) 및 방화벽은 권한 없는 내부자 액세스로부터 SQL Database 인스턴스를 보호합니다. 뿐만 아니라, Microsoft 회사 네트워크의 IP 주소 범위에 포함되는 사용자만이 Windows Fabric 플랫폼 관리 엔드포인트에 액세스할 수 있습니다.

## <a name="acls-and-firewalls-between-nodes-in-a-sql-database-cluster"></a>SQL Database 클러스터의 노드 간 ACL 및 방화벽
보안을 강화하기 위한 심층 방어 전략의 일부로 SQL Database 클러스터의 노드 사이에 ACL 및 방화벽이 구현되었습니다. Windows Fabric 플랫폼 클러스터 내부의 모든 통신뿐만 아니라 실행 중인 모든 코드를 신뢰할 수 있습니다.

## <a name="custom-monitoring-agents"></a>사용자 지정 모니터링 에이전트
SQL Database는 watchdog이라고도 하는 사용자 지정 MA(모니터링 에이전트)를 사용하여 SQL Database 클러스터의 상태를 모니터링합니다.

## <a name="web-protocols"></a>웹 프로토콜

### <a name="role-instance-monitoring-and-restart"></a>역할 인스턴스 모니터링 및 다시 시작
Azure는 배포되어 실행 중인 모든 역할(인터넷 연결 웹 또는 백 엔드 처리 작업자 역할)을 지속적으로 상태 모니터링하여 프로비전된 서비스를 효과적이고 효율적으로 제공합니다. 호스트되는 애플리케이션에 심각한 결함이 있거나 역할 인스턴스 자체에 기본 구성 문제가 있어서 역할이 비정상 상태가 되면 FC는 역할 인스턴스 내의 문제를 감지하고 수정 상태를 시작합니다.

### <a name="compute-connectivity"></a>Compute 연결
Azure는 배포된 애플리케이션 또는 서비스를 표준 웹 기반 프로토콜을 통해 연결할 수 있게 해줍니다. 인터넷 연결 웹 역할 가상 인스턴스는 외부 인터넷과 연결되므로 웹 사용자가 직접 연결할 수 있습니다. 작업자 역할이 공개적으로 액세스할 수 있는 웹 역할 가상 인스턴스 대신 수행하는 작업의 민감도 및 무결성을 보호하기 위해, 백 엔드 처리 작업자 역할의 가상 인스턴스는 외부 인터넷에 연결되어 있지만 외부 웹 사용자가 직접 액세스할 수 없습니다.

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
- [Azure 고객 데이터 보호](azure-protection-of-customer-data.md)
