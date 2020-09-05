---
title: HSM 보호 키를 생성하고 전송하는 방법 – BYOK – Azure Key Vault
description: 이 문서의 설명에 따라 Azure Key Vault에서 사용할 고유의 HSM 보호 키를 계획하고 생성하고 전송할 수 있습니다. 이 방법을 BYOK(Bring Your Own Key)라고 합니다.
services: key-vault
author: amitbapat
manager: devtiw
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: tutorial
ms.date: 05/29/2020
ms.author: ambapat
ms.openlocfilehash: 1869ec9b617a7451ec42fa9d092ea3bb5834f9e8
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88585477"
---
# <a name="import-hsm-protected-keys-to-key-vault-byok"></a>Key Vault로 HSM 보호 키 가져오기(BYOK)

보안을 강화하기 위해 Azure Key Vault 사용 시 HSM(하드웨어 보안 모듈)에서 키를 가져오거나 생성할 수 있습니다. 이렇게 하면 키가 HSM 경계를 절대 벗어나지 않습니다. 이 시나리오를 흔히 *BYOK(Bring Your Own Key)* 라고 합니다. Key Vault는 HSM(FIPS 140-2 Level 2 유효성 검사 통과)의 nCipher nShield 제품군을 사용하여 키를 보호합니다.

이 문서의 정보를 참조하여 Azure Key Vault에서 사용할 고유의 HSM 보호 키를 계획하고 생성하고 전송할 수 있습니다.

