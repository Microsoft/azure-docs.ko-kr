---
title: Azure Spring Cloud 문제 해결 가이드 | Microsoft Docs
description: Azure Spring Cloud 문제 해결 가이드
author: bmitchell287
ms.service: spring-cloud
ms.topic: troubleshooting
ms.date: 11/04/2019
ms.author: brendm
ms.openlocfilehash: 5dcdb03a6d4ec4f448108dbd771a44f362aa7f20
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/19/2020
ms.locfileid: "76277573"
---
# <a name="troubleshoot-common-azure-spring-cloud-issues"></a>일반적인 Azure 스프링 클라우드 문제 해결

이 문서에서는 Azure 스프링 클라우드 개발 문제를 해결 하기 위한 지침을 제공 합니다. 자세한 내용은 [Azure 스프링 클라우드 FAQ](spring-cloud-faq.md)를 참조 하세요.

## <a name="availability-performance-and-application-issues"></a>가용성, 성능 및 애플리케이션 문제

### <a name="my-application-cant-start-for-example-the-endpoint-cant-be-connected-or-it-returns-a-502-after-a-few-retries"></a>응용 프로그램을 시작할 수 없습니다. 예를 들어 엔드포인트은 연결할 수 없으며 몇 번의 재시도 후에는 502을 반환 합니다.

Azure Log Analytics로 로그를 내보냅니다. 스프링 응용 프로그램 로그에 대 한 테이블 이름은 *AppPlatformLogsforSpring*입니다. 자세히 알아보려면 [진단 설정을 사용 하 여 로그 및 메트릭 분석](diagnostic-services.md)을 참조 하세요.

로그에 다음과 같은 오류 메시지가 나타날 수 있습니다.

> "springframework" 웹 서버를 시작할 수 없습니다. "

이 메시지는 다음과 같은 두 가지 문제 중 하나를 나타냅니다. 
* 빈 중 하나 또는 해당 종속성 중 하나가 누락되었습니다.
* 빈 속성 중 하나가 누락되거나 잘못되었습니다. 이 경우 "IllegalArgumentException"가 표시 될 가능성이 높습니다.

서비스 바인딩에서 응용 프로그램 시작 오류를 일으킬 수도 있습니다. 로그를 쿼리하려면 바인딩된 서비스와 관련 된 키워드를 사용 합니다. 예를 들어 응용 프로그램에 로컬 시스템 시간으로 설정 된 MySQL 인스턴스에 대 한 바인딩이 있다고 가정해 보겠습니다. 응용 프로그램이 시작 되지 않으면 로그에 다음 오류 메시지가 표시 될 수 있습니다.

> "SQLException: 서버 표준 시간대 값 ' 협정 세계시 '를 인식할 수 없거나 둘 이상의 표준 시간대를 나타냅니다."

이 오류를 해결 하려면 MySQL 인스턴스의 `server parameters`으로 이동 하 여 `time_zone` 값을 *시스템* 에서 *+ 0:00*로 변경 합니다.


### <a name="my-application-crashes-or-throws-an-unexpected-error"></a>애플리케이션이 충돌하거나 예기치 않은 오류를 throw함

응용 프로그램 충돌을 디버깅 하는 경우 응용 프로그램의 실행 상태 및 검색 상태를 확인 하 여 시작 합니다. 이렇게 하려면 Azure Portal의 _앱 관리_ 로 이동 하 여 모든 응용 프로그램의 상태를 _실행_ 하 고 있는지 확인 _합니다._

