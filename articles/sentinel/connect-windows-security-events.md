---
title: Windows 보안 이벤트 데이터를 Azure 미리 보기 Sentinel 연결할 | Microsoft Docs
description: Azure Sentinel에 Windows 보안 이벤트 데이터를 연결 하는 방법을 알아봅니다.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: d51d2e09-a073-41c8-b396-91d60b057e6a
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: d131d39a9d9770046fa3f9368250204e4d0b0162
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60596696"
---
# <a name="connect-windows-security-events"></a>Windows 보안 이벤트 연결 

> [!IMPORTANT]
> Azure Sentinel은 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

Azure Sentinel 작업 영역에 연결 된 Windows 서버에서 모든 보안 이벤트를 스트리밍할 수 있습니다. 이 연결을 사용 하면 대시보드를 볼, 사용자 지정 경고 및 조사를 향상 시킬 수 있습니다. 이 조직의 네트워크에 대 한 자세한 정보를 제공 하며 사용자 보안 작업 기능을 향상 시킵니다.  이벤트 스트림를 선택할 수 있습니다.

- **모든 이벤트** -모든 Windows 보안 및 AppLocker 이벤트입니다.
- **일반적인** -감사 목적에 대 한 이벤트의 표준 집합입니다.
- **최소** -소수의 잠재적인 위협 요소를 나타낼 수 있는 이벤트입니다. 이 옵션을 설정 하면 전체 감사 내역을 얻을 수 없습니다.
- **None** -보안 또는 AppLocker 이벤트가 없습니다.

> [!NOTE]
> 
> - 데이터 작업 영역의 Azure Sentinel 실행 하는 지리적 위치에 저장 됩니다.

## <a name="set-up-the-windows-security-events-connector"></a>Windows 보안 이벤트 connector 설정

완전히 통합할 Windows 보안 이벤트 Azure Sentinel:

1. Sentinel Azure portal에서 선택 **데이터 커넥터** 를 클릭 하 고는 **Windows 보안 이벤트** 바둑판식으로 배열 합니다. 
1. 스트리밍 하려는 데이터 유형을 선택 합니다.
1. **업데이트**를 클릭합니다.
6. Log Analytics에서 관련 스키마를 사용 하 여 Windows 보안 이벤트를 검색할 **SecurityEvent**합니다.

## <a name="validate-connectivity"></a>연결 유효성 검사

로그를 Log Analytics에 나타나기 시작 될 때까지 약 20 분 정도 걸릴 수 있습니다. 



## <a name="next-steps"></a>다음 단계
이 문서에서는 Azure Sentinel에 Windows 보안 이벤트를 연결 하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- 에 대해 알아봅니다 하는 방법 [데이터에 잠재적 위협을 파악](quickstart-get-visibility.md)합니다.
- 시작 [사용 하 여 Azure Sentinel 위협을 감지 하도록](tutorial-detect-threats.md)합니다.

