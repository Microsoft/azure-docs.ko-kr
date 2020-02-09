---
title: Microsoft Azure에서 보안 응용 프로그램 디자인
description: 이 문서에서는 웹 응용 프로그램 프로젝트의 요구 사항 및 디자인 단계에서 고려해 야 할 모범 사례를 설명 합니다.
author: TerryLanfear
manager: barbkess
ms.author: terrylan
ms.date: 06/11/2019
ms.topic: article
ms.service: security
ms.subservice: security-develop
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: 316ed596cfa49987e229004c388267286ff50927
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/15/2019
ms.locfileid: "71000976"
---
# <a name="design-secure-applications-on-azure"></a>Azure에서 보안 응용 프로그램 디자인
이 문서에서는 클라우드 용 응용 프로그램을 디자인할 때 고려해 야 할 보안 작업 및 제어를 제공 합니다. Microsoft [SDL (보안 개발 수명 주기)](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx) 의 요구 사항 및 디자인 단계에서 고려해 야 하는 보안 질문 및 개념과 함께 학습 리소스를 다룹니다. 목표는 보다 안전한 응용 프로그램을 디자인 하는 데 사용할 수 있는 활동 및 Azure 서비스를 정의 하는 데 도움을 주는 것입니다.

이 문서에서 다루는 SDL 단계는 다음과 같습니다.

- 학습
- 요구 사항
- 디자인

## <a name="training"></a>학습
클라우드 응용 프로그램 개발을 시작 하기 전에 Azure에서 보안 및 개인 정보를 이해 하는 데 시간이 걸립니다. 이 단계를 수행 하 여 응용 프로그램에서 악용 가능한 취약점의 수 및 심각도를 줄일 수 있습니다. 지속적으로 변화 하는 위협 환경에 적절 하 게 대응할 수 있도록 준비 하는 것이 좋습니다.

