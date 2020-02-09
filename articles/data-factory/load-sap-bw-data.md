---
title: SAP Business Warehouse에서 데이터 로드
description: Azure Data Factory를 사용 하 여 SAP Business Warehouse에서 데이터 복사 (BW)
services: data-factory
author: linda33wj
ms.author: jingwang
manager: shwang
ms.reviewer: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/22/2019
ms.openlocfilehash: 971871c28bd1b38b134c04b0334fbe99d1d655c1
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75440191"
---
# <a name="copy-data-from-sap-business-warehouse-by-using-azure-data-factory"></a>Azure Data Factory를 사용 하 여 SAP Business Warehouse에서 데이터 복사

이 문서에서는 Azure Data Factory를 사용 하 여 개방형 허브를 통해 SAP Business Warehouse (BW)에서 Azure Data Lake Storage Gen2로 데이터를 복사 하는 방법을 보여 줍니다. 유사한 프로세스를 사용 하 여 다른 [지원 되는 싱크 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats)로 데이터를 복사할 수 있습니다.

> [!TIP]
> SAP BW에서 데이터를 복사 하는 방법에 대한 일반적인 내용은 SAP BW Open Hub 통합 및 델타 추출 흐름을 참조 하세요. [Azure Data Factory를 사용 하 여 개방형 허브를 통해 SAP Business Warehouse에서 데이터 복사](connector-sap-business-warehouse-open-hub.md)를 참조 하세요.

## <a name="prerequisites"></a>필수 조건

