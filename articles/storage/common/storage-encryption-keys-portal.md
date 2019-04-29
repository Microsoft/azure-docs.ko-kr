---
title: Azure portal에서 Azure Storage 암호화에 대 한 고객 관리 키를 구성 합니다.
description: Azure Storage 암호화에 대 한 고객 관리 키를 구성 하려면 Azure portal을 사용 하는 방법에 알아봅니다. 고객 관리 키를 사용 하 여 만들기, 회전, 해제 및 액세스 제어를 취소할 수 있습니다.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/16/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 47dd38d9b5fd99431c8661c4891c4ebcdf5341d6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61593669"
---
# <a name="configure-customer-managed-keys-for-azure-storage-encryption-from-the-azure-portal"></a>Azure portal에서 Azure Storage 암호화에 대 한 고객 관리 키를 구성 합니다.

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

이 문서에서는 사용 하 여 고객 관리 키를 사용 하 여 key vault를 구성 하는 방법을 보여 줍니다.는 [Azure portal](https://portal.azure.com/)합니다. Azure portal을 사용 하 여 key vault를 만드는 방법에 알아보려면 참조 [빠른 시작: 설정 하 고 Azure portal을 사용 하 여 Azure Key Vault에서 비밀을 검색](../../key-vault/quick-create-portal.md)합니다. 


> [!IMPORTANT]
> 고객 관리 키를 사용 하 여 Azure Storage 암호화를 사용 하 여 있어야 key vault를 구성 하는 두 개의 필수 속성만 **일시 삭제** 하 고 **안 함**합니다. 이러한 속성은 Azure portal에서 새 키 자격 증명 모음을 만들 때 기본적으로 활성화 됩니다. 그러나 기존 key vault에서 이러한 속성을 사용 하도록 설정 해야 할 경우 PowerShell 또는 Azure CLI 사용 해야 합니다.

## <a name="enable-customer-managed-keys"></a>고객 관리 키를 사용 하도록 설정

Azure portal에서 고객 관리 키를 사용 하도록 설정 하려면 다음이 단계를 수행 합니다.

1. 저장소 계정으로 이동합니다.
1. 저장소 계정에 대한 **설정** 블레이드에서 **암호화**를 클릭합니다. 다음 그림과 같이 **사용자 고유 키 사용** 옵션을 선택합니다.

    ![암호화 옵션을 보여 주는 포털 스크린샷](./media/storage-encryption-keys-portal/ssecmk1.png)

## <a name="specify-a-key"></a>키를 지정 합니다.

고객 관리 키를 사용 하도록 설정한 후에 저장소 계정에 연결 하는 키를 지정 하는 기회가 해야 합니다.

### <a name="specify-a-key-as-a-uri"></a>키를 URI로 지정

키 URI를 지정 하려면 다음이 단계를 수행 합니다.

1. Azure portal에서 키 URI을 찾으려면 key vault를 찾아서 선택 합니다 **키** 설정 합니다. 원하는 키를 선택한 다음 해당 설정을 보려면 키를 클릭 합니다. 값을 복사 합니다 **키 식별자** URI를 제공 하는 필드입니다.

    ![스크린 샷 보여 주는 키 자격 증명 모음 키 URI](media/storage-encryption-keys-portal/key-uri-portal.png)

1. 에 **암호화** 저장소 계정에 대 한 설정을 선택 합니다 **키 URI 입력** 옵션입니다.
1. **키 URI** 필드에 URI를 지정합니다.

   ![키 URI를 입력 하는 방법을 보여 주는 스크린샷](./media/storage-encryption-keys-portal/ssecmk2.png)

### <a name="specify-a-key-from-a-key-vault"></a>Key Vault에서 키 지정

Key vault에서 키를 지정 하려면 먼저 key vault는 키가 들어 있는지 확인 합니다. Key vault에서 키를 지정 하려면 다음이 단계를 수행 합니다.

1. **Key Vault에서 선택** 옵션을 선택합니다.
2. 사용하려는 키가 포함된 Key Vault를 선택합니다.
3. Key Vault에서 키를 선택합니다.

   ![고객 관리 키 옵션을 보여 주는 스크린샷](./media/storage-encryption-keys-portal/ssecmk3.png)

## <a name="update-the-key-version"></a>키 버전 업데이트

키의 새 버전을 만들면 새 버전을 사용 하 여 저장소 계정을 업데이트 해야 합니다. 다음 단계를 수행하세요.

1. 저장소 계정으로 이동 하 고 표시 합니다 **암호화** 설정 합니다.
1. 새 키 버전에 대 한 URI를 지정 합니다. 또는 key vault 및 키 버전을 업데이트를 다시 선택할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [미사용 데이터에 대 한 azure Storage 암호화](storage-service-encryption.md)
- [Azure Key Vault 란](https://docs.microsoft.com/azure/key-vault/key-vault-whatis)?
