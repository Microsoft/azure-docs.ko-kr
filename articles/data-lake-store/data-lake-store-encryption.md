---
title: Azure Data Lake Storage Gen1의 암호화 | Microsoft Docs
description: Azure Data Lake Storage Gen1의 암호화를 통해 데이터를 보고하고 엔터프라이즈 보안 정책을 구현하고 규정 준수 요구 사항을 충족할 수 있습니다. 이 문서에서는 디자인 개요를 제공하고 구현의 일부 기술적인 측면을 설명합니다.
services: data-lake-store
documentationcenter: ''
author: esung22
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: yagupta
ms.openlocfilehash: a009f212bd8baaa353d602dc6090aeeccddd4936
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60878445"
---
# <a name="encryption-of-data-in-azure-data-lake-storage-gen1"></a>Azure Data Lake Storage Gen1의 데이터 암호화

Azure Data Lake Storage Gen1의 암호화를 통해 데이터를 보고하고 엔터프라이즈 보안 정책을 구현하고 규정 준수 요구 사항을 충족할 수 있습니다. 이 문서에서는 디자인 개요를 제공하고 구현의 일부 기술적인 측면을 설명합니다.

Data Lake Storage Gen1은 미사용 또는 전송 중인 데이터의 암호화를 지원합니다. Data Lake Storage Gen1은 미사용 데이터에 대해 기본적으로 투명한 암호화를 지원합니다. 이러한 용어의 의미를 좀 더 자세히 알아보겠습니다.

* **기본적으로**: 새 Data Lake 저장소 Gen1 계정을 만들면 기본 설정은 암호화를 지원 합니다. 따라서 Data Lake Storage Gen1에 저장되는 데이터는 영구 미디어에 저장되기 전에 항상 암호화됩니다. 이것은 모든 데이터에 적용되는 동작이며 계정을 만든 후 변경할 수 없습니다.
* **투명 한**: 데이터 레이크 저장소 Gen1는 자동으로 유지 하기 전에 데이터를 암호화 하 고 검색 하기 전에 데이터를 해독 합니다. 암호화는 Data Lake Storage Gen1 계정 수준에서 관리자에 의해 구성 및 관리됩니다. 데이터 액세스 API는 변경되지 않습니다. 따라서 암호화 때문에 Data Lake Storage Gen1과 상호 작용하는 애플리케이션 및 서비스를 변경할 필요가 없습니다.

전송되는 데이터(즉, 동작 중인 데이터)는 항상 Data Lake Storage Gen1에서 암호화됩니다. 영구 미디어에 저장하기 전에 데이터를 암호화하는 것 외에도 HTTPS를 사용하여 전송 중 데이터를 항상 안전하게 보호합니다. HTTPS는 Data Lake Storage Gen1 REST 인터페이스에 지원되는 유일한 프로토콜입니다. 다음 다이어그램에서는 Data Lake Storage Gen1에서 데이터가 암호화되는 방법을 보여줍니다.

![Data Lake Storage Gen1의 데이터 암호화 다이어그램](./media/data-lake-store-encryption/fig1.png)


## <a name="set-up-encryption-with-data-lake-storage-gen1"></a>Data Lake Storage Gen1을 사용하여 암호화 설정

Data Lake Storage Gen1 암호화는 계정을 만드는 동안 설정되며, 항상 기본적으로 사용됩니다. 사용자가 직접 키를 관리하거나 Data Lake Storage Gen1에서 관리하도록 허용(기본값)할 수 있습니다.

자세한 내용은 [시작](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal)을 참조하세요.

## <a name="how-encryption-works-in-data-lake-storage-gen1"></a>Data Lake Storage Gen1에서 암호화가 작동하는 방법

다음 정보에서는 마스터 암호화 키를 관리하는 방법을 설명하고 Data Lake Storage Gen1의 데이터 암호화에서 사용할 수 있는 세 가지 종류의 키에 대해 설명합니다.

### <a name="master-encryption-keys"></a>마스터 암호화 키

Data Lake Storage Gen1은 MEK(마스터 암호화 키)를 관리하는 두 가지 모드를 제공합니다. 지금은 마스터 암호화 키가 최상위 수준 키라고 가정하겠습니다. Data Lake Storage Gen1에 저장된 데이터를 해독하려면 마스터 암호화 키 액세스 권한이 필요합니다.

마스터 암호화 키를 관리하기 두 가지 모드는 다음과 같습니다.

*   서비스 관리 키
*   고객 관리 키

