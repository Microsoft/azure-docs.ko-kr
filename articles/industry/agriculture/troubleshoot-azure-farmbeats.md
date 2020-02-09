---
title: Azure FarmBeats 문제 해결
description: 이 문서에서는 Azure FarmBeats 문제를 해결 하는 방법을 설명 합니다.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: fb4b06eca0d6df6848e2e215d8890569701f7596
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/23/2020
ms.locfileid: "76705618"
---
# <a name="troubleshoot"></a>문제 해결

이 문서에서는 일반적인 Azure FarmBeats 문제에 대 한 해결 방법을 제공 합니다.

추가 도움말을 보려면 farmbeatssupport@microsoft.com에 문의 하세요. 전자 메일에 **배포자** 파일을 포함 해야 합니다.

**배포자** 파일을 다운로드 하려면 다음을 수행 합니다.

1. **Azure Portal** 에 로그인 하 고 구독과 Azure AD 테넌트를 선택 합니다.
2. Azure Portal의 위쪽 탐색 모음에서 Cloud Shell을 시작합니다.
3. 기본 Cloud Shell 환경으로 **Bash** 를 선택 합니다.
4. 강조 표시 된 아이콘을 선택 하 고 드롭다운 목록에서 **다운로드**를 선택 합니다.

    ![프로젝트 FarmBeats](./media/troubleshoot-azure-farmbeats/download-deployer-log-1.png)

5. 다음 창에 **배포자** 파일에 대 한 경로를 입력 합니다. 예를 들어 **farmbeats-deployer**을 입력 합니다.

## <a name="sensor-telemetry"></a>센서 원격 분석

### <a name="cant-view-telemetry-data"></a>원격 분석 데이터를 볼 수 없음

**증상**: 장치 또는 센서가 배포 되 고 FarmBeats를 장치 파트너와 연결 했지만 FarmBeats에서 원격 분석 데이터를 가져오거나 볼 수 없습니다.

**수정 동작**:

1. FarmBeats Datahub 리소스 그룹으로 이동 합니다.   
2. **이벤트 허브** (DatafeedEventHubNamespace)를 선택 하 고 들어오는 메시지 수를 확인 합니다.
3. 다음 중 하나를 수행합니다.   
   - *들어오는 메시지가*없으면 장치 파트너에 게 문의 하세요.  
   - *들어오는 메시지가*있는 경우 farmbeatssupport@microsoft.com에 문의 하세요. Datahub 및 Accelerator 로그와 캡처된 원격 분석을 연결 합니다.

