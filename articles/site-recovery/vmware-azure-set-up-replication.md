---
title: Azure Site Recovery를 사용하여 Azure에 대한 VMware 재해 복구의 복제 정책 구성 및 관리 | Microsoft Docs
description: Azure Site Recovery를 사용하여 Azure에 대한 VMware 재해 복구의 복제 설정을 구성하는 방법에 대해 설명합니다.
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: sutalasi
ms.openlocfilehash: b60d8a8fb9b9300a6914ad33b2f760fb5adde3b4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60723520"
---
# <a name="configure-and-manage-replication-policies-for-vmware-disaster-recovery-to-azure"></a>Azure에 대한 VMware 재해 복구의 복제 정책 구성 및 관리
이 아티클에서는 VMware VM을 Azure에 복제할 때 [Azure Site Recovery](site-recovery-overview.md)를 사용하여 복제 정책을 구성하는 방법을 설명합니다.

## <a name="create-a-policy"></a>정책 만들기

1. **관리** > **Site Recovery 인프라**를 선택합니다.
2. **VMware 및 실제 컴퓨터의 경우**에서 **복제 정책**을 선택합니다.
3. **+복제 정책**을 클릭하고 정책 이름을 지정합니다.
4. **RPO 임계값**에서 RPO 제한을 지정합니다. 연속 복제가 이 제한을 초과하면 경고가 생성됩니다.
5. **복구 지점 보존**에서 각 복구 지점의 보존 기간을 시간 단위로 지정합니다. 보호된 컴퓨터는 보존 기간 내의 모든 지점으로 복구할 수 있습니다. Premium Storage에 복제된 컴퓨터의 경우 최대 24시간 동안 보존하도록 지원됩니다. 표준 저장소에서 최대 72시간 동안 지원됩니다.
6. **앱 일치 스냅숏 빈도**, 선택 빈도 (시간 단위로) 드롭다운 목록에서 응용 프로그램 일치 스냅숏이 포함 된 복구 지점을 만들어야 합니다. 응용 프로그램 일관성 지점 생성을 해제 하려는 경우 "Off" 값 드롭다운 목록에서 선택 합니다.
7. **확인**을 클릭합니다. 정책은 30~60초 내에 만들어야 합니다.

복제 정책을 만들 때 접미사 "failback"을 사용하여 장애 복구 복제 일치 정책이 자동으로 만들어집니다. 정책을 만든 후에 해당 정책 > **설정 편집**을 선택하여 편집할 수 있습니다.

## <a name="associate-a-configuration-server"></a>구성 서버 연결

온-프레미스 구성 서버와 복제 정책을 연결합니다.

1. **연결**을 클릭하여 구성 서버를 선택합니다.

    ![구성 서버 연결](./media/vmware-azure-set-up-replication/associate1.png)
2. **확인**을 클릭합니다. 구성 서버는 1~2분 내에 연결해야 합니다.

    ![구성 서버 연결](./media/vmware-azure-set-up-replication/associate2.png)

## <a name="edit-a-policy"></a>정책 편집

1. 선택 **관리할** > **Site Recovery 인프라** > **복제 정책**합니다.
2. 수정 하려는 복제 정책을 선택 합니다.
3. 클릭 **설정 편집**, 필요에 따라 RPO 임계값/복구 지점 보존 시간/앱 일치 스냅숏 빈도 필드를 업데이트 합니다.
4. 응용 프로그램 일관성 지점 생성을 해제 하려는 경우 "꺼짐" 필드의 드롭다운 목록에서 값 선택 **앱 일치 스냅숏 빈도**합니다.
5. **저장**을 클릭합니다. 정책은 30 ~ 60 초 내에 업데이트 되어야 합니다.

## <a name="disassociate-or-delete-a-replication-policy"></a>복제 정책 분리 또는 삭제

1. 복제 정책을 선택합니다.
    a. 정책 구성 서버에서 연결을 분리하려면 복제된 컴퓨터가 정책을 사용하지 않도록 합니다. 그런 다음, **분리**를 클릭합니다.
    b. 정책을 삭제하려면 해당 정책이 구성 서버와 연결되지 않도록 합니다. 그런 다음, **삭제**를 클릭합니다. 삭제하는 데 30~60초가 걸립니다.
2. **확인**을 클릭합니다.
