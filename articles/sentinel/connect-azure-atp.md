---
title: Azure ATP 데이터 Azure Sentinel 미리 보기에 연결 | Microsoft Docs
description: Azure ATP 데이터 Azure Sentinel를 연결 하는 방법에 알아봅니다.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 5bf3cc44-ecda-4c78-8a63-31ab42f43605
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: 153b6cb44cbcf5ecca9a4941c6bef7a8721dc56c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60597190"
---
# <a name="connect-data-from-azure-advanced-threat-protection-atp"></a>데이터에서 Azure ATP Advanced Threat Protection ()에 연결

> [!IMPORTANT]
> Azure Sentinel은 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.


로그를 스트리밍하려면 [Azure Advanced Threat Protection](https://docs.microsoft.com/azure-advanced-threat-protection/what-is-atp) 번의 클릭으로 Azure Sentinel에 있습니다.

## <a name="prerequisites"></a>필수 조건

- 전역 관리자 또는 보안 관리자 권한이 있는 사용자
- Azure atp 비공개 미리 보기 고객 이어야 합니다.

## <a name="connect-to-azure-atp"></a>Azure ATP에 연결

Azure ATP 비공개 미리 보기 버전 인지 확인 [네트워크에서 사용 하도록 설정](https://docs.microsoft.com/azure-advanced-threat-protection/install-atp-step1)합니다.
Azure ATP 배포 되 고 데이터를 수집, 의심 스러운 경고를 쉽게 수행할 수 있으면 Azure Sentinel로 스트림할 수 있습니다. Azure Sentinel에 스트리밍을 시작 하려면 경고에 대 한 최대 24 시간이 걸릴 수 있습니다.



1. Azure Sentinel 선택 **데이터 커넥터** 클릭 하 고는 **Azure ATP** 바둑판식으로 배열 합니다.

2. **Connect**를 클릭합니다.

6. Log Analytics에서 관련 스키마를 사용 하 여 Azure ATP 경고를 검색할 **SecurityAlert**합니다.

## <a name="next-steps"></a>다음 단계
이 문서에서는 Azure Sentinel Azure Advanced Threat Protection 연결 하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- 에 대해 알아봅니다 하는 방법 [데이터에 잠재적 위협을 파악](quickstart-get-visibility.md)합니다.
- 시작 [사용 하 여 Azure Sentinel 위협을 감지 하도록](tutorial-detect-threats.md)합니다.

