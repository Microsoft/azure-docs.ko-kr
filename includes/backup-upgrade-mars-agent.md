---
title: Azure Backup 에이전트 업그레이드
description: 이 정보는 Azure Backup 에이전트를 업그레이드해야 하는 이유와 업그레이드를 다운로드할 수 있는 위치를 설명합니다.
services: backup
cloud: ''
suite: ''
author: dcurwin
manager: carmonm
ms.service: backup
ms.tgt_pltfrm: <optional>
ms.devlang: <optional>
ms.topic: article
ms.date: 8/29/2018
ms.author: dacurwin
ms.openlocfilehash: c22fbcd07286ddffedd8fc2fdc12017b9338d7f7
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77161853"
---
## <a name="upgrade-the-mars-agent"></a>MARS 에이전트 업그레이드

MARS(Microsoft Azure Recovery Service) 에이전트 2.0.9083.0 버전 미만은 Azure ACS(Access Control Service)에 종속성이 있습니다. MARS 에이전트는 Azure Backup 에이전트라고도 합니다. 2018년에 Azure는 [Azure ACS(Access Control Service) 사용을 중지했습니다](../articles/active-directory/azuread-dev/active-directory-acs-migration.md). 2018년 3월 19일부터는 2.0.9083.0 미만의 모든 MARS 에이전트 버전에서 백업이 실패합니다. 백업 실패를 방지하거나 해결하려면 [MARS 에이전트를 최신 버전으로 업그레이드](https://go.microsoft.com/fwlink/?linkid=229525)하세요. MARS 에이전트를 업그레이드해야 하는 서버를 식별하려면 [MARS를 업그레이드하기 위한 백업 블로그](https://blogs.technet.microsoft.com/srinathv/2018/01/17/updating-azure-backup-agents/)의 단계를 수행하세요. MARS 에이전트는 Azure에 파일 및 폴더, 시스템 상태 데이터를 백업하는 데 사용됩니다. System Center DPM 및 Azure Backup Server는 MARS 에이전트를 사용하여 Azure로 데이터를 백업합니다.
