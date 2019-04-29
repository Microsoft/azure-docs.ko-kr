---
title: Azure Security Center에서 모니터링되는 Azure 정책 정의 | Microsoft Docs
description: Azure Security Center에서 모니터링되는 Azure 정책 정의를 다룹니다.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: c89cb1aa-74e8-4ed1-980a-02a7a25c1a2f
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/15/2019
ms.author: rkarlin
ms.openlocfilehash: 9d9369afd36f64c27cd2222cab0de5912aa913de
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60909199"
---
# <a name="azure-security-policies-monitored-by-security-center"></a>Security Center에서 모니터링 azure 보안 정책
이 문서에서는 Azure Security Center에서 모니터링할 수 있는 Azure 정책 정의의 목록을 제공 합니다. 보안 정책에 대한 자세한 내용은 [보안 정책 작업](tutorial-security-policy.md)을 참조하세요.

## <a name="available-security-policies"></a>사용 가능한 보안 정책

Security Center에서 모니터링 되는 기본 제공 정책에 대 한 자세한 내용은 다음 표를 참조 합니다.

| 정책 | 정책에서 수행하는 작업 |
| --- | --- |
|Azure Service Fabric 및 virtual machine scale sets에서 로그의 진단 설정 감사|활동 추적을 인시던트 또는 손상 후 조사를 위해 사용할 수 있도록 로그를 사용 하는 것이 좋습니다.|
|Event Hubs 네임 스페이스 권한 부여 규칙을 감사|Azure Event Hubs 클라이언트에 모든 큐와 토픽 네임 스페이스에 대 한 액세스를 제공 하는 네임 스페이스 수준 액세스 정책을 사용 하지 않아야 합니다. 최소 권한 보안 모델을 맞추려면 큐 및 토픽만 특정 엔터티에 액세스할 수 있도록 엔터티 수준 액세스 정책을 만들어야 합니다.|
|Event Hubs 엔터티에서 권한 부여 규칙의 존재 여부 감사|최소 권한 액세스 권한을 부여 하려면 Event Hubs 엔터티에서 권한 부여 규칙의 존재 여부를 감사 합니다.|
|스토리지 계정에 대한 무제한 네트워크 액세스 감사|스토리지 계정 방화벽 설정에서 무제한 네트워크 액세스를 감사합니다. 허용 된 네트워크의 응용 프로그램만 저장소 계정에 액세스할 수 있도록 네트워크 규칙을 구성 합니다. 특정 인터넷 또는 온-프레미스 클라이언트에서 연결을 허용 하도록 범위는 특정 Azure virtual network 또는 공용 인터넷 ip 트래픽에 대 한 액세스를 부여 합니다.|
|사용자 지정 RBAC 규칙의 사용 감사|오류가 발생 하기 쉬운 사용자 지정 역할 기반 액세스 제어 (RBAC) 역할을 하는 대신 "소유자, 참가자, 읽기 권한자"와 같은 기본 제공 역할을 감사 합니다. 사용자 지정 역할을 사용 하 여 예외로 처리 되 고 엄격한 검토 및 위협 모델링 필요 합니다.|
|Azure Stream Analytics에서 진단 로그의 사용 감사|감사 로그를 사용 하도록 설정 하 고 최대 1 년에 대 한 유지 하세요. 이 보안 인시던트 발생 하거나 네트워크 손상 될 경우 조사를 위해 작업 내역을 만듭니다.|
|스토리지 계정에 대한 보안 전송 감사|저장소 계정에서 보안 전송의 요구 사항을 감사 합니다. 보안 전송은 사용자의 스토리지 계정이 보안 연결(HTTPS)에서 오는 요청만 수락하도록 강제 적용하는 옵션입니다. HTTPS 사용 하면 서버와 서비스 간 인증 합니다. 또한 man-에--the-middle, 도청 및 세션 하이재킹 등의 네트워크 레이어 공격 으로부터 전송 중인 데이터를 보호 합니다.|
|SQL Server에 대 한 Azure Active Directory 관리자 프로 비전 감사|Azure AD 인증을 사용 하려면 SQL Server에 대 한 Azure Active Directory (Azure AD) 관리자를 프로 비전 감사 합니다. Azure AD 인증 간소화 된 사용 권한 관리 및 데이터베이스 사용자 및 다른 Microsoft 서비스의 중앙 집중식된 id 관리를 지원합니다.|
|Service Bus 네임스페이스의 권한 부여 규칙 감사|Azure Service Bus 클라이언트에 모든 큐와 토픽 네임 스페이스에 대 한 액세스를 제공 하는 네임 스페이스 수준 액세스 정책을 사용 하지 않아야 합니다. 최소 권한 보안 모델을 맞추려면 큐 및 토픽만 특정 엔터티에 액세스할 수 있도록 엔터티 수준 액세스 정책을 만듭니다.|
|Service Bus에서 진단 로그의 사용 감사|감사 로그를 사용 하도록 설정 하 고 1 년에 등록 유지 하세요. 이 보안 인시던트 발생 하거나 네트워크 손상 될 경우 조사를 위해 작업 내역을 만듭니다.|
|Service Fabric에서 EncryptAndSign에 대한 ClusterProtectionLevel 속성의 설정 감사|서비스 패브릭은 세 가지 수준의 기본 클러스터 인증서를 사용 하는 노드 간 통신에 대 한 보호를 제공 합니다. None, 서명 및 암호화 및 서명 합니다. 모든 노드 간 메시지가 암호화되고 디지털로 서명될 수 있게 보호 수준을 설정합니다.|
|Service Fabric의 클라이언트 인증에 대한 Azure Active Directory의 사용 감사|Service Fabric에서 Azure AD 통해서만 클라이언트 인증 사용을 감사 합니다.|
|검색 서비스에 대해 진단 로그의 사용 감사|감사 로그를 사용 하도록 설정 하 고 최대 1 년에 대 한 유지 하세요. 이 보안 인시던트 발생 하거나 네트워크 손상 될 경우 조사를 위해 작업 내역을 만듭니다.|
|보안에 대 한 연결만 Azure Cache Redis에 대 한 사용 감사|감사만 Redis에 대 한 Azure 캐시에는 SSL 통해 연결을 사용 합니다. 보안 연결을 사용 하면 서버와 서비스 간 인증 합니다. 또한 man-에--the-middle, 도청 및 세션 하이재킹 등의 네트워크 레이어 공격 으로부터 전송 중인 데이터를 보호 합니다.|
|Logic Apps에서 진단 로그의 사용 감사|감사 로그를 사용 하도록 설정 하 고 최대 1 년에 대 한 유지 하세요. 이 보안 인시던트 발생 하거나 네트워크 손상 될 경우 조사를 위해 작업 내역을 만듭니다.|
|Key Vault에서 진단 로그의 사용 감사|감사 로그를 사용 하도록 설정 하 고 최대 1 년에 대 한 유지 하세요. 이 보안 인시던트 발생 하거나 네트워크 손상 될 경우 조사를 위해 작업 내역을 만듭니다.|
|Event Hubs에서 진단 로그의 사용을 감사합니다|감사 로그를 사용 하도록 설정 하 고 최대 1 년에 대 한 유지 하세요. 이 보안 인시던트 발생 하거나 네트워크 손상 될 경우 조사를 위해 작업 내역을 만듭니다.|
|Azure Data Lake Store에서 진단 로그의 사용 감사|감사 로그를 사용 하도록 설정 하 고 최대 1 년 유지 하세요. 이 보안 인시던트 발생 하거나 네트워크 손상 될 경우 조사를 위해 작업 내역을 만듭니다.|
|Data Lake Analytics에서 진단 로그의 사용 감사|감사 로그를 사용 하도록 설정 하 고 최대 1 년에 대 한 유지 하세요. 이 보안 인시던트 발생 하거나 네트워크 손상 될 경우 조사를 위해 작업 내역을 만듭니다.|
|클래식 스토리지 계정의 사용 감사|저장소 계정에 대 한 Azure Resource Manager를 사용 하 여 향상 된 보안 기능을 제공 합니다. 내용은 다음과 같습니다. <br>-보다 강력한 액세스 제어 (RBAC)<br>기능 감사 향상<br>-Azure Resource Manager 기반 배포 및 거 버 넌 스<br>관리 되는 id에 대 한 액세스<br>Azure Key vault에 대해 액세스 암호<br>-Azure AD 기반 인증<br>태그 및 보안 관리를 간소화 하기 위해 리소스 그룹에 대 한 지원|
|클래식 가상 머신의 사용 감사|가상 컴퓨터에 대 한 Azure Resource Manager를 사용 하 여 향상 된 보안 기능을 제공 합니다.  내용은 다음과 같습니다. <br>-보다 강력한 액세스 제어 (RBAC)<br>기능 감사 향상<br>-Azure Resource Manager 기반 배포 및 거 버 넌 스<br>관리 되는 id에 대 한 액세스<br>Azure Key vault에 대해 액세스 암호<br>-Azure AD 기반 인증<br>태그 및 보안 관리를 간소화 하기 위해 리소스 그룹에 대 한 지원|
|배치 계정의 메트릭 경고 규칙 구성 감사|필요한 메트릭을 사용 하도록 Azure Batch 계정에서 메트릭 경고 규칙의 감사 구성 합니다.|
|배치 계정에서 진단 로그의 사용 감사|감사 로그를 사용 하도록 설정 하 고 최대 1 년에 대 한 유지 하세요. 이 보안 인시던트 발생 하거나 네트워크 손상 될 경우 조사를 위해 작업 내역을 만듭니다.|
|Automation 계정에 대 한 변수의 암호화를 사용 하도록 설정 감사|중요 한 데이터를 저장 하는 경우 Azure Automation 계정에 대 한 변수 자산 암호화를 사용 하도록 설정 하는 것이 반드시 합니다.|
|App Service에서 진단 로그의 사용을 감사합니다|앱에서 진단 로그 사용을 감사합니다. 이 보안 인시던트 발생 하거나 네트워크 손상 될 경우 조사를 위해 작업 내역을 만듭니다.|
|투명한 데이터 암호화 상태 감사|SQL 데이터베이스에 대한 투명한 데이터 암호화 상태를 감사합니다.|
|SQL Server 수준 감사 설정 감사|SQL 서버 수준 감사의 존재 여부를 감사 합니다.|
|\[미리 보기]: Azure Security Center에서 암호화되지 않은 SQL 데이터베이스 모니터링|Azure Security Center는 암호화 되지 않은 SQL 서버나 데이터베이스 권장 사항에 따라 모니터링 합니다.|
|\[미리 보기]: Azure Security Center에서 감사되지 않은 SQL 데이터베이스 모니터링|Azure Security Center는 SQL server 및 SQL 감사 권장 사항에 따라 설정 되지 않은 데이터베이스를 모니터링 합니다.|
|\[미리 보기]: Azure Security Center에서 누락된 시스템 업데이트 모니터링|Azure Security Center 권장 사항에 따라 서버에서 누락 된 보안 시스템 업데이트를 모니터링 합니다.|
|\[미리 보기]: 스토리지 계정에 대해 누락된 Blob 암호화 감사|Blob 암호화를 사용 하지 않는 저장소 계정을 감사 합니다. Microsoft 저장소 리소스 형식에 다른 공급자의 저장소 없습니다만 적용 됩니다. Azure Security Center 권장 사항에 따라 just in time 액세스 가능한 네트워크를 모니터링 합니다.|
|\[미리 보기]: Azure Security Center에서 just in time 액세스 가능한 네트워크 모니터링|Azure Security Center 권장 사항에 따라 just in time 액세스 가능한 네트워크를 모니터링 합니다.|
|\[미리 보기]: Azure Security Center에서 가능한 앱 허용 목록 모니터링|Azure Security Center 모니터링 가능한 응용 프로그램 허용 목록을 구성 합니다.|
|\[미리 보기]: Azure Security Center에서 관대한 네트워크 액세스 모니터링|Azure Security Center에서 모니터링은 네트워크 보안 그룹 규칙이 너무 관대 한 권장 사항에 따라 합니다.|
|\[미리 보기]: Azure Security Center에서 OS 취약성 모니터링|Azure Security Center 권장 사항에 따라 구성 된 기준을 만족 하지 않는 서버를 모니터링 합니다.| 
|\[미리 보기]: Azure Security Center에서 누락된 Endpoint Protection 모니터링|Azure Security Center 권장 사항에 따라 설치 된 Microsoft System Center Endpoint Protection 에이전트 없는 서버를 모니터링 합니다.|
|\[미리 보기]: Azure Security Center에서 암호화 되지 않은 VM 디스크를 모니터링 합니다.|Azure Security Center 권장 사항에 따라 사용 하도록 설정 하는 디스크 암호화 되지 않은 가상 컴퓨터를 모니터링 합니다.|
|\[미리 보기]: Azure Security Center의 VM 취약점 모니터|취약성 평가 솔루션 및 취약성 평가 솔루션에에서 없는 Azure Security Center 권장 사항에 따라 Vm을 통해 검색 되는 취약점을 모니터링 합니다.|
|\[미리 보기]: Azure Security Center에서 보호되지 않은 웹 애플리케이션 모니터링|Azure Security Center에서 모니터링 웹 응용 프로그램 권장 사항에 따라 웹 응용 프로그램 방화벽 보호 부족 합니다.|
|\[미리 보기]: Azure Security Center에서 보호되지 않은 네트워크 엔드포인트 모니터링|Azure Security Center에서 모니터링 되지 않은 다음 세대 방화벽 보호 권장 사항에 따라 끝점 네트워크입니다.|
|\[미리 보기]: Azure Security Center에서 SQL 취약성 평가 결과 모니터링|모니터 취약성 평가 검사 결과 및 데이터베이스 취약성을 해결 하는 방법을 권장 합니다.|
|\[미리 보기]: 구독에 대한 최대 소유자 수 감사|손상 된 소유자가 위반에 대 한 가능성을 줄이려면 최대 3 명의 구독 소유자를 지정 하는 것이 좋습니다.|
|\[미리 보기]: 구독에 대한 최소 소유자 수 감사|관리자가 액세스할 수 있도록 둘 이상의 구독 소유자를 지정 하는 것이 좋습니다 중복성입니다.|
|\[미리 보기]: 구독에서 MFA가 사용하도록 설정되지 않은 소유자 권한이 있는 계정 감사|Multi-factor authentication (MFA) 계정 또는 리소스 위반을 방지 하려면 소유자 권한이 있는 모든 구독 계정에 대해 활성화 되어야 합니다.|
|\[미리 보기]: 구독에서 MFA가 사용하도록 설정되지 않은 쓰기 권한이 있는 계정 감사|Multi-factor authentication 계정 또는 리소스 위반을 방지 하려면 쓰기 권한이 있는 모든 구독 계정에 대해 활성화 되어야 합니다.|
|\[미리 보기]: 구독에서 MFA가 사용하도록 설정되지 않은 읽기 권한이 있는 계정 감사|Multi-factor authentication 계정 또는 리소스 위반을 방지 하기 위한 읽기 권한이 있는 모든 구독 계정에 대해 활성화 되어야 합니다.|
|\[미리 보기]: 구독에서 소유자 권한이 있는 사용되지 않는 계정 감사|소유자 권한이 있는 사용 되지 않는 계정은 구독에서 제거 해야 합니다. 사용 되지 않는 계정 로그인에서 차단 되었습니다.|
|\[미리 보기]: 구독에서 사용되지 않는 계정 감사|더 이상 사용되지 않는 계정을 구독에서 제거해야 합니다. 사용 되지 않는 계정 로그인에서 차단 되었습니다.|
|\[미리 보기]: 구독에서 소유자 권한이 있는 외부 계정 감사|소유자 권한이 있는 외부 계정 권한 액세스를 방지 하기 위해 구독에서 제거 해야 합니다.|
|\[미리 보기]: 구독에서 쓰기 권한이 있는 외부 계정 감사|이 있는 외부 계정 모니터링 되지 않는 액세스를 방지 하기 위해 구독에서 제거 해야 하는 권한을 작성 합니다.|
|\[미리 보기]: 구독에서 읽기 권한이 있는 외부 계정 감사|모니터링 되지 않는 액세스를 방지 하기 위해 구독에서 읽기 권한이 있는 외부 계정은 제거 해야 합니다.|




