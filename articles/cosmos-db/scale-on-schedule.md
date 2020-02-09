---
title: Azure Functions 타이머를 사용 하 여 일정에 따라 Azure Cosmos DB 크기 조정
description: PowerShell 및 Azure Functions를 사용 하 여 Azure Cosmos DB 처리량의 크기를 조정 하는 방법에 대해 알아봅니다.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: mjbrown
ms.openlocfilehash: 68ba40ea212c061fa5c8bbddc47ea0dfc6d8caa4
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/14/2020
ms.locfileid: "75935169"
---
# <a name="scale-azure-cosmos-db-throughput-by-using-azure-functions-timer-trigger"></a>Azure Functions 타이머 트리거를 사용 하 여 Azure Cosmos DB 처리량 크기 조정

Azure Cosmos 계정의 성능은 초당 요청 단위로 표현 된 프로 비전 된 처리량의 양을 기반으로 합니다. 프로 비전은 두 번째 세분성 이며 시간당 최고 r u/초를 기준으로 요금이 청구 됩니다. 이러한 프로비전된 용량 모델을 사용하면 서비스는 짧은 대기 시간 및 높은 가용성이 보장되는 예측 가능하고 일관된 처리량을 제공할 수 있습니다. 대부분의 프로덕션 워크 로드는 이러한 기능을 제공 합니다. 그러나 Azure Cosmos DB 작업 시간 동안만 사용 되는 개발 및 테스트 환경에서는 작업 시간 이후 오전에 처리량을 확장 하 고 저녁에 축소할 수 있습니다.

코어 (SQL) API 계정에 대한 [Azure Resource Manager 템플릿](resource-manager-samples.md), [Azure CLI](cli-samples.md)및 [PowerShell](powershell-samples-sql.md)을 통해 또는 언어별 Azure Cosmos DB sdk를 사용 하 여 처리량을 설정할 수 있습니다. 리소스 관리자 템플릿, Azure CLI 또는 PowerShell을 사용 하는 경우의 혜택은 모든 Azure Cosmos DB 모델 Api를 지원 한다는 것입니다.

## <a name="throughput-scheduler-sample-project"></a>처리량 scheduler 샘플 프로젝트

일정에 따라 Azure Cosmos DB 크기를 조정 하는 프로세스를 간소화 하기 위해 [Azure Cosmos 처리량 스케줄러](https://github.com/Azure-Samples/azure-cosmos-throughput-scheduler)라는 샘플 프로젝트를 만들었습니다. 이 프로젝트는 두 개의 타이머 트리거 ("ScaleUpTrigger" 및 "ScaleDownTrigger")가 있는 Azure Functions 앱입니다. 트리거는 각 트리거의 `resources.json` 파일에 정의 된 각 리소스에 대한 처리량을 설정 하는 PowerShell 스크립트를 실행 합니다. ScaleUpTrigger는 오전 8 시에 실행 되도록 구성 되 고 ScaleDownTrigger는 오후 6 시 UTC로 실행 되도록 구성 되며 이러한 시간은 각 트리거의 `function.json` 파일 내에서 쉽게 업데이트할 수 있습니다.

이 프로젝트를 로컬로 복제 하 고, 수정 하 여 확장 및 축소할 Azure Cosmos DB 리소스를 지정 하 고, 실행할 일정을 지정할 수 있습니다. 나중에 azure 구독에 배포 하 고 "Azure Cosmos DB operator" 역할을 사용 하 여 RBAC ( [역할 기반 Access Control](role-based-access-control.md) ) 권한을 가진 관리 서비스 id를 사용 하 여 azure Cosmos 계정에 대한 처리량을 설정 하는 보안을 유지할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- 자세히 알아보고 [Azure Cosmos DB 처리량 스케줄러](https://github.com/Azure-Samples/azure-cosmos-throughput-scheduler)에서 샘플을 다운로드 하세요.
