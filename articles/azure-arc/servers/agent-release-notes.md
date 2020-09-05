---
title: Azure Arc 사용 서버 (미리 보기) 에이전트의 새로운 기능
description: 이 문서에는 Azure Arc 사용 서버 (미리 보기) 에이전트에 대 한 릴리스 정보가 포함 되어 있습니다. 요약 된 많은 문제에 대 한 자세한 내용은 추가 정보를 참조 하세요.
ms.topic: conceptual
ms.date: 08/31/2020
ms.openlocfilehash: 0f5060189d6f9cac620b49f414e0e27bed3e356e
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89236696"
---
# <a name="whats-new-with-azure-arc-enabled-servers-preview-agent"></a>Azure Arc 사용 서버 (미리 보기) 에이전트의 새로운 기능

Azure Arc 사용 서버 (미리 보기)에 연결 된 컴퓨터 에이전트는 지속적으로 향상 된 기능을 받습니다. 최신 개발 정보를 확인할 수 있도록 이 문서에서는 다음에 대한 정보를 제공합니다.

- 최신 릴리스
- 알려진 문제
- 버그 수정

## <a name="august-2020"></a>2020년 8월

버전: 0.11

- Ubuntu 20.04에 대 한 지원.

- 확장 배포에 대 한 안정성 향상.

### <a name="known-issues"></a>알려진 문제

이전 버전의 Linux 에이전트를 사용 하 고 프록시 서버를 사용 하도록 구성한 경우 업그레이드 후 프록시 서버 설정을 다시 구성 해야 합니다. 이 위해 실행 `sudo azcmagent_proxy add http://proxyserver.local:83`합니다.

## <a name="next-steps"></a>다음 단계

여러 하이브리드 컴퓨터에서 Arc 사용 서버 (미리 보기)를 평가 하거나 사용 하도록 설정 하기 전에 [연결 된 컴퓨터 에이전트 개요](agent-overview.md) 를 검토 하 여 요구 사항, 에이전트에 대 한 기술 세부 정보 및 배포 방법을 파악 하십시오.