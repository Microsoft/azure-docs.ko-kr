---
title: Cloud Foundry 모니터링을 위해 Azure Log Analytics 노즐 배포
description: Azure Log Analytics을 위한 Cloud Foundry Loggregator Nozzle 배포에 대한 단계별 지침. Nozzle을 사용하여 Cloud Foundry 시스템 상태 및 성능 메트릭을 모니터링합니다.
services: virtual-machines-linux
author: ningk
tags: Cloud-Foundry
ms.assetid: 00c76c49-3738-494b-b70d-344d8efc0853
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/22/2017
ms.author: ningk
ms.openlocfilehash: bf6691310ec964a1d6293f3a60c151e3d6f8e641
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/19/2020
ms.locfileid: "76277351"
---
# <a name="deploy-azure-log-analytics-nozzle-for-cloud-foundry-system-monitoring"></a>Cloud Foundry 시스템 모니터링용 Azure Log Analytics Nozzle 배포

[Azure Monitor](https://azure.microsoft.com/services/log-analytics/) 는 Azure의 서비스입니다. Log Analytics를 사용하면 클라우드 및 온-프레미스 환경에서 생성되는 데이터를 수집하고 분석할 수 있습니다.

Log Analytics 노즐 (노즐)은 [Cloud Foundry loggregator](https://docs.cloudfoundry.org/loggregator/architecture.html) firehose에서 Azure Monitor 로그로 메트릭을 전달 하는 CLOUD FOUNDRY (CF) 구성 요소입니다. Nozzle을 사용하면 여러 배포에서 CF 시스템 상태와 성능 메트릭을 수집, 확인 및 분석할 수 있습니다.

이 문서에서는 CF 환경에 노즐을 배포한 다음 Azure Monitor logs 콘솔에서 데이터에 액세스 하는 방법에 대해 알아봅니다.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>필수 조건

다음 단계는 Nozzle 배포를 위한 필수 구성 요소입니다.

### <a name="1-deploy-a-cf-or-pivotal-cloud-foundry-environment-in-azure"></a>1. Azure에서 CF 또는 Pivotal Cloud Foundry 환경을 배포 합니다.

오픈 소스 CF 배포나 PCF(Pivotal Cloud Foundry) 배포에서 Nozzle을 사용할 수 있습니다.

* [Azure에서 Cloud Foundry 배포](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/blob/master/docs/guidance.md)

* [Azure에서 Pivotal Cloud Foundry 배포](https://docs.pivotal.io/pivotalcf/1-11/customizing/azure.html)

### <a name="2-install-the-cf-command-line-tools-for-deploying-the-nozzle"></a>2. 노즐을 배포 하기 위한 CF 명령줄 도구 설치

Nozzle은 CF 환경에서 애플리케이션을 실행합니다. 애플리케이션을 배포하려면 CF CLI가 필요합니다.

Nozzle은 또한 Loggregator Firehose 및 클라우드 컨트롤러에 대한 액세스 권한이 필요합니다. 사용자를 만들고 구성하려면 UAA(사용자 계정 및 인증) 서비스가 필요합니다.

* [Cloud Foundry CLI 설치](https://docs.cloudfoundry.org/cf-cli/install-go-cli.html)

* [Cloud Foundry UAA 명령줄 클라이언트 설치](https://github.com/cloudfoundry/cf-uaac/blob/master/README.md)

UAA 명령줄 클라이언트를 설정 하기 전에 RubyGems가 설치 되어 있는지 확인 합니다.

### <a name="3-create-a-log-analytics-workspace-in-azure"></a>3. Azure에서 Log Analytics 작업 영역 만들기

수동으로 또는 템플릿을 사용하여 Log Analytics 작업 영역을 만들 수 있습니다. 템플릿은 Azure Monitor 로그 콘솔에 대한 미리 구성 된 KPI 보기 및 경고의 설정을 배포 합니다. 

#### <a name="to-create-the-workspace-manually"></a>수동으로 작업 영역을 만들려면 다음을 수행합니다.

1. Azure Portal에서 Azure Marketplace의 서비스 목록을 검색 한 다음 Log Analytics 작업 영역을 선택 합니다.
2. **만들기**를 선택한 후, 다음 항목에 대한 선택 사항을 지정합니다.

   * **Log Analytics 작업 영역**: 작업 영역의 이름을 입력합니다.
   * **구독**: 구독이 여러 개인 경우 CF 배포와 동일한 구독을 선택합니다.
   * **리소스 그룹**: 새 리소스 그룹을 만들거나 CF 배포가 포함된 그룹과 같은 그룹을 사용할 수 있습니다.
   * **위치**: 위치를 입력합니다.
   * **가격 책정 계층**: **확인**을 클릭하여 완료합니다.

자세한 내용은 [Azure Monitor 로그 시작](https://docs.microsoft.com/azure/log-analytics/log-analytics-get-started)을 참조 하세요.

#### <a name="to-create-the-log-analytics-workspace-through-the-monitoring-template-from-azure-market-place"></a>Azure Marketplace에서 모니터링 템플릿을 통해 Log Analytics 작업 영역을 만들려면:

1. Azure Portal을 엽니다.
1. "+" 기호를 클릭하거나 왼쪽 위 모서리에서 "리소스 만들기"를 클릭합니다.
1. 검색 창에 "Cloud Foundry"를 입력하고 "Cloud Foundry 모니터링 솔루션"을 선택합니다.
1. Cloud Foundry 모니터링 솔루션 템플릿 첫 페이지가 로드되며, “만들기”를 클릭하여 템플릿 블레이드를 실행합니다.
1. 필수 매개 변수를 입력합니다.
    * **구독**: Log Analytics 작업 영역에 대한 Azure 구독을 선택합니다. 보통은 Cloud Foundry 배포와 동일합니다.
    * **리소스 그룹**: Log Analytics 작업 영역에 대해 기존 리소스 그룹을 선택하거나 새 리소스 그룹을 만듭니다.
    * **리소스 그룹 위치**: 리소스 그룹의 위치를 선택합니다.
    * **OMS_Workspace_Name**: 작업 영역 이름을 입력합니다. 작업 영역이 없는 경우 템플릿에서 새로 만듭니다.
    * **OMS_Workspace_Region**: 작업 영역에 대한 위치를 선택합니다.
    * **OMS_Workspace_Pricing_Tier**: Log Analytics 작업 영역 SKU를 선택합니다. 참조는 [가격 책정 지침](https://azure.microsoft.com/pricing/details/log-analytics/)에서 확인하세요.
    * **약관**: 약관을 클릭한 다음, "만들기"를 클릭하여 약관에 동의합니다.
1. 모든 매개 변수를 지정한 후 "만들기"를 클릭하여 템플릿을 배포합니다. 배포가 완료되면 상태가 알림 탭에 표시됩니다.


## <a name="deploy-the-nozzle"></a>Nozzle 배포

PCF 타일로 또는 CF 애플리케이션과 같은 다양한 방법으로 Nozzle을 배포할 수 있습니다.

### <a name="deploy-the-nozzle-as-a-pcf-ops-manager-tile"></a>PCF Ops Manager 타일로 Nozzle 배포

단계에 따라 [Azure Log Analytics Nozzle for PCF를 설치하고 구성합니다](https://docs.pivotal.io/partners/azure-log-analytics-nozzle/installing.html). 이것은 간단한 방식이기 때문에 PCF Ops Manager 타일이 자동으로 노즐을 구성하여 푸시합니다. 

### <a name="deploy-the-nozzle-manually-as-a-cf-application"></a>자동으로 Nozzle을 CF 애플리케이션으로 배포

PCF Ops Manager를 사용하지 않는 경우 Nozzle을 애플리케이션으로 배포합니다. 다음 섹션은 이 프로세스를 설명합니다.

#### <a name="sign-in-to-your-cf-deployment-as-an-admin-through-cf-cli"></a>CF CLI를 통해 관리자로 CF 배포에 로그인

다음 명령 실행:
```
cf login -a https://api.${SYSTEM_DOMAIN} -u ${CF_USER} --skip-ssl-validation
```

"SYSTEM_DOMAIN"은 CF 도메인 이름입니다. CF 배포 매니페스트 파일에서 "SYSTEM_DOMAIN"을 검색하면 이 이름을 검색할 수 있습니다. 

"CF_User"는 CF 관리자 이름입니다. CF 배포 매니페스트 파일에서 “scim” 섹션을 검색하고 이름 및 “cf_admin_password”를 찾으면 이름과 암호를 검색할 수 있습니다.

#### <a name="create-a-cf-user-and-grant-required-privileges"></a>CF 사용자를 만들고 필요한 권한 부여

다음 명령을 실행합니다.
```
uaac target https://uaa.${SYSTEM_DOMAIN} --skip-ssl-validation
uaac token client get admin
cf create-user ${FIREHOSE_USER} ${FIREHOSE_USER_PASSWORD}
uaac member add cloud_controller.admin ${FIREHOSE_USER}
uaac member add doppler.firehose ${FIREHOSE_USER}
```

"SYSTEM_DOMAIN"은 CF 도메인 이름입니다. CF 배포 매니페스트 파일에서 "SYSTEM_DOMAIN"을 검색하면 이 이름을 검색할 수 있습니다.

#### <a name="download-the-latest-log-analytics-nozzle-release"></a>최신 Log Analytics Nozzle 릴리스 다운로드

다음 명령 실행:
```
git clone https://github.com/Azure/oms-log-analytics-firehose-nozzle.git
cd oms-log-analytics-firehose-nozzle
```

#### <a name="set-environment-variables"></a>환경 변수 설정

이제 현재 디렉터리에서 manifest.yml 파일에 환경 변수를 설정할 수 있습니다. 다음은 Nozzle용 앱 매니페스트를 보여 줍니다. 값을 사용자의 특정 Log Analytics 작업 영역 정보로 바꿉니다.

```
OMS_WORKSPACE             : Log Analytics workspace ID: Open your Log Analytics workspace in the Azure portal, select **Advanced settings**, select **Connected Sources**, and select **Windows Servers**.
OMS_KEY                   : OMS key: Open your Log Analytics workspace in the Azure portal, select **Advanced settings**, select **Connected Sources**, and select **Windows Servers**.
OMS_POST_TIMEOUT          : HTTP post timeout for sending events to Azure Monitor logs. The default is 10 seconds.
OMS_BATCH_TIME            : Interval for posting a batch to Azure Monitor logs. The default is 10 seconds.
OMS_MAX_MSG_NUM_PER_BATCH : The maximum number of messages in a batch to Azure Monitor logs. The default is 1000.
API_ADDR                  : The API URL of the CF environment. For more information, see the preceding section, "Sign in to your CF deployment as an admin through CF CLI."
DOPPLER_ADDR              : Loggregator's traffic controller URL. For more information, see the preceding section, "Sign in to your CF deployment as an admin through CF CLI."
FIREHOSE_USER             : CF user you created in the preceding section, "Create a CF user and grant required privileges." This user has firehose and Cloud Controller admin access.
FIREHOSE_USER_PASSWORD    : Password of the CF user above.
EVENT_FILTER              : Event types to be filtered out. The format is a comma-separated list. Valid event types are METRIC, LOG, and HTTP.
SKIP_SSL_VALIDATION       : If true, allows insecure connections to the UAA and the traffic controller.
CF_ENVIRONMENT            : Enter any string value for identifying logs and metrics from different CF environments.
IDLE_TIMEOUT              : The Keep Alive duration for the firehose consumer. The default is 60 seconds.
LOG_LEVEL                 : The logging level of the Nozzle. Valid levels are DEBUG, INFO, and ERROR.
LOG_EVENT_COUNT           : If true, the total count of events that the Nozzle has received and sent are logged to Azure Monitor logs as CounterEvents.
LOG_EVENT_COUNT_INTERVAL  : The time interval of the logging event count to Azure Monitor logs. The default is 60 seconds.
```

### <a name="push-the-application-from-your-development-computer"></a>개발 컴퓨터에서 애플리케이션 푸시

현재 위치가 oms-log-analytics-firehose-nozzle 폴더인지 확인합니다. 다음 명령 실행:
```
cf push
```

## <a name="validate-the-nozzle-installation"></a>Nozzle 설치 유효성 검사

### <a name="from-apps-manager-for-pcf"></a>Apps Manager에서(PCF의 경우)

1. Ops Manager에 로그인하여 설치 대시보드에 타일이 표시되는지 확인합니다.
2. Apps Manager에 로그인하여 Nozzle용으로 만든 영역이 사용 현황 보고서에 나열되며 상태가 정상인지 확인합니다.

### <a name="from-your-development-computer"></a>개발 컴퓨터에서

CF CLI 창에서 다음을 입력합니다.
```
cf apps
```
OMS Nozzle 애플리케이션이 실행되고 있는지 확인합니다.

## <a name="view-the-data-in-the-azure-portal"></a>Azure Portal에서 데이터 보기

마켓플레이스를 통해 모니터링 솔루션을 배포한 경우 Azure Portal로 이동하고 솔루션을 찾습니다. 이 솔루션은 템플릿에서 지정한 리소스 그룹에 있습니다. 솔루션을 클릭 하 고, "log analytics console"로 이동 하 여 미리 구성 된 보기가 나열 됩니다. 여기에는 상위 Cloud Foundry 시스템 Kpi, 응용 프로그램 데이터, 경고 및 VM 상태 메트릭이 포함 됩니다. 

Log Analytics 작업 영역을 수동으로 만든 경우 다음 단계에 따라 뷰와 경고를 만듭니다.

### <a name="1-import-the-oms-view"></a>1. OMS 보기 가져오기

OMS 포털에서 **뷰 디자이너** > **가져오기** > **찾아보기**로 이동하고 omsview 파일 중 하나를 선택합니다. 예를 들어 *Cloud Foundry.omsview*를 선택하고 보기를 저장합니다. 이제 타일이 **개요** 페이지에 표시됩니다. 시각화된 메트릭을 보려면 이 항목을 선택합니다.

**뷰 디자이너**를 통해 새 뷰를 만들거나 이러한 뷰를 사용자 지정할 수 있습니다.

*“Cloud Foundry.omsview”* 는 Cloud Foundry OMS 보기 템플릿의 미리 보기 버전입니다. 이것은 완전히 구성된 기본 템플릿입니다. 템플릿에 대한 제안 사항이나 의견이 있으시면 [문제 섹션](https://github.com/Azure/oms-log-analytics-firehose-nozzle/issues)으로 보내주세요.

### <a name="2-create-alert-rules"></a>2. 경고 규칙 만들기

[경고를 작성](https://docs.microsoft.com/azure/log-analytics/log-analytics-alerts)하고 필요에 따라 쿼리와 임계값을 사용자 지정할 수 있습니다. 권장되는 경고는 다음과 같습니다.

| 검색 쿼리                                                                  | 경고 생성 조건 | Description                                                                       |
| ----------------------------------------------------------------------------- | ----------------------- | --------------------------------------------------------------------------------- |
| Type=CF_ValueMetric_CL Origin_s=bbs Name_s="Domain.cf-apps"                   | 결과 수 < 1   | **bbs.Domain.cf-apps**는 cf-apps 도메인이 최신 상태인지 여부를 나타냅니다. 즉 Cloud Controller로부터의 CF App 요청이 실행을 위해 bbs.LRPsDesired(Diego에 적합한 AI)로 동기화되는지 여부를 나타냅니다. 수신되는 데이터가 없으면 지정한 기간에 cf-apps 도메인이 최신 상태가 아닌 것입니다. |
| Type=CF_ValueMetric_CL Origin_s=rep Name_s=UnhealthyCell Value_d>1            | 결과 수 > 0   | Diego 셀의 경우 값이 0이면 정상 상태이고 1이면 비정상 상태입니다. 지정한 기간에 비정상 Diego 셀이 여러 개 검색되면 경고를 설정합니다. |
| Type=CF_ValueMetric_CL Origin_s="bosh-hm-forwarder" Name_s="system.healthy" Value_d=0 | 결과 수 > 0 | 값이 1이면 시스템이 정상 상태인 것이고 0이면 시스템이 정상 상태가 아닌 것입니다. |
| Type=CF_ValueMetric_CL Origin_s=route_emitter Name_s=ConsulDownMode Value_d>0 | 결과 수 > 0   | Consul은 상태를 주기적으로 내보냅니다. 값이 0이면 시스템이 정상 상태이며, 1이면 경로 내보내기에서 Consul이 다운되었음을 감지한 것입니다. |
| Type=CF_CounterEvent_CL Origin_s=DopplerServer (Name_s="TruncatingBuffer.DroppedMessages" or Name_s="doppler.shedEnvelopes") Delta_d>0 | 결과 수 > 0 | 백 프레셔로 인해 Doppler가 의도적으로 삭제한 메시지의 델타 번호입니다. |
| Type=CF_LogMessage_CL SourceType_s=LGR MessageType_s=ERR                      | 결과 수 > 0   | Loggregator는 로깅 프로세스의 문제를 나타내기 위해 **LGR**을 내보냅니다. 이러한 문제의 예는 로그 메시지 출력이 너무 높은 경우입니다. |
| Type=CF_ValueMetric_CL Name_s=slowConsumerAlert                               | 결과 수 > 0   | 노즐은 loggregator에서 저속 소비자 경고를 받으면 **slowConsumerAlert** ValueMetric를 Azure Monitor 로그에 보냅니다. |
| Type=CF_CounterEvent_CL Job_s=nozzle Name_s=eventsLost Delta_d>0              | 결과 수 > 0   | 손실된 이벤트의 델타 번호가 임계값에 도달하는 경우 Nozzle 실행과 관련하여 문제가 있을 수 있습니다. |

## <a name="scale"></a>확장성

Nozzle과 Loggregator를 확장할 수 있습니다.

### <a name="scale-the-nozzle"></a>Nozzle 확장

Nozzle 인스턴스를 두 개 이상 포함하여 시작하는 것이 좋습니다. Firehose는 모든 Nozzle 인스턴스에 워크로드를 배포합니다.
Nozzle이 Firehose에서의 데이터 트래픽을 처리할 수 있는지 확인하려면 **slowConsumerAlert** 경고(이전 섹션 “경고 규칙 만들기”에 나열됨)를 설치합니다. 경고를 받은 후 [느린 Nozzle에 대한 지침](https://docs.pivotal.io/pivotalcf/1-11/loggregator/log-ops-guide.html#slow-noz)을 따라 크기 조정이 필요한지 여부를 확인합니다.
Nozzle을 강화하려면 Apps Manager 또는 CF CLI를 사용하여 Nozzle용 인스턴스 수 또는 메모리/디스크 리소스를 늘립니다.

### <a name="scale-the-loggregator"></a>Loggregator 비율 크기 조정

Loggregator는 로깅 프로세스의 문제를 나타내기 위해 **LGR** 로그 메시지를 보냅니다. 경고를 모니터링하여 Loggregator를 강화해야 하는지 여부를 결정할 수 있습니다.
Loggregator를 강화하려면 Doppler 버퍼 크기를 늘리거나 CF 매니페스트에 Doppler 서버 인스턴스를 추가합니다. 자세한 내용은 [Loggregator 크기 조정을 위한 지침](https://docs.cloudfoundry.org/running/managing-cf/logging-config.html#scaling)을 참조하세요.

## <a name="update"></a>업데이트

Nozzle을 최신 버전으로 업데이트하려면 새 Nozzle 릴리스를 다운로드한 다음, “Nozzle 배포” 섹션의 단계에 따라 애플리케이션을 다시 푸시합니다.

### <a name="remove-the-nozzle-from-ops-manager"></a>Ops Manager에서 Nozzle 제거

1. Ops Manager에 로그인합니다.
2. **PCF용 Microsoft Azure Log Analytics Nozzle** 타일을 찾습니다.
3. 휴지통 아이콘을 선택하고 삭제 작업을 확인합니다.

### <a name="remove-the-nozzle-from-your-development-computer"></a>개발 컴퓨터에서 Nozzle 제거

CF CLI 창에서 다음을 입력합니다.
```
cf delete <App Name> -r
```

Nozzle을 제거하는 경우 OMS 포털의 데이터는 자동으로 제거되지 않습니다. Azure Monitor 로그 보존 설정에 따라 만료 됩니다.

## <a name="support-and-feedback"></a>지원 및 피드백

Azure Log Analytics Nozzle은 오픈 소스입니다. [GitHub 섹션](https://github.com/Azure/oms-log-analytics-firehose-nozzle/issues)에 질문이나 의견을 보내 주세요. Azure 지원 요청을 시작하려면 서비스 범주로 “Cloud Foundry를 실행하는 Virtual Machine”을 선택합니다. 

## <a name="next-step"></a>다음 단계

PCF 2.0에서 VM 성능 메트릭은 시스템 메트릭 전달자에 의해 Azure Log Analytics 노즐에 전송 되 고 Log Analytics 작업 영역에 통합 됩니다. 이제 VM 성능 메트릭에 Log Analytics 에이전트가 필요하지 않습니다. 그러나 Log Analytics 에이전트를 사용하여 Syslog 정보를 계속 수집할 수 있습니다. Log Analytics 에이전트는 Bosh 추가 기능으로 CF VM에 설치됩니다. 

자세한 내용은 [Cloud Foundry 배포에 Log Analytics 에이전트 배포](https://github.com/Azure/oms-agent-for-linux-boshrelease)를 참조하세요.