두 모드에서 마스터 암호화 키는 Azure Key Vault에 저장하여 보호됩니다. Key Vault는 Azure에서 완벽하게 관리되는 매우 안전한 서비스로 암호화 키를 보호하는 데 사용할 수 있습니다. 자세한 내용은 [Key Vault](https://azure.microsoft.com/services/key-vault)를 참조하세요.

다음은 두 MEK 관리 모드에서 제공하는 기능을 간략히 비교한 것입니다.

|  | 서비스 관리 키 | 고객 관리 키 |
| --- | --- | --- |
|데이터가 어떻게 저장되나요?|저장하기 전에 항상 암호화됩니다.|저장하기 전에 항상 암호화됩니다.|
|마스터 암호화 키가 어디에 저장되나요?|Key Vault|Key Vault|
|Key Vault 외부에 저장되는 암호화 키가 있나요? |아닙니다.|아닙니다.|
|Key Vault에서 MEK를 검색할 수 있나요?|아니요. MEK는 Key Vault에 저장된 후에 암호화 및 암호 해독에만 사용할 수 있습니다.|안 됩니다. MEK는 Key Vault에 저장된 후에 암호화 및 암호 해독에만 사용할 수 있습니다.|
|Key Vault 인스턴스 및 MEK 소유자는 누구인가요?|Data Lake Storage Gen1 서비스|사용자는 고유한 Azure 구독에 속한 Key Vault 인스턴스를 소유합니다. Key Vault의 MEK는 소프트웨어 또는 하드웨어로 관리할 수 있습니다.|
|사용자가 Data Lake Storage Gen1 서비스의 MEK에 대한 액세스를 취소할 수 있나요?|아닙니다.|예. 사용자는 Key Vault의 액세스 제어 목록을 관리하고 Data Lake Storage Gen1 서비스의 서비스 ID에 대한 액세스 제어 항목을 제거할 수 있습니다.|
|MEK를 영구적으로 삭제할 수 있나요?|아닙니다.|예. Key Vault에서 MEK를 삭제하면 Data Lake Storage Gen1 서비스를 포함하여 누구도 Data Lake Storage Gen1 계정의 데이터를 해독할 수 없습니다. <br><br> Key Vault에서 MEK를 삭제하기 전에 명시적으로 백업하면 MEK를 복원하고 데이터 복구할 수 있습니다. 그러나 Key Vault에서 MEK를 삭제하기 전에 백업하지 않으면 이후에 Data Lake Storage Gen1 계정의 데이터를 해독할 수 없습니다.|


MEK 및 MEK가 상주하는 Key Vault 인스턴스를 관리하는 사람이 다르다는 차이점을 제외한 디자인의 나머지 부분은 두 모드가 동일합니다.

마스터 암호화 키의 모드를 선택하는 경우 다음 사항에 주의해야 합니다.

*   Data Lake Storage Gen1 계정을 프로비전할 때 고객 관리 키와 서비스 관리 키 중에서 무엇을 사용할 것인지 선택할 수 있습니다.
*   Data Lake Storage Gen1 계정을 프로비전한 후에는 모드를 변경할 수 없습니다.

### <a name="encryption-and-decryption-of-data"></a>데이터 암호화 및 암호 해독

데이터 암호화 디자인에 사용되는 세 가지 종류의 키가 있습니다. 다음 테이블에서는 요약 정보를 제공합니다.

| 키                   | 약어 | 연결 대상 | 저장 위치                             | Type       | 메모                                                                                                   |
|-----------------------|--------------|-----------------|----------------------------------------------|------------|---------------------------------------------------------------------------------------------------------|
| 마스터 암호화 키 | MEK          | Data Lake Storage Gen1 계정 | Key Vault                              | 비대칭 | Data Lake Storage Gen1 또는 사용자가 관리할 수 있습니다.                                                              |
| 데이터 암호화 키   | DEK          | Data Lake Storage Gen1 계정 | 영구 저장소, Data Lake Storage Gen1 서비스에서 관리 | 대칭  | DEK는 MEK에서 암호화됩니다. 암호화된 DEK는 영구 미디어에 저장됩니다. |
| 블록 암호화 키  | BEK          | 데이터 블록 | 없음                                         | 대칭  | BEK는 DEK 및 데이터 블록에서 파생됩니다.                                                      |

다음 다이어그램은 이러한 개념을 보여 줍니다.

![데이터 암호화의 키](./media/data-lake-store-encryption/fig2.png)

#### <a name="pseudo-algorithm-when-a-file-is-to-be-decrypted"></a>파일 암호를 해독해야 할 때의 의사 알고리즘:
1.  Data Lake Storage Gen1 계정의 DEK가 캐시되었으며 사용 가능한지 확인합니다.
    - 그렇지 않으면 영구 저장소에서 암호화된 DEK를 읽고 Key Vault로 보내서 해독합니다. 메모리에서 해독된 DEK를 캐시합니다. 이제 사용할 준비가 되었습니다.
2.  파일에 있는 모든 데이터 블록에 대해
    - 영구 저장소에서 암호화된 데이터 블록을 읽습니다.
    - DEK 및 암호화된 데이터 블록에서 BEK를 생성합니다.
    - BEK를 사용하여 데이터를 해독합니다.


#### <a name="pseudo-algorithm-when-a-block-of-data-is-to-be-encrypted"></a>데이터 블록을 암호화해야 할 때의 의사 알고리즘:
1.  Data Lake Storage Gen1 계정의 DEK가 캐시되었으며 사용 가능한지 확인합니다.
    - 그렇지 않으면 영구 저장소에서 암호화된 DEK를 읽고 Key Vault로 보내서 해독합니다. 메모리에서 해독된 DEK를 캐시합니다. 이제 사용할 준비가 되었습니다.
2.  DEK에서 데이터 블록에 대한 고유의 BEK를 생성합니다.
3.  AES-256 암호화를 사용하여 BEK로 데이터 블록을 암호화합니다.
4.  암호화된 데이터 블록을 영구 저장소에 저장합니다.

> [!NOTE] 
> DEK는 영구 미디어이든 메모리에 캐시되든 상관 없이 항상 MEK로 암호화되어 저장됩니다.

## <a name="key-rotation"></a>키 회전

고객이 관리하는 키를 사용하는 경우 MEK를 회전할 수 있습니다. 고객 관리 키를 사용하여 Data Lake Storage Gen1 계정을 설정하는 방법을 알아보려면 [시작](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal)을 참조하세요.

### <a name="prerequisites"></a>필수 조건

Data Lake Storage Gen1 계정을 설정할 때에 고유한 키를 사용하기로 했습니다. 계정을 만든 후에는 이 옵션을 변경할 수 없습니다. 다음 단계에서는 사용자가 고객 관리 키를 사용(즉, Key Vault에서 사용자 고유의 키를 선택함)한다고 가정합니다.

암호화에 대한 기본 옵션을 사용하는 경우 데이터는 항상 Data Lake Storage Gen1에서 관리되는 키를 사용하여 암호화됩니다. 이 옵션에는 Data Lake Storage Gen1에서 관리하는 것처럼 키를 회전하는 기능이 없습니다.

### <a name="how-to-rotate-the-mek-in-data-lake-storage-gen1"></a>Data Lake Storage Gen1에서 MEK를 회전하는 방법

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. Data Lake Storage Gen1 계정에 연결된 키를 저장하는 Key Vault 인스턴스로 이동합니다. **키**를 선택합니다.

    ![Key Vault의 스크린샷](./media/data-lake-store-encryption/keyvault.png)

3. Data Lake Storage Gen1 계정에 연결된 키를 선택하고 이 키의 새 버전을 만듭니다. 현재 Data Lake Storage Gen1은 새 버전의 키에 대한 키 회전만을 지원합니다. 다른 키에 대한 회전을 지원하지 않습니다.

   ![강조 표시된 새 버전에서 키 창의 스크린샷](./media/data-lake-store-encryption/keynewversion.png)

4. Data Lake Storage Gen1 계정으로 이동하여 **암호화**를 선택합니다.

   ![강조 표시된 암호화에서 Data Lake Storage Gen1 계정 창의 스크린샷](./media/data-lake-store-encryption/select-encryption.png)

5. 메시지는 새로운 주요 버전의 키를 사용할 수 있는지 알려 줍니다. **키 회전**을 클릭하여 키를 새 버전으로 업데이트합니다.

   ![강조 표시된 메시지와 회전 키에서 Data Lake Storage Gen1 창의 스크린샷](./media/data-lake-store-encryption/rotatekey.png)

이 작업은 2분 이내에 완료되며 키 회전으로 인해 예상되는 가동 중지 시간은 없습니다. 작업이 완료되면 새 버전의 키가 사용됩니다.

> [!IMPORTANT]
> 키 회전 작업이 완료되면 이전 버전의 키는 데이터를 암호화하는 데 더 이상 적극적으로 사용되지 않습니다.  그러나 데이터의 중복 복사본도 영향을 받는 예기치 않은 오류의 드문 경우에 여전히 이전 키를 사용하는 백업에서 데이터를 복원할 수 있습니다. 이러한 드문 경우에서 데이터에 액세스할 수 있도록 이전 버전의 암호화 키 복사본을 보관합니다. 재해 복구 계획에 대한 모범 사례는 [Data Lake Storage Gen1의 데이터에 대한 재해 복구 가이드](data-lake-store-disaster-recovery-guidance.md)를 참조하세요. 