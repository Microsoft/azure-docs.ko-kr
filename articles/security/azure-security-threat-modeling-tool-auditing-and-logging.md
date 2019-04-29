---
title: 감사 및 로깅 - Microsoft 위협 모델링 도구 - Azure | Microsoft Docs
description: 위협 모델링 도구에 노출되는 위협 완화
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: jegeib
ms.openlocfilehash: 4843828c89b04e36b0bcc73dcedf9c5735b73729
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60610852"
---
# <a name="security-frame-auditing-and-logging--mitigations"></a>보안 프레임: 감사 및 로깅 | 완화 

| 제품/서비스 | 문서 |
| --------------- | ------- |
| **Dynamics CRM**    | <ul><li>[솔루션의 민감한 엔터티를 식별하고 변경 감사 구현](#sensitive-entities)</li></ul> |
| **웹 애플리케이션** | <ul><li>[애플리케이션에 감사 및 로깅 적용](#auditing)</li><li>[로그 회전 및 분리가 작동하는지 확인](#log-rotation)</li><li>[애플리케이션이 민감한 사용자 데이터를 기록하지 않도록 확인](#log-sensitive-data)</li><li>[감사 및 로그 파일의 액세스 제한](#log-restricted-access)</li><li>[사용자 관리 이벤트 기록](#user-management)</li><li>[시스템에 악용 방지 수단을 기본적으로 제공](#inbuilt-defenses)</li><li>[Azure App Service에서 웹앱에 대한 진단 로깅 설정](#diagnostics-logging)</li></ul> |
| **데이터베이스** | <ul><li>[SQL Server에서 로그인 감사를 사용하도록 설정](#identify-sensitive-entities)</li><li>[Azure SQL에서 위협 감지 사용](#threat-detection)</li></ul> |
| **Azure Storage** | <ul><li>[Azure 스토리지 분석을 사용하여 Azure Storage에 대한 액세스 감사](#analytics)</li></ul> |
| **WCF** | <ul><li>[충분한 로깅 구현](#sufficient-logging)</li><li>[충분한 감사 실패 처리 구현](#audit-failure-handling)</li></ul> |
| **앱 API** | <ul><li>[웹 API에 감사 및 로깅 적용](#logging-web-api)</li></ul> |
| **IoT 필드 게이트웨이** | <ul><li>[필드 게이트웨이에 적절한 감사 및 로깅 적용](#logging-field-gateway)</li></ul> |
| **IoT 클라우드 게이트웨이** | <ul><li>[클라우드 게이트웨이에 적절한 감사 및 로깅 적용](#logging-cloud-gateway)</li></ul> |

## <a id="sensitive-entities"></a>솔루션의 민감한 엔터티를 식별하고 변경 감사 구현

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | Dynamics CRM | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | N/A  |
| **단계**                   | 솔루션에서 중요한 데이터가 포함된 엔터티를 식별하고 이러한 엔터티 및 필드에 대한 변경 감사 구현 |

## <a id="auditing"></a>애플리케이션에 감사 및 로깅 적용

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | 웹 애플리케이션 | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | N/A  |
| **단계**                   | 모든 구성 요소에 감사 및 로깅을 사용하도록 설정합니다. 감사 로그는 사용자 컨텍스트를 캡처해야 합니다. 모든 중요한 이벤트를 식별하고 해당 이벤트를 기록합니다. 중앙 집중식 로깅 구현 |

## <a id="log-rotation"></a>로그 회전 및 분리가 작동하는지 확인

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | 웹 애플리케이션 | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | N/A  |
| **단계**                   | <p>로그 회전은 날짜가 지정된 로그 파일이 보관되는 시스템 관리에 사용되는 자동화 프로세스입니다. 대형 애플리케이션을 실행하는 서버는 모든 요청을 기록하는 경우가 많은데, 대규모 로그가 발생하는 경우 로그 회전은 최근 이벤트의 분석을 계속 허용하면서도 로그의 총 크기를 제한할 수 있습니다. </p><p>로그 분리는 기본적으로 서비스 거부 공격 또는 애플리케이션 성능 저하를 방지하기 위해 OS/애플리케이션 실행되고 있는 여러 파티션에 로그 파일을 저장해야 한다는 의미입니다.</p>|

## <a id="log-sensitive-data"></a>애플리케이션이 민감한 사용자 데이터를 기록하지 않도록 확인

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | 웹 애플리케이션 | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | N/A  |
| **단계**                   | <p>사용자가 사이트에 제출하는 민감한 데이터를 기록하지 않도록 확인합니다. 의도적 로깅과 설계 문제로 인한 부작용을 확인합니다. 다음은 민감한 데이터의 예입니다.</p><ul><li>사용자 자격 증명</li><li>사회 보장 번호 또는 기타 식별 정보</li><li>신용 카드 번호 또는 기타 금융 정보</li><li>의료 정보</li><li>개인 키 또는 암호화된 정보를 해독하는 데 사용할 수 있는 기타 데이터</li><li>애플리케이션을 보다 효과적으로 공격하는 데 용할 수 있는 시스템 또는 애플리케이션 정보</li></ul>|

## <a id="log-restricted-access"></a>감사 및 로그 파일의 액세스 제한

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | 웹 애플리케이션 | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | N/A  |
| **단계**                   | <p>로그 파일에 대한 액세스 권한이 적절하게 설정되었는지 확인합니다. 애플리케이션 계정은 쓰기 전용 액세스만 가능해야 하고 운영자 및 고객 지원 담당자는 필요에 따라 읽기 전용 액세스만 가능해야 합니다.</p><p>관리자 계정은 전체 액세스가 가능한 유일한 계정입니다. 액세스 권한이 적절하게 제한되었는지 그 파일에서 Windows ACL을 확인합니다.</p><ul><li>애플리케이션 계정은 쓰기 전용 액세스만 가능해야 함</li><li>운영자 및 고객 지원 담당자는 필요에 따라 읽기 전용 액세스만 가능해야 함</li><li>관리자 계정은 전체 액세스가 가능한 유일한 계정</li></ul>|

## <a id="user-management"></a>사용자 관리 이벤트 기록

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | 웹 애플리케이션 | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | N/A  |
| **단계**                   | <p>애플리케이션에서 성공 및 실패한 사용자 로그인, 암호 재설정, 암호 변경, 계정 잠금, 사용자 등록 등의 사용자 관리 이벤트를 모니터링하도록 합니다. 이렇게 하면 의심스러운 동작을 감지하고 적절하게 대응할 수 있습니다. 뿐만 아니라 운영 데이터를 수집할 수 있습니다. 예를 들어 누가 애플리케이션에 액세스하는지 추적할 수 있습니다.</p>|

## <a id="inbuilt-defenses"></a>시스템에 악용 방지 수단을 기본적으로 제공

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | 웹 애플리케이션 | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | N/A  |
| **단계**                   | <p>애플리케이션이 잘못 사용될 경우 보안 예외를 throw하는 컨트롤이 작동해야 합니다. 예를 들어 입력 유효성 검사가 작동하면 공격자가 regex와 일치하지 않는 악성 코드를 삽입하려고 시도할 때 시스템 악용을 의미하는 보안 예외를 throw할 수 있습니다.</p><p>예를 들어 다음과 같은 문제에 대해 보안 예외를 기록하고 적절한 작업을 수행하는 것이 좋습니다.</p><ul><li>입력 유효성 검사</li><li>CSRF 위반</li><li>무차별 암호 대입(리소스당 사용자별 요청 수에 대한 상한)</li><li>파일 업로드 위반</li><ul>|

## <a id="diagnostics-logging"></a>Azure App Service에서 웹앱에 대한 진단 로깅 설정

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | 웹 애플리케이션 | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | EnvironmentType - Azure |
| **참조**              | N/A  |
| **단계** | <p>Azure는 App Service 웹앱을 디버그하는 데 도움이 되는 기본 제공 진단을 제공합니다. API 앱 및 모바일 앱에도 적용됩니다. App Service 웹앱은 웹 서버와 웹 애플리케이션 모두의 정보를 로깅할 수 있도록 진단 기능을 제공합니다.</p><p>이는 논리적으로 웹 서버 진단 및 애플리케이션 진단으로 구분됩니다.</p>|

## <a id="identify-sensitive-entities"></a>SQL Server에서 로그인 감사를 사용하도록 설정

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | 데이터베이스 | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | [로그인 감사 구성](https://msdn.microsoft.com/library/ms175850.aspx) |
| **단계** | <p>암호 추측 공격을 감지/확인할 수 있도록 데이터베이스 서버 로그인 감사를 사용해야 합니다. 실패한 로그인 시도를 캡처하는 것이 중요합니다. 성공한 로그인 시도와 실패한 로그인 시도를 모두 캡처하면 법정 조사 시 추가적인 이점이 있습니다.</p>|

## <a id="threat-detection"></a>Azure SQL에서 위협 감지 사용

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | 데이터베이스 | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | SQL Azure |
| **특성**              | SQL 버전 - V12 |
| **참조**              | [SQL Database 위협 감지 시작](https://azure.microsoft.com/documentation/articles/sql-database-threat-detection-get-started/)|
| **단계** |<p>위협 감지는 데이터베이스에 대한 잠재적인 보안 위협을 나타내는 비정상적인 데이터베이스 활동을 감지합니다. 위협 감지는 비정상적인 활동에 대한 보안 경고를 제공하여 잠재적인 위협이 발생하면 고객이 이를 감지하고 대응할 수 있도록 하는 새로운 차원의 보안을 제공합니다.</p><p>사용자는 데이터베이스의 데이터를 액세스, 침범 또는 악용하려는 시도로 인해 의심스러운 이벤트가 발생했는지를 판단하기 위해서 Azure SQL Database 감사를 사용하여 의심스러운 이벤트를 살펴 볼 수 있습니다.</p><p>위협 감지는 보안 전문가가 되거나 고급 보안 모니터링 시스템을 관리할 필요 없이 데이터베이스에 대한 잠재적인 위협에 간단하게 대처할 수 있도록 합니다.</p>|

## <a id="analytics"></a>Azure 스토리지 분석을 사용하여 Azure Storage에 대한 액세스 감사

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | Azure Storage | 
| **SDL 단계**               | 배포 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A |
| **참조**              | [저장소 분석을 사용하여 인증 유형 모니터링](https://azure.microsoft.com/documentation/articles/storage-security-guide/#storage-analytics) |
| **단계** | <p>각 Storage 계정에 대해 Azure Storage 분석을 사용하도록 설정하여 로깅을 수행하고 메트릭 데이터를 저장할 수 있습니다. 스토리지 분석 로그는 누군가가 스토리지에 액세스할 때 사용한 인증 방법 등의 중요한 정보를 제공합니다.</p><p>저장소에 대한 액세스를 엄격히 관리하는 경우에는 이 방법이 도움이 될 수 있습니다. 예를 들어, Blob Storage에서 모든 컨테이너를 개인으로 설정하고 애플리케이션 전체에서 SAS 서비스를 사용을 구현할 수 있습니다. 그런 다음, 스토리지 계정 키를 사용하여 Blob에 액세스하는지(보안 위반을 의미할 수 있음) 또는 Blob이 공용이지만 공용이면 안 되는지 알아보기 위해 로그를 주기적으로 확인할 수 있습니다.</p>|

## <a id="sufficient-logging"></a>충분한 로깅 구현

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | WCF | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | .NET Framework |
| **특성**              | N/A  |
| **참조**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Fortify, 영국](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_insufficient_logging) |
| **단계** | <p>보안 문제가 발생한 후 적절한 감사 내역이 부족하면 범죄 수사가 제한될 수 있습니다. WCF(Windows Communication Foundation)는 성공한/실패한 인증 시도를 기록하는 기능을 제공합니다.</p><p>실패한 인증 시도를 기록하면 관리자에게 무차별 암호 대입 공격 가능성을 경고할 수 있습니다. 마찬가지로, 성공한 인증 이벤트를 기록하면 합법적 계정이 손상될 때 유용한 감사 추적을 제공할 수 있습니다. WCF의 서비스 보안 감사 기능을 사용하도록 설정 |

### <a name="example"></a>예
다음은 감사를 사용하도록 설정된 구성 예제입니다.
```
<system.serviceModel>
    <behaviors>
        <serviceBehaviors>
            <behavior name=""NewBehavior"">
                <serviceSecurityAudit auditLogLocation=""Default""
                suppressAuditFailure=""false"" 
                serviceAuthorizationAuditLevel=""SuccessAndFailure""
                messageAuthenticationAuditLevel=""SuccessAndFailure"" />
                ...
            </behavior>
        </servicebehaviors>
    </behaviors>
</system.serviceModel>
```

## <a id="audit-failure-handling"></a>충분한 감사 실패 처리 구현

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | WCF | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | .NET Framework |
| **특성**              | N/A  |
| **참조**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Fortify, 영국](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_insufficient_audit_failure_handling) |
| **단계** | <p>개발된 솔루션은 감사 로그에 쓸 수 없는 경우 예외를 생성하지 않도록 구성됩니다. 감사 로그에 쓸 수 없는 경우 예외를 throw하지 않도록 WCF가 구성되면 프로그램에서는 실패에 대한 알림을 받지 못하고 중요한 보안 이벤트의 감사가 발생하지 않을 수 있습니다.</p>|

### <a name="example"></a>예
아래에 보이는 WCF 구성 파일의 `<behavior/>` 요소는 WCF가 감사 로그에 쓸 수 없는 경우 그 사실을 애플리케이션에게 알리지 말라고 WCF에 지시합니다.
```
<behaviors>
    <serviceBehaviors>
        <behavior name="NewBehavior">
            <serviceSecurityAudit auditLogLocation="Application"
            suppressAuditFailure="true"
            serviceAuthorizationAuditLevel="Success"
            messageAuthenticationAuditLevel="Success" />
        </behavior>
    </serviceBehaviors>
</behaviors>
```
감사 로그에 쓸 수 없을 때마다 프로그램에 알리도록 WCF를 구성합니다. 감사 추적이 유지되지 않는다는 사실을 조직에 경고할 수 있도록 프로그램에 대체 알림 체계가 있어야 합니다. 

## <a id="logging-web-api"></a>웹 API에 감사 및 로깅 적용

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | Web API | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | N/A  |
| **단계** | 웹 API에서 감사 및 로깅을 사용하도록 설정합니다. 감사 로그는 사용자 컨텍스트를 캡처해야 합니다. 모든 중요한 이벤트를 식별하고 해당 이벤트를 기록합니다. 중앙 집중식 로깅 구현 |

## <a id="logging-field-gateway"></a>필드 게이트웨이에 적절한 감사 및 로깅 적용

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | IoT 필드 게이트웨이 | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | N/A  |
| **단계** | <p>여러 디바이스가 필드 게이트웨이에 연결하는 경우 개별 디바이스에 대한 연결 시도 및 인증 상태(성공 또는 실패)를 기록하고 필드 게이트웨이에 유지해야 합니다.</p><p>또한 필드 게이트웨이에서 개별 디바이스의 IoT Hub 자격 증명을 유지하는 경우 이러한 자격 증명이 검색될 때 감사를 수행해야 합니다. 로그를 장기간 보존할 수 있도록 Azure IoT Hub/스토리지에 주기적으로 로그를 업로드하는 프로세스를 개발합니다.</p> |

## <a id="logging-cloud-gateway"></a>클라우드 게이트웨이에 적절한 감사 및 로깅 적용

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | IoT 클라우드 게이트웨이 | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | [IoT Hub 작업 모니터링 소개](https://azure.microsoft.com/documentation/articles/iot-hub-operations-monitoring/) |
| **단계** | <p>IoT Hub 작업 모니터링을 통해 모은 감사 데이터를 수집하고 저장하도록 설계합니다. 다음 모니터링 범주를 사용하도록 설정합니다.</p><ul><li>디바이스 ID 작업</li><li>디바이스-클라우드 통신</li><li>클라우드-장치 통신</li><li>연결</li><li>파일 업로드</li></ul>|