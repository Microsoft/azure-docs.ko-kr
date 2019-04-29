---
title: Azure Service Fabric 보안 검사 목록 | Microsoft Docs
description: 이 문서에서는 Azure Fabric 보안에 대한 검사 목록을 제공합니다.
services: security
documentationcenter: na
author: unifycloud
manager: barbkess
editor: tomsh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/16/2019
ms.author: tomsh
ms.openlocfilehash: 06a1903e5e27d748310c1b7846105b8069b73437
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60611533"
---
# <a name="azure-service-fabric-security-checklist"></a>Azure Service Fabric 보안 검사 목록
이 문서에서는 Azure Service Fabric 환경을 보호하는 데 도움이 되는 사용하기 쉬운 검사 목록을 제공합니다.

## <a name="introduction"></a>소개
Azure Service Fabric은 손쉽게 패키지하고 배포하며 확장 가능하고 안정성이 뛰어난 마이크로 서비스를 관리하는 분산된 시스템 플랫폼입니다. 또한 서비스 패브릭은 클라우드 애플리케이션 개발 및 관리에서 발생하는 중요한 과제를 해결합니다. 개발자와 관리자가 복잡한 인프라 문제를 피하고 업무 수행에 필수적인 까다로운 워크로드를 확장 가능하고 신뢰할 수 있으며 관리가 가능하도록 구현하는 데 집중할 수 있습니다.

## <a name="checklist"></a>검사 목록
다음 검사 목록을 사용하여 안전한 Azure Service Fabric 솔루션의 관리 및 구성에서 중요한 문제를 간과하지 않게 확인할 수 있습니다.


|검사 목록 범주| 설명 |
| ------------ | -------- |
|[RBAC(역할 기반 액세스 제어)](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security-roles) | <ul><li>액세스 제어를 사용하면 클러스터 관리자가 사용자 그룹마다 특정 클러스터 작업에 대한 액세스를 제한하여 클러스터의 보안을 강화할 수 있습니다.</li><li>관리자는 관리 기능(읽기/쓰기 기능만 포함)에 대한 모든 권한을 가집니다. </li><li> 사용자는 기본적으로 관리 기능(예: 쿼리 기능)에 대한 읽기 권한 및 애플리케이션과 서비스를 확인하는 기능만 갖습니다.</li></ul>|
|[X.509 인증서 및 Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security) | <ul><li>프로덕션 워크로드를 실행하는 클러스터에 사용되는 [인증서](https://docs.microsoft.com/dotnet/framework/wcf/feature-details/working-with-certificates)는 올바르게 구성된 Windows Server 인증서 서비스를 사용하여 만들어지거나 승인된 [CA(인증 기관)](https://en.wikipedia.org/wiki/Certificate_authority)에서 획득해야 합니다.</li><li>프로덕션 환경에서 [MakeCert.exe](https://msdn.microsoft.com/library/windows/desktop/aa386968.aspx)와 같은 도구를 사용하여 만든 [임시 또는 테스트 인증서](https://docs.microsoft.com/dotnet/framework/wcf/feature-details/how-to-create-temporary-certificates-for-use-during-development)를 사용하지 마세요. </li><li>[자체 서명된 인증서](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-x509-security)를 사용할 수 있지만 프로덕션이 아닌 테스트 클러스터에 대해서만 이러한 인증서를 사용해야 합니다.</li></ul>|
|[클러스터 보안](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security) | <ul><li>클러스터 보안 시나리오에는 노드 간 보안, 클라이언트-노드 보안, [역할 기반 액세스 제어(RBAC)](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security-roles)가 포함됩니다.</li></ul>|
|[클러스터 인증](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) | <ul><li>클러스터 페더레이션에 대한 [노드 간 통신](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/service-fabric/service-fabric-cluster-security.md)을 인증합니다. </li></ul>|
|[서버 인증](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) | <ul><li>관리 클라이언트에 대해 [클러스터 관리 엔드포인트](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-portal)를 인증합니다.</li></ul>|
|[애플리케이션 보안](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm)| <ul><li>애플리케이션 구성 값의 암호화 및 암호 해독</li><li>   복제 중에 노드 간 데이터 암호화</li></ul>|
|[클러스터 인증서](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-x509-security) | <ul><li>이 인증서는 클러스터에서 노드 간의 통신을 보호해야 합니다.</li><li>    지문 섹션에서 기본 인증서의 지문 및 ThumbprintSecondary 변수에서 보조 인증서의 지문을 설정합니다.</li></ul>|
|[ServerCertificate](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-x509-security)| <ul><li>이 인증서가 클러스터에 연결하려고 시도할 때 클라이언트에 표시됩니다. 업그레이드에 두 개의 다른 서버 인증서, 기본 및 보조 인증서를 사용할 수 있습니다.</li></ul>|
|ClientCertificateThumbprints| <ul><li>인증된 클라이언트에 설치하려는 인증서의 집합입니다. </li></ul>|
|ClientCertificateCommonNames| <ul><li>CertificateCommonName에 대한 첫 번째 클라이언트 인증서의 일반 이름을 설정합니다. CertificateIssuerThumbprint는 이 인증서의 발급자 지문입니다. </li></ul>|
|ReverseProxyCertificate| <ul><li>이는 [역방향 프록시](https://docs.microsoft.com/azure/service-fabric/service-fabric-reverseproxy)를 보호하려는 경우 지정될 수 있는 선택적인 인증서입니다. </li></ul>|
|Key Vault| <ul><li>Azure에서 Service Fabric 클러스터에 대한 인증서를 관리하는 데 사용됩니다.  </li></ul>|


## <a name="next-steps"></a>다음 단계

- [Service Fabric 보안 모범 사례](azure-service-fabric-security-best-practices.md)
- [서비스 패브릭 클러스터 업그레이드 프로세스 및 사용자 기대 수준](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-upgrade)
- [Visual Studio에서 서비스 패브릭 애플리케이션 관리](https://docs.microsoft.com/azure/service-fabric/service-fabric-manage-application-in-visual-studio).
- [Service Fabric 상태 모델 소개](https://docs.microsoft.com/azure/service-fabric/service-fabric-health-introduction)
