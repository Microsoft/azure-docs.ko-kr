---
title: Syslog 데이터를 Azure Sentinel 미리 보기에 연결 | Microsoft Docs
description: Azure Sentinel에 Syslog 데이터를 연결 하는 방법을 알아봅니다.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 5dd59729-c623-4cb4-b326-bb847c8f094b
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: 9144f20eae5a925ad88054e972c99ae024bcefe3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60714662"
---
# <a name="connect-your-external-solution-using-syslog"></a>Syslog를 사용 하 여 외부 솔루션 연결

> [!IMPORTANT]
> Azure Sentinel은 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

Syslog Azure Sentinel를 지 원하는 모든 온-프레미스 어플라이언스를 연결할 수 있습니다. 이 어플라이언스 및 Azure Sentinel 간에 Linux 컴퓨터를 기반으로 하는 에이전트를 사용 하 여 이루어집니다. Azure에 Linux 컴퓨터에 있으면 기기 또는 전용된 작업 영역을 Azure에 만들고 연결 하는 응용 프로그램에서 로그를 스트리밍할 수 있습니다. Azure에 Linux 컴퓨터에 없는 경우에서 스트림할 수 있습니다 로그 어플라이언스에서 전용 온-프레미스 VM 또는 컴퓨터에 Linux 용 에이전트를 설치 합니다. 

> [!NOTE]
> 어플라이언스에서 Syslog CEF를 지 원하는, 연결이 더 완료 되 고이 옵션을 선택 하 고 지침에 따라 해야 [CEF에서 데이터를 연결](connect-common-event-format.md)합니다.

## <a name="how-it-works"></a>작동 방법

Syslog 연결 Linux 용 에이전트를 사용 하 여 수행 됩니다. 기본적으로 Linux 용 에이전트 이벤트를 받는 Syslog 디먼에서 UDP를 통해 하지만 Linux 컴퓨터를 필요한 경우 많은 양의 Syslog 이벤트를 수집 하도록 구성 하도록 수정 됩니다 Linux 에이전트는 다른 장치에서 이벤트를 수신, 하는 경우와 같은 경우 Syslog 디먼 및 에이전트 간의 TCP 전송을 사용 합니다.

## <a name="connect-your-syslog-appliance"></a>Syslog 어플라이언스에서 연결

1. Sentinel Azure portal에서 선택 **데이터 커넥터** 선택 합니다 **Syslog** 바둑판식으로 배열 합니다.
2. Linux 컴퓨터에 Azure 내에서 없으면 다운로드 하 고 Azure Sentinel 설치 **Linux 용 에이전트** 어플라이언스입니다. 
1. Azure에서 작업 하는 경우에 선택 하거나 Syslog 메시지를 수신 하는 전용 Azure Sentinel 작업 영역 내는 VM를 만듭니다. Azure Sentinel 작업 영역에서 VM을 선택 하 고 클릭 **Connect** 왼쪽 창의 맨 위에 있는 합니다.
3. 클릭 **연결할 로그 구성** Syslog 커넥터 설치 프로그램에서 다시 합니다. 
4. 클릭 **구성 블레이드를 열려면 여기를 눌러**합니다.
1. 선택 **데이터** 차례로 **Syslog**합니다.
   - 테이블에서 Syslog로 전송 하는 각 기능 인지 확인 합니다. 각 기능에 대해 하려는 모니터링, 심각도 설정 합니다. **적용**을 클릭합니다.
1. Syslog 컴퓨터에서는 이러한 기능을 전송 하는 확인 합니다. 

3. Log Analytics에서 관련 스키마를 사용 하 여 Syslog 로그를 검색할 **Syslog**합니다.




## <a name="next-steps"></a>다음 단계
이 문서에서는 Azure Sentinel Syslog 온-프레미스 어플라이언스 연결 하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- 에 대해 알아봅니다 하는 방법 [데이터에 잠재적 위협을 파악](quickstart-get-visibility.md)합니다.
- 시작 [사용 하 여 Azure Sentinel 위협을 감지 하도록](tutorial-detect-threats.md)합니다.