## <a name="next-steps"></a>다음 단계
이 문서에서는 Security Center에서 보안 정책을 구성하는 방법을 알아보았습니다. Security Center에 대 한 자세한 내용은 다음 문서를 참조 하세요.

* [Azure Security Center 계획 및 작업 가이드](security-center-planning-and-operations-guide.md): 계획 및 Azure Security Center에서 디자인 고려 사항을 이해 하는 방법에 알아봅니다.
* [Azure Security Center에서 보안 상태 모니터링](security-center-monitoring.md): Azure 리소스의 상태를 모니터링하는 방법을 알아봅니다.
* [Azure Security Center에서 보안 경고 관리 및 응답](security-center-managing-and-responding-alerts.md): 보안 경고를 관리하고 응답하는 방법을 알아봅니다.
* [Azure Security Center를 사용하여 파트너 솔루션 모니터링](security-center-partner-solutions.md): 파트너 솔루션의 상태를 모니터링하는 방법을 알아봅니다.
* [Azure Security Center FAQ](security-center-faq.md): 서비스 사용에 관한 질문과 대답에 대한 답을 가져옵니다.
* [Azure 보안 블로그](https://blogs.msdn.com/b/azuresecurity/): Azure 보안 및 규정 준수에 관한 블로그 게시물을 찾습니다.

Azure Policy에 대 한 자세한 내용은 참조 하세요 [Azure Policy 란?](../governance/policy/overview.md)합니다.
