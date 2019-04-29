---
title: CLI를 사용하여 원격 모니터링 솔루션 배포 - Azure | Microsoft Docs
description: 이 방법 가이드에서는 CLI를 사용하여 원격 모니터링 솔루션 가속기를 프로비전하는 방법을 보여줍니다.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 03/08/2019
ms.topic: conceptual
ms.openlocfilehash: ea96b2b996ea79efacdcda50c6370f25e26e0aa2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61447015"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-using-the-cli"></a>CLI를 사용하여 원격 모니터링 솔루션 가속기 배포

이 방법 가이드에서는 원격 모니터링 솔루션 가속기를 배포하는 방법을 보여줍니다. CLI를 사용하여 솔루션을 배포합니다. 이 옵션 참조에 대해 자세히 알아보려면 azureiotsolutions.com에서 웹 기반 UI를 사용 하 여 솔루션을 배포할 수도 있습니다는 [원격 모니터링 솔루션 가속기 배포](quickstart-remote-monitoring-deploy.md) 빠른 시작 합니다.

## <a name="prerequisites"></a>필수 조건

원격 모니터링 솔루션 가속기를 배포하려면 활성 Azure 구독이 필요합니다.

계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 [Azure 무료 체험](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.

CLI를 실행하려면 [Node.js](https://nodejs.org/)를 로컬 컴퓨터에 설치해야 합니다.

## <a name="install-the-cli"></a>CLI 설치

CLI를 설치하려면 명령줄 환경에서 다음 명령을 실행합니다.

```cmd/sh
npm install iot-solutions -g
```

## <a name="sign-in-to-the-cli"></a>CLI에 로그인

솔루션 가속기를 배포하기 전에 CLI를 사용하여 Azure 구독에 로그인해야 합니다.

```cmd/sh
pcs login
```

화면의 지시에 따라 로그인 프로세스를 완료합니다.

## <a name="deployment-options"></a>배포 옵션

솔루션 가속기를 배포하는 경우 배포 프로세스를 구성하는 여러 옵션이 있습니다.

| 옵션 | 값 | 설명 |
| ------ | ------ | ----------- |
| SKU    | `basic`, `standard`, `local` | _기본_ 배포는 테스트 및 데모를 위한 것이며 단일 가상 머신에 모든 마이크로 서비스를 배포합니다. _표준_ 배포는 프로덕션을 위한 것이며 여러 가상 머신에 마이크로 서비스를 배포합니다. _로컬_ 배포는 로컬 머신에서 마이크로 서비스를 실행하고, 스토리지 및 Cosmos DB 같은 Azure 클라우드 서비스를 사용하도록 Docker 컨테이너를 구성합니다. |
| 런타임 | `dotnet`, `java` | 마이크로 서비스의 언어 구현을 선택합니다. |

로컬 배포 옵션을 사용하는 방법에 대한 자세한 내용은 [로컬에서 원격 모니터링 솔루션 실행](iot-accelerators-remote-monitoring-deploy-local.md)을 참조하세요.

## <a name="basic-and-standard-deployments"></a>기본 및 표준 배포

이 섹션에서는 기본 배포와 표준 배포 간의 주요 차이점을 요약합니다.

### <a name="basic"></a>Basic

기본 배포에서 수행할 수 있습니다 [azureiotsolutions.com](https://www.azureiotsolutions.com/Accelerators) 또는 CLI를 사용 합니다.

기본 배포는 솔루션을 보여주기 위한 것입니다. 비용을 줄이기 위해 모든 마이크로 서비스가 단일 가상 머신에 배포됩니다. 이 배포는 프로덕션 레디 아키텍처를 사용하지 않습니다.

기본 배포는 Azure 구독에 다음 서비스를 만듭니다.

| 카운트 | 리소스                       | Type         | 용도 |
|-------|--------------------------------|--------------|----------|
| 1     | [Linux 가상 머신](https://azure.microsoft.com/services/virtual-machines/) | 표준 D1 V2  | 마이크로 서비스 호스트 |
| 1     | [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/)                  | S1 - 표준 계층 | 디바이스 관리 및 통신 |
| 1     | [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)              | Standard        | 구성 데이터, 규칙, 경고 및 기타 콜드 스토리지 저장 |  
| 1     | [Azure Storage 계정](https://docs.microsoft.com/azure/storage/common/storage-introduction#types-of-storage-accounts)  | Standard        | VM 및 스트리밍 검사점에 대한 저장소 |
| 1     | [웹 애플리케이션](https://azure.microsoft.com/services/app-service/web/)        |                 | 프런트 엔드 웹 애플리케이션 호스트 |
| 1     | [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)        |                 | 사용자 ID 및 보안 관리 |
| 1     | [Azure Maps](https://azure.microsoft.com/services/azure-maps/)        | Standard                | 자산 위치 보기 |
| 1     | [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/)        |   3개 단위              | 실시간 분석 사용 |
| 1     | [Azure Device Provisioning Service](https://docs.microsoft.com/azure/iot-dps/)        |       S1          | 대규모 디바이스 프로비저닝 |
| 1     | [Azure Time Series Insights](https://azure.microsoft.com/services/time-series-insights/)        |   S1 – 1개 단위              | 메시지 데이터용 저장 영역이며 심도 있는 원격 분석 사용 |

### <a name="standard"></a>Standard

CLI를 통해서만 표준 배포를 수행할 수 있습니다.

표준 배포는 개발자가 사용자 지정하고 확장할 수 있는 프로덕션 레디 배포입니다. 크기 조정 및 확장성을 위해 빌드된 프로덕션 레디 아키텍처를 사용자 지정할 준비가 완료되었으면 표준 배포 옵션을 사용합니다. 애플리케이션 마이크로 서비스는 Azure Kubernetes Service를 사용하여 Docker 컨테이너로 빌드되고 배포됩니다. Kubernetes 오케스트레이터는 마이크로 서비스를 배포, 확장 및 관리합니다.

표준 배포는 Azure 구독에 다음 서비스를 만듭니다.

| 카운트 | 리소스                                     | SKU/크기      | 용도 |
|-------|----------------------------------------------|-----------------|----------|
| 1     | [Azure Kubernetes Service](https://azure.microsoft.com/services/kubernetes-service)| 완전 관리형 Kubernetes 컨테이너 오케스트레이션 서비스를 사용하세요. 기본값은 에이전트 3개입니다.|
| 1     | [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/)                     | S2 - 표준 계층 | 디바이스 관리, 명령 및 제어 |
| 1     | [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)                 | Standard        | 구성 데이터 및 디바이스 원격 분석(예: 규칙, 경고 및 메시지) 저장 |
| 5     | [Azure Storage 계정](https://docs.microsoft.com/azure/storage/common/storage-introduction#types-of-storage-accounts)    | Standard        | VM 저장소용 4개, 스트리밍 검사점용 1개 |
| 1     | [App Service](https://azure.microsoft.com/services/app-service/web/)             | S1 표준     | SSL을 통한 Application Gateway |
| 1     | [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)        |                 | 사용자 ID 및 보안 관리 |
| 1     | [Azure Maps](https://azure.microsoft.com/services/azure-maps/)        | Standard                | 자산 위치 보기 |
| 1     | [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/)        |   3개 단위              | 실시간 분석 사용 |
| 1     | [Azure Device Provisioning Service](https://docs.microsoft.com/azure/iot-dps/)        |       S1          | 대규모 디바이스 프로비저닝 |
| 1     | [Azure Time Series Insights](https://azure.microsoft.com/services/time-series-insights/)        |   S1 – 1개 단위              | 메시지 데이터용 저장 영역이며 심도 있는 원격 분석 사용 |

> [!NOTE]
> 이러한 서비스의 가격 책정 정보는 [https://azure.microsoft.com/pricing](https://azure.microsoft.com/pricing)에서 확인할 수 있습니다. 구독의 사용량 및 요금 청구 정보는 [Azure Portal](https://portal.azure.com/)에서 찾을 수 있습니다.

## <a name="deploy-the-solution-accelerator"></a>솔루션 가속기 배포

배포 예제:

### <a name="example-deploy-net-version"></a>예제: .NET 버전 배포

다음 예제는 원격 모니터링 솔루션 가속기의 기본, .NET 버전을 배포하는 방법을 보여줍니다.

```cmd/sh
pcs -t remotemonitoring -s basic -r dotnet
```

### <a name="example-deploy-java-version"></a>예제: Java 버전 배포

다음 예제는 원격 모니터링 솔루션 가속기의 표준, Java 버전을 배포하는 방법을 보여줍니다.

```cmd/sh
pcs -t remotemonitoring -s standard -r java
```

### <a name="pcs-command-options"></a>pcs 명령 옵션

`pcs` 명령을 실행하여 솔루션을 배포할 때 다음 질문에 답해야 합니다.

- 솔루션에 대한 이름 이 이름은 고유해야 합니다.
- 사용할 Azure 구독입니다.
- 위치
- 마이크로 서비스를 호스팅하는 가상 머신에 대한 자격 증명 이러한 자격 증명을 사용하여 문제 해결을 위해 가상 머신에 액세스할 수 있습니다.

`pcs` 명령이 완료되면 새 솔루션 가속기의 URL이 표시됩니다. `pcs` 명령 역시 생성된 IoT Hub의 이름 같은 정보를 포함하는 `{deployment-name}-output.json` 파일을 만듭니다.

명령줄 매개 변수에 대한 자세한 내용은 다음을 실행합니다.

```cmd/sh
pcs -h
```

CLI에 대한 자세한 내용은 [CLI를 사용하는 방법](https://github.com/Azure/pcs-cli/blob/master/README.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

이 방법 가이드에서는 다음 작업 방법을 배웁니다.

> [!div class="checklist"]
> * 솔루션 가속기 구성
> * 솔루션 가속기 배포
> * 솔루션 가속기에 로그인

원격 모니터링 솔루션을 배포했으니, 다음 단계는 [솔루션 대시보드의 기능을 탐색](./quickstart-remote-monitoring-deploy.md)하는 것입니다.

<!-- Next how-to guides in the sequence -->
