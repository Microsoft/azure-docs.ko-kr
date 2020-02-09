---
title: AKS 가상 컴퓨터 호스트의 보안 강화
description: AKS VM 호스트 OS의 보안 강화에 대한 자세한 정보
services: container-service
author: saudas
ms.service: container-service
ms.topic: article
ms.date: 09/11/2019
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: 8b7e50fdc02ab47c50cecb95073f1b51393db898
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73885640"
---
# <a name="security-hardening-in-aks-virtual-machine-hosts"></a>AKS 가상 컴퓨터 호스트의 보안 강화 

AKS (Azure Kubernetes Service)는 SOC, ISO, PCI DSS 및 HIPAA 표준과 호환 되는 보안 서비스입니다. 이 문서에서는 AKS 가상 컴퓨터 호스트에 적용 되는 보안 강화에 대해 설명 합니다. AKS 보안에 대한 자세한 내용은 [AKS (Azure Kubernetes Service)의 응용 프로그램 및 클러스터에 대한 보안 개념](https://docs.microsoft.com/azure/aks/concepts-security)을 참조 하세요.

AKS 클러스터는 보안에 최적화 된 OS를 실행 하는 호스트 가상 컴퓨터에 배포 됩니다. 이 호스트 OS는 현재 추가 보안 강화 단계 집합이 적용 된 Ubuntu 16.04 이미지를 기반으로 합니다 (보안 강화 세부 정보 참조).   

보안이 강화 된 호스트 OS의 목표는 공격 노출 영역을 줄이고 컨테이너 배포를 안전한 방식으로 허용 하는 것입니다. 

> [!Important]
> 보안이 강화 된 OS는 CIS 하도록 벤치마킹이 아닙니다. CIS 벤치 마크와 겹치는 경우 목표는 CI와 호환 되지 않습니다. 호스트 OS 강화의 목표는 Microsoft의 내부 호스트 보안 표준과 일관 된 보안 수준을 유지 하는 것입니다. 

## <a name="security-hardening-features"></a>보안 강화 기능 

* AKS은 기본적으로 보안에 최적화 된 호스트 OS를 제공 합니다. 현재 다른 운영 체제를 선택할 수 있는 옵션은 없습니다. 

* Azure는 AKS 가상 머신 호스트에 일일 패치 (보안 패치 포함)를 적용 합니다. 이러한 패치 중 일부는 다시 부팅 해야 하지만 다른 패치는 그렇지 않습니다. 필요에 따라 AKS VM 호스트 재부팅을 예약 해야 합니다. AKS 패치를 자동화 하는 방법에 대한 지침은 [AKS 노드 패치](https://docs.microsoft.com/azure/aks/node-updates-kured)를 참조 하세요.

다음은 보안에 최적화 된 호스트 OS를 생성 하기 위해 AKS에서 구현 되는 이미지 강화 작업의 요약입니다. [이 GitHub 프로젝트에서](https://github.com/Azure/aks-engine/projects/7)작업을 구현 했습니다.  

AKS 엔진은 현재 특정 보안 표준을 준수 하거나 준수 하지 않지만, 해당 되는 경우 CI (인터넷 보안 센터) 감사 Id가 편의를 위해 제공 됩니다. 

## <a name="whats-configured"></a>구성 된 기능

| 들  | 감사 설명| 
|---|---|
| 1.1.1.1 |Cramfs 파일 시스템의 탑재를 사용 하지 않도록 설정|
| 1.1.1.2 |Freevxfs 파일 시스템의 탑재를 사용 하지 않도록 설정 했는지 확인 합니다.|
| 1.1.1.3 |Jffs2 파일 시스템의 탑재를 사용 하지 않도록 설정|
| 1.1.1.4 |HFS 파일 시스템의 탑재를 사용 하지 않도록 설정 했는지 확인 합니다.|
| 1.1.1.5 |HFS Plus 파일 시스템의 탑재를 사용 하지 않도록 설정 했는지 확인 합니다.|
|1.4.3 |단일 사용자 모드에 대한 인증이 필요한 지 확인 |
|1.7.1.2 |로컬 로그인 경고 배너가 올바르게 구성 되어 있는지 확인 합니다. |
|1.7.1.3 |원격 로그인 경고 배너가 올바르게 구성 되어 있는지 확인 합니다. |
|1.7.1.5 |/Etc/issue에 대한 사용 권한이 구성 되었는지 확인 |
|1.7.1.6 |/Etc/issue.net에 대한 사용 권한이 구성 되었는지 확인 |
|2.1.5 |--스트리밍-연결-유휴 시간 제한이 0으로 설정 되어 있지 않은지 확인 합니다. |
|3.1.2 |패킷 리디렉션 전송이 사용 하지 않도록 설정 되어 있는지 확인 |
|3.2.1 |원본으로 라우팅된 패키지가 허용 되지 않는지 확인 합니다. |
|3.2.2 |ICMP 리디렉션이 허용 되지 않는지 확인 합니다. |
|3.2.3 |보안 ICMP 리디렉션이 허용 되지 않는지 확인 합니다. |
|3.2.4 |의심 스러운 패킷이 기록 되는지 확인 |
|3.3.1 |IPv6 라우터 알림이 수락 되지 않았는지 확인 합니다. |
|3.5.1 |DCCP를 사용 하지 않도록 설정 |
|3.5.2 |SCTP를 사용 하지 않도록 설정 |
|3.5.3 |RDS를 사용 하지 않도록 설정 |
|3.5.4 |TIPC를 사용 하지 않도록 설정 |
|4.2.1.2 |로깅이 구성 되어 있는지 확인 |
|5.1.2 |/Etc/crontab에 대한 사용 권한이 구성 되었는지 확인 |
|5.2.4 |SSH X11 전달이 사용 하지 않도록 설정 되어 있는지 확인 |
|5.2.5 |SSH MaxAuthTries를 4 이하로 설정 해야 합니다. |
|5.2.8 |SSH 루트 로그인을 사용 하지 않도록 설정 |
|5.2.10 |SSH PermitUserEnvironment 사용 안 함 확인 |
|5.2.11 |승인 된 최대 알고리즘만 사용 되는지 확인 |
|5.2.12 |SSH 유휴 시간 제한 간격을 구성 해야 합니다. |
|5.2.13 |SSH LoginGraceTime 1 분 이하로 설정 되었는지 확인 합니다. |
|5.2.15 |SSH 경고 배너가 구성 되어 있는지 확인 합니다. |
|5.3.1 |암호 만들기 요구 사항이 구성 되어 있는지 확인 |
|5.4.1.1 |암호 만료가 90 일 이내 인지 확인 |
|5.4.1.4 |비활성 암호 잠금이 30 일 이내 인지 확인 |
|5.4.4 |기본 사용자 umask 027 이상 인지 확인 |
|5.6 |Su 명령에 대한 액세스가 제한 되어 있는지 확인|

## <a name="additional-notes"></a>추가적인 참고 사항
 
* 공격 노출 영역을 더 줄이기 위해 일부 불필요 한 커널 모듈 드라이버는 OS에서 사용 하지 않도록 설정 되었습니다. 

* 보안 강화 된 OS는 AKS 플랫폼 외부에서 지원 되지 않습니다. 

## <a name="next-steps"></a>다음 단계  

AKS 보안에 대한 자세한 내용은 다음 문서를 참조 하세요. 

[AKS(Azure Kubernetes Service)](https://docs.microsoft.com/azure/aks/intro-kubernetes)

[AKS 보안 고려 사항](https://docs.microsoft.com/azure/aks/concepts-security)

[AKS 모범 사례](https://docs.microsoft.com/azure/aks/best-practices)
