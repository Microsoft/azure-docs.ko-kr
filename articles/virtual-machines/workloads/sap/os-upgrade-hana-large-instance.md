---
title: SAP HANA on Azure(대형 인스턴스)에 대한 운영 체제 업그레이드 | Microsoft Docs
description: SAP HANA on Azure(대형 인스턴스)에 대한 운영 체제 업그레이드 수행
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/28/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d7d451f3831309b4755170915b35a23da8910510
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60935639"
---
# <a name="operating-system-upgrade"></a>운영 체제 업그레이드
이 문서에서는 HANA 대형 인스턴스에서 운영 체제 업그레이드에 대한 세부 정보를 설명합니다.

>[!NOTE]
>OS 업그레이드는 고객 책임이며, Microsoft 운영 지원팀은 업그레이드 하는 동안 주의해야 할 주요 영역을 안내할 수 있습니다. 업그레이드에 대해 계획하기 전에도 해당 운영 체제 공급 업체와 상담해야 합니다.

HLI 단위 프로비전 시 Microsoft 운영 팀은 운영 체제를 설치합니다. 시간이 지남에 따라 HLI 단위에서 운영 체제를 유지 관리해야 합니다(예: 패치, 튜닝, 업그레이드 등).

운영 체제를 대폭 변경하기 전에(예: SP1을 SP2로 업그레이드) 지원 티켓을 열어 Microsoft 운영 팀에 문의해야 합니다.

티켓에 포함하세요.

* HLI 구독 ID
* 서버 이름
* 적용하려는 패치 수준
* 변경하려는 날짜 

운영 팀이 서버 블레이드에서 펌웨어 업그레이드가 필요한지 확인하기 때문에 원하는 업그레이드 날짜 최소 일주일 전에 이 티켓을 여는 것이 좋습니다.


Linux 버전이 다른 여러 SAP HANA 버전에 대한 지원 매트릭스는 [SAP Note #2235581](https://launchpad.support.sap.com/#/notes/2235581)을 참조하세요.


## <a name="known-issues"></a>알려진 문제

다음은 업그레이드 동안 몇 가지 알려진 일반적인 문제입니다.
- SKU Type II 클래스 SKU에서 SFS(software foundation server)는 OS 업그레이드 후 제거됩니다. OS 업그레이드 후 호환 가능한 SFS를 다시 설치해야 합니다.
- 이더넷 카드 드라이버(ENIC 및 FNIC)가 이전 버전으로 롤백했습니다. 업그레이드 후 호환 가능한 버전의 드라이버를 다시 설치해야 합니다.

## <a name="next-steps"></a>다음 단계
- OS 백업 Type I SKU 클래스에 대해서는 [Backup 및 복원](hana-overview-high-availability-disaster-recovery.md)을 참조합니다.
- Type II SKU 클래스에 대해서는 [Type II SKU용 OS Backup](os-backup-type-ii-skus.md)을 참조합니다.
