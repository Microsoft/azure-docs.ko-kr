---
title: Azure Security Center에서 모니터링되는 Azure 정책 정의 | Microsoft Docs
description: 이 문서에서는 Azure Security Center에서 모니터링할 수 있는 Azure Policy 정의의 목록을 제공 합니다.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 8/22/2019
ms.author: memildin
ms.openlocfilehash: 07d45f7cafa09ef6c5341acd5ffd0914cc56e4fd
ms.sourcegitcommit: b5d59c6710046cf105236a6bb88954033bd9111b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/27/2019
ms.locfileid: "74559256"
---
# <a name="azure-security-policies-monitored-by-security-center"></a>Security Center에서 모니터링 하는 Azure 보안 정책
이 문서에서는 Azure Security Center에서 모니터링할 수 있는 Azure Policy 정의의 목록을 제공 합니다. 보안 정책에 대한 자세한 내용은 [보안 정책 작업](tutorial-security-policy.md)을 참조하세요.

## <a name="available-security-policies"></a>사용 가능한 보안 정책

Security Center에서 모니터링 하는 기본 제공 정책에 대 한 자세한 내용은 다음 표를 참조 하십시오.

| 정책 | 정책에서 수행하는 작업 |
| --- | --- |
|Virtual Machine Scale Sets의 진단 로그를 사용 하도록 설정 해야 합니다.|인시던트 또는 손상 후에 작업 내역을 조사할 수 있도록 로그를 사용 하는 것이 좋습니다.|
|RootManageSharedAccessKey를 제외한 모든 권한 부여 규칙은 Event Hub 네임 스페이스에서 제거 해야 합니다.|Azure Event Hubs 클라이언트는 네임 스페이스의 모든 큐 및 토픽에 대 한 액세스를 제공 하는 네임 스페이스 수준 액세스 정책을 사용 하지 않아야 합니다. 최소 권한 보안 모델과 맞추려면 특정 엔터티에 대 한 액세스를 제공 하기 위해 큐 및 토픽에 대 한 엔터티 수준에서 액세스 정책을 만들어야 합니다.|
|이벤트 허브 엔터티에 대 한 권한 부여 규칙을 정의 해야 합니다.|Event Hubs 엔터티에 대 한 권한 부여 규칙이 있는지 감사 하 여 최소 권한 액세스를 부여 합니다.|
|방화벽 및 가상 네트워크 구성을 사용 하 여 저장소 계정에 대 한 액세스를 제한 해야 합니다.|스토리지 계정 방화벽 설정에서 무제한 네트워크 액세스를 감사합니다. 허용 된 네트워크의 응용 프로그램만 저장소 계정에 액세스할 수 있도록 네트워크 규칙을 구성 합니다. 특정 인터넷 또는 온-프레미스 클라이언트의 연결을 허용 하려면 특정 Azure virtual network 또는 공용 인터넷 IP 주소 범위에서 트래픽에 대 한 액세스 권한을 부여 합니다.|
|사용자 지정 RBAC 규칙의 사용 감사|오류가 발생 하기 쉬운 사용자 지정 RBAC (역할 기반 액세스 제어) 역할 대신 "소유자, 참가자, 읽기 권한자"와 같은 기본 제공 역할을 감사 합니다. 사용자 지정 역할의 사용은 예외로 처리 되며 엄격한 검토 및 위협 모델링이 필요 합니다.|
|Azure Stream Analytics의 진단 로그를 사용 하도록 설정 해야 합니다.|로그 사용을 감사 하 고 최대 1 년 동안 보관 합니다. 이렇게 하면 보안 인시던트가 발생 하거나 네트워크가 손상 된 경우 조사를 위한 활동 기록이 생성 됩니다.|
|Storage 계정에 보안 전송을 사용하도록 설정해야 합니다.|저장소 계정에서 보안 전송의 감사 요구 사항 보안 전송은 사용자의 스토리지 계정이 보안 연결(HTTPS)에서 오는 요청만 수락하도록 강제 적용하는 옵션입니다. HTTPS를 사용 하면 서버와 서비스 간의 인증이 보장 됩니다. 또한 메시지 가로채기 (man-in-the-middle), 도청 및 세션 하이재킹과 같은 네트워크 계층 공격 으로부터 전송 중인 데이터를 보호 합니다.|
|SQL server에 대 한 Azure AD 관리자를 프로 비전 해야 합니다.|Azure AD 인증을 사용 하도록 설정 하려면 SQL Server에 대 한 Azure Active Directory (Azure AD) 관리자의 프로 비전을 감사 합니다. Azure AD 인증은 데이터베이스 사용자 및 기타 Microsoft 서비스의 간소화 된 권한 관리 및 중앙 집중식 id 관리를 지원 합니다.|
|RootManageSharedAccessKey를 제외한 모든 권한 부여 규칙은 Service Bus 네임 스페이스에서 제거 해야 합니다.|Azure Service Bus 클라이언트는 네임 스페이스의 모든 큐 및 항목에 대 한 액세스를 제공 하는 네임 스페이스 수준 액세스 정책을 사용 하지 않아야 합니다. 최소 권한 보안 모델과 맞추려면 특정 엔터티에 대 한 액세스를 제공 하는 큐 및 토픽에 대 한 엔터티 수준에서 액세스 정책을 만듭니다.|
|Service Bus의 진단 로그를 사용 하도록 설정 해야 합니다.|로그 사용을 감사 하 고 1 년까지 유지 합니다. 이렇게 하면 보안 인시던트가 발생 하거나 네트워크가 손상 된 경우 조사를 위한 활동 기록이 생성 됩니다.|
|Service Fabric EncryptAndSign의 ClusterProtectionLevel 속성을 설정 해야 합니다.|Service Fabric는 기본 클러스터 인증서를 사용 하는 노드 간 통신에 대해 None, Sign 및 EncryptAndSign의 세 가지 보호 수준을 제공 합니다. 모든 노드 간 메시지가 암호화되고 디지털로 서명될 수 있게 보호 수준을 설정합니다.|
|클라이언트 인증 Azure Active Directory을 사용 해야 합니다.|Service Fabric에서 Azure AD를 통해서만 클라이언트 인증 사용을 감사 합니다.|
|검색 서비스의 진단 로그를 사용 하도록 설정 해야 합니다.|로그 사용을 감사 하 고 최대 1 년 동안 보관 합니다. 이렇게 하면 보안 인시던트가 발생 하거나 네트워크가 손상 된 경우 조사를 위한 활동 기록이 생성 됩니다.|
|Redis Cache에 보안 연결만 사용하도록 설정해야 합니다.|SSL을 통한 Redis 용 Azure 캐시로의 연결만 사용 하도록 감사 합니다. 보안 연결을 사용 하면 서버와 서비스 간의 인증을 보장 합니다. 또한 메시지 가로채기 (man-in-the-middle), 도청 및 세션 하이재킹과 같은 네트워크 계층 공격 으로부터 전송 중인 데이터를 보호 합니다.|
|Logic Apps의 진단 로그를 사용 하도록 설정 해야 합니다.|로그 사용을 감사 하 고 최대 1 년 동안 보관 합니다. 이렇게 하면 보안 인시던트가 발생 하거나 네트워크가 손상 된 경우 조사를 위한 활동 기록이 생성 됩니다.|
|Key Vault의 진단 로그를 사용 하도록 설정 해야 합니다.|로그 사용을 감사 하 고 최대 1 년 동안 보관 합니다. 이렇게 하면 보안 인시던트가 발생 하거나 네트워크가 손상 된 경우 조사를 위한 활동 기록이 생성 됩니다.|
|이벤트 허브의 진단 로그를 사용 하도록 설정 해야 합니다.|로그 사용을 감사 하 고 최대 1 년 동안 보관 합니다. 이렇게 하면 보안 인시던트가 발생 하거나 네트워크가 손상 된 경우 조사를 위한 활동 기록이 생성 됩니다.|
|Azure Data Lake Store의 진단 로그를 사용 하도록 설정 해야 합니다.|로그 사용을 감사 하 고 1 년까지 유지 합니다. 이렇게 하면 보안 인시던트가 발생 하거나 네트워크가 손상 된 경우 조사를 위한 활동 기록이 생성 됩니다.|
|Data Lake Analytics의 진단 로그를 사용 하도록 설정 해야 합니다.|로그 사용을 감사 하 고 최대 1 년 동안 보관 합니다. 이렇게 하면 보안 인시던트가 발생 하거나 네트워크가 손상 된 경우 조사를 위한 활동 기록이 생성 됩니다.|
|저장소 계정은 new AzureRM 리소스에 마이그레이션해야 합니다.|저장소 계정에 대 한 Azure Resource Manager를 사용 하 여 보안 강화 기능을 제공 합니다. 해당 지역은 다음과 같습니다. <br>-더 강력한 액세스 제어 (RBAC)<br>-더 나은 감사<br>-Azure Resource Manager 기반 배포 및 거 버 넌 스<br>-관리 되는 id에 대 한 액세스<br>-암호에 대 한 Azure Key Vault 액세스<br>-Azure AD 기반 인증<br>-보다 쉬운 보안 관리를 위한 태그 및 리소스 그룹 지원|
|가상 컴퓨터를 새 AzureRM 리소스에 마이그레이션해야 합니다.|가상 컴퓨터에 대 한 Azure Resource Manager를 사용 하 여 보안 강화 기능을 제공 합니다. 해당 지역은 다음과 같습니다. <br>-더 강력한 액세스 제어 (RBAC)<br>-더 나은 감사<br>-Azure Resource Manager 기반 배포 및 거 버 넌 스<br>-관리 되는 id에 대 한 액세스<br>-암호에 대 한 Azure Key Vault 액세스<br>-Azure AD 기반 인증<br>-보다 쉬운 보안 관리를 위한 태그 및 리소스 그룹 지원|
|Batch 계정에서 메트릭 경고 규칙을 구성 해야 합니다.|Azure Batch 계정에서 메트릭 경고 규칙의 구성을 감사 하 여 필요한 메트릭을 사용 하도록 설정 합니다.|
|Batch 계정의 진단 로그를 사용 하도록 설정 해야 합니다.|로그 사용을 감사 하 고 최대 1 년 동안 보관 합니다. 이렇게 하면 보안 인시던트가 발생 하거나 네트워크가 손상 된 경우 조사를 위한 활동 기록이 생성 됩니다.|
|Automation 계정 변수에 암호화를 사용 하도록 설정 해야 합니다.|중요 한 데이터를 저장 하는 경우 Azure Automation 계정 변수 자산의 암호화를 사용 하도록 설정 하는 것이 중요 합니다.|
|App Services의 진단 로그를 사용 하도록 설정 해야 합니다.|앱에서 진단 로그 사용을 감사합니다. 이렇게 하면 보안 인시던트가 발생 하거나 네트워크가 손상 된 경우 조사를 위한 활동 기록이 생성 됩니다.|
|SQL 데이터베이스에 투명한 데이터 암호화를 사용하도록 설정해야 합니다.|SQL 데이터베이스에 대한 투명한 데이터 암호화 상태를 감사합니다.|
|SQL server 감사를 사용 하도록 설정 해야 합니다.|서버 수준에서 SQL 감사가 있는지 감사 합니다.|
|\[미리 보기]: Azure Security Center에서 암호화 되지 않은 SQL 데이터베이스를 모니터링 합니다.|Azure Security Center는 암호화 되지 않은 SQL 서버나 데이터베이스를 권장 하는 대로 모니터링 합니다.|
|\[미리 보기]: Azure Security Center에서 감사 되지 않은 SQL 데이터베이스를 모니터링 합니다.|Azure Security Center SQL 감사를 설정 하지 않은 SQL server 및 데이터베이스를 모니터링 하는 것이 좋습니다.|
|\[Preview]: 시스템 업데이트를 컴퓨터에 설치 해야 합니다.|Azure Security Center은 권장 사항에 따라 서버에서 누락 된 보안 시스템 업데이트를 모니터링 합니다.|
|\[미리 보기]: 저장소 계정에 대 한 누락 된 blob 암호화 감사|Blob 암호화를 사용 하지 않는 저장소 계정을 감사 합니다. 이는 다른 공급자의 저장소가 아닌 Microsoft storage 리소스 유형에만 적용 됩니다. Azure Security Center는 권장 된 대로 네트워크 just-in-time 액세스를 모니터링할 수 있습니다.|
|\[미리 보기]: Just-in-time 네트워크 액세스 제어를 가상 컴퓨터에 적용 해야 합니다.|Azure Security Center는 권장 된 대로 네트워크 just-in-time 액세스를 모니터링할 수 있습니다.|
|\[미리 보기]: 적응형 응용 프로그램 제어는 가상 컴퓨터에서 사용 하도록 설정 해야 합니다.|Azure Security Center에서 가능한 응용 프로그램 허용 목록 구성을 모니터링 합니다.|
|\[미리 보기]: 가상 컴퓨터에 대 한 누락 된 네트워크 보안 그룹을 구성 해야 합니다.|Azure Security Center은 권장 사항에 따라 허용 규칙이 너무 많은 네트워크 보안 그룹을 모니터링 합니다.|
|\[미리 보기]: 컴퓨터에서 보안 구성의 취약성을 재구성 해야 합니다.|Azure Security Center 구성 된 기준을 충족 하지 않는 서버를 모니터링 하는 것이 좋습니다.| 
|\[Preview]: Endpoint protection을 가상 컴퓨터에 설치 해야 합니다.|Azure Security Center는 Microsoft System Center Endpoint Protection 에이전트가 설치 되어 있지 않은 서버를 모니터링 하는 것이 좋습니다.|
|\[미리 보기]: 디스크 암호화를 가상 컴퓨터에 적용 해야 합니다.|Azure Security Center 권장 된 대로 디스크 암호화를 사용 하지 않는 가상 컴퓨터를 모니터링 합니다.|
|\[미리 보기]: 취약점 평가 솔루션에서 취약점을 수정 해야 합니다.|취약성 평가 솔루션에 의해 검색 된 취약점과 Azure Security Center의 취약점 평가 솔루션이 없는 Vm을 모니터링 하는 것이 좋습니다.|
|\[미리 보기]: Azure Security Center에서 보호 되지 않는 웹 응용 프로그램을 모니터링 합니다.|Azure Security Center 웹 응용 프로그램 방화벽 보호가 권장 된 대로 웹 응용 프로그램을 모니터링 합니다.|
|\[Preview]: Endpoint protection 솔루션을 가상 컴퓨터에 설치 해야 합니다.|Azure Security Center는 권장 된 대로 차세대 방화벽 보호가 없는 네트워크 엔드포인트을 모니터링 합니다.|
|\[미리 보기]: SQL 데이터베이스에 대 한 취약성을 재구성 해야 합니다.|취약성 평가 검사 결과를 모니터링 하 고 데이터베이스 취약성을 해결 하는 방법을 권장 합니다.|
|\[미리 보기]: 구독에 대해 최대 3 개의 소유자를 지정 해야 합니다.|손상 된 소유자가 위반 가능성을 줄이려면 최대 3 개의 구독 소유자를 지정 하는 것이 좋습니다.|
|\[미리 보기]: 구독에 둘 이상의 소유자가 할당 되어 있어야 합니다.|관리자 액세스 중복성을 보장 하기 위해 둘 이상의 구독 소유자를 지정 하는 것이 좋습니다.|
|\[미리 보기]: 구독에 대 한 소유자 권한이 있는 계정에서 MFA를 사용 하도록 설정 해야 합니다. |계정 또는 리소스의 위반을 방지 하려면 소유자 권한이 있는 모든 구독 계정에 대해 MFA (multi-factor authentication)를 사용 하도록 설정 해야 합니다.|
|\[미리 보기]: 쓰기 권한이 있는 구독 계정에서 MFA를 사용 하도록 설정 해야 합니다.|계정이 나 리소스의 위반을 방지 하기 위해 쓰기 권한이 있는 모든 구독 계정에 대해 multi-factor authentication을 사용 하도록 설정 해야 합니다.|
|\[미리 보기]: 읽기 권한이 있는 구독 계정에서 MFA를 사용 하도록 설정 해야 합니다.|계정 또는 리소스의 위반을 방지 하기 위해 읽기 권한이 있는 모든 구독 계정에 대해 multi-factor authentication을 사용 하도록 설정 해야 합니다.|
|\[미리 보기]: 소유자 권한이 있는 사용 되지 않는 계정은 구독에서 제거 해야 합니다.|소유자 권한이 있는 사용 되지 않는 계정은 구독에서 제거 해야 합니다. 사용 되지 않는 계정이 로그인에서 차단 되었습니다.|
|\[미리 보기]: 사용 되지 않는 계정을 구독에서 제거 해야 합니다.|더 이상 사용되지 않는 계정을 구독에서 제거해야 합니다. 사용 되지 않는 계정이 로그인에서 차단 되었습니다.|
|\[미리 보기]: 소유자 권한이 있는 외부 계정을 구독에서 제거 해야 합니다.|권한 액세스를 방지 하려면 소유자 권한이 있는 외부 계정을 구독에서 제거 해야 합니다.|
|\[미리 보기]: 쓰기 권한이 있는 외부 계정을 구독에서 제거 해야 합니다.|모니터링 되지 않는 액세스를 방지 하려면 쓰기 권한이 있는 외부 계정을 구독에서 제거 해야 합니다.|
|\[미리 보기]: 읽기 권한이 있는 외부 계정을 구독에서 제거 해야 합니다.|모니터링 되지 않는 액세스를 방지 하려면 읽기 권한이 있는 외부 계정을 구독에서 제거 해야 합니다.|