로그를 다운로드 하는 방법을 이해 하려면 ["수동으로 로그 수집"](#collect-logs-manually) 섹션으로 이동 하세요.  

### <a name="cant-view-telemetry-data-after-ingesting-historicalstreaming-data-from-your-sensors"></a>센서에서 기록/스트리밍 데이터를 수집 후 원격 분석 데이터를 볼 수 없음

**증상**: 장치 또는 센서가 배포 되 고 FarmBeats 및 수집 원격 분석에서 장치/센서를 EventHub에 만들었지만 FarmBeats에서 원격 분석 데이터를 가져오거나 볼 수 없습니다.

**수정 동작**:

1. 파트너 등록이 올바르게 완료 되었는지 확인-datahub swagger로 이동 하 고,/파트너 API로 이동 하 고, Get을 수행 하 고, 파트너가 등록 되었는지 확인 하 여이를 확인할 수 있습니다. 그렇지 않은 경우 파트너를 추가 하려면 [여기의 단계](get-sensor-data-from-sensor-partner.md#enable-device-integration-with-farmbeats) 를 따르세요.
2. 올바른 원격 분석 메시지 형식을 사용 했는지 확인 합니다.

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
          "<sensor measure name (as defined in the Sensor Model)>": <value>
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

**수정 동작**:

1. Datahub Swagger에서 파트너 API로 이동 합니다.
2. **가져오기** > 선택 **하** > **실행**을 시도 합니다.
3. 관심이 있는 센서 파트너의 파트너 ID를 적어 둡니다.
4. 파트너 API로 돌아가서 **Get/\<ID >** 를 선택 합니다.
5. 3 단계에서 만든 파트너 ID를 지정 하 고 **실행**을 선택 합니다.

   API 응답에는 Event Hubs 연결 문자열이 있어야 합니다.

### <a name="device-appears-offline"></a>장치가 오프 라인으로 표시 됩니다.

**증상**: 장치가 설치 되 고 FarmBeats를 장치 파트너와 연결 했습니다. 장치가 온라인 상태이 고 원격 분석 데이터를 전송 하지만 오프 라인으로 표시 됩니다.

**정정 동작**:이 장치에 대해 보고 간격이 구성 되지 않았습니다. 보고 간격을 설정 하려면 장치 제조업체에 문의 하십시오. 

### <a name="error-deleting-a-device"></a>장치를 삭제 하는 동안 오류 발생

장치를 삭제 하는 동안 다음과 같은 일반적인 오류 시나리오 중 하나가 발생할 수 있습니다.  

**메시지**: "장치가 센서에서 참조 됨: 장치와 연결 된 센서가 하나 이상 있습니다. 센서를 삭제 한 다음 장치를 삭제 합니다. "  

**의미**: 장치는 팜에 배포 된 여러 센서와 연결 되어 있습니다.   

**수정 동작**:  

1. 가속기를 통해 장치와 연결 된 센서를 삭제 합니다.  
2. 센서를 다른 장치에 연결 하려면 장치 파트너에 게 동일한 작업을 수행 하도록 요청 합니다.  
3. `DELETE API` 호출을 사용 하 여 장치를 삭제 하 고 force 매개 변수를 *true*로 설정 합니다.  

**메시지**: "장치가 parentdeviceid로 장치에서 참조 됨:이 장치와 연결 된 하나 이상의 장치를 자식 장치로 사용 합니다. 이 장치를 삭제 하 고 삭제 합니다. "  

**의미**: 장치에 다른 장치가 연결 되어 있습니다.  

**정정 작업**

1. 이 특정 장치와 연결 된 장치를 삭제 합니다.  
2. 특정 장치를 삭제 합니다.  

    > [!NOTE]
    > 센서가 연결 되어 있는 경우에는 장치를 삭제할 수 없습니다. 관련 센서를 삭제 하는 방법에 대 한 자세한 내용은 [센서 파트너에서 센서 데이터 가져오기](get-sensor-data-from-sensor-partner.md)의 "센서 삭제" 섹션을 참조 하세요.


## <a name="issues-with-jobs"></a>작업 관련 문제

### <a name="farmbeats-internal-error"></a>FarmBeats 내부 오류

**메시지**: "FarmBeats 내부 오류, 자세한 내용은 문제 해결 가이드를 참조 하세요."

**정정 동작**:이 문제는 데이터 파이프라인에서 일시적인 오류가 발생할 수 있습니다. 작업을 다시 만듭니다. 오류가 계속 발생 하면 FarmBeats 포럼의 게시물에 오류 메시지를 추가 하거나 FarmBeatsSupport@microsoft.com에 문의 하세요.

## <a name="accelerator-troubleshooting"></a>액셀러레이터 키 문제 해결

### <a name="access-control"></a>Access Control

**문제**: 역할 할당을 추가 하는 동안 오류가 발생 합니다.

**메시지**: "일치 하는 사용자를 찾을 수 없습니다."

**정정 작업**: 역할 할당을 추가 하려는 전자 메일 ID를 확인 합니다. 전자 메일 ID는 Active Directory에서 해당 사용자에 대해 등록 된 ID와 정확히 일치 해야 합니다. 오류가 계속 발생 하면 FarmBeats 포럼의 게시물에 오류 메시지를 추가 하거나 FarmBeatsSupport@microsoft.com에 문의 하세요.

### <a name="unable-to-log-in-to-accelerator"></a>액셀러레이터에 로그인 할 수 없습니다.

**메시지**: "오류: 서비스를 호출할 수 있는 권한이 없습니다. 권한 부여에 대 한 관리자에 게 문의 하십시오. "

**정정 작업**: 관리자에 게 FarmBeats 배포에 대 한 액세스 권한을 부여 하도록 요청 합니다. 이 작업은 RoleAssignment의 게시를 수행 하거나 **설정** 창의 액셀러레이터 키에서 Access Control를 통해 수행할 수 있습니다.  

이미 액세스 권한이 부여 되 고이 오류가 발생 하는 경우 페이지를 새로 고쳐 다시 시도 하세요. 오류가 계속 발생 하면 FarmBeats 포럼의 게시물에 오류 메시지를 추가 하거나 FarmBeatsSupport@microsoft.com에 문의 하세요.

![프로젝트 FarmBeats](./media/troubleshoot-azure-farmbeats/accelerator-troubleshooting-1.png)

### <a name="accelerator-issues"></a>액셀러레이터 키 문제  

**문제**: 알 수 없는 원인으로 인 한 가속기 오류가 발생 했습니다.

**메시지**: "오류: 알 수 없는 오류가 발생 했습니다."

**정정 작업**:이 오류는 페이지를 유휴 상태로 유지 하는 경우에 발생 합니다. 페이지를 새로 고칩니다.  

오류가 계속 발생 하면 FarmBeats 포럼의 게시물에 오류 메시지를 추가 하거나 FarmBeatsSupport@microsoft.com에 문의 하세요.

**문제**: FarmBeatsDeployment을 업그레이드 한 후에도 FarmBeats Accelerator가 최신 버전을 표시 하지 않습니다.

**정정 동작**:이 오류는 브라우저에서 서비스 작업자 지 속성으로 인해 발생 합니다. 다음을 수행합니다.
1. 액셀러레이터 키가 열려 있는 모든 브라우저 탭을 닫고 브라우저 창을 닫습니다.
2. 브라우저의 새 인스턴스를 시작 하 고 액셀러레이터 키 URI를 다시 로드 합니다. 이 작업을 수행 하면 새 버전의 가속기가 로드 됩니다.

## <a name="sentinel-imagery-related-issues"></a>센티널: 이미지와 관련 된 문제

### <a name="wrong-username-or-password"></a>사용자 이름 또는 암호가 잘못 되었습니다.

**작업 실패 메시지**: "이 리소스에 액세스 하려면 전체 인증이 필요 합니다."

**수정 동작**:

다음 중 하나를 수행합니다.
- 올바른 사용자 이름 및 암호를 사용 하 여 Datahub를 업그레이드 하기 위해 설치 관리자를 다시 실행 합니다.
- 실패 한 작업을 다시 실행 하거나, 5-7 일의 날짜 범위에 대해 위성 인덱스 작업을 실행 한 후 작업이 성공 했는지 확인 합니다.

### <a name="sentinel-hub-wrongurlor-site-not-accessible"></a>센티널 허브: 잘못 된 URL 또는 사이트에 액세스할 수 없음 

**작업 실패 메시지**: "오류가 발생 했습니다. 액세스 하려는 페이지를 일시적으로 사용할 수 없습니다. " 

**수정 동작**:
1. 브라우저에서 [센티널](https://scihub.copernicus.eu/dhus/) 을 열어 웹 사이트에 액세스할 수 있는지 여부를 확인 합니다. 
2. 웹 사이트에 액세스할 수 없는 경우 방화벽, 회사 네트워크 또는 기타 차단 소프트웨어에서 웹 사이트에 대 한 액세스를 차단 하는지 확인 한 다음 필요한 단계를 수행 하 여 센티널 URL을 허용 하십시오. 
3. 실패 한 작업을 다시 실행 하거나, 5-7 일의 날짜 범위에 대해 위성 인덱스 작업을 실행 한 후 작업이 성공 했는지 확인 합니다.  

### <a name="sentinel-server-down-for-maintenance"></a>센티널 서버: 유지 관리를 위한 다운

**작업 실패 메시지**: "Copernicus 오픈 액세스 허브가 곧 다시 출시 될 예정입니다. 불편을 끼쳐 드려 지금은 몇 가지 유지 관리 작업을 수행 하 고 있습니다. 곧 다시 온라인 상태가 될 예정입니다. " 

**수정 동작**:

이 문제는 센티널 서버에서 유지 관리 작업을 수행 하는 경우 발생할 수 있습니다.

1. 유지 관리를 수행 하기 때문에 작업 또는 파이프라인이 실패 하면 일정 시간 후에 작업을 다시 제출 합니다. 

   계획 되거나 계획 되지 않은 센티널 유지 관리 활동에 대 한 자세한 내용은 [Copernicus Open Access Hub News](https://scihub.copernicus.eu/news/) 사이트로 이동 하세요.  
2. 실패 한 작업을 다시 실행 하거나, 5-7 일의 날짜 범위에 대해 위성 인덱스 작업을 실행 한 후 작업이 성공 했는지 확인 합니다.

### <a name="sentinel-maximum-number-of-connections-reached"></a>센티널: 최대 연결 수에 도달 했습니다.

**작업 실패 메시지**: "사용자가 '\<사용자 이름 > '에 의해 발생 하는 최대 두 개의 동시 흐름 수입니다."

**의미**: 최대 연결 수에 도달 하 여 작업이 실패 하는 경우 동일한 센티널 계정이 다른 소프트웨어 배포에서 사용 되 고 있습니다.

**정정 작업**: 다음 중 하나를 시도 합니다.
* 새 센티널 계정을 만든 다음 설치 관리자를 다시 실행 하 여 새 센티널 사용자 이름 및 암호를 사용 하 여 Datahub를 업그레이드 합니다.  
* 실패 한 작업을 다시 실행 하거나, 5-7 일의 날짜 범위에 대해 위성 인덱스 작업을 실행 한 후 작업이 성공 했는지 확인 합니다.

### <a name="sentinel-server-refused-connection"></a>센티널 서버: 연결 거부 됨 

**작업 실패 메시지**: "서버에서 연결을 거부 했습니다." http://172.30.175.69:8983/solr/dhus. " 

**정정 동작**: 센티널 서버에서 유지 관리 작업을 수행 하는 경우이 문제가 발생할 수 있습니다. 
1. 유지 관리를 수행 하기 때문에 작업 또는 파이프라인이 실패 하면 일정 시간 후에 작업을 다시 제출 합니다. 

   계획 되거나 계획 되지 않은 센티널 유지 관리 활동에 대 한 자세한 내용은 [Copernicus Open Access Hub News](https://scihub.copernicus.eu/news/) 사이트로 이동 하세요.  
2. 실패 한 작업을 다시 실행 하거나, 5-7 일의 날짜 범위에 대해 위성 인덱스 작업을 실행 한 후 작업이 성공 했는지 확인 합니다.

## <a name="collect-logs-manually"></a>수동으로 로그 수집

[Azure Storage 탐색기를 설치 하 고 배포]( https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows)합니다.

### <a name="collect-azure-data-factory-job-logs-in-datahub"></a>Datahub에서 Azure Data Factory 작업 로그 수집
1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **검색** 상자에서 FarmBeats datahub 리소스 그룹을 검색 합니다.

    > [!NOTE]
    > FarmBeats 설치 중에 지정한 Datahub 리소스 그룹을 선택 합니다.

3. **리소스 그룹** 대시보드에서 *datahublogs\** storage 계정을 검색 합니다. 예를 들어 **datahublogsmvxmq**를 검색 합니다.  
4. **이름** 열에서 저장소 계정을 선택 하 여 **저장소 계정** 대시보드를 확인 합니다.
5. **Datahubblogs\*** 창에서 **탐색기에서 열기** 를 선택 하 여 열려 있는 **Azure Storage 탐색기** 응용 프로그램을 봅니다.
6. 왼쪽 창에서 **Blob 컨테이너**를 선택 하 고 **작업 로그**를 선택 합니다.
7. **작업-로그** 창에서 **다운로드**를 선택 합니다.
8. 컴퓨터의 로컬 폴더에 로그를 다운로드 합니다.
9. 다운로드 한 .zip 파일을 farmbeatssupport@microsoft.com에 전자 메일로 보냅니다.

    ![프로젝트 FarmBeats](./media/troubleshoot-azure-farmbeats/collecting-logs-manually-1.png)

### <a name="collect-azure-data-factory-job-logs-in-accelerator"></a>Accelerator에서 Azure Data Factory 작업 로그 수집

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **검색** 상자에서 FarmBeats Accelerator 리소스 그룹을 검색 합니다.

    > [!NOTE]
    > FarmBeats 설치 중에 지정한 액셀러레이터 리소스 그룹을 선택 합니다.

3. **리소스 그룹** 대시보드에서 저장소 *\** 저장소 계정을 검색 합니다. 예를 들어 **storagedop4k\*** 를 검색 합니다.
4. **저장소** 계정 대시보드를 보려면 **이름** 열에서 storage 계정을 선택 합니다.
5. **저장소\*** 창에서 **탐색기에서 열기** 를 선택 하 여 Azure Storage 탐색기 응용 프로그램을 엽니다.
6. 왼쪽 창에서 **Blob 컨테이너**를 선택 하 고 **작업 로그**를 선택 합니다.
7. **작업-로그** 창에서 **다운로드**를 선택 합니다.
8. 컴퓨터의 로컬 폴더에 로그를 다운로드 합니다.
9. 다운로드 한 .zip 파일을 farmbeatssupport@microsoft.com에 전자 메일로 보냅니다.


### <a name="collect-datahub-app-service-logs"></a>Datahub app service 로그 수집

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **검색** 상자에서 FarmBeats datahub 리소스 그룹을 검색 합니다.

    > [!NOTE]
    > FarmBeats 설치 중에 지정한 Datahub 리소스 그룹을 선택 합니다.

3. 리소스 그룹에서 *datahublogs\** storage 계정을 검색 합니다. 예를 들어 **fordatahublogsmvxmq\*** 를 검색 합니다.
4. **저장소** 계정 대시보드를 보려면 **이름** 열에서 storage 계정을 선택 합니다.
5. **Datahubblogs\*** 창에서 **탐색기에서 열기** 를 선택 하 여 Azure Storage 탐색기 응용 프로그램을 엽니다.
6. 왼쪽 창에서 **Blob 컨테이너**를 선택 하 고 **appinsights-로그**를 선택 합니다.
7. **Appinsights-로그** 창에서 **다운로드**를 선택 합니다.
8. 컴퓨터의 로컬 폴더에 로그를 다운로드 합니다.
9. 다운로드 한 .zip 파일을 farmbeatssupport@microsoft.com에 전자 메일로 보냅니다.

### <a name="collect-accelerator-app-service-logs"></a>Accelerator app service 로그 수집

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **검색** 상자에서 FarmBeats Accelerator 리소스 그룹을 검색 합니다.

    > [!NOTE]
    > FarmBeats 설치 중에 제공 된 FarmBeats Accelerator 리소스 그룹을 선택 합니다.

3. 리소스 그룹에서 저장소 *\** 저장소 계정을 검색 합니다. 예를 들어 **storagedop4k\*** 를 검색 합니다.
4. **저장소** 계정 대시보드를 보려면 **이름** 열에서 storage 계정을 선택 합니다.
5. **저장소\*** 창에서 **탐색기에서 열기** 를 선택 하 여 Azure Storage 탐색기 응용 프로그램을 엽니다.
6. 왼쪽 창에서 **Blob 컨테이너**를 선택 하 고 **appinsights-로그**를 선택 합니다.
7. **Appinsights-로그** 창에서 **다운로드**를 선택 합니다.
8. 컴퓨터의 로컬 폴더에 로그를 다운로드 합니다.
9. 다운로드 한 폴더를 farmbeatssupport@microsoft.com에 전자 메일로 보냅니다.

## <a name="known-issues"></a>알려진 문제

## <a name="batch-related-issues"></a>일괄 처리 관련 문제

**오류 메시지**: "지정 된 구독에 대 한 Batch 계정의 지역 계정 할당량에 도달 했습니다."

**정정 작업**: 할당량을 늘리거나 사용 하지 않는 batch 계정을 삭제 한 후 배포를 다시 실행 합니다.

### <a name="azure-active-directory-azure-ad-related-issues"></a>Azure Active Directory (Azure AD) 관련 문제

**오류 메시지**: "Azure AD 앱에 필요한 설정을 업데이트할 수 없습니다. d41axx40-xx21-97xxx9e2xxc0: 작업을 완료할 수 있는 권한이 없습니다. 위의 설정이 Azure AD 앱에 대해 올바르게 구성 되어 있는지 확인 하십시오. "

**의미**: Azure AD 앱 등록 구성이 제대로 완료 되지 않았습니다.  

**정정 작업**: IT 관리자 (테넌트 읽기 액세스 권한이 있는 사용자)에 게 Azure AD 앱 등록을 만드는 [스크립트](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect/tree/master/AppCreationScripts) 를 사용 하도록 요청 합니다. 이 스크립트는 구성 단계도 자동으로 처리 합니다.

**오류 메시지**: "이 테넌트에서 새 Active Directory 응용 프로그램 '\<응용 프로그램 이름\>'을 (를) 만들 수 없습니다. 속성 식별자 uri에 대해 동일한 값을 가진 다른 개체가 이미 있습니다."

**의미**: 이름이 같은 Azure AD 앱 등록이 이미 있습니다.

**수정 작업**: 기존 Azure AD 앱 등록을 삭제 하거나 설치를 위해 다시 사용 합니다. 기존 Azure AD 앱 등록을 다시 사용 하는 경우 응용 프로그램 ID 및 클라이언트 암호를 설치 관리자에 전달 하 고 다시 배포 합니다.

## <a name="issues-with-the-inputjson-file"></a>Input. json 파일의 문제

**오류**: *입력. json* 파일에서 입력을 읽는 동안 오류가 발생 했습니다.

**정정 작업**:이 문제는 일반적으로 올바른 *입력. json* 파일 경로를 지정 하는 데 오류가 발생 하거나 설치 관리자에 이름을 지정 하는 경우 발생 합니다. 적절 한 수정 작업을 수행 하 고 배포를 다시 시도 합니다.

**오류**: *입력. json* 파일의 값을 구문 분석 하는 동안 오류가 발생 했습니다.

**정정 동작**:이 문제는 주로 *입력. json* 파일 내의 잘못 된 값 때문에 발생 합니다. 적절 한 수정 작업을 수행 하 고 배포를 다시 시도 합니다.

## <a name="high-cpu-usage"></a>높은 CPU 사용량

**오류**: **높은 CPU 사용량 경고**를 참조 하는 전자 메일 경고를 받게 됩니다. 

**수정 동작**: 
1. FarmBeats Datahub 리소스 그룹으로 이동 합니다.
2. **App service**를 선택 합니다.  
3. 규모 확장 [App Service 가격 책정 페이지로](https://azure.microsoft.com/pricing/details/app-service/windows/)이동한 다음 적절 한 가격 책정 계층을 선택 합니다.

## <a name="next-steps"></a>다음 단계

FarmBeats 문제가 여전히 발생 하는 경우 [지원 포럼](https://social.msdn.microsoft.com/Forums/home?forum=ProjectFarmBeats)에 문의 하세요.
