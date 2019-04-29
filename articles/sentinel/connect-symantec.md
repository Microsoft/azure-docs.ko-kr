---
title: Azure 미리 보기 Sentinel Symantec ICDX 데이터 연결할 | Microsoft Docs
description: Symantec ICDX 데이터 Azure Sentinel를 연결 하는 방법에 알아봅니다.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: d068223f-395e-46d6-bb94-7ca1afd3503c
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: 1a6843fb1668307aa442011233999c648901d404
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60714547"
---
# <a name="connect-your-symantec-icdx-appliance"></a>Symantec ICDX 어플라이언스에서 연결 

> [!IMPORTANT]
> Azure Sentinel은 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

Symantec ICDX 커넥터를 사용 하면 프로그램 Azure Sentinel, 대시보드 보기, 사용자 지정 경고를 만들고, 조사가 향상으로 모든 Symantec 보안 솔루션 로그를 쉽게 연결할 수 있습니다. 이 조직의 네트워크에 대 한 자세한 정보를 제공 하며 사용자 보안 작업 기능을 향상 시킵니다. Symantec ICDX와 Azure Sentinel 간의 통합을 사용 하면 REST API를 사용 합니다.


> [!NOTE]
> 데이터 작업 영역의 Azure Sentinel 실행 하는 지리적 위치에 저장 됩니다.

## <a name="configure-and-connect-symantec-icdx"></a>구성 하 고 Symantec ICDX 연결 

Symantec ICDX 통합 하 고 Azure Sentinel에 직접 로그를 내보낼 수 있습니다.

1. ICDX 관리 콘솔을 엽니다.
2. 왼쪽된 탐색 메뉴에서 선택 **Configuration** 차례로 합니다 **전달자** 탭 합니다.
3. Microsoft Azure Log Analytics 행에서 클릭 **자세한**차례로 **편집**합니다. 
4. 에 **Microsoft Azure Log Analytics Forwarder** 창에서 다음을 설정 합니다.
    - 사용자 지정 로그 이름을 SymantecICDX 기본값으로 그대로 둡니다.
    - 작업 영역 ID를 복사 하 고 붙여 넣습니다 합니다 **고객 식별자** 필드입니다. 복사 합니다 **기본 키** 공유 키 필드에 붙여 넣습니다. 선택 하 여 Azure Sentinel 포털에서 이러한 값을 복사할 수 있습니다 **데이터 커넥터** 차례로 **Symantec ICDX**합니다.
6. Log Analytics에서 관련 스키마를 사용 하 여 Symantec ICDX 이벤트를 검색할 **SymantecICDX_CL**합니다.


## <a name="validate-connectivity"></a>연결 유효성 검사

수준도 로그를 Log Analytics에 나타나기 시작 될 때까지 20 분 정도 걸릴 수 있습니다. 



## <a name="next-steps"></a>다음 단계
이 문서에서는 Azure Sentinel Symantec ICDX 연결 하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- 에 대해 알아봅니다 하는 방법 [데이터에 잠재적 위협을 파악](quickstart-get-visibility.md)합니다.
- 시작 [사용 하 여 Azure Sentinel 위협을 감지 하도록](tutorial-detect-threats.md)합니다.