* 상태가 _실행_ 중이지만 검색 상태가 _작동_하지 않는 경우 ["내 응용 프로그램을 등록할 수 없습니다."](#my-application-cant-be-registered) 섹션으로 이동 합니다.

* 검색 상태가 _UP_이면 메트릭으로 이동 하 여 응용 프로그램의 상태를 확인 합니다. 다음 메트릭을 검사합니다.


  - `TomcatErrorCount` (_tomcat_): 모든 스프링 응용 프로그램 예외는 여기에서 계산 됩니다. 이 수가 크면 Azure Log Analytics로 이동 하 여 응용 프로그램 로그를 검사 합니다.

  - `AppMemoryMax` (_jvm. max_): 응용 프로그램에 사용할 수 있는 최대 메모리 양입니다. 크기는 정의 되지 않은 것일 수도 있고, 정의 된 경우 시간이 지남에 따라 변경 될 수도 있습니다. 정의 된 경우 사용 된 메모리와 커밋된 메모리의 양은 항상 max 보다 작거나 같습니다. 그러나 *< 사용* 하는 경우에 사용 되 *>* 는 메모리를 증가 시키는 경우에도 `OutOfMemoryError` 메시지와 함께 메모리 할당이 실패할 수 있습니다. 이러한 상황에서 `-Xmx` 매개 변수를 사용 하 여 최대 힙 크기를 늘립니다.

  - `AppMemoryUsed` (_jvm. used_): 응용 프로그램에서 현재 사용 하 고 있는 메모리의 양 (바이트)입니다. 일반적인 로드 Java 응용 프로그램의 경우이 메트릭 시리즈는 *패턴이 톱니 모양* 패턴을 형성 합니다 .이 패턴을 사용 하면 메모리 사용이 조금씩 증가 하 고 감소 하 여 급격 하 게 감소 하 고 그 후에도 패턴이 되풀이 됩니다. 이 메트릭 계열은 컬렉션 작업이 패턴이 톱니 모양 패턴에 대 한 삭제를 나타내는 Java 가상 머신 내부의 가비지 수집 때문에 발생 합니다.
    
    이 메트릭은 다음과 같은 메모리 문제를 식별 하는 데 중요 합니다.
    * 메모리 급증이 시작 됩니다.
    * 특정 논리 경로에 대 한 서 지 메모리 할당입니다.
    * 메모리 누수가 점진적으로 발생 합니다.

  자세한 내용은 [메트릭](spring-cloud-concept-metrics.md)을 참조 하세요.

Azure Log Analytics에 대해 자세히 알아보려면 [Azure Monitor에서 Log Analytics 시작](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)을 참조 하세요.

### <a name="my-application-experiences-high-cpu-usage-or-high-memory-usage"></a>애플리케이션에서 높은 CPU 사용 또는 높은 메모리 사용 경험

응용 프로그램에 높은 CPU 또는 메모리 사용량이 발생 하는 경우 다음 두 가지 중 하나가 충족 됩니다.
* 모든 앱 인스턴스에 높은 CPU 또는 메모리 사용량이 발생 합니다.
* 일부 앱 인스턴스는 높은 CPU 또는 메모리 사용을 경험 합니다.

적용 되는 상황을 확인 하려면 다음을 수행 합니다.

1. **메트릭**으로 이동한 다음 **서비스 CPU 사용량 비율** 또는 **사용 되는 서비스 메모리**를 선택 합니다.
2. 앱을 추가 합니다. **=** 필터를 선택 하 여 모니터링할 응용 프로그램을 지정 합니다.
3. 메트릭을 **인스턴스로**분할 합니다.

*모든 인스턴스에* 높은 cpu 또는 메모리 사용량이 발생 하는 경우 응용 프로그램을 확장 하거나 cpu 또는 메모리 사용량을 확장 해야 합니다. 자세한 내용은 [자습서: Azure 스프링 클라우드에서 응용 프로그램 크기 조정](spring-cloud-tutorial-scale-manual.md)을 참조 하세요.

*일부 인스턴스에* 높은 CPU 또는 메모리 사용량이 발생 하는 경우 인스턴스 상태와 해당 검색 상태를 확인 합니다.

자세한 내용은 [Azure 스프링 클라우드의 메트릭](spring-cloud-concept-metrics.md)을 참조 하세요.

모든 인스턴스가 실행 되 고 있는 경우 Azure Log Analytics로 이동 하 여 응용 프로그램 로그를 쿼리하고 코드 논리를 검토 합니다. 이를 통해 확장 분할에 영향을 줄 수 있는지 여부를 확인할 수 있습니다. 자세한 내용은 [진단 설정을 사용 하 여 로그 및 메트릭 분석](diagnostic-services.md)을 참조 하세요.

Azure Log Analytics에 대해 자세히 알아보려면 [Azure Monitor에서 Log Analytics 시작](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)을 참조 하세요. [Kusto 쿼리 언어](https://docs.microsoft.com/azure/kusto/query/)를 사용 하 여 로그를 쿼리 합니다.

### <a name="checklist-for-deploying-your-spring-application-to-azure-spring-cloud"></a>Azure 스프링 클라우드에 스프링 응용 프로그램을 배포 하기 위한 검사 목록

응용 프로그램을 등록 하기 전에 다음 조건을 충족 하는지 확인 합니다.

* 지정된 Java 런타임 버전을 사용하여 애플리케이션을 로컬로 실행할 수 있습니다.
* 환경 구성(CPU/RAM/인스턴스)에서 애플리케이션 공급자가 설정한 최소 요구 사항을 충족합니다.
* 구성 항목의 예상 값은입니다. 자세한 내용은 [Config 서버](spring-cloud-tutorial-config-server.md)를 참조하세요.
* 환경 변수에는 필요한 값이 있습니다.
* JVM 매개 변수에는 필요한 값이 있습니다.
* 응용 프로그램 패키지에서 포함 된 _구성 서버_ 및 _스프링 서비스 레지스트리_ 서비스를 사용 하지 않도록 설정 하거나 제거 하는 것이 좋습니다.
* Azure 리소스가 _서비스 바인딩_을 통해 바인딩될 경우 대상 리소스가 실행 중인지 확인합니다.

## <a name="configuration-and-management"></a>구성 및 관리

### <a name="i-encountered-a-problem-with-creating-an-azure-spring-cloud-service-instance"></a>Azure 스프링 클라우드 서비스 인스턴스를 만드는 데 문제가 발생 했습니다.

Azure Portal를 사용 하 여 Azure 스프링 클라우드 서비스 인스턴스를 설정 하는 경우 Azure 스프링 클라우드는 유효성 검사를 수행 합니다.

그러나 [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) 또는 [Azure Resource Manager 템플릿을](https://docs.microsoft.com/azure/azure-resource-manager/)사용 하 여 Azure 스프링 클라우드 서비스 인스턴스를 설정 하려는 경우 다음을 확인 하세요.

* 구독이 활성 상태입니다.
* 이 위치는 Azure 스프링 클라우드에서 [지원](spring-cloud-faq.md) 됩니다.
* 인스턴스에 대한 리소스 그룹이 이미 만들어졌습니다.
* 리소스 이름이 명명 규칙을 준수합니다. 소문자, 숫자 및 하이픈만 포함 해야 합니다. 첫 번째 자리는 반드시 문자여야 합니다. 마지막 문자는 문자 또는 숫자여야 합니다. 값에는 2 ~ 32 문자를 포함 해야 합니다.

리소스 관리자 템플릿을 사용 하 여 Azure 스프링 클라우드 서비스 인스턴스를 설정 하려면 먼저 [Azure Resource Manager 템플릿의 구조 및 구문 이해](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)를 참조 하세요.

Azure 스프링 클라우드 서비스 인스턴스 이름은 `azureapps.io`에서 하위 도메인 이름을 요청 하는 데 사용 되므로 이름이 기존 이름과 충돌 하는 경우 설치에 실패 합니다. 활동 로그에서 자세한 내용을 확인할 수 있습니다.

### <a name="i-cant-deploy-a-jar-package"></a>JAR 패키지를 배포할 수 없습니다.

Azure Portal 또는 리소스 관리자 템플릿을 사용 하 여/source 패키지를 업로드할 수 없습니다.

[Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)를 사용 하 여 응용 프로그램 패키지를 배포 하는 경우 Azure CLI는 배포 진행률을 주기적으로 폴링하고, 결과적으로 배포 결과를 표시 합니다.

폴링이 중단된 경우에도 다음 명령을 사용하여 배포 로그를 계속 가져올 수 있습니다.

`az spring-cloud app show-deploy-log -n <app-name>`

응용 프로그램이 올바른 [실행 JAR 형식](https://docs.spring.io/spring-boot/docs/current/reference/html/executable-jar.html)으로 패키지 되었는지 확인 합니다. 올바르게 패키지 되지 않은 경우 다음과 유사한 오류 메시지가 표시 됩니다.

> "오류: 잘못 되었거나 손상 된 jarfile/jar/38bc8ea1-a6bb-4736-8e93-e8f3b52c8714"

### <a name="i-cant-deploy-a-source-package"></a>원본 패키지를 배포할 수 없습니다.

Azure Portal 또는 리소스 관리자 템플릿을 사용 하 여 JAR/원본 패키지를 업로드할 수 없습니다.

[Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)를 사용 하 여 응용 프로그램 패키지를 배포 하는 경우 Azure CLI는 배포 진행률을 주기적으로 폴링하고, 결과적으로 배포 결과를 표시 합니다.

폴링이 중단된 경우에도 다음 명령을 사용하여 빌드 및 배포 로그를 계속 가져올 수 있습니다.

`az spring-cloud app show-deploy-log -n <app-name>`

그러나 한 번에 하나의 Azure 스프링 클라우드 서비스 인스턴스에서 하나의 원본 패키지에 대해 하나의 빌드 작업만 트리거할 수 있습니다. 자세한 내용은 Azure 스프링 클라우드에서 [응용 프로그램 배포](spring-cloud-quickstart-launch-app-portal.md) 및 [스테이징 환경 설정](spring-cloud-howto-staging-environment.md)을 참조 하세요.

### <a name="my-application-cant-be-registered"></a>응용 프로그램을 등록할 수 없습니다.

대부분의 경우이 문제는 *필요한 종속성* 및 *서비스 검색이* 프로젝트 개체 모델 (pom) 파일에 제대로 구성 되지 않은 경우에 발생 합니다. 구성 된 후 기본 제공 서비스 레지스트리 서버 엔드포인트은 응용 프로그램을 사용 하 여 환경 변수로 삽입 됩니다. 그런 다음 응용 프로그램은 서비스 레지스트리 서버에 등록 하 고 다른 종속 마이크로 서비스를 검색 합니다.

새로 등록 된 인스턴스가 트래픽을 받기 시작 하기 전에 2 분 이상 기다립니다.

기존 스프링 클라우드 기반 솔루션을 Azure로 마이그레이션하는 경우에는 Azure 스프링 클라우드에서 제공 되는 관리 되는 인스턴스와 충돌 하지 않도록 임시 _서비스 레지스트리_ 및 _구성 서버_ 인스턴스를 제거 하거나 해제 해야 합니다.

Azure Log Analytics에서 _서비스 레지스트리_ 클라이언트 로그를 확인할 수도 있습니다. 자세한 내용은 [진단 설정을 사용 하 여 로그 및 메트릭 분석](diagnostic-services.md) 을 참조 하세요.

Azure Log Analytics에 대해 자세히 알아보려면 [Azure Monitor에서 Log Analytics 시작](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)을 참조 하세요. [Kusto 쿼리 언어](https://docs.microsoft.com/azure/kusto/query/)를 사용 하 여 로그를 쿼리 합니다.

### <a name="i-want-to-inspect-my-applications-environment-variables"></a>내 애플리케이션의 환경 변수를 검사하려고 합니다.

환경 변수는 azure에서 응용 프로그램을 구성 하는 서비스를 구성 하는 위치와 방법을 이해 하 고 있는지 확인 하는 Azure 스프링 클라우드 프레임 워크를 알려 줍니다. 환경 변수가 올바른지 확인하는 것은 잠재적인 문제 해결에 필수적인 첫 번째 단계입니다.  Spring Boot Actuator 엔드포인트를 사용하여 환경 변수를 검토할 수 있습니다.  

> [!WARNING]
> 이 프로시저는 테스트 엔드포인트을 사용 하 여 환경 변수를 노출 합니다.  테스트 엔드포인트에 공개적으로 액세스할 수 있거나 애플리케이션에 도메인 이름을 할당한 경우에는 더이상 진행하지 마십시오.

1. [https://editor.swagger.io](`https://<your application test endpoint>/actuator/health`) 로 이동합니다.  
    - `{"status":"UP"}`과 유사한 응답은 엔드포인트가 사용하도록 설정되었음을 나타냅니다.
    - 응답이 음수 이면 *Pom .xml* 파일에 다음 종속성을 포함 합니다.

        ```xml
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-actuator</artifactId>
            </dependency>
        ```

1. 스프링 부팅 발동기 엔드포인트이 사용 하도록 설정 된 상태에서 Azure Portal로 이동 하 고 응용 프로그램의 구성 페이지를 찾습니다.  `MANAGEMENT_ENDPOINTS_WEB_EXPOSURE_INCLUDE` 이름 및 `*` 값을 사용 하 여 환경 변수를 추가 합니다. 

1. 애플리케이션을 다시 시작합니다.

1. `https://<your application test endpoint>/actuator/env`로 이동 하 여 응답을 검사 합니다.  다음과 같이 표시되어야 합니다.

    ```json
    {
        "activeProfiles": [],
        "propertySources": {,
            "name": "server.ports",
            "properties": {
                "local.server.port": {
                    "value": 1025
                }
            }
        }
    }
    ```

`systemEnvironment`이라는 자식 노드를 찾습니다.  이 노드에는 애플리케이션의 환경 변수가 포함됩니다.

> [!IMPORTANT]
> 애플리케이션에 대한 공용 액세스를 가능하게 하려면 환경 변수의 노출을 역방향으로 바꾸어야 합니다.  Azure Portal로 이동 하 고, 응용 프로그램의 구성 페이지를 검색 하 고, `MANAGEMENT_ENDPOINTS_WEB_EXPOSURE_INCLUDE`환경 변수를 삭제 합니다.

### <a name="i-cant-find-metrics-or-logs-for-my-application"></a>응용 프로그램에 대 한 메트릭 또는 로그를 찾을 수 없습니다.

**앱 관리** 로 이동 하 여 응용 프로그램 상태를 _실행_ 하 고 있는지 확인 _합니다._

_JVM_ 에서 메트릭을 볼 수 있지만 _Tomcat_에서 메트릭을 볼 수 없는 경우에는 응용 프로그램 패키지에서 `spring-boot-actuator` 종속성이 사용 하도록 설정 되어 있고 성공적으로 부팅 되었는지 확인 하십시오.

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

응용 프로그램 로그를 저장소 계정에 보관할 수는 있지만 Azure Log Analytics로 보내지 않은 경우 [작업 영역을 올바르게 설정](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)했는지 확인 합니다. Azure Log Analytics의 무료 계층을 사용 하는 경우 [무료 계층은 SLA (서비스 수준 계약)를 제공 하지](https://azure.microsoft.com/support/legal/sla/log-analytics/v1_3/)않습니다.
