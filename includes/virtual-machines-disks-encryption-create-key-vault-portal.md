---
title: 포함 파일
description: 포함 파일
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/23/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: a967777b65c06cf23239a47e8e691fb3a29231b4
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88815473"
---
디스크에 대해 고객이 관리 하는 키를 설정 하려면 특정 순서로 리소스를 만들어야 합니다 .이 작업을 처음 수행 하는 경우에는이 작업을 수행 해야 합니다. 먼저 Azure Key Vault를 만들고 설정 해야 합니다.

## <a name="set-up-your-azure-key-vault"></a>Azure Key Vault 설정

1. [Azure Portal](https://aka.ms/diskencryptionupdates)에 로그인합니다.
1. **키 자격 증명 모음**을 검색 하 고 선택 합니다.

    [![검색 대화 상자가 확장 된 Azure Portal의 스크린샷](./media/virtual-machines-disk-encryption-portal/server-side-encryption-key-vault-portal-search.png)](./media/virtual-machines-disk-encryption-portal/sever-side-encryption-key-vault-portal-search-expanded.png#lightbox)

    > [!IMPORTANT]
    > 배포에 성공 하려면 Azure 주요 자격 증명 모음, 디스크 암호화 집합, VM, 디스크 및 스냅숏이 모두 동일한 지역 및 구독에 있어야 합니다.

1. **+ 추가** 를 선택 하 여 새 Key Vault을 만듭니다.
1. 새 리소스 그룹 만들기
1. 키 자격 증명 모음 이름을 입력 하 고, 지역을 선택 하 고, 가격 책정 계층을 선택 합니다.

    > [!NOTE]
    > Key Vault 인스턴스를 만드는 경우 일시 삭제 및 제거 보호를 사용하도록 설정해야 합니다. 일시 삭제를 사용하면 지정된 보존 기간(기본적으로 90일) 동안 Key Vault에 삭제된 키를 보관하게 됩니다. 제거 보호를 사용하면 보존 기간이 지날 때까지 삭제된 키를 영구 삭제할 수 없습니다. 이러한 설정은 실수로 삭제하여 데이터가 손실되지 않도록 보호합니다. 이러한 설정은 관리 디스크를 암호화하기 위해 Key Vault를 사용하는 경우 필수입니다.

1. **검토 + 만들기**를 선택 하 고, 선택 사항을 확인 한 다음, **만들기**를 선택 합니다.

    ![Azure Key Vault 만들기 환경의 스크린샷 만든 특정 값 표시](./media/virtual-machines-disk-encryption-portal/server-side-encryption-create-a-key-vault.png)

1. 키 자격 증명 모음이 배포를 완료 한 후 선택 합니다.
1. **설정**에서 **키** 를 선택 합니다.
1. **생성/가져오기**를 선택합니다.

    ![Key Vault 리소스 설정 창의 스크린샷 설정 내에 생성/가져오기 단추를 표시 합니다.](./media/virtual-machines-disk-encryption-portal/sever-side-encryption-key-vault-generate-settings.png)

1. **Rsa** 및 **rsa 키 크기** 를 모두 **2048**로 설정 하 여 두 **키 유형을** 모두 그대로 둡니다.
1. 원하는 대로 나머지 선택 항목을 입력 하 고 **만들기**를 선택 합니다.

    ![생성/가져오기 단추를 클릭 하면 표시 되는 키 만들기 블레이드의 스크린샷이 선택 됩니다.](./media/virtual-machines-disk-encryption-portal/server-side-encryption-create-a-key-generate.png)

## <a name="set-up-your-disk-encryption-set"></a>디스크 암호화 집합 설정

1. **디스크 암호화 집합** 을 검색 하 고 선택 합니다.
1. **디스크 암호화 집합** 블레이드에서 **+ 추가**를 선택 합니다.

    ![디스크 암호화 포털 주 화면의 스크린샷 추가 단추 강조 표시](./media/virtual-machines-disk-encryption-portal/sever-side-encryption-create-disk-encryption-set.png)

1. 리소스 그룹을 선택 하 고, 암호화 집합의 이름을로 설정 하 고, 키 자격 증명 모음과 동일한 지역을 선택 합니다.
1. **암호화 유형** **에서 고객이 관리 하는 키를 사용 하 여 미사용 암호화**를 선택 합니다.

    > [!NOTE]
    > 특정 암호화 유형을 사용 하 여 디스크 암호화 세트를 만든 후에는 변경할 수 없습니다. 다른 암호화 유형을 사용 하려면 새 디스크 암호화 집합을 만들어야 합니다.

1. **키를 선택 하려면 클릭**하십시오 .를 선택 합니다.
1. 이전에 만든 키 자격 증명 모음 및 키와 버전을 선택 합니다.
1. **선택**을 누릅니다.
1. **검토 + 만들기** 및 **만들기**를 차례로 선택 합니다.

    ![디스크 암호화 생성 블레이드의 스크린샷 구독, 리소스 그룹, 디스크 암호화 집합 이름, 지역 및 키 자격 증명 모음 + 키 선택기를 표시 합니다.](./media/virtual-machines-disk-encryption-portal/server-side-encryption-disk-set-blade.png)

1. 만들기가 완료 되 면 디스크 암호화 집합을 열고 팝업 되는 경고를 선택 합니다.

    ![경고 팝업 스크린샷: ' 디스크, 이미지 또는 스냅숏을 디스크 암호화 집합에 연결 하려면 키 자격 증명 모음에 대 한 사용 권한을 부여 해야 합니다. ' 계속 하려면이 경고를 선택 합니다.](./media/virtual-machines-disk-encryption-portal/server-side-encryption-disk-encryption-set-alert-fix.png)

    두 개의 알림이 표시 되 고 성공 합니다. 이를 통해 키 자격 증명 모음에 설정 된 디스크 암호화를 사용할 수 있습니다.

    ![주요 자격 증명 모음에 대 한 성공적인 사용 권한 및 역할 할당의 스크린샷](./media/virtual-machines-disk-encryption-portal/disk-encryption-notification-success.png)