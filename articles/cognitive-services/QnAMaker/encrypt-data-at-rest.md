---
title: 휴지 상태의 데이터 암호화 QnA Maker
titleSuffix: Azure Cognitive Services
description: Microsoft는 Microsoft에서 관리 하는 암호화 키를 제공 하며, CMK (고객이 관리 하는 키) 라고 하는 고유한 키를 사용 하 여 Cognitive Services 구독을 관리할 수도 있습니다. 이 문서에서는 QnA Maker에 대 한 저장소 데이터 암호화 및 CMK를 사용 하 고 관리 하는 방법을 설명 합니다.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.author: egeaney
ms.openlocfilehash: 19dc0f3a676d5373b28e4b7055050477c426f847
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100524463"
---
# <a name="qna-maker-encryption-of-data-at-rest"></a>휴지 상태의 데이터 암호화 QnA Maker

QnA Maker은 클라우드에 유지 될 때 데이터를 자동으로 암호화 하 여 조직의 보안 및 규정 준수 목표를 충족 하는 데 도움을 줍니다.

## <a name="about-encryption-key-management"></a>암호화 키 관리 정보

기본적으로 구독은 Microsoft에서 관리하는 암호화 키를 사용합니다. CMK (고객이 관리 하는 키) 라고 하는 고유한 키를 사용 하 여 구독을 관리 하는 옵션도 있습니다. CMK는 액세스 제어를 만들고, 회전 하 고, 비활성화 하 고, 취소할 수 있는 유연성을 제공 합니다. 데이터를 보호하는 데 사용되는 암호화 키를 감사할 수도 있습니다. CMK가 구독에 대해 구성 된 경우 두 번째 보안 계층을 제공 하는 동시에 두 번째 암호화가 제공 되며,이를 통해 Azure Key Vault를 통해 암호화 키를 제어할 수 있습니다.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker 일반 공급(안정적인 릴리스)](#tab/v1)

QnA Maker는 Azure search에서 CMK 지원을 사용 합니다. [Azure Key Vault를 사용 하 여 Azure Search에서 Cmk를](../../search/search-security-manage-encryption-keys.md)구성 합니다. CMK를 사용 하도록 설정 하려면이 Azure 인스턴스를 QnA Maker 서비스와 연결 해야 합니다.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker 관리형(미리 보기 릴리스)](#tab/v2)

QnA Maker는 [azure search의 cmk 지원을](../../search/search-security-manage-encryption-keys.md)사용 하 고 제공 된 cmk를 자동으로 연결 하 여 azure search 인덱스에 저장 된 데이터를 암호화 합니다.

---

> [!IMPORTANT]
> Azure Search 서비스 리소스는 2019 년 1 월 일 후에 생성 되어야 하며 무료 (공유) 계층에 있을 수 없습니다. Azure Portal에서 고객이 관리 하는 키를 구성 하는 것은 지원 되지 않습니다.

## <a name="enable-customer-managed-keys"></a>고객 관리형 키 사용

QnA Maker 서비스는 Azure Search 서비스의 CMK를 사용 합니다. CMKs를 사용 하도록 설정 하려면 다음 단계를 수행 합니다.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker 일반 공급(안정적인 릴리스)](#tab/v1)

1. 새 Azure Search 인스턴스를 만들고 [Azure Cognitive Search에 대 한 고객이 관리 하는 주요 필수 조건](../../search/search-security-manage-encryption-keys.md#prerequisites)에 설명 된 필수 구성 요소를 사용 하도록 설정 합니다.

   ![암호화 설정 보기 1](../media/cognitive-services-encryption/qna-encryption-1.png)

2. QnA Maker 리소스를 만들면 자동으로 Azure Search 인스턴스와 연결 됩니다. 이 인스턴스는 CMK와 함께 사용할 수 없습니다. CMK를 사용 하려면 1 단계에서 만든 Azure Search의 새로 만든 인스턴스를 연결 해야 합니다. 특히 `AzureSearchAdminKey` QnA Maker 리소스에서 및를 업데이트 해야 `AzureSearchName` 합니다.

   ![암호화 설정 보기 2](../media/cognitive-services-encryption/qna-encryption-2.png)

3. 다음으로 새 응용 프로그램 설정을 만듭니다.
   * **이름**:를로 설정 합니다. `CustomerManagedEncryptionKeyUrl`
   * **값**: Azure Search 인스턴스를 만들 때 1 단계에서 얻은 값을 사용 합니다.

   ![암호화 설정 3 보기](../media/cognitive-services-encryption/qna-encryption-3.png)

4. 완료 되 면 런타임을 다시 시작 합니다. 이제 QnA Maker 서비스는 CMK를 사용 합니다.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker 관리형(미리 보기 릴리스)](#tab/v2)

1.  QnA Maker 관리 (미리 보기) 서비스의 **암호화** 탭으로 이동 합니다.
2.  **고객 관리 키** 옵션을 선택 합니다. [고객 관리 키](../../storage/common/customer-managed-keys-configure-key-vault.md?tabs=portal) 에 대 한 세부 정보를 제공 하 고 **저장** 을 클릭 합니다.

     :::image type="content" source="../media/cognitive-services-encryption/qnamaker-v2-encryption-cmk.png" alt-text="QnA Maker 관리 (미리 보기) CMK 설정" lightbox="../media/cognitive-services-encryption/qnamaker-v2-encryption-cmk.png":::

3.  저장이 완료 되 면 CMK가 Azure Search 인덱스에 저장 된 데이터를 암호화 하는 데 사용 됩니다.

> [!IMPORTANT]
> 새 Azure Cognitive Search 서비스에서 기술 자료를 만들기 전에 CMK를 설정 하는 것이 좋습니다. 기존 기술 자료를 사용 하 여 QnA Maker 서비스에서 CMK를 설정 하면 해당 서비스에 대 한 액세스 권한이 손실 될 수 있습니다. Azure 인식 검색에서 [암호화 된 콘텐츠 작업](../../search/search-security-manage-encryption-keys.md#work-with-encrypted-content) 에 대해 자세히 알아보세요.

> [!NOTE]
> 고객 관리 키를 사용 하는 기능을 요청 하려면 [Cognitive Services Customer-Managed 키 요청 양식](https://aka.ms/cogsvc-cmk)을 작성 하 고 제출 합니다.

---

## <a name="regional-availability"></a>지역별 가용성

모든 Azure Search 지역에서 고객 관리 키를 사용할 수 있습니다.

## <a name="encryption-of-data-in-transit"></a>전송 중 데이터 암호화

QnA Maker 포털은 사용자의 브라우저에서 실행 됩니다. 모든 작업은 각 인식 서비스 API에 대 한 직접 호출을 트리거합니다. 따라서 QnA Maker은 전송 중인 데이터를 준수 합니다.
그러나 QnA Maker 포털 서비스는 미국 서 부에서 호스트 되므로 미국 이외 고객에 게는 적합 하지 않습니다. 

## <a name="next-steps"></a>다음 단계

* [Azure Key Vault에서 CMKs를 사용 하 Azure Search의 암호화](../../search/search-security-manage-encryption-keys.md)
* [저장 데이터 암호화](../../security/fundamentals/encryption-atrest.md)
* [Azure Key Vault에 대해 자세히 알아보기](../../key-vault/general/overview.md)