---
title: Office 365 데이터를 Azure Sentinel 미리 보기에 연결 | Microsoft Docs
description: Azure Sentinel에 Office 365 데이터를 연결 하는 방법을 알아봅니다.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: ff7c862e-2e23-4a28-bd18-f2924a30899d
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: 77587b0b7506ef0ccadbeb6d1f010f5b6a72d93e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60714560"
---
# <a name="connect-data-from-office-365-logs"></a>Office 365 로그에서 데이터 연결

> [!IMPORTANT]
> Azure Sentinel은 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

감사 로그를 스트리밍하려면 [Office 365](https://docs.microsoft.com/office365/admin/admin-home?view=o365-worldwide) 번의 클릭으로 Azure Sentinel에 있습니다. Azure Sentinel 단일 작업 영역에 여러 테 넌 트에서 감사 로그를 스트리밍할 수 있습니다. Office 365 활동 로그 커넥터를 지속적인 사용자 활동에 대 한 정보를 제공합니다. Office 365에서 다양 한 사용자, 관리자, 시스템 및 정책 작업 및 이벤트에 대 한 정보를 받습니다. Office 365 로그 Sentinel Azure에 연결 하 여 대시보드 보기, 사용자 지정 경고 및 조사 프로세스를 개선 하려면이 데이터를 사용할 수 있습니다.


## <a name="prerequisites"></a>필수 조건

- 테 넌 트에서 전역 관리자 또는 보안 관리자 여야 합니다.
- 컴퓨터에 있는 연결을 만들려면 Azure Sentinel 로그인 포트 4433 웹 트래픽을 열려 있는지를 확인 합니다.

## <a name="connect-to-office-365"></a>Office 365에 연결

1. Azure Sentinel 선택 **데이터 커넥터** 클릭 하 고는 **Office 365** 바둑판식으로 배열 합니다.

2. 하지 이미 설정한 경우, 아래 **연결** 사용 합니다 **사용 하도록 설정** Office 365 솔루션을 사용 하도록 설정 하려면 단추입니다. 이미 설정 된 경우 연결 화면을 이미 사용 하도록 설정에서 식별 됩니다.
1. Office 365을 사용 하면 여러 테 넌 트가 Azure Sentinel에서 스트림 데이터를 할 수 있습니다. 각 테 넌 트에 연결 하려는 추가에서 테 넌 트 **테 넌 트 Azure Sentinel 연결할**합니다. 
1. Active Directory 화면이 열립니다. Azure Sentinel에 연결 하 고 해당 로그를 읽을 Azure Sentinel에 게 권한을 부여 하려는 각 테 넌 트의 전역 관리자 사용자를 사용 하 여 인증 하 라는 메시지가 표시 됩니다. 
5. Stream Office 365 활동 로그, 클릭 **선택** Azure Sentinel를 스트리밍 하려는 로그 유형을 선택 합니다. 현재 Azure Sentinel 지 원하는 Exchange 및 SharePoint입니다.

4. 클릭 **변경 내용을 적용**합니다.

3. Log Analytics에서 관련 스키마를 사용 하 여 Office 365 로그를 검색할 **OfficeActivity**합니다.


## <a name="next-steps"></a>다음 단계
이 문서에서는 Azure Sentinel에 Office 365를 연결 하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- 에 대해 알아봅니다 하는 방법 [데이터에 잠재적 위협을 파악](quickstart-get-visibility.md)합니다.
- 시작 [사용 하 여 Azure Sentinel 위협을 감지 하도록](tutorial-detect-threats.md)합니다.

