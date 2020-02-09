---
title: 계정 관리-Azure VMware 솔루션 (AVS) 포털
description: Azure VMware 솔루션 (AVS) 포털에서 계정을 관리 하는 방법을 설명 합니다.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 1fd7cb1c55fb664828448cef0b67ea9b16323bdf
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025370"
---
# <a name="manage-accounts-on-the-azure-vmware-solutions-avs-portal"></a>Azure VMware 솔루션 (AVS) 포털에서 계정 관리

AVS 서비스를 만들면 AVS에서 계정이 만들어집니다. 이 계정은 서비스가 있는 Azure 구독과 연결 됩니다. 구독에서 소유자 및 참가자 역할이 있는 모든 사용자는 AVS 포털에 액세스할 수 있습니다. AVS 서비스와 연결 된 Azure 구독 ID 및 테넌트 ID는 계정 페이지에서 찾을 수 있습니다.

AVS 포털에서 계정을 관리 하려면 [포털에 액세스](access-cloudsimple-portal.md) 하 고 측면 메뉴에서 **계정** 을 선택 합니다.

회사의 AVS 구성에 대 한 정보를 보려면 **요약** 을 선택 합니다. 최대 수의 AVS 사설 클라우드, 총 저장소, vSphere 클러스터 구성, 노드 수, 계산 코어 수를 포함 하 여 클라우드 구성의 현재 용량이 표시 됩니다. 현재 구성이 모든 요구를 충족 하지 않는 경우 추가 노드를 구매 하기 위한 링크가 포함 됩니다.

## <a name="email-alerts"></a>전자 메일 경고

AVS 사설 클라우드 구성 변경 내용에 대해 알릴 사용자의 이메일 주소를 추가할 수 있습니다.

1. **추가 전자 메일 경고** 영역에서 **새로 추가**를 클릭 합니다.
2. 전자 메일 주소를 입력 합니다.
3. Return 키를 누릅니다.  

항목을 제거 하려면 **X**를 클릭 합니다.

## <a name="avs-operator-access"></a>AVS 운영자 액세스

운영자 액세스 설정을 사용 하면 지원 엔지니어가 AVS 포털에 로그인 하도록 허용 하 여 문제 해결에 도움이 됩니다. 설정은 기본적으로 사용 하도록 설정 되어 있습니다. 고객 계정에 로그인 할 때 지원 엔지니어가 수행 하는 모든 작업은 **작업** > **감사** 페이지에서 기록 되 고 검토에 사용할 수 있습니다.

**AVS 연산자 액세스 사용** 설정/해제를 클릭 하 여 액세스를 설정 하거나 해제 합니다.
