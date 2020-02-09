---
title: Microsoft Azure에서 보안 응용 프로그램 개발
description: 이 문서에서는 웹 응용 프로그램 프로젝트의 구현 및 확인 단계에서 고려할 모범 사례를 설명 합니다.
author: TerryLanfear
manager: barbkess
ms.author: terrylan
ms.date: 06/12/2019
ms.topic: article
ms.service: security
ms.subservice: security-develop
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: 76219ec454fd105ed4bbf1a2fa0e922475d8dd1f
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76834980"
---
# <a name="develop-secure-applications-on-azure"></a>Azure에서 보안 애플리케이션 개발
이 문서에서는 클라우드 용 응용 프로그램을 개발할 때 고려해 야 할 보안 작업 및 제어를 제공 합니다. Microsoft [SDL (보안 개발 수명 주기)](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx) 의 구현 및 확인 단계 중에 고려해 야 하는 보안 질문 및 개념을 다룹니다. 목표는 보다 안전한 응용 프로그램을 개발 하는 데 사용할 수 있는 활동 및 Azure 서비스를 정의 하는 데 도움을 주는 것입니다.

이 문서에서 다루는 SDL 단계는 다음과 같습니다.

- 구현
- 확인

## <a name="implementation"></a>구현
구현 단계는 초기 방지를 위한 모범 사례를 설정 하 고 코드에서 보안 문제를 검색 하 고 제거 하는 것입니다.
응용 프로그램을 사용 하지 않으려는 방법으로 응용 프로그램을 사용 한다고 가정 합니다. 이를 통해 응용 프로그램의 우발적 이거나 의도적인 오용 으로부터 보호할 수 있습니다.

### <a name="perform-code-reviews"></a>코드 검토 수행