> [!NOTE]
> 이 기능은 Azure 중국 21Vianet에 사용할 수 없습니다. 
> 
> 이 가져오기 방법은 [지원되는 HSM](#supported-hsms)에만 사용할 수 있습니다. 

자세한 내용 및 Key Vault를 시작하는 방법에 대한 자습서(HSM 보호 키에 대한 키 자격 증명 모음을 만드는 방법 포함)는 [Azure Key Vault란?](../general/overview.md)을 참조하세요.

## <a name="overview"></a>개요

다음은 프로세스의 개요입니다. 구체적인 단계는 이 문서의 뒷부분에서 설명합니다.

* Key Vault에서 키(*KEK(키 교환 키)* 라고 함)를 생성합니다. KEK는 `import` 키 작업만 포함하는 RSA-HSM 키여야 합니다. Key Vault Premium SKU만 RSA-HSM 키를 지원합니다.
* KEK 공개 키를 .pem 파일로 다운로드합니다.
* KEK 공개 키를 온-프레미스 HSM에 연결된 오프라인 컴퓨터로 전송합니다.
* 오프라인 컴퓨터에서, HSM 공급업체에서 제공한 BYOK 도구를 사용하여 BYOK 파일을 만듭니다. 
* 대상 키는 KEK(키 교환 키)로 암호화되며, Key Vault HSM으로 전송될 때까지 암호화 상태를 유지합니다. 암호화된 버전의 키만 온-프레미스 HSM을 벗어납니다.
* Key Vault HSM 내에 생성된 KEK는 내보낼 수 없습니다. HSM은 Key Vault HSM 외부에는 KEK의 일반 버전이 없다는 규칙을 적용합니다.
* KEK는 대상 키를 가져올 키 자격 증명 모음과 동일한 키 자격 증명 모음에 있어야 합니다.
* BYOK 파일이 Key Vault에 업로드되면 Key Vault HSM은 KEK 프라이빗 키를 사용하여 대상 키 자료를 해독하고 HSM 키로 가져옵니다. 이 작업은 Key Vault HSM 내에서만 발생합니다. 대상 키는 항상 HSM 보호 경계에 남아 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

다음 표에는 Azure Key Vault에서 BYOK를 사용하기 위한 전제 조건이 나열되어 있습니다.

| 요구 사항 | 추가 정보 |
| --- | --- |
| Azure 구독 |Azure Key Vault에서 키 자격 증명 모음을 만들려면 Azure 구독이 필요합니다. [평가판에 가입](https://azure.microsoft.com/pricing/free-trial/)합니다. |
| HSM 보호 키를 가져올 Key Vault Premium SKU |Azure Key Vault의 서비스 계층 및 기능에 대한 자세한 내용은 [Key Vault 가격 책정](https://azure.microsoft.com/pricing/details/key-vault/)을 참조하세요. |
| 지원되는 HSM 목록의 HSM과 HSM 공급업체에서 제공한 BYOK 도구 및 지침 | HSM 사용 권한과 HSM 사용 방법에 대한 기본 지식이 필요합니다. [지원되는 HSM](#supported-hsms)을 참조하세요. |
| Azure CLI 버전 2.1.0 이상 | [Azure CLI 설치](/cli/azure/install-azure-cli?view=azure-cli-latest)를 참조하세요.|

## <a name="supported-hsms"></a>지원되는 HSM

|공급업체 이름|공급업체 유형|지원되는 HSM 모델|자세한 정보|
|---|---|---|---|
|nCipher|제조업체,<br/>서비스로 제공되는 HSM|<ul><li>HSM의 nShield 제품군</li><li>서비스로 제공되는 nShield</ul>|[nCipher 새 BYOK 도구 및 설명서](https://www.ncipher.com/products/key-management/cloud-microsoft-azure)|
|Thales|제조업체|<ul><li>펌웨어 버전 7.3 이상을 사용하는 Luna HSM 7 제품군</li></ul>| [Luna BYOK 도구 및 설명서](https://supportportal.thalesgroup.com/csm?id=kb_article_view&sys_kb_id=3892db6ddb8fc45005c9143b0b961987&sysparm_article=KB0021016)|
|Fortanix|제조업체,<br/>서비스로 제공되는 HSM|<ul><li>SDKMS(자체 방어 키 관리 서비스)</li><li>Equinix SmartKey</li></ul>|[BYOK용 클라우드 공급자에게 SDKMS 키 내보내기 - Azure Key Vault](https://support.fortanix.com/hc/en-us/articles/360040071192-Exporting-SDKMS-keys-to-Cloud-Providers-for-BYOK-Azure-Key-Vault)|
|Marvell|제조업체|다음을 사용하는 모든 LiquidSecurity HSM<ul><li>펌웨어 버전 2.0.4 이상</li><li>펌웨어 버전 3.2 이상</li></ul>|[Marvell BYOK 도구 및 설명서](https://www.marvell.com/products/security-solutions/nitrox-hs-adapters/exporting-marvell-hsm-keys-to-cloud-azure-key-vault.html)|
|Cryptomathic|ISV(엔터프라이즈 키 관리 시스템)|다음을 포함한 여러 HSM 브랜드 및 모델<ul><li>nCipher</li><li>Thales</li><li>Utimaco</li></ul>자세한 내용은 [Cryptomathic 사이트](https://www.cryptomathic.com/azurebyok) 참조|[Cryptomathic BYOK 도구 및 설명서](https://www.cryptomathic.com/azurebyok)|



## <a name="supported-key-types"></a>지원되는 키 유형

|키 이름|키 유형|키 크기|원본|Description|
|---|---|---|---|---|
|KEK(키 교환 키)|RSA| 2048비트<br />3072비트<br />4096비트|Azure Key Vault HSM|Azure Key Vault에서 생성된 HSM 지원 RSA 키 쌍|
|대상 키|RSA|2048비트<br />3072비트<br />4096비트|공급업체 HSM|Azure Key Vault HSM으로 전송할 키|

## <a name="generate-and-transfer-your-key-to-the-key-vault-hsm"></a>키를 생성하여 Key Vault HSM으로 전송

키를 생성하여 Key Vault HSM으로 전송하는 방법은 다음과 같습니다.

* [1단계: KEK 생성](#step-1-generate-a-kek)
* [2단계: KEK 공개 키 다운로드](#step-2-download-the-kek-public-key)
* [3단계: 키를 생성하고 전송 준비](#step-3-generate-and-prepare-your-key-for-transfer)
* [4단계: Azure Key Vault에 키 전송](#step-4-transfer-your-key-to-azure-key-vault)

### <a name="step-1-generate-a-kek"></a>1단계: KEK 생성

KEK는 Key Vault HSM에서 생성되는 RSA 키입니다. KEK는 가져오려는 키(*대상* 키)를 암호화하는 데 사용됩니다.

KEK는 다음과 같아야 합니다.
- RSA HSM 키(2048비트, 3072비트 또는 4096비트)이어야 함
- 대상 키를 가져오려는 키 자격 증명 모음과 동일한 키 자격 증명 모음에 만들어야 함
- 허용되는 키 작업이 `import`로 설정된 상태에서 만들어야 함

> [!NOTE]
> KEK의 유일하게 허용되는 키 작업은 'import'여야 합니다. 'import'는 다른 모든 키 작업과 상호 배타적 관계입니다.

[az keyvault key create](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-create) 명령을 사용하여 키 작업이 `import`로 설정된 KEK를 만듭니다. 다음 명령에서 반환된 키 식별자(`kid`)를 기록해 둡니다. ([3단계](#step-3-generate-and-prepare-your-key-for-transfer)에서 `kid` 값이 사용됩니다.)

```azurecli
az keyvault key create --kty RSA-HSM --size 4096 --name KEKforBYOK --ops import --vault-name ContosoKeyVaultHSM
```

### <a name="step-2-download-the-kek-public-key"></a>2단계: KEK 공개 키 다운로드

[az keyvault key download](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-download) 명령을 사용하여 KEK 공개 키를 .pem 파일로 다운로드합니다. 가져오는 대상 키는 KEK 공개 키를 사용하여 암호화됩니다.

```azurecli
az keyvault key download --name KEKforBYOK --vault-name ContosoKeyVaultHSM --file KEKforBYOK.publickey.pem
```

KEKforBYOK.publickey.pem 파일을 오프라인 컴퓨터로 전송합니다. 다음 단계에서 이 파일이 필요합니다.

### <a name="step-3-generate-and-prepare-your-key-for-transfer"></a>3단계: 키를 생성하고 전송 준비

HSM 공급업체의 설명서를 참조하여 BYOK 도구를 다운로드하고 설치합니다. HSM 공급업체의 지침에 따라 대상 키를 생성한 다음, 키 전송 패키지(BYOK 파일)를 만듭니다. BYOK 도구는 [1단계](#step-1-generate-a-kek)의 `kid` 및 [2단계](#step-2-download-the-kek-public-key)에서 다운로드한 KEKforBYOK.publickey.pem 파일을 사용하여 BYOK 파일에 암호화된 대상 키를 생성합니다.

BYOK 파일을 연결된 컴퓨터로 전송합니다.

> [!NOTE] 
> RSA 1024비트 키를 가져오는 것은 지원되지 않습니다. 현재 타원 곡선(Elliptic Curve) 키 가져오기는 지원되지 않습니다.
> 
> **알려진 문제**: Luna HSM에서 RSA 4K 대상 키 가져오기는 펌웨어 7.4.0 이상에서만 지원됩니다.

### <a name="step-4-transfer-your-key-to-azure-key-vault"></a>4단계: Azure Key Vault에 키 전송

키 가져오기를 완료하려면 연결이 끊어진 컴퓨터의 키 전송 패키지(BYOK 파일)를 인터넷에 연결된 컴퓨터로 전송합니다. [az keyvault key import](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-import) 명령을 사용하여 BYOK 파일을 Key Vault HSM에 업로드합니다.

```azurecli
az keyvault key import --vault-name ContosoKeyVaultHSM --name ContosoFirstHSMkey --byok-file KeyTransferPackage-ContosoFirstHSMkey.byok
```

업로드가 성공하면 가져온 키의 속성이 Azure CLI에 표시됩니다.

## <a name="next-steps"></a>다음 단계

이제 주요 자격 증명 모음에서 이 HSM 보호된 키를 사용할 수 있습니다. 자세한 내용은 [가격 및 기능 비교](https://azure.microsoft.com/pricing/details/key-vault/)를 참조하세요.



