---
title: Azure 센티널에 Microsoft Defender ATP 데이터 연결 | Microsoft Docs
description: Microsoft Defender Advanced Threat Protection 데이터를 Azure 센티널에 연결 하는 방법을 알아봅니다.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/13/2019
ms.author: rkarlin
ms.openlocfilehash: 19d496ebb61a3ceb47f69f661e30ab529dc64f3d
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72256748"
---
# <a name="connect-alerts-from-microsoft-defender-advanced-threat-protection"></a>Microsoft Defender Advanced Threat Protection에서 경고 연결 


> [!IMPORTANT]
> Microsoft Defender Advanced Threat Protection 로그 수집은 현재 공개 미리 보기로 제공 됩니다.
> 이 기능은 서비스 수준 계약 없이 제공 되며 프로덕션 워크 로드에는 권장 되지 않습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.
 

한 번의 클릭으로 [Microsoft Defender Advanced Threat Protection](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection) 에서 Azure 센티널로 경고를 스트리밍할 수 있습니다. 이 연결을 통해 Microsoft Defender Advanced Threat Protection에서 Azure 센티널로 경고를 스트리밍할 수 있습니다. 

## <a name="prerequisites"></a>사전 요구 사항

- [라이선스 프로 비전 유효성 검사 및 Microsoft Defender Advanced Threat protection에 대 한 전체 설정](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/licensing)에 설명 된 대로 사용 하도록 설정 된 Microsoft Defender Advanced threat protection에 대 한 유효한 라이선스. 
- Azure 센티널 테넌트의 관리자 또는 보안 관리자 여야 합니다.


## <a name="connect-to-microsoft-defender-advanced-threat-protection"></a>Microsoft Defender Advanced Threat Protection에 연결

Microsoft Defender Advanced Threat Protection을 배포 하 고 데이터를 수집 경고를 쉽게 Azure 센티널로 스트리밍할 수 있습니다.


1. Azure 센티널에서 **데이터 커넥터**를 선택 하 고 **Microsoft Defender Advanced Threat Protection** 타일을 클릭 한 다음 **커넥터 열기 페이지**를 선택 합니다.
1. **연결**을 클릭합니다. 
1. Defender ATP 경고에 대 한 Log Analytics에서 관련 스키마를 사용 하려면 **Securityalert** 를 검색 하 고 **공급자 이름은** **mdatp**로 검색 합니다.




## <a name="next-steps"></a>다음 단계
이 문서에서는 Microsoft Defender ATP를 Azure 센티널에 연결 하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터 및 잠재적 위협에 대 한 가시성을 얻는](quickstart-get-visibility.md)방법에 대해 알아봅니다.
- [Azure 센티널로 위협 검색을](tutorial-detect-threats.md)시작 합니다.