코드를 체크 인하기 전에 코드 [검토](https://docs.microsoft.com/azure/devops/learn/devops-at-microsoft/code-reviews-not-primarily-finding-bugs) 를 수행 하 여 전체 코드 품질을 높이고 버그 생성의 위험을 줄입니다. [Visual Studio](https://docs.microsoft.com/azure/devops/repos/tfvc/get-code-reviewed-vs?view=vsts) 를 사용 하 여 코드 검토 프로세스를 관리할 수 있습니다.

### <a name="perform-static-code-analysis"></a>정적 코드 분석 수행

[정적 코드 분석](https://www.owasp.org/index.php/Static_Code_Analysis) ( *소스 코드 분석*이 라고도 함)은 일반적으로 코드 검토의 일부로 수행 됩니다. 정적 코드 분석은 일반적으로 정적 코드 분석 도구를 실행 하 여 [taint 확인](https://en.wikipedia.org/wiki/Taint_checking) 및 [데이터 흐름 분석과](https://en.wikipedia.org/wiki/Data-flow_analysis)같은 기술을 사용 하 여 실행 되지 않는 코드에서 잠재적 취약성을 찾는 방법을 나타냅니다.

Azure Marketplace에서는 정적 코드 분석을 수행 하 고 코드 검토를 지 원하는 [개발자 도구](https://azuremarketplace.microsoft.com/marketplace/apps/category/developer-tools?page=1&search=code%20review) 를 제공 합니다.

### <a name="validate-and-sanitize-every-input-for-your-application"></a>응용 프로그램에 대 한 모든 입력의 유효성을 검사 하 고 정리 합니다.

모든 입력을 신뢰할 수 없는 것으로 간주 하 여 가장 일반적인 웹 응용 프로그램 취약성 으로부터 응용 프로그램을 보호 합니다. 신뢰할 수 없는 데이터는 주입 공격을 위한 차량입니다. 응용 프로그램에 대 한 입력에는 URL의 매개 변수, 사용자의 입력, 데이터베이스 또는 API의 데이터 및 사용자가 잠재적으로 조작할 수 있는 전달 된 모든 항목이 포함 됩니다. 응용 프로그램은 응용 프로그램이 데이터를 사용 하기 전에 (사용자에 게 다시 표시를 포함 하 여) 데이터를 구문적으로 유효한 지 [유효성을 검사](https://www.owasp.org/index.php/OWASP_Proactive_Controls_2016#4:_Validate_All_Inputs) 해야 합니다.

데이터 흐름 초기에 입력의 유효성을 검사 하 여 올바른 형식의 데이터만 워크플로에 입력 되도록 합니다. 잘못 된 형식의 데이터를 데이터베이스에 유지 하거나 다운스트림 구성 요소에서 오작동을 트리거하는 것을 원하지 않습니다.

블랙 리스트 및 허용 목록는 입력 구문 유효성 검사를 수행 하는 두 가지 일반적인 방법입니다.

  - 블랙 리스트는 지정 된 사용자 입력에 "악의적인 것으로 알려져 있는" 콘텐츠가 포함 되지 않았는지 확인 하려고 시도 합니다.

  - 허용 목록는 지정 된 사용자 입력이 "알려진 양호" 입력 집합과 일치 하는지 확인 하려고 시도 합니다. 문자 기반 허용 목록는 응용 프로그램에서 사용자 입력이 "알려진 양호" 문자만 포함 하거나 해당 입력이 알려진 형식과 일치 하는지 확인 하는 허용 목록 형식입니다.
    예를 들어 사용자 이름에 영숫자 문자만 포함 되어 있거나 정확히 두 개의 숫자가 포함 되어 있는지 확인 하는 것이 포함 될 수 있습니다.

허용 목록는 보안 소프트웨어를 빌드하기 위한 기본 방법입니다.
블랙 리스트는 잠재적으로 잘못 된 입력의 전체 목록을 고려할 수 없기 때문에 오류가 발생 하기 쉽습니다.

클라이언트 쪽 (또는 서버 및 클라이언트 쪽)이 아닌 서버에서이 작업을 수행 합니다.

### <a name="verify-your-applications-outputs"></a>응용 프로그램의 출력 확인

시각적으로 또는 문서 내에 표시 되는 모든 출력은 항상 인코딩 및 이스케이프 되어야 합니다. [이스케이프](https://www.owasp.org/index.php/Injection_Theory#Escaping_.28aka_Output_Encoding.29)( *출력 인코딩*이 라고도 함)는 신뢰할 수 없는 데이터가 삽입 공격의 차량이 되지 않도록 하는 데 사용 됩니다. 데이터 유효성 검사와 결합 된 이스케이프는 시스템 전체에 대 한 보안을 강화 하기 위해 계층화 된 방어 기능을 제공 합니다.

이스케이프를 통해 모든 항목이 출력으로 표시 되도록 *합니다.* 또한 이스케이프를 통해 데이터가 실행 되지 않는다는 것을 알 수 있으며,이로 인해 공격이 작동 하지 않습니다. 이는 XSS ( *교차 사이트 스크립팅* ) 라는 또 다른 일반적인 공격 기술입니다.

타사에서 웹 프레임 워크를 사용 하는 경우 [OWASP XSS 방지 참고 자료 시트](https://github.com/OWASP/CheatSheetSeries/blob/master/cheatsheets/Cross_Site_Scripting_Prevention_Cheat_Sheet.md)를 사용 하 여 웹 사이트에서 출력 인코딩에 대 한 옵션을 확인할 수 있습니다.

### <a name="use-parameterized-queries-when-you-contact-the-database"></a>데이터베이스에 연결할 때 매개 변수가 있는 쿼리 사용

코드에서 인라인 데이터베이스 쿼리를 즉시 만들지 않으며 데이터베이스로 직접 보냅니다. 응용 프로그램에 삽입 된 악성 코드는 잠재적으로 데이터베이스를 도난당 하거나, 초기화 하거나, 수정할 수 있습니다. 응용 프로그램을 사용 하 여 데이터베이스를 호스트 하는 운영 체제에서 악의적인 운영 체제 명령을 실행할 수도 있습니다.

대신 매개 변수가 있는 쿼리 또는 저장 프로시저를 사용 합니다. 매개 변수가 있는 쿼리를 사용 하는 경우 코드에서 프로시저를 안전 하 게 호출 하 고 쿼리 문의 일부로 처리 될 걱정 없이 문자열을 전달할 수 있습니다.

### <a name="remove-standard-server-headers"></a>표준 서버 헤더 제거

서버, X 공급 및 X-AspNet 버전과 같은 헤더는 서버 및 기본 기술에 대 한 정보를 표시 합니다. 응용 프로그램의 지문 인식을을 방지 하려면 이러한 헤더를 표시 하지 않는 것이 좋습니다.
[Azure websites에서 표준 서버 헤더 제거](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/)를 참조 하세요.

### <a name="segregate-your-production-data"></a>프로덕션 데이터 분리

프로덕션 데이터 또는 "실제" 데이터를 개발, 테스트 또는 비즈니스 의도에 대 한 다른 용도로 사용해 서는 안 됩니다. 모든 개발 및 테스트에 마스킹된 ([익명화](https://en.wikipedia.org/wiki/Data_anonymization)) 데이터 집합을 사용 해야 합니다.

즉, 실제 데이터에 액세스할 수 있는 사용자가 적을수록 공격 노출 영역을 줄일 수 있습니다. 또한 직원의 수를 줄여 개인 데이터를 볼 수 있으며,이로 인해 기밀성의 잠재적 위반이 제거 됩니다.

### <a name="implement-a-strong-password-policy"></a>강력한 암호 정책 구현

무차별 암호 대입 및 사전 기반 추측을 방지 하려면 강력한 암호 정책을 구현 하 여 사용자가 복잡 한 암호를 만들 수 있도록 해야 합니다 (예: 12 자 최소 길이 및 영숫자 및 특수 문자 필요).

Id 프레임 워크를 사용 하 여 암호 정책을 만들고 적용할 수 있습니다. Azure AD B2C는 [기본 제공 정책](../../active-directory-b2c/tutorial-create-user-flows.md#create-a-password-reset-user-flow), [셀프 서비스 암호 재설정](../../active-directory-b2c/user-flow-self-service-password-reset.md)등을 제공 하 여 암호 관리에 도움이 됩니다.

기본 계정에 대 한 공격 으로부터 보호 하려면 모든 키와 암호를 교체할 수 있는지 확인 하 고 리소스를 설치한 후에 생성 되거나 교체 되었는지 확인 합니다.

응용 프로그램에서 암호를 자동으로 생성 해야 하는 경우 생성 된 암호가 무작위로 생성 되 고 높은 엔트로피를 갖고 있는지 확인 합니다.

### <a name="validate-file-uploads"></a>파일 업로드 유효성 검사

응용 프로그램에서 [파일 업로드](https://www.owasp.org/index.php/Unrestricted_File_Upload)를 허용 하는 경우이 위험한 작업에 대해 수행할 수 있는 예방 조치를 고려 하십시오. 대부분의 공격에 대 한 첫 번째 단계는 공격을 받는 시스템에 악성 코드를 가져오는 것입니다. 파일 업로드를 사용 하면 공격자가이를 수행할 수 있습니다. OWASP는 파일의 유효성을 검사 하는 솔루션을 제공 하 여 업로드 하는 파일이 안전한 지 확인 합니다.

맬웨어 방지 보호는 바이러스, 스파이웨어 및 기타 악성 소프트웨어를 식별 하 고 제거 하는 데 도움이 됩니다. [Microsoft 맬웨어 방지 프로그램](../fundamentals/antimalware.md) 또는 microsoft 파트너의 endpoint protection 솔루션 ([Trend 마이크로](https://www.trendmicro.com/azure/), [Symantec](https://www.symantec.com/products), [McAfee](https://www.mcafee.com/us/products.aspx), [Windows Defender](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-in-windows-10)및 [Endpoint Protection](https://docs.microsoft.com/configmgr/protect/deploy-use/endpoint-protection))을 설치할 수 있습니다.

[Microsoft 맬웨어 방지 프로그램](../fundamentals/antimalware.md) 에는 실시간 보호, 예약 된 검색, 맬웨어 재구성, 서명 업데이트, 엔진 업데이트, 샘플 보고 및 제외 이벤트 수집과 같은 기능이 포함 되어 있습니다. 배포 및 기본 제공 검색(경고 및 인시던트)을 용이하게 하기 위해 [Azure Security Center](../../security-center/security-center-partner-integration.md)와 Microsoft 맬웨어 방지 프로그램 및 파트너 솔루션을 통합할 수 있습니다.

### <a name="dont-cache-sensitive-content"></a>중요 한 콘텐츠 캐시 안 함

브라우저에서 중요 한 콘텐츠를 캐시 하지 않습니다. 브라우저는 캐싱 및 기록에 대 한 정보를 저장할 수 있습니다. 캐시 된 파일은 Internet Explorer의 경우 임시 인터넷 파일 폴더와 같은 폴더에 저장 됩니다. 이러한 페이지를 다시 참조 하는 경우 브라우저는 해당 캐시의 페이지를 표시 합니다. 중요 한 정보 (주소, 신용 카드 정보, 주민 등록 번호, 사용자 이름)가 사용자에 게 표시 되는 경우 정보는 브라우저의 캐시에 저장 되 고 브라우저의 캐시를 검사 하거나 브라우저의 **뒤로** 단추를 누르기만 하면 검색할 수 있습니다.

## <a name="verification"></a>확인
확인 단계에는 코드가 이전 단계에서 설정 된 보안 및 개인 정보 개념을 충족 하는지 확인 하는 포괄적인 노력이 포함 됩니다.

### <a name="find-and-fix-vulnerabilities-in-your-application-dependencies"></a>응용 프로그램 종속성의 취약점 찾기 및 수정

응용 프로그램 및 해당 종속 라이브러리를 검색 하 여 알려진 취약 한 구성 요소를 식별 합니다. 이 검색을 수행 하는 데 사용할 수 있는 제품에는 [OWASP Dependency Check](https://www.owasp.org/index.php/OWASP_Dependency_Check),[Snyk](https://snyk.io/)및 [Black 오리](https://www.blackducksoftware.com/)가 포함 됩니다.

[Tinfoil security](https://www.tinfoilsecurity.com/) 에서 제공 하는 취약성 검색은 Azure App Service Web Apps에서 사용할 수 있습니다. [App Service를 통한 Tinfoil security 검색을 통해](https://azure.microsoft.com/blog/web-vulnerability-scanning-for-azure-app-service-powered-by-tinfoil-security/) 개발자와 관리자는 악의적인 행위자가 사용할 수 있기 전에 취약성을 신속 하 게 통합 하 고 경제적으로 해결할 수 있습니다.

> [!NOTE]
> [Tinfoil security를 AZURE AD와 통합할](../../active-directory/saas-apps/tinfoil-security-tutorial.md)수도 있습니다. Tinfoil Security를 Azure AD와 통합 하면 다음과 같은 이점이 제공 됩니다.
>  - Azure AD에서 Tinfoil Security에 대 한 액세스 권한이 있는 사용자를 제어할 수 있습니다.
>  - 사용자는 해당 Azure AD 계정을 사용 하 여 Tinfoil Security (Single Sign-On)에 자동으로 로그인 할 수 있습니다.
>  - 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

### <a name="test-your-application-in-an-operating-state"></a>운영 상태에서 응용 프로그램 테스트

EAST (동적 응용 프로그램 보안 테스트)는 응용 프로그램을 운영 상태에서 테스트 하 여 보안 취약성을 확인 하는 프로세스입니다. 단일 AST 도구는 실행 중인 프로그램을 분석 하 여 메모리 손상, 안전 하지 않은 서버 구성, 사이트 간 스크립팅, 사용자 권한 문제, SQL 삽입 및 기타 중요 한 보안 문제와 같은 보안 취약성을 찾습니다.

Aast는 정적 SAST (응용 프로그램 보안 테스트)와는 다릅니다. SAST 도구는 보안 결함을 찾기 위해 코드가 실행 되 고 있지 않을 때 소스 코드 또는 컴파일된 코드 버전을 분석 합니다.

보안 전문가 ( [침투 테스터](../fundamentals/pen-testing.md) 또는 취약점 접근자)를 지원 하 여, 보안 전문가를 사용할 수 없는 경우 웹 프록시 스캐너 및 몇 가지 교육을 사용 하 여 사용자가 직접 수행할 수 있습니다. 코드에 명백한 보안 문제가 발생 하지 않도록 하기 위해 초기에 6AST 스캐너를 연결 합니다. 웹 응용 프로그램 취약성 스캐너 목록은 [OWASP](https://www.owasp.org/index.php/Category:Vulnerability_Scanning_Tools) 사이트를 참조 하세요.

### <a name="perform-fuzz-testing"></a>퍼징 테스트 수행

[퍼징 테스트](https://cloudblogs.microsoft.com/microsoftsecure/2007/09/20/fuzz-testing-at-microsoft-and-the-triage-process/)에서는 응용 프로그램에 잘못 된 형식이 나 임의의 데이터를 의도적으로 도입 하 여 프로그램 오류를 유도 합니다. 유도 프로그램 오류는 응용 프로그램을 릴리스하기 전에 잠재적인 보안 문제를 표시 하는 데 도움이 됩니다.

[보안 위험 검색](https://docs.microsoft.com/security-risk-detection/) 은 소프트웨어에서 보안에 중요 한 버그를 찾기 위한 Microsoft 고유의 퍼징 테스트 서비스입니다.

### <a name="conduct-attack-surface-review"></a>공격 노출 영역 검토 수행

코드 완성 후 공격 노출 영역을 검토 하면 응용 프로그램 또는 시스템에 대 한 디자인 또는 구현이 변경 되었는지 확인할 수 있습니다. 위협 모델을 포함 하 여 변경의 결과로 생성 된 새로운 공격 벡터가 검토 되 고 완화 되었는지 확인 하는 데 도움이 됩니다.

응용 프로그램을 검사 하 여 공격 노출 영역 그림을 빌드할 수 있습니다. Microsoft는 attack [Surface Analyzer](https://www.microsoft.com/download/details.aspx?id=24487)라는 공격 노출 영역 분석 도구를 제공 합니다. [OWASP Zed Attack Proxy 프로젝트](https://www.owasp.org/index.php/OWASP_Zed_Attack_Proxy_Project), [arachni](http://arachni-scanner.com/), [skipfish](https://code.google.com/p/skipfish/)및 [w3af](http://w3af.sourceforge.net/)를 비롯 한 다양 한 상업적 동적 테스트 및 취약성 검색 도구 또는 서비스 중에서 선택할 수 있습니다. 이러한 검색 도구는 앱을 탐색 하 고 웹을 통해 액세스할 수 있는 응용 프로그램의 일부를 매핑합니다. 또한 유사한 [개발자 도구](https://azuremarketplace.microsoft.com/marketplace/apps/category/developer-tools?page=1)에 대 한 Azure Marketplace 검색할 수 있습니다.

### <a name="perform-security-penetration-testing"></a>보안 침투 테스트 수행

응용 프로그램의 보안을 유지 하는 것은 다른 기능을 테스트 하는 것 만큼 중요 합니다. [침투 테스트](../fundamentals/pen-testing.md) 를 빌드 및 배포 프로세스의 표준 부분으로 만듭니다. 배포 된 응용 프로그램에 대 한 일반 보안 테스트 및 취약성 검색을 예약 하 고 오픈 포트, 엔드포인트 및 공격을 모니터링 합니다.

### <a name="run-security-verification-tests"></a>보안 확인 테스트 실행

[Azure에 대 한 보안 DevOps 키트](https://azsk.azurewebsites.net/index.html) (AzSK)는 azure 플랫폼의 여러 서비스에 대 한 svts를 포함 합니다. 이러한 SVTs를 정기적으로 실행 하 여 Azure 구독 및 응용 프로그램을 구성 하는 다른 리소스가 안전한 상태 인지 확인 합니다. 또한 AzSK의 CI/CD (연속 통합/연속 배포) 확장 기능을 사용 하 여 이러한 테스트를 자동화할 수 있습니다. 그러면 SVTs를 Visual Studio 확장으로 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계
다음 문서에서는 보안 응용 프로그램을 디자인 하 고 배포 하는 데 도움이 되는 보안 제어 및 작업을 권장 합니다.

- [보안 응용 프로그램 디자인](secure-design.md)
- [보안 응용 프로그램 배포](secure-deploy.md)
