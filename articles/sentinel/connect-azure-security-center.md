---
title: Azure Security Center 데이터 Azure Sentinel 미리 보기에 연결 | Microsoft Docs
description: Azure Security Center 데이터 Azure Sentinel를 연결 하는 방법에 알아봅니다.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: d28c2264-2dce-42e1-b096-b5a234ff858a
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: bd6fe027da8c16157c7fce6c348e49c0a6b73ea3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60596611"
---
# <a name="connect-data-from-azure-security-center"></a>Azure Security Center에서 데이터 연결

> [!IMPORTANT]
> Azure Sentinel은 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.



Azure Sentinel에서 경고를 연결할 수 있게 해 [Azure Security Center](../security-center/security-center-intro.md) Azure Sentinel에 스트리밍하 고 합니다. 

## <a name="prerequisites"></a>필수 조건

- Azure Security Center에서 경고를 내보내려면 스트리밍할 수 있는 로그 구독 참가자 여야 합니다.

- 있어야 합니다 [Azure Security Center 표준 계층과](../security-center/security-center-pricing.md) 구독에서 실행 합니다. 그러지 않으면 [표준 구독을 업그레이드](https://azure.microsoft.com/pricing/details/security-center/)합니다.

- 연결 하려는 각 구독에 전역 관리자 또는 보안 관리자 권한이 있는 사용자를 로그인 해야 합니다.


## <a name="connect-to-azure-security-center"></a>Azure Security Center에 연결

1. Azure Sentinel 선택 **데이터 커넥터** 클릭 하 고는 **Azure Security Center** 바둑판식으로 배열 합니다.
1. 오른쪽 클릭 **Connect** 옆에 있는 각 구독에 Azure Sentinel 스트리밍 하려는 경고가 포함 합니다. 각 구독은 Azure Sentinel에 경고를 스트림 하려면 Azure Security Center 표준 계층으로 업그레이드 해야 합니다.

3. **Connect**를 클릭합니다.

4. Azure Security Center 경고에 대 한 Log Analytics에서 관련 스키마를 사용, 검색 **SecurityEvent**합니다.

## <a name="next-steps"></a>다음 단계
이 문서에서는 Azure Sentinel에 Azure Security Center를 연결 하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- 에 대해 알아봅니다 하는 방법 [데이터에 잠재적 위협을 파악](quickstart-get-visibility.md)합니다.
- 시작 [사용 하 여 Azure Sentinel 위협을 감지 하도록](tutorial-detect-threats.md)합니다.