- **Azure Data Factory**: 없는 경우 [데이터 팩터리를 만드는](quickstart-create-data-factory-portal.md#create-a-data-factory)단계를 수행 합니다.

- **대상 유형이 "데이터베이스 테이블" 인 ohd (SAP BW Open Hub destination)** : ohd를 만들거나 ohd가 Data Factory 통합에 대해 올바르게 구성 되었는지 확인 하려면이 문서의 [SAP BW 개방형 허브 대상 구성](#sap-bw-open-hub-destination-configurations) 섹션을 참조 하세요.

- **SAP BW 사용자에 게 다음 권한이 필요**합니다.

  - RFC (원격 함수 호출) 및 SAP BW에 대한 권한 부여입니다.
  - **S_SDSAUTH** 권한 부여 개체의 "실행" 작업에 대한 사용 권한

- **SAP .net connector 3.0를 사용 하는 [자체 호스팅 IR (통합 런타임)](concepts-integration-runtime.md#self-hosted-integration-runtime)** 다음 설정 단계를 따르세요.

  1. 자체 호스팅 통합 런타임 버전 3.13 이상을 설치 하 고 등록 합니다. 이 프로세스는이 문서의 뒷부분에 설명 되어 있습니다.

  2. SAP의 웹 사이트에서 [Microsoft .NET 3.0 용 64 비트 Sap 커넥터](https://support.sap.com/en/product/connectors/msnet.html) 를 다운로드 하 고 자체 호스팅 IR과 동일한 컴퓨터에 설치 합니다. 설치 하는 동안 다음 그림에 표시 된 것 처럼 **선택적 설정 단계** 대화 상자에서 **GAC에 어셈블리 설치** 를 선택 해야 합니다.

     ![SAP .NET 커넥터 설정 대화 상자](media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

## <a name="do-a-full-copy-from-sap-bw-open-hub"></a>SAP BW 열려 있는 허브에서 전체 복사본을 만듭니다.

Azure Portal에서 데이터 팩터리로 이동합니다. **작성자 & 모니터** 를 선택 하 여 별도의 탭에서 Data Factory UI를 엽니다.

1. **시작** 하기 페이지에서 **데이터 복사** 를 선택 하 여 데이터 복사 도구를 엽니다.

2. **속성** 페이지에서 **작업 이름을**지정 하 고 **다음**을 선택 합니다.

3. **원본 데이터 저장소** 페이지에서 **+ 새 연결 만들기**를 선택 합니다. 커넥터 갤러리에서 **SAP BW 열려 있는 허브** 를 선택 하 고 **계속**을 선택 합니다. 커넥터를 필터링 하기 위해 검색 상자에 **SAP** 를 입력할 수 있습니다.

4. **SAP BW 열려 있는 허브 연결 지정** 페이지에서 다음 단계에 따라 새 연결을 만듭니다.

   ![허브 연결 된 서비스 페이지 SAP BW 열기](media/load-sap-bw-data/create-sap-bw-open-hub-linked-service.png)

   1. **Integration runtime을 통해 연결** 목록에서 기존 자체 호스팅 IR을 선택 합니다. 또는 아직 없는 경우 새로 만들기를 선택 합니다.

      새 자체 호스팅 IR을 만들려면 **+ 새로**만들기를 선택 하 고 **자체 호스팅**을 선택 합니다. **이름을**입력 하 고 **다음**을 선택 합니다. 현재 컴퓨터에 설치 하려면 **빠른 설치** 를 선택 하 고, 제공 된 **수동 설정** 단계를 따릅니다.

      [필수 구성 요소](#prerequisites)에 설명 된 대로 Microsoft .NET 3.0 용 SAP Connector가 자체 호스팅 IR이 실행 되는 컴퓨터에 설치 되어 있는지 확인 합니다.

   2. SAP BW **서버 이름**, **시스템 번호**, **클라이언트 ID,** **언어** ( **en-us**이외의 경우), **사용자 이름**및 **암호**를 입력 합니다.

   3. **연결 테스트** 를 선택 하 여 설정의 유효성을 검사 한 다음 **마침**을 선택 합니다.

   4. 새 연결이 생성 됩니다. **다음**을 선택합니다.

5. 열려 있는 **허브 대상 선택** 페이지에서 SAP BW에서 사용할 수 있는 열려 있는 허브 대상을 검색 합니다. 데이터를 복사할 OHD를 선택 하 고 **다음**을 선택 합니다.

   ![SAP BW 열려 있는 허브 대상 테이블을 선택 합니다.](media/load-sap-bw-data/select-sap-bw-open-hub-table.png)

6. 필요한 경우 필터를 지정 합니다. OHD에 단일 요청 ID를 사용 하는 단일 DTP (데이터 전송 프로세스) 실행의 데이터만 포함 하거나 DTP이 완료 되 고 데이터를 복사 하려면 **마지막 요청 제외** 확인란의 선택을 취소 합니다.

   이러한 설정에 대한 자세한 내용은이 문서의 [SAP BW 열린 허브 대상 구성](#sap-bw-open-hub-destination-configurations) 섹션을 참조 하세요. **유효성 검사** 를 선택 하 여 반환할 데이터를 두 번 선택 합니다. 그런 후 **다음**을 선택합니다.

   ![SAP BW 열린 허브 필터 구성](media/load-sap-bw-data/configure-sap-bw-open-hub-filter.png)

7. **대상 데이터 저장소** 페이지에서 **+ 새 연결 만들기** > **Azure Data Lake Storage Gen2** 선택 하 > **계속**합니다.

8. **Azure Data Lake Storage 연결 지정** 페이지에서 다음 단계를 수행 하 여 연결을 만듭니다.

   ![ADLS Gen2 연결 된 서비스 페이지 만들기](media/load-sap-bw-data/create-adls-gen2-linked-service.png)

   1. **이름** 드롭다운 목록에서 Data Lake Storage Gen2 사용할 수 있는 계정을 선택 합니다.
   2. **마침**을 선택하여 연결을 만듭니다. 그런 후 **다음**을 선택합니다.

9. **출력 파일 또는 폴더 선택** 페이지에서 출력 폴더 이름으로 **copyfromopenhub** 을 입력 합니다. 그런 후 **다음**을 선택합니다.

   ![출력 폴더 선택 페이지](media/load-sap-bw-data/choose-output-folder.png)

10. **파일 형식 설정** 페이지에서 **다음** 을 선택 하 여 기본 설정을 사용 합니다.

    ![싱크 형식 지정 페이지](media/load-sap-bw-data/specify-sink-format.png)

11. **설정** 페이지에서 **성능 설정**을 확장 합니다. 5와 같은 **복사 병렬 처리 수준** 에 대한 값을 입력 하 여 동시에 SAP BW에서 로드 합니다. 그런 후 **다음**을 선택합니다.

    ![복사 설정 구성](media/load-sap-bw-data/configure-copy-settings.png)

12. **요약** 페이지에서 설정을 검토합니다. 그런 후 **다음**을 선택합니다.

13. **배포** 페이지에서 **모니터** 를 선택 하 여 파이프라인을 모니터링 합니다.

    ![배포 페이지](media/load-sap-bw-data/deployment.png)

14. 페이지의 왼쪽에 있는 **모니터** 탭이 자동으로 선택 됩니다. **작업** 열에는 활동 실행 세부 정보를 보고 파이프라인을 다시 실행 하기 위한 링크가 포함 되어 있습니다.

    ![파이프라인 모니터링 보기](media/load-sap-bw-data/pipeline-monitoring.png)

15. 파이프라인 실행과 연결 된 활동 실행을 보려면 **작업** 열에서 **활동 실행 보기** 를 선택 합니다. 파이프라인에는 하나의 작업(복사 작업)만 있으므로 하나의 항목만 표시됩니다. 파이프라인 실행 보기로 다시 전환 하려면 위쪽의 **파이프라인** 링크를 선택 합니다. **새로 고침**을 선택하여 목록을 새로 고칩니다.

    ![작업-모니터링 화면](media/load-sap-bw-data/activity-monitoring.png)

16. 각 복사 작업의 실행 세부 정보를 모니터링 하려면 작업 모니터링 보기의 **작업** 아래에 있는 안경 아이콘 인 **세부 정보** 링크를 선택 합니다. 사용 가능한 세부 정보에는 원본에서 싱크로 복사 된 데이터 볼륨, 데이터 처리량, 실행 단계 및 기간, 사용 되는 구성이 포함 됩니다.

    ![작업 모니터링 세부 정보](media/load-sap-bw-data/activity-monitoring-details.png)

17. **최대 요청 ID**를 보려면 작업 모니터링 보기로 돌아가 **작업**에서 **출력** 을 선택 합니다.

    ![작업 출력 화면](media/load-sap-bw-data/activity-output.png)

    ![작업 출력 정보 보기](media/load-sap-bw-data/activity-output-details.png)

## <a name="incremental-copy-from-sap-bw-open-hub"></a>SAP BW 열린 허브의 증분 복사

> [!TIP]
> Data Factory에서 열린 허브 커넥터가 SAP BW에서 증분 데이터를 복사 하 SAP BW는 방법에 대한 자세한 내용은 [SAP BW 열린 허브 커넥터 델타 추출 흐름](connector-sap-business-warehouse-open-hub.md#delta-extraction-flow) 을 참조 하세요. 이 문서는 기본 커넥터 구성을 이해 하는 데에도 도움이 됩니다.

이제 SAP BW 열려 있는 허브에서 증분 복사를 계속 구성 하겠습니다.

증분 복사는 **요청 ID**를 기반으로 하는 "상위 워터 마크" 메커니즘을 사용 합니다. 이 ID는 DTP에서 열린 허브 대상 SAP BW 자동으로 생성 됩니다. 다음 다이어그램은이 워크플로를 보여 줍니다.

![증분 복사 워크플로 흐름 차트](media/load-sap-bw-data/incremental-copy-workflow.png)

데이터 팩터리 **시작** 페이지에서 기본 제공 템플릿을 사용 하도록 **템플릿에서 파이프라인 만들기** 를 선택 합니다.

1. **SAP BW** 를 검색 하 여 **SAP BW에서 Azure Data Lake Storage Gen2 템플릿으로의 증분 복사** 를 찾아 선택 합니다. 이 템플릿은 데이터를 Azure Data Lake Storage Gen2에 복사 합니다. 유사한 워크플로를 사용 하 여 다른 싱크 형식으로 복사할 수 있습니다.

2. 템플릿의 주 페이지에서 다음 세 개의 연결을 선택 하거나 만든 다음 창의 오른쪽 아래 모퉁이에서 **이 템플릿 사용** 을 선택 합니다.

   - **Azure blob storage**:이 연습에서는 azure blob storage를 사용 하 여 *최대 복사 된 요청 ID*인 상위 워터 마크를 저장 합니다.
   - **SAP BW 열린 허브**: 데이터를 복사할 원본입니다. 자세한 구성은 이전 전체 복사 연습을 참조 하세요.
   - **Azure Data Lake Storage Gen2**: 데이터를 복사할 싱크입니다. 자세한 구성은 이전 전체 복사 연습을 참조 하세요.

   ![SAP BW 템플릿에서 증분 복사](media/load-sap-bw-data/incremental-copy-from-sap-bw-template.png)

3. 이 템플릿은 다음과 같은 세 가지 작업을 사용 하 여 파이프라인을 생성 하 고 성공 시 *조회*, *데이터 복사*및 *웹*에 연결 합니다.

   파이프라인 **매개 변수** 탭으로 이동 합니다. 제공 해야 하는 모든 구성이 표시 됩니다.

   ![SAP BW 구성의 증분 복사](media/load-sap-bw-data/incremental-copy-from-sap-bw-pipeline-config.png)

   - **SAPOpenHubDestinationName**: 데이터를 복사할 열린 허브 테이블 이름을 지정 합니다.

   - **Data_Destination_Container**: 데이터를 복사할 대상 Azure Data Lake Storage Gen2 컨테이너를 지정 합니다. 컨테이너가 존재 하지 않는 경우 Data Factory 복사 작업을 실행 하는 동안 작업을 만듭니다.
  
   - **Data_Destination_Directory**: 데이터를 복사할 Azure Data Lake Storage Gen2 컨테이너 아래에 폴더 경로를 지정 합니다. 경로가 존재 하지 않는 경우 Data Factory 복사 작업을 실행 하는 동안 경로를 만듭니다.
  
   - **HighWatermarkBlobContainer**: 상위 워터 마크 값을 저장할 컨테이너를 지정 합니다.

   - **HighWatermarkBlobDirectory**: 컨테이너 아래에 있는 폴더 경로를 지정 하 여 상위 워터 마크 값을 저장 합니다.

   - **HighWatermarkBlobName**: `requestIdCache.txt`와 같은 상위 워터 마크 값을 저장할 blob 이름을 지정 합니다. Blob storage에서 HighWatermarkBlobContainer + HighWatermarkBlobDirectory + HighWatermarkBlobName의 해당 경로 (예: *컨테이너/경로/requestIdCache .txt*)로 이동 합니다. 콘텐츠가 0 인 blob을 만듭니다.

      ![BLOB 콘텐츠](media/load-sap-bw-data/blob.png)

   - **LogicAppURL**:이 템플릿에서는 webactivity를 사용 하 여 Blob storage에서 상위 워터 마크 값을 설정 하 Azure Logic Apps를 호출 합니다. 또는 Azure SQL Database를 사용 하 여 저장할 수 있습니다. 저장 프로시저 작업을 사용 하 여 값을 업데이트 합니다.

      다음 그림에 나와 있는 것 처럼 먼저 논리 앱을 만들어야 합니다. 그런 다음 **HTTP POST URL**에 붙여 넣습니다.

      ![논리 앱 구성](media/load-sap-bw-data/logic-app-config.png)

      1. Azure Portal로 이동합니다. 새 **Logic Apps** 서비스를 선택 합니다. **+ 빈 논리 앱** 을 선택 하 **Logic Apps 디자이너로**이동 합니다.

      2. **HTTP 요청을 받을 때**의 트리거를 만듭니다. 다음과 같이 HTTP 요청 본문을 지정 합니다.

         ```json
         {
            "properties": {
               "sapOpenHubMaxRequestId": {
                  "type": "string"
               }
            },
            "type": "object"
         }
         ```

      3. **Blob 만들기** 작업을 추가 합니다. **폴더 경로** 및 **Blob 이름**에 대해 이전에 *HighWatermarkBlobContainer + HighWatermarkBlobDirectory* 및 *HighWatermarkBlobName*에서 구성한 것과 동일한 값을 사용 합니다.

      4. **저장**을 선택합니다. 그런 다음 Data Factory 파이프라인에서 사용할 **HTTP POST URL** 의 값을 복사 합니다.

4. Data Factory 파이프라인 매개 변수를 제공한 후 **디버그** > **마침** 을 선택 하 여 실행을 호출 하 여 구성의 유효성을 검사 합니다. 또는 **게시** 를 선택 하 여 모든 변경 내용을 게시 한 후 **트리거 추가** 를 선택 하 여 실행을 실행 합니다.

## <a name="sap-bw-open-hub-destination-configurations"></a>SAP BW 열린 허브 대상 구성

이 섹션에서는 Data Factory에서 SAP BW Open Hub 커넥터를 사용 하 여 데이터를 복사 하는 SAP BW 쪽의 구성을 소개 합니다.

### <a name="configure-delta-extraction-in-sap-bw"></a>SAP BW에서 델타 추출 구성

기록 복사 및 증분 복사 나 증분 복사본만 모두 필요한 경우 SAP BW에서 델타 추출을 구성 합니다.

1. 오픈 허브 대상을 만듭니다. 필요한 변환 및 데이터 전송 프로세스를 자동으로 만드는 SAP Transaction RSA1에서 OHD를 만들 수 있습니다. 다음 설정을 사용합니다.

   - **ObjectType**: 모든 개체 유형을 사용할 수 있습니다. 여기에서는 **InfoCube** 를 예로 사용 합니다.
   - **대상 유형**: **데이터베이스 테이블**을 선택 합니다.
   - **테이블의 키**: **기술 키**를 선택 합니다.
   - **추출**: **데이터 유지 및 테이블에 레코드 삽입**을 선택 합니다.

   ![SAP BW OHD 델타 추출 대화 상자 만들기](media/load-sap-bw-data/create-sap-bw-ohd-delta.png)

   ![SAP BW OHD delta2 추출 대화 상자 만들기](media/load-sap-bw-data/create-sap-bw-ohd-delta2.png)

   DTP에 대해 실행 중인 병렬 SAP 작업 프로세스의 수를 늘릴 수 있습니다.

   ![create-sap-bw-ohd-delta3](media/load-sap-bw-data/create-sap-bw-ohd-delta3.png)

2. 프로세스 체인에서 DTP를 예약 합니다.

   큐브의 델타 DTP는 필요한 행이 압축 되지 않은 경우에만 작동 합니다. 열 허브 테이블이 DTP 전에 BW 큐브 압축이 실행 되지 않는지 확인 합니다. 이 작업을 수행 하는 가장 쉬운 방법은 DTP를 기존 프로세스 체인에 통합 하는 것입니다. 다음 예에서는 DTP (OHD)이 *조정* (집계 롤업) 및 *축소* (큐브 압축) 단계 사이에 프로세스 체인에 삽입 됩니다.

   ![SAP BW 프로세스 체인 흐름 차트 만들기](media/load-sap-bw-data/create-sap-bw-process-chain.png)

### <a name="configure-full-extraction-in-sap-bw"></a>SAP BW에서 전체 추출 구성

델타 추출 외에도 동일한 SAP BW InfoProvider의 전체 추출을 원할 수 있습니다. 이는 일반적으로 전체 복사를 수행 하 되 증분을 수행 하지 않거나 [델타 추출을 다시 동기화](#resync-delta-extraction)하려는 경우에 적용 됩니다.

동일한 OHD에는 두 개 이상의 DTP를 사용할 수 없습니다. 따라서 델타 추출 전에 추가 OHD를 만들어야 합니다.

![SAP BW OHD full 만들기](media/load-sap-bw-data/create-sap-bw-ohd-full.png)

전체 로드 OHD의 경우 델타 추출에 대해 다른 옵션을 선택 합니다.

- OHD: **추출** 옵션을 설정 하 여 **데이터를 삭제 하 고 레코드를 삽입**합니다. 그렇지 않으면 BW 프로세스 체인에서 DTP를 반복 하면 데이터가 여러 번 추출 됩니다.

- DTP: **추출 모드** 를 **Full**로 설정 합니다. 이 이미지에 표시 된 것 처럼 OHD를 만든 직후 자동으로 만들어진 DTP를 **델타** 에서 **전체** 로 변경 해야 합니다.

   !["전체" 추출을 위해 구성 SAP BW OHD 대화 상자 만들기](media/load-sap-bw-data/create-sap-bw-ohd-full2.png)

- Data Factory의 BW Open Hub 커넥터에서 **마지막 요청 제외**를 해제 합니다. 그렇지 않으면 아무 것도 추출 되지 않습니다.

일반적으로 전체 DTP를 수동으로 실행 합니다. 또는 전체 DTP에 대한 프로세스 체인을 만들 수 있습니다. 일반적으로 기존 프로세스 체인과 독립적인 별도의 체인입니다. 두 경우 모두 *Data Factory 복사를 사용 하 여 추출을 시작 하기 전에 DTP이 완료 되었는지 확인*합니다. 그렇지 않으면 부분 데이터만 복사 됩니다.

### <a name="run-delta-extraction-the-first-time"></a>처음으로 델타 추출 실행

첫 번째 델타 추출은 기술적으로 *전체 추출*입니다. 기본적으로 SAP BW 열린 허브 커넥터는 데이터를 복사할 때 마지막 요청을 제외 합니다. 첫 번째 델타 추출의 경우 후속 DTP가 별도의 요청 ID를 사용 하 여 테이블에서 델타 데이터를 생성할 때까지 Data Factory 복사 작업에 의해 데이터가 추출 되지 않습니다. 이 시나리오를 방지 하는 방법에는 두 가지가 있습니다.

- 첫 번째 델타 추출에 대한 **마지막 요청 제외** 옵션을 해제 합니다. 델타 추출을 처음 시작 하기 전에 첫 번째 델타 DTP이 완료 되었는지 확인 합니다.
-  다음 섹션에 설명 된 대로 델타 추출을 다시 동기화 하는 절차를 사용 합니다.

### <a name="resync-delta-extraction"></a>델타 추출 다시 동기화

다음 시나리오에서는 SAP BW 큐브의 데이터를 변경 하지만 델타 DTP에서 고려 하지 않습니다.

- 선택적 삭제 SAP BW (필터 조건을 사용 하 여 행의 경우)
- SAP BW 요청 삭제 (잘못 된 요청)

SAP Open Hub 대상은 2015 이후의 모든 SAP BW 지원 패키지에서 데이터 마트 제어 된 데이터 대상이 아닙니다. 따라서 OHD의 데이터를 변경 하지 않고 큐브에서 데이터를 삭제할 수 있습니다. 그런 다음 Data Factory 큐브의 데이터를 다시 동기화 해야 합니다.

1. SAP에서 전체 DTP를 사용 하 여 Data Factory에서 전체 추출을 실행 합니다.
2. 델타 DTP의 열려 있는 허브 테이블에서 모든 행을 삭제 합니다.
3. 델타 DTP의 상태를 **인출**하도록 설정 합니다.

그 후 모든 후속 델타 DTPs 및 Data Factory 델타 추출은 예상 대로 작동 합니다.

델타 DTP의 상태를 **인출**하도록 설정 하려면 다음 옵션을 사용 하 여 델타 dtp를 수동으로 실행 합니다.

    *No Data Transfer; Delta Status in Source: Fetched*

## <a name="next-steps"></a>다음 단계

SAP BW 개방형 허브 커넥터 지원에 대해 알아봅니다.

> [!div class="nextstepaction"]
>[SAP Business Warehouse 오픈 허브 커넥터](connector-sap-business-warehouse-open-hub.md)
