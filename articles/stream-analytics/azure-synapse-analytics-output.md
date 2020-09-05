---
title: Azure Stream Analytics의 Azure Synapse Analytics 출력
description: 이 문서에서는 Azure Stream Analytics에 대 한 출력으로 Azure Synapse Analytics에 대해 설명 합니다.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: 5d281bf33542354e2904805deed2f1deaf2725a2
ms.sourcegitcommit: 927dd0e3d44d48b413b446384214f4661f33db04
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88875915"
---
# <a name="azure-synapse-analytics-output-from-azure-stream-analytics-preview"></a>Azure Stream Analytics의 Azure Synapse Analytics 출력 (미리 보기)

[Azure Synapse Analytics](https://azure.microsoft.com/services/synapse-analytics)(이전의 SQL Data Warehouse)는 엔터프라이즈 데이터 웨어하우징과 빅 데이터 분석을 결합한 무제한 분석 서비스입니다. 

Azure Stream Analytics 작업은 Azure Synapse Analytics의 SQL 풀 테이블로 출력할 수 있으며 최대 200MB/초의 처리량 속도를 처리할 수 있습니다. 이 경우 보고 및 대시보드와 같은 워크로드에 대한 가장 까다로운 실시간 분석 및 실행 부하 과다 경로 데이터 처리 요구 사항을 지원할 수 있습니다.  

먼저 SQL 풀 테이블이 있어야 이를 Stream Analytics 작업의 출력으로 추가할 수 있습니다. 테이블 스키마는 작업 출력의 필드 및 해당 형식과 일치해야 합니다. 

Azure Synapse를 출력으로 사용하려면 스토리지 계정이 구성되어 있는지 확인해야 합니다. 스토리지 계정 설정으로 이동하여 스토리지 계정을 구성합니다. 테이블을 지원하는 스토리지 계정 유형(범용 V2 및 범용 V1)만 허용됩니다. 표준 계층만 선택합니다. 프리미엄 계층은 지원되지 않습니다.

## <a name="output-configuration"></a>출력 구성

다음 표에는 Azure Synapse Analytics 출력을 만들기 위한 속성 이름 및 해당 설명이 나와 있습니다.

|속성 이름|Description|
|-|-|
|출력 별칭 |쿼리 출력을 이 데이터베이스로 보내기 위해 쿼리에서 사용되는 식별 이름입니다. |
|데이터베이스 |출력을 보내는 SQL 풀 이름입니다. |
|서버 이름 |Azure Synapse 서버 이름입니다.  |
|사용자 이름 |데이터베이스에 대한 쓰기 액세스 권한이 있는 사용자 이름입니다. Stream Analytics는 SQL 인증만 지원합니다. |
|암호 |데이터베이스에 연결하는 암호입니다. |
|테이블  | 출력을 기록되는 테이블 이름입니다. 테이블 이름은 대/소문자를 구분합니다. 이 테이블의 스키마는 작업 출력에서 생성하는 필드 수 및 해당 형식과 정확히 일치해야 합니다.|

## <a name="next-steps"></a>다음 단계

* [빠른 시작: Azure Portal을 사용하여 Stream Analytics 작업 만들기](stream-analytics-quick-create-portal.md)
* [빠른 시작: Azure CLI를 사용하여 Azure Stream Analytics 작업 만들기](quick-create-azure-cli.md)
* [빠른 시작: ARM 템플릿을 사용하여 Azure Stream Analytics 작업 만들기](quick-create-azure-resource-manager.md)
* [빠른 시작: Azure PowerShell를 사용 하 여 Stream Analytics 작업 만들기](stream-analytics-quick-create-powershell.md)
* [빠른 시작: Visual Studio를 사용하여 Azure Stream Analytics 작업 만들기](stream-analytics-quick-create-vs.md)
* [빠른 시작: Visual Studio Code에서 Azure Stream Analytics 작업 만들기](quick-create-vs-code.md)