## <a name="next-steps"></a>다음 단계
이 문서에서는 Security Center에서 보안 정책을 구성하는 방법을 알아보았습니다. Security Center에 대해 자세히 알아보려면 다음 문서를 참조 하세요.

* [Azure Security Center 계획 및 운영 가이드](security-center-planning-and-operations-guide.md): Azure Security Center에서 디자인 고려 사항을 계획 하 고 이해 하는 방법을 알아봅니다.
* [Azure Security Center에서 보안 상태 모니터링](security-center-monitoring.md): Azure 리소스의 상태를 모니터링하는 방법을 알아봅니다.
* [Azure Security Center에서 보안 경고 관리 및 대응](security-center-managing-and-responding-alerts.md): 보안 경고를 관리하고 대응하는 방법을 알아봅니다.
* [Azure Security Center에서 파트너 솔루션 모니터링](security-center-partner-solutions.md): 파트너 솔루션의 상태를 모니터링하는 방법을 알아봅니다.
* [Azure Security Center FAQ](security-center-faq.md): 서비스 사용에 관한 질문과 대답을 가져옵니다.
* [Azure 보안 블로그](https://blogs.msdn.com/b/azuresecurity/) - Azure 보안 및 규정 준수에 관한 블로그 게시물을 찾습니다.

Azure Policy에 대 한 자세한 내용은 [Azure Policy 무엇입니까?](../governance/policy/overview.md)를 참조 하세요.