학습 단계 중에 다음 리소스를 사용 하 여 개발자가 사용할 수 있는 Azure 서비스와 Azure에 대 한 보안 모범 사례를 숙지 합니다.

  - [Azure에 대 한 개발자 가이드](https://azure.microsoft.com/campaigns/developer-guide/) 에서는 azure를 시작 하는 방법을 보여 줍니다. 이 가이드에서는 응용 프로그램을 실행 하 고, 데이터를 저장 하 고, 인텔리전스를 통합 하 고, IoT 앱을 빌드하고, 보다 효율적이 고 안전한 방법으로 솔루션을 배포 하는 데 사용할 수 있는 서비스를 보여 줍니다.

  - [Azure 개발자를 위한 시작 가이드](../../guides/developer/azure-developer-guide.md) 에서는 개발 요구 사항에 따라 azure 플랫폼을 사용 하 여 시작 하려는 개발자를 위한 필수 정보를 제공 합니다.

  - [Sdk 및 도구](https://docs.microsoft.com/azure/index#pivot=sdkstools) 는 Azure에서 사용할 수 있는 도구에 대해 설명 합니다.

  - [Azure DevOps Services](https://docs.microsoft.com/azure/devops/) 는 개발 공동 작업 도구를 제공 합니다. 이러한 도구에는 고성능 파이프라인, 무료 Git 리포지토리, 구성 가능한 간판 보드, 광범위 한 자동화 된 클라우드 기반 부하 테스트가 포함 됩니다.
    [Devops 리소스 센터](https://docs.microsoft.com/azure/devops/learn/) 는 devops 사례, Git 버전 제어, agile 방법, Microsoft에서 devops를 사용 하는 방법 및 사용자 고유의 devops 진행을 평가 하는 방법을 학습 하기 위한 리소스를 결합 합니다.

  - [프로덕션으로 푸시 하기 전에 고려해 야 할 상위 5 개 보안 항목](https://docs.microsoft.com/learn/modules/top-5-security-items-to-consider/index?WT.mc_id=Learn-Blog-tajanca) 은 Azure에서 웹 응용 프로그램을 보호 하 고 가장 일반적이 고 위험한 웹 응용 프로그램 공격 으로부터 앱을 보호 하는 방법을 보여 줍니다.

  - [Secure Devops Kit For azure](https://azsk.azurewebsites.net/index.html) 는 광범위 한 자동화를 사용 하는 devops 팀의 포괄적인 Azure 구독 및 리소스 보안 요구를 맞춘 하는 스크립트, 도구, 확장 및 자동화 모음입니다. Azure 용 보안 DevOps 키트는 보안을 기본 DevOps 워크플로에 원활 하 게 통합 하는 방법을 보여 줄 수 있습니다. 키트는 SVTs (보안 확인 테스트)와 같은 도구를 처리 합니다 .이 도구를 통해 개발자는 보안 코드를 작성 하 고 코딩 및 초기 개발 단계에서 클라우드 응용 프로그램의 보안 구성을 테스트할 수 있습니다.

  - [Azure 솔루션에 대 한 보안 모범 사례](https://azure.microsoft.com/resources/security-best-practices-for-azure-solutions) 는 azure를 사용 하 여 클라우드 솔루션을 디자인, 배포 및 관리 하는 데 사용할 수 있는 보안 모범 사례 모음을 제공 합니다.

## <a name="requirements"></a>요구 사항
요구 사항 정의 단계는 응용 프로그램의 정의와 릴리스 시 수행할 작업을 정의 하는 중요 한 단계입니다. 요구 사항 단계는 응용 프로그램에 빌드할 보안 제어에 대해 고려해 야 하는 시간 이기도 합니다. 이 단계에서는 보안 응용 프로그램을 릴리스 및 배포 하기 위해 SDL에서 수행 하는 단계를 시작 합니다.

### <a name="consider-security-and-privacy-issues"></a>보안 및 개인 정보 문제 고려
이 단계는 기본적인 보안 및 개인 정보 보호 문제를 고려 하는 데 가장 적합 한 시간입니다. 프로젝트 시작 시 적절 한 수준의 보안 및 개인 정보를 정의 하면 팀이 도움이 됩니다.

- 보안 문제와 관련 된 위험을 이해 합니다.
- 개발 중에 보안 버그를 식별 하 고 해결 합니다.
- 전체 프로젝트에서 설정 된 보안 및 개인 정보 수준을 적용 합니다.

응용 프로그램에 대 한 요구 사항을 작성할 때 응용 프로그램 및 데이터를 안전 하 게 유지 하는 데 도움이 되는 보안 제어를 고려해 야 합니다.

### <a name="ask-security-questions"></a>보안 질문 하기
다음과 같은 보안 질문을 요청 합니다.

  - 응용 프로그램에 중요 한 데이터가 포함 되어 있나요?

  - 내 응용 프로그램은 [연방 금융 기관 조사 Council (FFIEC)](../blueprints/ffiec-analytics-overview.md) 또는 [지불 카드 업계 데이터 보안 표준 (PCI DSS)과 같은 업계 표준 및 규정 준수 프로그램을 준수 하도록 요구 하는 데이터를 수집 하거나 저장 합니다. ](../blueprints/pcidss-analytics-overview.md)?

  - 내 응용 프로그램은 자체 또는 다른 정보와 함께 사용 될 수 있는 중요 한 개인 또는 고객 데이터를 수집 하거나 포함 하 여 단일 사용자를 식별 하거나 연락 하거나 찾을 수 있나요?

  - 내 응용 프로그램이 개인의 의료, 교육, 재무 또는 고용 정보에 액세스 하는 데 사용할 수 있는 데이터를 수집 하거나 포함 합니까? 요구 사항 단계에서 데이터의 민감도를 식별 하면 데이터를 분류 하 고 응용 프로그램에 사용할 데이터 보호 방법을 식별 하는 데 도움이 됩니다.

  - 내 데이터는 어디에 저장 되나요? 응용 프로그램에서 예기치 않은 변경에 사용 하는 저장소 서비스 (예: 느린 응답 시간)를 모니터링 하는 방법을 고려 합니다. 자세한 데이터를 수집 하 고 문제를 자세히 분석 하는 로깅에 영향을 줄 수 있나요?

  - 응용 프로그램을 공용 (인터넷)에서 사용할 수 있습니까? 아니면 내부적 으로만 사용할 수 있나요? 응용 프로그램을 공용에서 사용할 수 있는 경우 수집 될 수 있는 데이터를 잘못 된 방식으로 보호 하려면 어떻게 해야 하나요? 응용 프로그램을 내부적 으로만 사용할 수 있는 경우에는 조직에서 응용 프로그램에 액세스할 수 있는 사용자와 액세스 권한을 보유 해야 하는 기간을 고려해 야 합니다.

  - 응용 프로그램 디자인을 시작 하기 전에 id 모델을 이해 하 고 있습니까? 사용자가 누구 인지 확인 하는 방법 및 사용자에 게 권한이 부여 된 사용자를 확인 하려면 어떻게 해야 하나요?

  - 응용 프로그램에서 중요 한 작업 또는 중요 한 작업 (예: 비용 전송, 문 잠금 해제 또는 의약품 전달)을 수행 하나요?
    중요 한 작업을 수행 하는 사용자에 게 작업을 수행할 수 있는 권한이 있는지 확인 하는 방법 및 사용자에 게 표시 되는 사용자를 인증 하는 방법을 고려 합니다. 인증 (인증)은 인증 된 보안 주체에 게 작업을 수행할 수 있는 권한을 부여 하는 행위입니다. 인증 (인증)은 합법적인 자격 증명을 위해 파티를 어렵게 하는 행위입니다.

  - 응용 프로그램이 사용자가 파일 또는 다른 데이터를 업로드 하거나 다운로드할 수 있도록 허용 하는 등 위험한 소프트웨어 활동을 수행 합니까? 응용 프로그램에서 위험한 활동을 수행 하는 경우 응용 프로그램에서 악성 파일이 나 데이터를 처리 하지 못하도록 보호 하는 방법을 고려 합니다.

### <a name="review-owasp-top-10"></a>OWASP 상위 10 개 검토
[<span class="underline">OWASP 상위 10 개 응용 프로그램 보안 위험</span>](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project)을 검토 하는 것이 좋습니다.
OWASP 상위 10 개는 웹 응용 프로그램에 대 한 중요 한 보안 위험을 해결 합니다.
이러한 보안 위험을 인식 하면 응용 프로그램에서 이러한 위험을 최소화 하는 요구 사항 및 디자인 결정을 내리는 데 도움이 됩니다.

보안 제어를 고려 하 여 위반을 방지 하는 것이 중요 합니다.
그러나 위반이 발생 하 [는 것으로 간주할](https://docs.microsoft.com/azure/devops/learn/devops-at-microsoft/security-in-devops) 수도 있습니다. 보안 위험을 통해 사전에 발생 하는 보안에 대 한 몇 가지 중요 한 질문에 답변 하는 데 도움이 될 것으로 가정 합니다.

  - 공격을 어떻게 검색 하나요?

  - 공격 또는 위반이 발생 하는 경우 어떻게 해야 하나요?

  - 데이터 누출 또는 변조와 같은 공격 으로부터 복구 하려면 어떻게 해야 하나요?

## <a name="design"></a>디자인

디자인 단계는 디자인 및 기능 사양에 대 한 모범 사례를 설정 하는 데 중요 합니다. 또한 프로젝트 전체에서 보안 및 개인 정보 보호 문제를 완화 하는 데 도움이 되는 위험 분석을 수행 하는 것이 중요 합니다.

보안 요구 사항을 충족 하 고 보안 디자인 개념을 사용 하는 경우 보안 결함에 대 한 기회를 방지 하거나 최소화할 수 있습니다. 보안 결함은 응용 프로그램이 릴리스된 후 사용자가 악의적인 동작이 나 예기치 않은 작업을 수행할 수 있도록 하는 응용 프로그램 디자인의 감독입니다.

디자인 단계에서는 계층에서 보안을 적용 하는 방법에 대해서도 생각해 볼 수 있습니다. 한 가지 수준의 방어는 충분 하지 않을 수 있습니다. 공격자가 WAF (웹 응용 프로그램 방화벽)를 초과 하면 어떻게 되나요? 공격 으로부터 보호 하기 위해 다른 보안 제어를 원합니다.

이 점을 염두에 두면 보안 응용 프로그램을 디자인할 때 고려해 야 하는 다음과 같은 보안 디자인 개념과 보안 제어에 대해 설명 합니다.

- 보안 코딩 라이브러리와 소프트웨어 프레임 워크를 사용 합니다.
- 취약 한 구성 요소를 검색 합니다.
- 응용 프로그램을 디자인 하는 동안 위협 모델링을 사용 합니다.
- 공격 노출 영역을 줄입니다.
- Id의 정책을 기본 보안 경계로 채택 합니다.
- 중요 한 트랜잭션에 대해 재인증을 요구 합니다.
- 키 관리 솔루션을 사용 하 여 키, 자격 증명 및 기타 비밀을 보호 합니다.
- 중요 한 데이터를 보호 합니다.
- 유사 시 대기 측정을 구현 합니다.
- 오류 및 예외 처리를 활용 합니다.
- 로깅 및 경고를 사용 합니다.

### <a name="use-a-secure-coding-library-and-a-software-framework"></a>보안 코딩 라이브러리 및 소프트웨어 프레임 워크 사용

개발을 위해 보안 코딩 라이브러리와 포함 된 보안이 포함 된 소프트웨어 프레임 워크를 사용 합니다. 개발자는 보안 컨트롤을 처음부터 개발 하는 대신 기존의 검증 된 기능 (암호화, 입력 sanitation, 출력 인코딩, 키 또는 연결 문자열, 보안 컨트롤로 간주 되는 기타 모든 항목)을 사용할 수 있습니다. 이렇게 하면 보안 관련 디자인 및 구현 결함을 방지할 수 있습니다.

최신 버전의 프레임 워크와 프레임 워크에서 사용할 수 있는 모든 보안 기능을 사용 하 고 있는지를 알고 있어야 합니다. Microsoft는 모든 개발자가 모든 플랫폼 또는 언어를 사용 하 여 클라우드 응용 프로그램을 제공 하는 포괄적인 [개발 도구 집합](https://azure.microsoft.com/product-categories/developer-tools/) 을 제공 합니다. 다양 한 [sdk](https://azure.microsoft.com/downloads/)중에서 선택 하 여 원하는 언어로 코딩할 수 있습니다.
모든 기능을 갖춘 Ide (통합 개발 환경) 및 고급 디버깅 기능 및 기본 제공 Azure 지원이 있는 편집기를 활용할 수 있습니다.

Microsoft는 Azure에서 응용 프로그램을 개발 하는 데 사용할 수 있는 다양 한 [언어, 프레임 워크 및 도구](https://docs.microsoft.com/azure/index#pivot=sdkstools&panel=sdkstools-all) 를 제공 합니다. 예제는 [.net 및 .Net Core 개발자를 위한 Azure](https://docs.microsoft.com/dotnet/azure/)입니다. 제공 하는 각 언어와 프레임 워크에 대해 빠른 시작, 자습서 및 API 참조를 통해 빠르게 시작할 수 있습니다.

Azure는 웹 사이트 및 웹 응용 프로그램을 호스트 하는 데 사용할 수 있는 다양 한 서비스를 제공 합니다. 이러한 서비스를 사용 하면 .NET, .NET Core, Java, Ruby, node.js, PHP 또는 Python 등 원하는 언어로 개발할 수 있습니다.
[Azure App Service Web Apps](../../app-service/overview.md) (Web Apps)은 이러한 서비스 중 하나입니다.

Web Apps Microsoft Azure의 기능을 응용 프로그램에 추가 합니다. 여기에는 보안, 부하 분산, 자동 크기 조정 및 자동화 된 관리가 포함 됩니다. 패키지 관리, 스테이징 환경, 사용자 지정 도메인, SSL/TLS 인증서, Azure DevOps, GitHub, Docker 허브 및 기타 원본에서 지속적인 배포와 같은 Web Apps의 DevOps 기능을 활용할 수도 있습니다.

Azure는 웹 사이트 및 웹 응용 프로그램을 호스트 하는 데 사용할 수 있는 다른 서비스를 제공 합니다. 대부분의 시나리오의 경우 Web Apps를 사용하는 것이 좋습니다. 마이크로 서비스 아키텍처에 대 한 자세한 내용은 [Azure Service Fabric](https://azure.microsoft.com/documentation/services/service-fabric)를 참조 하세요.
코드가 실행되는 VM을 자세히 제어해야 하는 경우 [Azure Virtual Machines](https://azure.microsoft.com/documentation/services/virtual-machines/)를 사용하는 것이 좋습니다.
이러한 Azure 서비스 중에서 선택 하는 방법에 대 한 자세한 내용은 [Azure App Service, Virtual Machines, Service Fabric 및 Cloud Services 비교](/azure/architecture/guide/technology-choices/compute-decision-tree)를 참조 하세요.

### <a name="apply-updates-to-components"></a>구성 요소에 업데이트 적용

취약성을 방지 하려면 클라이언트 쪽 구성 요소와 서버 쪽 구성 요소 (예: 프레임 워크 및 라이브러리)와 업데이트에 대 한 종속성을 지속적으로 인벤토리 해야 합니다. 새 취약점 및 업데이트 된 소프트웨어 버전이 지속적으로 출시 됩니다. 사용 하는 라이브러리 및 구성 요소에 대 한 업데이트 또는 구성 변경 내용을 모니터링 하 고, 심사 하 고, 적용할 계획이 있는지 확인 합니다.

도구 제안에 대 한 [알려진 취약성이 있는 구성 요소 사용](https://www.owasp.org/index.php/Top_10-2017_A9-Using_Components_with_Known_Vulnerabilities) 에 대 한 [OWASP (Open Web Application Security Project)](https://www.owasp.org/index.php/Main_Page) 페이지를 참조 하세요. 사용 하는 구성 요소와 관련 된 보안 취약점에 대 한 전자 메일 경고를 구독할 수도 있습니다.

### <a name="use-threat-modeling-during-application-design"></a>애플리케이션 디자인 과정에서 위협 모델링 사용

위협 모델링은 비즈니스 및 응용 프로그램에 대 한 잠재적인 보안 위협을 식별 하 고 적절 한 완화를 보장 하는 프로세스입니다. SDL은 팀이 디자인 단계에서 위협 모델링을 수행 하도록 지정 합니다. 잠재적인 문제를 해결 하는 작업은 비교적 쉽고 비용 효율적입니다. 디자인 단계에서 위협 모델링을 사용 하면 개발의 총 비용을 크게 줄일 수 있습니다.

위협 모델링 프로세스를 용이 하 게 하기 위해 보안 전문가가 아닌 전문가와 [Threat Modeling Tool SDL](threat-modeling-tool.md) 을 설계 했습니다. 이 도구를 사용 하면 위협 모델을 만들고 분석 하는 방법에 대 한 명확한 지침을 제공 하 여 모든 개발자가 쉽게 위협 모델링을 수행할 수 있습니다

모든 트러스트 경계에서 응용 프로그램 디자인을 모델링 하 고 [STRIDE](https://docs.google.com/viewer?a=v&pid=sites&srcid=ZGVmYXVsdGRvbWFpbnxzZWN1cmVwcm9ncmFtbWluZ3xneDo0MTY1MmM0ZDI0ZjQ4ZDMy) 위협 (스푸핑, 변조, 부인, 정보 공개, 서비스 거부 및 권한 상승)을 열거 하는 것은 디자인 오류를 효과적으로 파악 하는 효과적인 방법을 입증 했습니다. 조기에 다음 표에서는 STRIDE 위협을 나열 하 고 Azure에서 제공 하는 기능을 사용 하는 몇 가지 예제 완화 방법을 제공 합니다. 이러한 완화 방식을 모든 상황에서 사용할 수 있는 것은 아닙니다.

| 위협 | 보안 속성 | 잠재적 Azure 플랫폼 완화 |
| ---------------------- | --------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| 스푸핑               | 인증        | [HTTPS 연결이 필요](https://docs.microsoft.com/aspnet/core/security/enforcing-ssl?view=aspnetcore-2.1&tabs=visual-studio)합니다. |
| 변조              | 무결성             | SSL/TLS 인증서의 유효성을 검사 합니다. SSL/TLS를 사용 하는 응용 프로그램은 연결 하는 엔터티의 x.509 인증서를 완전히 확인 해야 합니다. Azure Key Vault 인증서를 사용 하 여 [x509 인증서를 관리](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-certificates)합니다. |
| 거부            | 거부 없음       | Azure [모니터링 및 진단](https://docs.microsoft.com/azure/architecture/best-practices/monitoring)을 사용하도록 설정합니다.|
| 정보 공개 | 기밀성       | [미사용](../fundamentals/encryption-atrest.md) 및 [전송 중](../fundamentals/data-encryption-best-practices.md#protect-data-in-transit)에 중요 한 데이터를 암호화 합니다. |
| 서비스 거부      | 가용성          | 잠재적 서비스 거부 조건에 대 한 성능 메트릭을 모니터링 합니다. 연결 필터를 구현합니다. 응용 프로그램 설계 모범 사례와 결합 된 [Azure DDoS protection](../../virtual-network/ddos-protection-overview.md#next-steps)은 DDoS 공격에 대 한 방어를 제공 합니다.|
| 권한 상승 | Authorization         | Azure Active Directory <span class="underline"></span> [Privileged Identity Management](../../active-directory/privileged-identity-management/pim-configure.md)를 사용 합니다.|

### <a name="reduce-your-attack-surface"></a>공격 노출 영역 줄이기

공격 노출 영역은 잠재적 취약성이 발생할 수 있는 총 합계입니다. 이 문서에서는 응용 프로그램의 공격 노출 영역에 초점을 둡니다.
공격 으로부터 응용 프로그램을 보호 하는 데 중점을 둡니다. 공격 노출 영역을 최소화 하는 간단 하 고 빠른 방법은 응용 프로그램에서 사용 하지 않는 리소스 및 코드를 제거 하는 것입니다. 응용 프로그램 크기가 작을수록 공격 노출 영역이 작아집니다. 예를 들어 다음을 제거 합니다.

- 아직 릴리스되지 않은 기능을 위한 코드입니다.
- 디버깅 지원 코드입니다.
- 사용 되지 않거나 더 이상 사용 되지 않는 네트워크 인터페이스 및 프로토콜입니다.
- 사용 하지 않는 가상 컴퓨터 및 기타 리소스

리소스를 정기적으로 정리 하 고 사용 하지 않는 코드를 제거 하는 것이 악의적인 행위자의 공격에 대 한 기회가 적기를 보장 하는 좋은 방법입니다.

공격 노출 영역을 줄이는 보다 구체적이 고 심층적인 방법은 공격 노출 영역 분석을 완료 하는 것입니다. 공격 노출 영역 분석을 통해 보안 취약성을 검토 하 고 테스트 해야 하는 시스템 부분을 매핑할 수 있습니다.

공격 노출 영역 분석의 목적은 응용 프로그램의 위험 영역을 이해 하는 것입니다. 따라서 개발자와 보안 전문가는 응용 프로그램에서 공격에 노출 되는 부분을 인식 하 게 됩니다. 그런 다음 이러한 가능성을 최소화 하 고 공격 노출 영역을 변경 하는 시기와 방법 및 위험 관점에서의 의미를 추적 하는 방법을 찾을 수 있습니다.

공격 노출 영역 분석은 다음을 식별 하는 데 도움이 됩니다.

- 보안 취약성을 검토 하 고 테스트 하는 데 필요한 시스템의 기능 및 일부입니다.
- 심층 방어 보호를 필요로 하는 높은 위험 수준의 코드 영역 (보호 해야 하는 시스템의 일부)
- 공격 노출 영역을 변경 하 고 위협 평가를 새로 고쳐야 하는 경우

공격자가 잠재적인 weak 또는 취약성을 악용 하는 기회를 줄이려면 응용 프로그램의 전반적인 공격 노출 영역을 철저 하 게 분석 해야 합니다. 또한 시스템 서비스에 대 한 액세스를 사용 하지 않도록 설정 하거나 제한 하 고, 최소 권한의 원칙을 적용 하 고, 가능한 경우 계층화 된 방어를 사용 합니다.

SDL의 확인 단계에서 [공격 노출 영역 검토를 수행](secure-develop.md#conduct-attack-surface-review) 하는 방법을 설명 합니다.

> [!NOTE]
> **위협 모델링 및 공격 노출 영역 분석의 차이점은 무엇 인가요?**
위협 모델링은 응용 프로그램에 대 한 잠재적인 보안 위협을 식별 하 고 위협에 대 한 적절 한 완화를 보장 하는 프로세스입니다. 공격 노출 영역 분석은 공격에 노출 되는 높은 위험 수준의 코드 영역을 식별 합니다. 응용 프로그램을 배포 하기 전에 응용 프로그램의 위험 수준이 높은 영역을 보호 하 고 코드 영역을 검토 하 고 테스트 하는 방법을 찾는 것이 포함 됩니다.

### <a name="adopt-a-policy-of-identity-as-the-primary-security-perimeter"></a>기본 보안 경계로서의 ID 정책 채택

클라우드 응용 프로그램을 디자인 하는 경우 네트워크 중심 접근 방식에서 id 중심 방식으로 보안 경계 포커스를 확장 하는 것이 중요 합니다. 지금까지 기본 온-프레미스 보안 경계는 조직의 네트워크 였습니다. 대부분의 온-프레미스 보안 디자인은 네트워크를 기본 보안 피벗으로 사용 합니다. 클라우드 응용 프로그램의 경우 id를 기본 보안 경계로 고려 하 여 보다 효율적으로 처리할 수 있습니다.

웹 응용 프로그램을 개발 하기 위한 id 중심 접근 방법을 개발 하기 위해 수행할 수 있는 작업은 다음과 같습니다.

- 사용자에 대해 multi-factor authentication을 적용 합니다.
- 강력한 인증 및 권한 부여 플랫폼을 사용합니다.
- 최소 권한의 원칙을 적용 합니다.
- Just-in-time 액세스를 구현 합니다.

#### <a name="enforce-multi-factor-authentication-for-users"></a>사용자에 대해 multi-factor authentication 적용

따라서 2단계 인증을 사용해야 합니다. 2 단계 인증은 인증 및 권한 부여에 대 한 현재 표준 이며 인증의 사용자 이름 및 암호 유형에 내재 된 보안 약점을 방지 합니다. Azure 관리 인터페이스 (Azure Portal/원격 PowerShell) 및 고객 관련 서비스에 대 한 액세스는 [azure Multi-Factor Authentication](../../active-directory/authentication/concept-mfa-howitworks.md)를 사용 하도록 설계 및 구성 해야 합니다.

#### <a name="use-strong-authentication-and-authorization-platforms"></a>강력한 인증 및 권한 부여 플랫폼 사용

사용자 지정 코드 대신 플랫폼에서 제공하는 인증 및 권한 부여 메커니즘을 사용합니다. 이는 사용자 지정 인증 코드를 개발 하는 데 오류가 발생할 수 있기 때문입니다. 상업적 코드 (예: Microsoft)는 광범위 하 게 보안을 검토 하는 경우가 많습니다. [Azure AD(Azure Active Directory)](../../active-directory/fundamentals/active-directory-whatis.md)는 ID 및 액세스 관리를 위한 Azure 솔루션입니다. 이러한 Azure AD 도구 및 서비스는 보안 개발에 도움이 됩니다.

- 개발자가 사용자를 안전 하 게 로그인 하는 앱을 빌드하는 데 사용 하는 클라우드 id 서비스는 [AZURE ad id 플랫폼 (개발자 용 AZURE ad)](../../active-directory/develop/about-microsoft-identity-platform.md) 입니다. Azure AD는 다중 테넌트 앱을 개발 하려고 하는 단일 테넌트, LOB (기간 업무) 앱 및 개발자를 빌드하는 개발자를 지원 합니다. 기본 로그인 외에도, Azure AD를 사용 하 여 빌드된 앱은 Azure AD 플랫폼에서 빌드된 Microsoft Api 및 사용자 지정 Api를 호출할 수 있습니다. Azure AD id 플랫폼은 OAuth 2.0 및 Openid connect Connect와 같은 업계 표준 프로토콜을 지원 합니다.

- [Azure Active Directory B2C (Azure AD B2C)](../../active-directory-b2c/index.yml) 는 응용 프로그램을 사용할 때 고객이 자신의 프로필을 등록, 로그인 및 관리 하는 방법을 사용자 지정 하 고 제어 하는 데 사용할 수 있는 id 관리 서비스입니다. 여기에는 iOS, Android 및 .NET 용으로 개발 된 응용 프로그램이 포함 됩니다. Azure AD B2C는 고객 id를 보호 하면서 이러한 작업을 수행할 수 있도록 합니다.

#### <a name="apply-the-principle-of-least-privilege"></a>최소 권한의 원칙 적용

[최소 권한의](https://en.wikipedia.org/wiki/Principle_of_least_privilege) 개념은 사용자에 게 작업을 수행 하는 데 필요한 정확한 수준의 액세스 및 제어 권한을 부여 하는 것입니다.

소프트웨어 개발자에 게 도메인 관리자 권한이 필요 한가요? 관리 도우미가 개인용 컴퓨터의 관리 제어에 액세스 해야 하나요? 소프트웨어에 대 한 액세스를 평가 하는 것은 다릅니다. [RBAC (역할 기반 액세스 제어)](../../role-based-access-control/overview.md) 를 사용 하 여 응용 프로그램에서 사용자에 게 다른 기능 및 권한을 제공 하는 경우 모든 사용자에 게 모든 액세스 권한을 부여 하는 것은 아닙니다. 각 역할에 필요한 항목에 대 한 액세스를 제한 하 여 보안 문제가 발생 하는 위험을 제한할 수 있습니다.

응용 프로그램에서 액세스 패턴에 [최소한의 권한을](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models#in-applications) 적용 하는지 확인 합니다.

> [!NOTE]
>  최소 권한 규칙은 소프트웨어 및 소프트웨어를 만드는 사용자에 게 적용 해야 합니다. 소프트웨어 개발자는 너무 많은 액세스 권한을 제공 하는 경우 IT 보안에 큰 위험을 초래할 수 있습니다. 개발자가 악의적인 의도를가지고 있거나 너무 많은 액세스 권한을 제공 하는 경우 결과가 심각한 영향을 받을 수 있습니다. 개발 수명 주기 동안 개발자에 게 최소 권한 규칙을 적용 하는 것이 좋습니다.

#### <a name="implement-just-in-time-access"></a>Just-in-time 액세스 구현

JIT ( *just-in-time* ) 액세스를 구현 하 여 권한 노출 시간을 더 낮게 합니다. [Azure AD Privileged Identity Management](../../active-directory/users-groups-roles/directory-admin-roles-secure.md#stage-3-build-visibility-and-take-full-control-of-admin-activity) 사용:

- JIT만 필요한 사용 권한을 사용자에 게 제공 합니다.
- 권한을 자동으로 해지하는 짧은 기간 동안 자신 있게 역할을 할당합니다.

### <a name="require-re-authentication-for-important-transactions"></a>중요 한 트랜잭션에 대 한 재인증 필요

[사이트 간 요청 위조](https://docs.microsoft.com/aspnet/core/security/anti-request-forgery?view=aspnetcore-2.1) ( *XSRF* 또는 *csrf*라고도 함)는 악의적인 웹 앱이 클라이언트 브라우저와 해당 브라우저를 신뢰 하는 웹 앱 간의 상호 작용에 영향을 주는 웹 호스팅 앱에 대 한 공격입니다. 웹 브라우저에서 웹 사이트에 대 한 모든 요청과 함께 일부 형식의 인증 토큰을 자동으로 보내기 때문에 사이트 간 요청 위조 공격을 수행할 수 있습니다.
이러한 형태의 악용은 공격이 사용자의 이전에 인증 된 세션을 활용 하기 때문에 *한 번 클릭 공격* 또는 *세션 riding* 라고도 합니다.

이러한 종류의 공격 으로부터 보호 하는 가장 좋은 방법은 구매, 계정 비활성화 또는 암호 변경과 같은 중요 한 모든 트랜잭션 전에 사용자가 제공할 수 있는 항목을 사용자에 게 요청 하는 것입니다. 사용자에 게 암호를 다시 입력 하거나, captcha를 완료 하거나, 사용자 에게만 있는 비밀 토큰을 제출 하도록 요청할 수 있습니다. 가장 일반적인 방법은 비밀 토큰입니다.

### <a name="use-a-key-management-solution-to-secure-keys-credentials-and-other-secrets"></a>키 관리 솔루션을 사용 하 여 키, 자격 증명 및 기타 비밀 보호

키와 자격 증명을 잃어 버리는 것은 일반적인 문제입니다. 키 및 자격 증명을 분실하는 것보다 더 위험한 상황은 권한이 없는 사람이 해당 정보에 액세스하는 것뿐입니다. 공격자는 자동화 된 수동 기법을 활용 하 여 GitHub와 같은 코드 리포지토리에 저장 된 키 및 암호를 찾을 수 있습니다. 이러한 공용 코드 리포지토리 또는 다른 서버에는 키와 비밀을 넣지 마세요.

키 관리 솔루션에는 항상 키, 인증서, 암호 및 연결 문자열을 입력 합니다. 키와 비밀이 Hsm (하드웨어 보안 모듈)에 저장 되는 중앙 집중화 된 솔루션을 사용할 수 있습니다. Azure는 [Azure Key Vault](../../key-vault/key-vault-overview.md)를 사용 하 여 클라우드에서 HSM을 제공 합니다.

*비밀 저장소*Key Vault입니다. 응용 프로그램 암호를 저장 하는 중앙 집중식 클라우드 서비스입니다. Key Vault는 응용 프로그램 비밀을 단일 중앙 위치에 보관 하 고 보안 액세스, 권한 제어 및 액세스 로깅을 제공 하 여 기밀 데이터를 안전 하 게 유지 합니다.

비밀은 개별 *자격 증명 모음*에 저장 됩니다. 각 자격 증명 모음에는 액세스를 제어 하는 자체 구성 및 보안 정책이 있습니다. REST API 또는 대부분의 프로그래밍 언어에 사용할 수 있는 클라이언트 SDK를 통해 데이터에 액세스할 수 있습니다.

> [!IMPORTANT]
> Azure Key Vault은 서버 응용 프로그램에 대 한 구성 암호를 저장 하도록 설계 되었습니다. 앱 사용자에 게 속한 데이터를 저장 하기 위한 것이 아닙니다. 이는 성능 특성, API 및 비용 모델에 반영 됩니다.
>
> 사용자 데이터는 TDE (투명한 데이터 암호화) 또는 Azure Storage 서비스 암호화를 사용 하는 저장소 계정에 있는 Azure SQL Database 인스턴스와 같이 다른 곳에 저장 되어야 합니다. 응용 프로그램에서 이러한 데이터 저장소에 액세스 하는 데 사용 하는 암호는 Azure Key Vault에 유지 될 수 있습니다.

### <a name="protect-sensitive-data"></a>중요 한 데이터 보호

데이터 보호는 보안 전략의 필수적인 부분입니다.
데이터를 분류 하 고 데이터 보호 요구 사항을 파악 하면 데이터 보안을 고려 하 여 앱을 디자인 하는 데 도움이 됩니다. 민감도 및 비즈니스 영향을 기준으로 저장 된 데이터를 분류 (범주화) 하면 개발자가 데이터와 관련 된 위험을 확인할 수 있습니다.

데이터 형식을 디자인할 때 적용 가능한 모든 데이터에 중요 한 레이블을 지정 합니다. 응용 프로그램에서 적용 가능한 데이터를 중요 한 것으로 취급 하는지 확인 합니다. 이러한 사례는 중요 한 데이터를 보호 하는 데 도움이 됩니다.

- 암호화를 사용 합니다.
- 키 및 암호와 같은 암호를 하드 코딩 하지 마십시오.
- 액세스 제어 및 감사가 준비 되어 있는지 확인 합니다.

#### <a name="use-encryption"></a>암호화 사용

데이터 보호는 보안 전략의 필수적인 부분입니다.
데이터가 데이터베이스에 저장 되어 있거나 위치 간에 앞뒤로 이동 하는 경우에는 [미사용 데이터](../fundamentals/encryption-atrest.md) 암호화 (데이터베이스에 있는 경우) 및 [전송 중인 데이터](../fundamentals/data-encryption-best-practices.md#protect-data-in-transit) 암호화 (사용자, 데이터베이스, API 또는 서비스 엔드포인트)를 사용 합니다. 항상 SSL/TLS 프로토콜을 사용 하 여 데이터를 교환 하는 것이 좋습니다. 암호화에 최신 버전의 TLS를 사용 하는지 확인 합니다 (현재는 버전 1.2).

#### <a name="avoid-hard-coding"></a>하드 코딩 방지

일부 항목은 소프트웨어에서 하드 코드 되어서는 안 됩니다. 일부 예는 호스트 이름 또는 IP 주소, Url, 전자 메일 주소, 사용자 이름, 암호, 저장소 계정 키 및 기타 암호화 키입니다. 코드의 주석 섹션을 비롯 하 여 코드에서 하드 코딩 될 수 있거나 전혀 코딩 될 수 없는 요소에 대 한 요구 사항을 구현 하는 것이 좋습니다.

코드에 주석을 넣을 때 중요 한 정보를 저장 하지 않도록 합니다. 여기에는 전자 메일 주소, 암호, 연결 문자열, 조직의 다른 사용자만 인식 하는 응용 프로그램에 대 한 정보 및 응용 프로그램이 나 조직을 공격 하는 이점을 공격자에 게 제공할 수 있는 다른 모든 항목이 포함 됩니다. .

기본적으로 개발 프로젝트의 모든 항목은 배포 시 공용 지식이 있는 것으로 가정 합니다. 프로젝트에 모든 종류의 중요 한 데이터를 포함 하지 않도록 합니다.

이전에는 [Azure Key Vault](../../key-vault/key-vault-overview.md)에 대해 설명 했습니다. Key Vault를 사용 하 여 키 및 암호와 같은 암호를 하드 코딩 하는 대신 저장할 수 있습니다. Azure 리소스에 대 한 관리 되는 id와 함께 Key Vault를 사용 하는 경우 Azure 웹 앱은 원본 제어 또는 구성에 비밀을 저장 하지 않고도 안전 하 고 안전 하 게 암호 구성 값에 액세스할 수 있습니다. 자세한 내용은 [Azure Key Vault를 사용 하 여 서버 앱에서 비밀 관리](https://docs.microsoft.com/learn/modules/manage-secrets-with-azure-key-vault/)를 참조 하세요.

### <a name="implement-fail-safe-measures"></a>유사 시 대기 조치 구현

응용 프로그램은 실행 중에 발생 하는 [오류](https://docs.microsoft.com/dotnet/standard/exceptions/) 를 일관 된 방식으로 처리할 수 있어야 합니다. 응용 프로그램은 모든 오류를 catch 하 고 안전 하거나 종결 되지 않아야 합니다.

또한 의심 스러운 또는 악의적인 활동을 식별 하기 위해 충분 한 사용자 컨텍스트로 오류가 기록 되었는지 확인 해야 합니다. 로그는 지연 된 법정 분석을 허용 하기에 충분 한 시간 동안 보존 되어야 합니다. 로그는 로그 관리 솔루션에서 쉽게 사용할 수 있는 형식 이어야 합니다. 보안과 관련 된 오류에 대 한 경고가 트리거되는 지 확인 합니다. 로깅 및 모니터링 부족으로 인해 공격자가 시스템을 추가로 공격 하 고 지 속성을 유지할 수 있습니다.

### <a name="take-advantage-of-error-and-exception-handling"></a>오류 및 예외 처리를 활용 합니다.

올바른 오류 및 [예외 처리](https://docs.microsoft.com/dotnet/standard/exceptions/best-practices-for-exceptions) 를 구현 하는 것은 방어 코딩의 중요 한 부분입니다. 시스템을 안정적이 고 안전 하 게 만들려면 오류 및 예외 처리를 수행 하는 것이 중요 합니다. 오류 처리의 실수는 공격자에 게 정보 누출 및 사용자의 플랫폼과 디자인에 대해 더 잘 이해 하는 데 도움이 되는 다양 한 종류의 보안 취약성으로 이어질 수 있습니다.

다음 사항을 확인합니다.

- 코드에서 중복 된 [try/catch 블록](https://docs.microsoft.com/dotnet/standard/exceptions/how-to-use-the-try-catch-block-to-catch-exceptions) 을 방지 하기 위해 중앙 집중식으로 예외를 처리 합니다.

- 모든 예기치 않은 동작은 응용 프로그램 내에서 처리 됩니다.

- 사용자에 게 표시 되는 메시지는 중요 한 데이터를 유출 하지 않지만 문제를 설명 하는 데 충분 한 정보를 제공 합니다.

- 예외를 기록 하 고 조사 하는 법적 정보 또는 인시던트 대응 팀에 충분 한 정보를 제공 합니다.

[Azure Logic Apps](../../logic-apps/logic-apps-overview.md) 는 종속 시스템에 의해 발생 한 [오류 및 예외를 처리](../../logic-apps/logic-apps-exception-handling.md) 하는 최고 수준의 환경을 제공 합니다. Logic Apps를 사용 하 여 엔터프라이즈 및 조직에서 앱, 데이터, 시스템 및 서비스를 통합 하는 작업 및 프로세스를 자동화 하는 워크플로를 만들 수 있습니다.

### <a name="use-logging-and-alerting"></a>로깅 및 경고 사용

보안 조사에 대 한 보안 문제를 [기록](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1) 하 고 사용자가 적시에 문제를 알 수 있도록 문제에 대 한 경고를 트리거합니다. 모든 구성 요소에 감사 및 로깅을 사용하도록 설정합니다. 감사 로그는 사용자 컨텍스트를 캡처하고 모든 중요 이벤트를 식별 합니다.

사용자가 사이트에 제출 하는 중요 한 데이터를 기록 하지 않았는지 확인 합니다. 중요한 데이터의 예는 다음과 같습니다.

- 사용자 자격 증명
- 사회 보장 번호 또는 기타 식별 정보
- 신용 카드 번호 또는 기타 금융 정보
- 의료 정보
- 암호화 된 정보의 암호를 해독 하는 데 사용할 수 있는 개인 키 또는 기타 데이터
- 애플리케이션을 보다 효과적으로 공격하는 데 용할 수 있는 시스템 또는 애플리케이션 정보

응용 프로그램이 성공 및 실패 한 사용자 로그인, 암호 재설정, 암호 변경, 계정 잠금 및 사용자 등록과 같은 사용자 관리 이벤트를 모니터링 하는지 확인 합니다. 이러한 이벤트에 대 한 로깅을 통해 잠재적으로 의심 스러운 동작을 감지 하 고 대응할 수 있습니다. 응용 프로그램에 액세스 하는 사용자와 같은 작업 데이터를 수집할 수도 있습니다.

## <a name="next-steps"></a>다음 단계
다음 문서에서는 보안 응용 프로그램을 개발 하 고 배포 하는 데 도움이 되는 보안 제어 및 작업을 권장 합니다.

- [보안 응용 프로그램 개발](secure-develop.md)
- [보안 응용 프로그램 배포](secure-deploy.md)
