---
title: Azure Functions 런타임 개요 | Microsoft Docs
description: Azure Functions 런타임 미리 보기 개요
services: functions
author: apwestgarth
manager: stefsch
ms.assetid: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 11/28/2017
ms.author: anwestg
ms.openlocfilehash: 2af9575c50ee522d6330ddf46c75b666132b7a84
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61021233"
---
# <a name="azure-functions-runtime-overview-preview"></a>Azure Functions 런타임 개요(미리 보기)

[!INCLUDE [intro](../../includes/functions-runtime-preview-note.md)]

Azure Functions 런타임(미리 보기)은 Azure Functions 프로그래밍 모델 온-프레미스의 간편성 및 유연성을 활용하는 새로운 방법을 제공합니다. Azure Functions와 같은 오픈 소스 루트 기술을 기반으로 하는 Azure Functions 런타임은 온-프레미스에 배포되어 클라우드 서비스와 거의 동일한 개발 환경을 제공합니다.

![Azure Functions 런타임 미리 보기 포털][1]

Azure Functions 런타임은 클라우드로 커밋하기 전에 Azure Functions를 사용해볼 수 있는 방법을 제공합니다. 이러한 방식으로 빌드하는 코드 자산을 마이그레이션 시 클라우드로 가져올 수 있습니다.  또한 이 런타임은 온-프레미스 컴퓨터의 예비 계산 능력을 사용하여 야간에 배치 프로세스를 실행하는 것과 같은 새로운 옵션을 가능하게 합니다. 조직 내에서 디바이스를 사용하여 온-프레미스 및 클라우드의 다른 시스템으로 조건에 따라 데이터를 전송할 수도 있습니다.

Azure Functions 런타임은 다음 두 부분으로 구성됩니다.

* Azure Functions 런타임 관리 역할
* Azure Functions 런타임 작업자 역할

## <a name="azure-functions-management-role"></a>Azure Functions 관리 역할

Azure Functions 관리 역할은 온-프레미스에서 함수의 관리를 위한 호스트를 제공합니다. 이 역할은 다음 작업을 수행합니다.

* Azure Functions 관리 포털 호스팅([Azure Portal](https://portal.azure.com)에 표시되는 것과 동일함) - 포털에서 Azure Portal에서와 동일한 방식으로 함수를 개발할 수 있는 일관된 환경을 제공합니다.
* 여러 함수 작업자 간에 함수 배포
* 게시 프로필을 다운로드하고 가져와서 Microsoft Visual Studio에서 직접 함수를 게시할 수 있도록 게시 엔드포인트 제공

## <a name="azure-functions-worker-role"></a>Azure Functions 작업자 역할

Azure Functions 작업자 역할은 Windows 컨테이너에 배포되며, 여기서 함수 코드가 실행됩니다.  조직 전체에 여러 작업자 역할을 배포할 수 있으며, 이 옵션은 고객이 예비 계산 능력을 활용할 수 있는 핵심적인 방법입니다.  많은 조직에 예비 계산이 존재하는 한 가지 예로, 전원이 계속 켜져 있지만 오랜 시간 동안 사용되지 않는 머신이 있습니다.

## <a name="minimum-requirements"></a>최소 요구 사항

Azure Functions 런타임을 시작하려면 SQL Server 인스턴스에 대한 액세스 권한이 있는 Windows Server 2016 또는 Windows 10 크리에이터 업데이트가 설치된 컴퓨터가 있어야 합니다.

## <a name="next-steps"></a>다음 단계

[Azure Functions 런타임 미리 보기](https://aka.ms/azafrdoc) 설치

<!--Image references-->
[1]: ./media/functions-runtime-overview/AzureFunctionsRuntime_Portal.png
