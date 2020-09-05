---
title: Azure FarmBeats 문제 해결
description: 이 문서에서는 Azure FarmBeats 문제를 해결하는 방법을 설명합니다.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: c66d3d6fd3ee0bcba01db61183f40cd3ccf3f39d
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88797941"
---
# <a name="troubleshoot-azure-farmbeats"></a>Azure FarmBeats 문제 해결

이 문서에서는 일반적인 Azure FarmBeats 문제에 대한 해결 방법을 제공합니다. 추가 도움말을 보려면 microsoft [&지원 포럼](https://aka.ms/farmbeatssupport) 에 문의 하거나 전자 메일을 보내세요 farmbeatssupport@microsoft.com .

> [!NOTE]
  > 4월에 FarmBeats를 설치했고 빈 오류 메시지와 함께 작업이 실패하는 경우 중요한 상태 및 안전 조직에 대한 지원 우선 순위를 지정하는 배치 할당량이 설치에 할당되지 않았을 수 있습니다. 자세한 내용은 [여기](https://azure.microsoft.com/blog/update-2-on-microsoft-cloud-services-continuity/) 를 참조하십시오. 작업을 성공적으로 실행하려면 배치 계정에 할당할 VM을 요청해야 합니다.

## <a name="install-issues"></a>설치 문제

  > [!NOTE]
  > 오류로 인해 설치를 다시 시작하는 경우 설치를 다시 트리거하기 전에 **리소스 그룹**을 삭제하거나 리소스 그룹에서 모든 리소스를 삭제해야 합니다.

### <a name="invalid-sentinel-credentials"></a>잘못된 Sentinel 자격 증명

설치 중에 제공된 Sentinel 자격 증명이 잘못되었습니다. 올바른 자격 증명을 사용하여 설치를 다시 시작합니다.

### <a name="the-regional-account-quota-of-batch-accounts-for-the-specified-subscription-has-been-reached"></a>지정된 구독에 대한 배치 계정의 지역 계정 할당량에 도달함

할당량을 늘리거나 사용하지 않는 배치 계정을 삭제한 후 설치를 다시 시작합니다.

### <a name="invalid-resource-group-location"></a>잘못된 리소스 그룹 위치

**리소스 그룹**이 설치하는 동안 지정된 **지역**과 동일한 위치에 있는지 확인합니다.

### <a name="other-install-issues"></a>기타 설치 문제

다음 세부 정보를 사용하여 문의하세요.

- 구독 ID
- 리소스 그룹 이름
- 배포 실패에 대한 로그 파일을 첨부하려면 다음 단계를 수행합니다.

    1. Azure Portal에서 **리소스 그룹**으로 이동합니다.

    2. 왼쪽에 있는 **설정** 섹션에서 **배포**를 선택합니다.

    3. **실패함**으로 표시된 모든 배포에 대해 세부 정보를 선택하고 배포 세부 정보를 다운로드합니다. 이 파일을 메일에 첨부합니다.

## <a name="sensor-telemetry"></a>센서 원격 분석

### <a name="cant-view-telemetry-data"></a>원격 분석 데이터를 볼 수 없음

**증상**: 디바이스 또는 센서가 배포되고 디바이스 파트너와 FarmBeats를 연결했지만 FarmBeats에서 원격 분석 데이터를 가져오거나 볼 수 없습니다.

**정정 작업**

1. FarmBeats 리소스 그룹으로 이동 합니다.
2. **이벤트 허브** 네임 스페이스 ("센서-파트너-참조-네임 스페이스-xxxx")를 선택 하 고 "Event Hubs"을 클릭 한 다음 파트너에 할당 된 이벤트 허브에서 들어오는 메시지 수를 확인 합니다.
3. 다음 중 하나를 수행합니다.

   - ‘들어오는 메시지’가 없는 경우 디바이스 파트너에게 문의하세요.  
   - ‘들어오는 메시지’가 있는 경우 Datahub 및 가속기 로그와 캡처된 원격 분석을 사용하여 문의해 주세요.

로그를 다운로드하는 방법을 이해하려면 [“수동으로 로그 수집”](#collect-logs-manually) 섹션으로 이동합니다.  

### <a name="cant-view-telemetry-data-after-ingesting-historicalstreaming-data-from-your-sensors"></a>센서에서 기록/스트리밍 데이터를 수집한 후 원격 분석 데이터를 볼 수 없음

**증상**: 디바이스 또는 센서가 배포되고 FarmBeats에 디바이스/센서를 만들었고 EventHub로 원격 분석을 수집했지만 FarmBeats에서 원격 분석 데이터를 가져오거나 볼 수 없습니다.

**정정 작업**

1. 파트너 등록이 올바르게 완료되었는지 확인합니다. Datahub Swagger로 가서 /Partner API로 이동하고 Get을 수행하여 파트너가 등록되었는지 확인할 수 있습니다. 등록되지 않은 경우 다음 [단계](get-sensor-data-from-sensor-partner.md#enable-device-integration-with-farmbeats)에 따라 파트너를 추가합니다.

2. 올바른 원격 분석 메시지 형식을 사용했는지 확인합니다.

```json
{
"deviceid": "<id of the Device created>",
"timestamp": "<timestamp in ISO 8601 format>",
"version" : "1",
"sensors": [
    {
      "id": "<id of the sensor created>",
      "sensordata": [
        {
          "timestamp": "< timestamp in ISO 8601 format >",
          "<sensor measure name (as defined in the Sensor Model)>":<value>
        },
        {
          "timestamp": "<timestamp in ISO 8601 format>",
          "<sensor measure name (as defined in the Sensor Model)>": <value>
        }
      ]
    }
 ]
}
```

### <a name="dont-have-the-azure-event-hubs-connection-string"></a>Azure Event Hubs 연결 문자열이 없음

**정정 작업**

1. Datahub Swagger에서 Partner API로 이동합니다.
2. **Get** > **사용해 보기** > **실행**을 선택합니다.

> [!NOTE]
> 관심이 있는 센서 파트너의 파트너 ID입니다.

3. 파트너 API로 돌아가서 **가져오기/ \<ID> **를 선택 합니다.
4. 3단계의 파트너 ID를 지정하고 **실행**을 선택합니다.

   API 응답에는 Event Hubs 연결 문자열이 있어야 합니다.

### <a name="device-appears-offline"></a>디바이스가 오프라인으로 표시됨

**증상**: 디바이스가 설치되고 디바이스 파트너와 FarmBeats를 연결했습니다. 디바이스가 온라인 상태이고 원격 분석 데이터를 전송하지만 오프라인으로 표시됩니다.

**정정 작업** 이 디바이스에 대해 보고 간격이 구성되지 않았습니다. 보고 간격을 설정하려면 디바이스 제조업체에 문의하세요. 

### <a name="error-deleting-a-device"></a>디바이스를 삭제하는 동안 오류 발생

디바이스를 삭제하는 동안 다음과 같은 일반적인 오류 시나리오 중 하나가 발생할 수 있습니다.  

**메시지**: “디바이스가 센서에서 참조됩니다. 디바이스와 연결된 센서가 하나 이상 있습니다. 센서를 삭제한 다음 디바이스를 삭제합니다.”  

**의미**: 디바이스는 농장에 배포된 여러 센서와 연결되어 있습니다.

**정정 작업**  

1. 가속기를 통해 디바이스와 연결된 센서를 삭제합니다.  
2. 센서를 다른 디바이스에 연결하려면 디바이스 파트너에게 동일한 작업을 수행하도록 요청합니다.  
3. `DELETE API` 호출을 사용하여 디바이스를 삭제하고 force 매개 변수를 *true*로 설정합니다.  

**메시지**: “디바이스는 디바이스에서 ParentDeviceId로 참조됩니다. 이 디바이스와 자식 디바이스로 연결된 디바이스가 하나 이상 있습니다. 해당 디바이스를 삭제한 다음 이 디바이스를 삭제합니다.”  

**의미**: 디바이스에 다른 디바이스가 연결되어 있습니다.  

**정정 작업**

1. 이 특정 디바이스와 연결된 디바이스를 삭제합니다.  
2. 특정 디바이스를 삭제합니다.  

    > [!NOTE]
    > 센서가 연결되어 있는 경우에는 디바이스를 삭제할 수 없습니다. 관련 센서를 삭제하는 방법에 대한 자세한 내용은 [센서 파트너의 센서 데이터 가져오기](get-sensor-data-from-sensor-partner.md)에서 **센서 삭제** 섹션을 참조하세요.
    > 파트너에게 디바이스나 센서를 삭제할 수 있는 권한이 없습니다. 관리자만 삭제할 수 있는 권한이 있습니다.

## <a name="issues-with-jobs"></a>작업 관련 문제

### <a name="farmbeats-internal-error"></a>FarmBeats 내부 오류

**메시지**: “FarmBeats 내부 오류입니다. 자세한 내용은 문제 해결 가이드를 참조하세요.”

**정정 작업** 이 문제는 데이터 파이프라인의 일시적인 실패로 인해 발생할 수 있습니다. 작업을 다시 만듭니다. 오류가 계속 발생하면 오류 메시지/로그를 사용하여 문의해주세요.

## <a name="accelerator-troubleshooting"></a>가속기 문제 해결

### <a name="access-control"></a>Access Control

**문제**: 역할 할당을 추가하는 동안 오류가 발생합니다.

**메시지**: “일치하는 사용자를 찾을 수 없습니다.”

**정정 작업** 역할 할당을 추가하려는 메일 ID를 확인합니다. 메일 ID는 Active Directory에서 해당 사용자에 대해 등록된 ID와 정확히 일치해야 합니다. 오류가 계속 발생하면 오류 메시지/로그를 사용하여 문의해주세요.

### <a name="unable-to-log-in-to-accelerator"></a>가속기에 로그인할 수 없음

**메시지**: "오류: 서비스를 호출할 수 있는 권한이 없습니다. 관리자에게 권한 부여를 문의하세요.”

**정정 작업** FarmBeats 배포에 대한 액세스 권한을 부여하도록 관리자에게 요청합니다. 이 작업은 RoleAssignment API의 POST를 수행하거나 가속기의 **설정** 창에 있는 액세스 제어를 통해 수행할 수 있습니다.  

이미 액세스 권한이 부여되었는데도 이 오류가 발생한 경우 페이지를 새로 고치고 다시 시도하세요. 오류가 계속 발생하면 오류 메시지/로그를 사용하여 문의해주세요.

![프로젝트 FarmBeats](./media/troubleshoot-azure-farmbeats/accelerator-troubleshooting-1.png)

### <a name="accelerator-issues"></a>가속기 문제  

**문제**: 결정되지 않은 원인의 가속기 오류를 발생했습니다.

**메시지**: "오류: 알 수 없는 오류가 발생했습니다.”

**정정 작업** 이 오류는 페이지를 유휴 상태로 너무 오래 둘 경우 발생합니다. 페이지를 새로 고칩니다. 오류가 계속 발생하면 오류 메시지/로그를 사용하여 문의해주세요.

**문제**: FarmBeatsDeployment를 업그레이드한 후에도 FarmBeats 가속기에 최신 버전을 표시되지 않습니다.

**정정 작업** 이 오류는 브라우저에서 서비스 작업자 지속성으로 인해 발생합니다. 다음을 수행합니다.

1. 가속기가 열려 있는 모든 브라우저 탭을 닫고 브라우저 창을 닫습니다.
2. 브라우저의 새 인스턴스를 시작하고 가속기 URI를 다시 로드합니다. 이 작업을 수행하면 새 버전의 가속기가 로드됩니다.

## <a name="sentinel-imagery-related-issues"></a>Sentinel: 이미지 관련 문제

### <a name="wrong-username-or-password"></a>잘못된 사용자 이름 또는 암호

**작업 실패 메시지**: “이 리소스에 액세스하려면 전체 인증이 필요합니다.”

**정정 작업**: 다음 중 하나를 수행합니다.

- 아래 단계를 사용하여 FarmBeats를 올바른 사용자 이름/암호로 업데이트한 후 작업을 다시 시도하세요.

  **Sentinel 사용자 이름 업데이트**

    1. [Azure 포털](https://portal.azure.com)에 로그인합니다.
    2. **검색** 상자에서 FarmBeats Datahub 리소스 그룹을 검색합니다.
    3. Storage 계정 storage***** > **컨테이너** > **batch-prep-files** > **to_vm** > **config.ini**를 선택합니다.
    4. **편집**을 선택합니다.
    5. sentinel_account 섹션에서 사용자 이름을 업데이트합니다.

  **Sentinel 암호 업데이트**

    1. [Azure 포털](https://portal.azure.com)에 로그인합니다.
    2. **검색** 상자에서 FarmBeats Datahub 리소스 그룹을 검색합니다.
    3. keyvault-*****를 선택합니다.
    4. 설정 아래에서 액세스 정책을 선택합니다.
    5. **액세스 정책 추가**를 선택합니다.
    6. 템플릿에서 구성에 대해 **비밀 관리**를 사용하고 자신을 보안 주체에 추가합니다.
    7. **추가**를 선택하고 **액세스 정책** 페이지에서 **저장**을 선택합니다.
    8. **설정**에서 **비밀**을 선택합니다.
    9. **Sentinel-password**를 선택합니다.
    10. 값의 새 버전을 만들고 사용하도록 설정합니다.

- 실패한 작업을 다시 실행하거나, 5-7일의 날짜 범위로 위성 인덱스 작업을 실행한 후 작업이 성공했는지 확인합니다.

### <a name="sentinel-hub-wrongurlor-site-not-accessible"></a>Sentinel 허브: 잘못된 URL 또는 사이트에 액세스할 수 없음

**작업 실패 메시지**: “문제가 발생했습니다. 액세스하려는 페이지를 일시적으로 사용할 수 없습니다.”

**정정 작업**:

1. 브라우저에서 [Sentinel](https://scihub.copernicus.eu/dhus/)을 열어 웹 사이트에 액세스할 수 있는지 확인합니다.
2. 웹 사이트에 액세스할 수 없는 경우 방화벽, 회사 네트워크 또는 기타 차단 소프트웨어에서 웹 사이트에 대한 액세스를 막고 있는지 확인한 다음 필요한 단계를 수행하여 Sentinel URL을 허용합니다. 
3. 실패한 작업을 다시 실행하거나, 5-7일의 날짜 범위로 위성 인덱스 작업을 실행한 후 작업이 성공했는지 확인합니다.  

### <a name="sentinel-server-down-for-maintenance"></a>Sentinel 서버: 유지 관리를 위한 작동 중지

**작업 실패 메시지**: “Copernicus Open Access Hub가 곧 다시 출시될 예정입니다. 불편을 드려 죄송합니다. 지금은 몇 가지 유지 관리 작업을 수행하고 있습니다. 곧 다시 온라인 상태가 될 예정입니다.” 

**정정 작업**:

이 문제는 Sentinel 서버에서 유지 관리 작업을 수행하는 경우 발생할 수 있습니다.

1. 유지 관리를 수행하기 때문에 작업 또는 파이프라인이 실패할 경우 일정 시간 후에 작업을 다시 제출합니다. 

   계획되거나 계획되지 않은 Sentinel 유지 관리 활동에 대한 자세한 내용은 [Copernicus Open Access Hub News](https://scihub.copernicus.eu/news/) 사이트로 이동하세요.  

2. 실패한 작업을 다시 실행하거나, 5-7일의 날짜 범위로 위성 인덱스 작업을 실행한 후 작업이 성공했는지 확인합니다.

### <a name="sentinel-maximum-number-of-connections-reached"></a>Sentinel: 최대 연결 수 도달

**작업 실패 메시지**: "사용자 ' '이 (가) 달성 하는 최대 두 개의 동시 흐름 수 \<username> 입니다."

**의미**: 최대 연결 수에 도달하여 작업이 실패하는 경우 여러 작업에서 동일한 Sentinel 계정이 사용되고 있는 것입니다.

**정정 작업**: 다음 중 하나를 수행합니다.

* 실패한 작업을 다시 실행하기 전에 다른 작업이 완료될 때까지 기다립니다.
* 새 Sentinel 계정을 만든 다음 FarmBeats에서 Sentinel 사용자 이름 및 암호를 업데이트합니다.

### <a name="sentinel-server-refused-connection"></a>Sentinel 서버: 연결 거부됨

**작업 실패 메시지**: “서버 http://172.30.175.69:8983/solr/dhus 에서 연결을 거부했습니다.”

**정정 작업**: 이 문제는 Sentinel 서버에서 유지 관리 작업을 수행하는 경우 발생할 수 있습니다.

1. 유지 관리를 수행하기 때문에 작업 또는 파이프라인이 실패할 경우 일정 시간 후에 작업을 다시 제출합니다.

   계획되거나 계획되지 않은 Sentinel 유지 관리 활동에 대한 자세한 내용은 [Copernicus Open Access Hub News](https://scihub.copernicus.eu/news/) 사이트로 이동하세요.  

2. 실패한 작업을 다시 실행하거나, 5-7일의 날짜 범위로 위성 인덱스 작업을 실행한 후 작업이 성공했는지 확인합니다.

### <a name="soil-moisture-map-has-white-areas"></a>Soil Moisture 지도에 흰색 영역이 있음

**문제**: **Soil Moisture 지도**가 생성되었지만 지도가 대부분 흰색 영역입니다.

**정정 작업**: 지도가 요청된 시간에 대해 생성된 위성 인덱스에 0.3보다 작은 NDVI 값이 있는 경우 이 문제가 발생할 수 있습니다. 자세한 내용은 [Sentinel의 기술 가이드](https://earth.esa.int/web/sentinel/technical-guides/sentinel-2-msi/level-2a/algorithm)를 참조하세요.

1. 다른 날짜 범위에 대해 작업을 다시 실행하고 위성 인덱스의 NDVI 값이 0.3보다 큰지 확인합니다.

## <a name="collect-logs-manually"></a>수동으로 로그 수집

[Azure Storage Explorer를 설치하고 배포합니다]( https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows).

### <a name="collect-azure-data-factory-job-logs-or-app-service-logs-in-datahub"></a>Datahub에서 Azure Data Factory 작업 로그 또는 App Service 로그 수집

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **검색** 상자에서 FarmBeats Datahub 리소스 그룹을 검색합니다.
3. **리소스 그룹** 대시보드에서 *datahublogs\** 스토리지 계정을 검색합니다. 예를 들어 *datahublogsmvxmq*입니다.  
4. **이름** 열에서 스토리지 계정을 선택하여 **스토리지 계정** 대시보드를 확인합니다.
5. **datahubblogs\*** 창에서 **Explorer에서 열기**를 선택하여 **Azure Storage Explorer 열기** 애플리케이션을 확인합니다.
6. 왼쪽 창에서 **Blob 컨테이너**를 선택한 다음 Azure Data Factory 로그에 대해 **job-logs** 또는 App Service에 대해 **appinsights-logs**를 선택합니다.
7. **다운로드**를 선택하고 컴퓨터의 로컬 폴더에 로그를 다운로드합니다.

    ![프로젝트 FarmBeats](./media/troubleshoot-azure-farmbeats/collecting-logs-manually-1.png)

### <a name="collect-azure-data-factory-job-logs-or-app-service-logs-for-accelerator"></a>가속기에 대한 Azure Data Factory 작업 로그 또는 App Service 로그 수집

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **검색** 상자에서 FarmBeats 가속기 리소스 그룹을 검색합니다.
3. **리소스 그룹** 대시보드에서 *storage\** 스토리지 계정을 검색합니다. 예를 들어 *storagedop4k\** 입니다.
4. **이름** 열에서 스토리지 계정을 선택하여 **스토리지 계정** 대시보드를 확인합니다.
5. **storage\*** 창에서 **Explorer에서 열기**를 선택하여 Azure Storage Explorer 애플리케이션을 엽니다.
6. 왼쪽 창에서 **Blob 컨테이너**를 선택한 다음 Azure Data Factory 로그에 대해 **job-logs** 또는 App Service에 대해 **appinsights-logs**를 선택합니다.
7. **다운로드**를 선택하고 컴퓨터의 로컬 폴더에 로그를 다운로드합니다.

## <a name="high-cpu-usage"></a>높은 CPU 사용량

**오류**: **높은 CPU 사용량 경고**를 참조하는 메일 경고를 받게 됩니다.

**정정 작업**:

1. FarmBeats Datahub 리소스 그룹으로 이동합니다.
2. **App Service**를 선택합니다.  
3. [App Service 가격 책정 페이지](https://azure.microsoft.com/pricing/details/app-service/windows/) 스케일 업 페이지로 이동한 후 적절한 가격 책정 계층을 선택합니다.

## <a name="weather-data-job-failures"></a>날씨 데이터 작업 실패

**오류**: 날씨 데이터를 가져오기 위해 작업을 실행 하지만 작업이 실패 합니다.

### <a name="collect-logs-to-troubleshoot-weather-data-job-failures"></a>날씨 데이터 작업 오류 문제를 해결 하기 위한 로그 수집

1. Azure Portal에서 FarmBeats 리소스 그룹으로 이동 합니다.
2. 리소스 그룹의 일부인 Data Factory 서비스를 클릭 합니다. 서비스에는 "sku: Datahub" 태그가 있습니다.

> [!NOTE]
> 리소스 그룹 내에서 서비스의 태그를 보려면 "열 편집"을 클릭 하 고 리소스 그룹 보기에 "태그"를 추가 합니다.

:::image type="content" source="./media/troubleshoot-Azure-farmbeats/weather-log-1.png" alt-text="프로젝트 FarmBeats":::

3. 데이터 팩터리의 개요 페이지에서 **작성자 및 모니터**를 클릭 합니다. 브라우저에서 새 탭이 열립니다. **모니터** 를 클릭 합니다.

:::image type="content" source="./media/troubleshoot-Azure-farmbeats/weather-log-2.png" alt-text="프로젝트 FarmBeats":::

4. 날씨 작업 실행의 일부인 파이프라인 실행 목록이 표시 됩니다. 로그를 수집 하려는 작업을 클릭 합니다.
 
:::image type="content" source="./media/troubleshoot-Azure-farmbeats/weather-log-3.png" alt-text="프로젝트 FarmBeats":::

5. 파이프라인 개요 페이지에서 작업 실행 목록이 표시 됩니다. 로그를 수집 하려는 작업의 실행 Id를 적어 둡니다.
 
:::image type="content" source="./media/troubleshoot-Azure-farmbeats/weather-log-4.png" alt-text="프로젝트 FarmBeats":::

6. Azure Portal에서 FarmBeats 리소스 그룹으로 돌아가서 이름이 **datahublogs-XXXX** 인 저장소 계정을 클릭 합니다.
 
:::image type="content" source="./media/troubleshoot-Azure-farmbeats/weather-log-5.png" alt-text="프로젝트 FarmBeats":::

7. **컨테이너**  ->  **adfjobs**를 클릭 합니다. 검색 상자에 위의 5 단계에서 적어둔 작업 실행 ID를 입력 합니다.
 
:::image type="content" source="./media/troubleshoot-Azure-farmbeats/weather-log-6.png" alt-text="프로젝트 FarmBeats":::

8. 검색 결과에는 작업과 관련 된 로그가 있는 폴더가 포함 됩니다. 로그를 다운로드 하 여 문제를 디버깅 하는 데 도움이 되도록에 보냅니다 farmbeatssupport@microsoft.com .
