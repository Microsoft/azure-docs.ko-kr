---
title: Azure Storage에서 진단 데이터 저장 및 보기
description: 사용 가능한 여러 도구 중 하나를 사용하여 Azure Storage 계정에서 Azure Diagnostics 데이터를 볼 수 있도록 수집하는 방법을 알아봅니다.
services: azure-monitor
author: bwren
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 08/01/2016
ms.author: bwren
ms.subservice: diagnostic-extension
ms.openlocfilehash: 9e16d77c36f382e72d5e9902b07afbbec50b6876
ms.sourcegitcommit: 5b6acff3d1d0603904929cc529ecbcfcde90d88b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2020
ms.locfileid: "88723258"
---
# <a name="store-and-view-diagnostic-data-in-azure-storage"></a>Azure Storage에서 진단 데이터 저장 및 보기
진단 데이터를 Microsoft Azure Storage 에뮬레이터으로 전송 하거나 Azure Storage 하지 않는 한 진단 데이터는 영구적으로 저장 되지 않습니다. 스토리지에서 사용할 수 있는 여러 도구 중 하나로 한 번 볼 수 있습니다.

## <a name="specify-a-storage-account"></a>스토리지 계정 지정
ServiceConfiguration.cscfg 파일에서 사용하려는 스토리지 계정을 지정합니다. 계정 정보는 구성 설정에서 연결 문자열로 정의됩니다. 다음 예제에서는 Visual Studio에서 새 클라우드 서비스 프로젝트에 대해 생성된 기본 연결 문자열을 보여 줍니다.

```
    <ConfigurationSettings>
       <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
    </ConfigurationSettings>
```

이 연결 문자열을 변경하여 Azure Storage 계정에 대한 계정 정보를 제공할 수 있습니다.

수집되는 진단 데이터의 형식에 따라 Azure Diagnostics는 Blob 서비스 또는 Table 서비스를 사용합니다. 다음 표에서 유지되는 데이터 원본 및 해당 형식을 보여 줍니다.

| 데이터 원본 | 스토리지 형식 |
| --- | --- |
| Azure 로그 |테이블 |
| IIS 7.0 로그 |Blob |
| Azure Diagnostics 인프라 로그 |테이블 |
| 실패한 요청 추적 로그 |Blob |
| Windows 이벤트 로그 |테이블 |
| 성능 카운터 |테이블 |
| 크래시 덤프 |Blob |
| 사용자 지정 오류 로그 |Blob |

## <a name="transfer-diagnostic-data"></a>진단 데이터 전송
SDK 2.5 이상의 경우 구성 파일을 통해 진단 데이터 전송 요청이 발생할 수 있습니다. 구성에서 지정된 예약된 간격으로 진단 데이터를 전송할 수 있습니다.

SDK 2.4 및 이전 버전의 경우 프로그래밍 방식으로 구성 파일을 통해 진단 데이터 전송을 요청할 수 있습니다. 프로그래밍 방식을 사용하면 주문형 전송을 수행할 수 있습니다.

> [!IMPORTANT]
> Azure Storage 계정에 진단 데이터를 전송하는 경우 진단 데이터를 사용하는 스토리지 리소스에 대한 비용이 발생합니다.
> 
> 

## <a name="store-diagnostic-data"></a>진단 데이터 저장
로그 데이터는 다음과 같은 이름의 Blob 또는 Table Storage에 저장됩니다.

**테이블**

* **WadLogsTable** - 추적 수신기를 사용하여 코드에서 작성된 로그입니다.
* **WADDiagnosticInfrastructureLogsTable** - 진단 모니터 및 구성 변경 내용입니다.
* **WADDirectoriesTable** – 진단 모니터를 모니터링하는 디렉터리입니다.  IIS 로그, IIS 실패한 요청 로그 및 사용자 지정 디렉터리를 포함합니다.  Blob 로그 파일의 위치는 Container 필드에 지정되고 Blob의 이름은 RelativePath 필드에 있습니다.  AbsolutePath 필드는 Azure 가상 머신에 존재했던 파일의 이름과 위치를 나타냅니다.
* **WADPerformanceCountersTable** – 성능 카운터입니다.
* **WADWindowsEventLogsTable** – Windows 이벤트 로그입니다.

**Blob**

* **wad-control-container** – (SDK 2.4 및 이전 버전의 경우만) Azure 진단을 제어하는 XML 구성 파일을 포함합니다.
* **wad-iis-failedreqlogfiles** – IIS 실패한 요청 로그의 정보가 포함됩니다.
* **wad-iis-logfiles** – IIS 로그의 정보가 포함됩니다.
* **"custom"** – 진단 모니터에 의해 모니터링되는 구성 디렉터리에 기반한 사용자 지정 컨테이너입니다.  이 Blob 컨테이너의 이름은 WADDirectoriesTable에 지정됩니다.

## <a name="tools-to-view-diagnostic-data"></a>진단 데이터를 볼 도구
여러 도구를 사용하여 스토리지로 전송된 후 데이터를 볼 수 있습니다. 다음은 그 예입니다.

* Visual Studio의 서버 탐색기 - Microsoft Visual Studio용 Azure 도구를 설치한 경우 서버 탐색기에서 Azure Storage 노드를 사용하여 Azure Storage 계정에서 읽기 전용 Blob 및 테이블 데이터를 볼 수 있습니다. 로컬 스토리지 에뮬레이터 계정 및 Azure용으로 만든 스토리지 계정에서 데이터를 표시할 수 있습니다. 자세한 내용은 [서버 탐색기로 Storage 리소스 탐색 및 관리](/visualstudio/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage)를 참조하세요.
* [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md)는 Windows, OSX 및 Linux에서 Azure Storage 데이터로 손쉽게 작업할 수 있도록 해주는 독립 실행형 앱입니다.
* [Azure Management Studio](https://www.cerebrata.com/products/azure-management-studio/introduction) 에는 Azure에서 실행되는 애플리케이션으로 수집된 진단 데이터를 보고 다운로드하고 관리할 수 있는 Azure Diagnostics 관리자가 있습니다.

## <a name="next-steps"></a>다음 단계
[Azure Diagnostics로 Cloud Services 애플리케이션의 흐름 추적](../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md)


