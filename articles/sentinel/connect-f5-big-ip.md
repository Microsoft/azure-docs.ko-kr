---
title: F5 빅 IP 데이터를 Azure 센티널에 연결 | Microsoft Docs
description: Azure 센티널에 F5 빅 IP 데이터를 연결 하는 방법에 대해 알아봅니다.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: d068223f-395e-46d6-bb94-7ca1afd3503c
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: rkarlin
ms.openlocfilehash: 273bea7fa8629dd907c228416d06dac087b0ec85
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/02/2020
ms.locfileid: "75610593"
---
# <a name="connect-your-f5-big-ip-appliance"></a>F5 빅 IP 어플라이언스 연결 

> [!IMPORTANT]
> Azure 센티널의 F5 빅 IP 데이터 커넥터는 현재 공개 미리 보기로 제공 됩니다.
> 이 기능은 서비스 수준 계약 없이 제공 되며 프로덕션 워크 로드에는 권장 되지 않습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

F5 빅 IP 커넥터를 사용 하면 모든 F5 빅 IP 로그를 Azure 센티널과 쉽게 연결 하 고, 통합 문서를 보고, 사용자 지정 경고를 만들고, 조사를 개선할 수 있습니다. 이렇게 하면 조직의 네트워크에 대 한 자세한 정보를 제공 하 고 보안 작업 기능을 향상 시킬 수 있습니다. F5 빅 IP와 Azure 센티널 간의 통합은 REST API를 사용 합니다.


> [!NOTE]
> 데이터는 Azure 센티널을 실행 하는 작업 영역의 지리적 위치에 저장 됩니다.

## <a name="configure-and-connect-f5-big-ip"></a>F5 빅 IP 구성 및 연결 

F5 빅 IP는 로그를 Azure 센티널로 직접 통합 하 고 내보낼 수 있습니다.

1. Azure 센티널 포털에서 **데이터 커넥터** 를 클릭 하 고 **F5 빅 IP** 를 선택한 다음 **커넥터 페이지를 엽니다**. 
1. F5 빅 IP를 연결 하려면 시스템의 API 엔드포인트에 JSON 선언을 게시 해야 합니다. 이 작업을 수행 하는 방법에 대 한 지침은 [F5 빅 IP를 Azure 센티널과 통합](https://devcentral.f5.com/s/articles/Integrating-the-F5-BIGIP-with-Azure-Sentinel)을 참조 하세요.
8. F5 빅 IP 커넥터 페이지에서 작업 영역 ID 및 기본 키를 복사 하 고 [Azure Log Analytics로 데이터 스트리밍에](https://devcentral.f5.com/s/articles/Integrating-the-F5-BIGIP-with-Azure-Sentinel#streaming-data-to-azure-log-analytics)설명 된 대로 붙여넣습니다.
1. F5 큰 IP 지침을 완료 한 후 Azure 센티널 커넥터 페이지에 연결 된 데이터 형식이 표시 됩니다.
1. Log Analytics에서 관련 스키마를 사용 하 여 F5 큰 IP 이벤트를 사용 하려면 **F5Telemetry_LTM_CL**, **F5Telemetry_system_CL**및 **F5Telemetry_ASM_CL**를 검색 합니다.


## <a name="validate-connectivity"></a>연결 유효성 검사

로그가 Log Analytics 표시 되기 시작할 때까지 20 분 정도 걸릴 수 있습니다. 



## <a name="next-steps"></a>다음 단계
이 문서에서는 F5 빅 IP를 Azure 센티널에 연결 하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats-built-in.md)을 시작합니다.
- [통합 문서를 사용](tutorial-monitor-your-data.md) 하 여 데이터를 모니터링 합니다.


