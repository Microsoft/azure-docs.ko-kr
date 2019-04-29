---
title: Microsoft Azure 미사용 데이터 암호화 | Microsoft Docs
description: 이 문서에서는 Microsoft Azure 미사용 데이터 암호화 개요, 전체 기능 및 일반적인 고려 사항을 제공합니다.
services: security
documentationcenter: na
author: barclayn
manager: barbkess
editor: TomSh
ms.assetid: 9dcb190e-e534-4787-bf82-8ce73bf47dba
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/18/2018
ms.author: barclayn
ms.openlocfilehash: 4ced712b1b2716d85f0366ea892460053db598b8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60613019"
---
# <a name="azure-data-encryption-at-rest"></a>Azure 미사용 데이터 암호화

Microsoft Azure에는 회사의 보안 및 규정 준수 요구 사항에 따라 데이터를 보호하는 도구가 있습니다. 이 문서는 다음 내용을 집중적으로 다룹니다.

- Microsoft Azure에서 미사용 데이터를 보호하는 방법
- 데이터 보호 구현에 참여하는 다양한 구성 요소에 대해 설명
- 여러 키 관리 보호 방법의 장단점 검토 

미사용 데이터 암호화는 일반적인 보안 요구 사항입니다. Azure에서는 조직이 사용자 지정 키 관리 솔루션과 관련된 위험 또는 비용 없이 미사용 데이터를 암호화할 수 있습니다. 조직에는 Azure에서 미사용 데이터 암호화를 완전히 관리할 수 있도록 하는 옵션이 제공됩니다. 또한 암호화 또는 암호화 키를 면밀하게 관리할 수 있는 다양한 옵션도 제공됩니다.

## <a name="what-is-encryption-at-rest"></a>미사용 데이터 암호화란?

미사용 데이터 암호화는 데이터가 유지될 때의 인코딩(암호화)을 나타냅니다. Azure의 미사용 데이터 암호화 디자인에서는 다음과 같이 대칭 암호화를 사용하여 간단한 개념적 모델에 따라 많은 양의 데이터를 빠르게 암호화하고 암호 해독합니다.

- 데이터가 저장소에 쓰여질 때 대칭 암호화 키를 사용하여 데이터를 암호화합니다. 
- 동일한 암호화 키를 사용하여 메모리에서 사용하도록 준비된 데이터를 암호 해독합니다.
- 데이터는 분할될 수 있고, 각 파티션마다 다른 키가 사용될 수 있습니다.
- 키는 ID 기반 액세스 제어 및 감사 정책으로 안전하게 보호되는 위치에 저장해야 합니다. 데이터 암호화 키는 종종 액세스를 더욱 제한하기 위해 비대칭 암호화로 암호화됩니다.

실제로 확장성과 가용성 보장뿐만 아니라 키 관리 및 제어 시나리오에는 추가 구성이 필요합니다. Microsoft Azure 미사용 데이터 암호화의 개념 및 구성 요소에 대해서는 아래에서 설명합니다.

## <a name="the-purpose-of-encryption-at-rest"></a>미사용 데이터 암호화의 목적

미사용 데이터 암호화는 저장된 데이터(미사용)에 대한 데이터 보호를 제공합니다. 미사용 데이터에 대한 공격에는 데이터가 저장된 하드웨어에 대한 물리적 액세스를 얻은 다음 포함된 데이터를 손상시키는 시도가 포함됩니다. 유지 관리하는 동안 이러한 공격에서 서버의 하드 드라이브가 잘못 처리되어 공격자가 하드 드라이브를 제거할 수 있습니다. 나중에 공격자는 하드 드라이브를 자신의 통제 하에 있는 컴퓨터에 넣어 데이터에 액세스하려고 시도합니다. 

미사용 데이터 암호화는 디스크에 있을 때 데이터를 암호화하여 공격자가 암호화되지 않은 데이터에 액세스하지 못하도록 설계되었습니다. 데이터가 암호화된 하드 드라이브를 공격자가 획득했지만 암호화 키를 획득하지 못한 경우 공격자는 데이터를 읽으려면 암호화를 풀어야 합니다. 이 공격은 하드 드라이브의 암호화되지 않은 데이터에 액세스하는 것보다 훨씬 복잡하고 더 많은 리소스를 소모합니다. 이러한 이유로 미사용 데이터 암호화는 적극적으로 권장되며 많은 조직에서 우선 순위가 높은 요구 사항입니다. 

데이터 거버넌스 및 규정 준수 노력에 대한 조직의 요구 사항에 따라 미사용 데이터 암호화도 필요합니다. HIPAA, PCI, FedRAMP 등의 산업 및 정부 규정에는 데이터 보호 및 암호화 요구 사항과 관련된 구체적인 보호 조치가 명시되어 있습니다. 미사용 데이터 암호화는 이러한 규정 중 일부를 사용하는 규정 준수에 필수적인 조치입니다.

규정 준수 및 규제 요구 사항 충족 외에도, 저장 데이터 암호화는 심층 방어 보호를 제공합니다. Microsoft Azure는 서비스, 애플리케이션 및 데이터를 위한 규격 플랫폼을 제공합니다. 또한 포괄적인 시설과 물리적 보안, 데이터 액세스 제어 및 감사를 제공합니다. 그러나 다른 보안 조치 중 하나가 실패할 경우 저장 데이터 암호화를 통해 이러한 보안 조치를 제공하도록 추가적인 "중첩" 보안 조치를 제공하는 것이 중요합니다.

Microsoft는 클라우드 서비스에서 저장 데이터 암호화 옵션을 제공하고 고객에게 암호화 키 및 키 사용 로그를 제공하기 위해 최선을 다하고 있습니다. 또한 Microsoft는 기본적으로 고객의 모든 저장 데이터를 암호화하기 위해 노력하고 있습니다.

## <a name="azure-encryption-at-rest-components"></a>Azure 미사용 데이터 암호화의 구성 요소

앞에서 설명한 대로 미사용 데이터 암호화의 목표는 디스크에 유지되는 데이터가 비밀 암호 키로 암호화된다는 것입니다. 이 목표를 달성하려면 보안 키 생성, 저장소, 액세스 제어 및 암호화 키 관리가 제공되어야 합니다. 세부 정보는 다를 수 있지만, Azure 서비스의 미사용 데이터 암호화 구현은 다음 다이어그램에서 보여 주는 개념 측면에서 설명될 수 있습니다.

![구성 요소](./media/azure-security-encryption-atrest/azure-security-encryption-atrest-fig1.png)

### <a name="azure-key-vault"></a>Azure Key Vault

암호화 키의 저장소 위치와 해당 키에 대한 액세스 제어는 미사용 데이터 암호화 모델의 핵심입니다. 키는 철저히 보안되어야 하지만, 특정 사용자가 관리하고 특정 서비스에서 사용할 수 있어야 합니다. Azure 서비스의 경우 Azure Key Vault는 권장되는 키 저장소 솔루션이며 서비스 전반에서 공통된 관리 환경을 제공합니다. 키는 키 자격 증명 모음에 저장되어 관리되며, 사용자 또는 서비스에 키 자격 증명 모음에 대한 액세스 권한을 부여할 수 있습니다. Azure Key Vault는 고객이 관리하는 암호화 키 시나리오에서 사용할 고객 키 생성 또는 고객 키 가져오기를 지원합니다.

### <a name="azure-active-directory"></a>Azure Active Directory

미사용 데이터의 암호화 및 암호 해독을 위해 Azure Key Vault에 저장된 키를 사용하여 이러한 데이터를 관리하거나 액세스하는 권한은 Azure Active Directory 계정에 부여할 수 있습니다. 

### <a name="key-hierarchy"></a>키 계층 구조

두 가지 이상의 암호화 키가 미사용 데이터 암호화 구현에 사용됩니다. 비대칭 암호화는 키 액세스 및 관리에 필요한 신뢰와 인증을 설정하는 데 유용합니다. 대칭 암호화는 대량 암호화 및 암호 해독에 더 효율적이므로 더 강력한 암호화 및 더 나은 성능을 제공합니다. 단일 암호화 키의 사용을 제한하면 키가 손상될 위험과 키를 교체해야 할 때의 재암호화 비용이 줄어듭니다. Azure 미사용 데이터 암호화 모델은 다음과 같은 유형의 키로 이루어진 키 계층을 사용합니다.

- **DEK(데이터 암호화 키)** - 파티션이나 데이터 블록을 암호화하는 데 사용되는 대칭 AES256 키입니다.  단일 리소스에는 많은 파티션과 많은 데이터 암호화 키가 있을 수 있습니다. 서로 다른 키로 각 데이터 블록을 암호화하면 암호 분석 공격이 더욱 어려워집니다. DEK에 대한 액세스는 특정 블록을 암호화하고 암호 해독하는 리소스 공급자 또는 애플리케이션 인스턴스에 필요합니다. DEK가 새 키로 대체되면 연결된 블록의 데이터만 새 키로 다시 암호화해야 합니다.
- **KEK(키 암호화 키)** - 데이터 암호화 키를 암호화하는 데 사용되는 비대칭 암호화 키입니다. 키 암호화 키를 사용하면 데이터 암호화 키 자체를 암호화하고 제어할 수 있습니다. KEK에 액세스할 수 있는 엔터티는 DEK가 필요한 엔터티와 다를 수 있습니다. 엔터티는 특정 파티션에 대한 각 DEK의 액세스를 제한하기 위해 DEK에 액세스하는 broker일 수 있습니다. KEK는 DEK를 암호 해독하는 데 필요하므로 KEK는 실질적으로 KEK를 삭제함으로써 DEK를 효과적으로 삭제할 수 있는 단일 지점이 됩니다.

키 암호화 키로 암호화된 데이터 암호화 키는 별도로 저장되며, 키 암호화 키에 액세스할 수 있는 엔터티만 해당 키로 암호화된 데이터 암호화 키를 가져올 수 있습니다. 다양한 키 저장소 모델이 지원됩니다. 다음 섹션에서는 각 모델에 대해 자세히 설명합니다.

## <a name="data-encryption-models"></a>데이터 암호화 모델

Azure의 다양한 리소스 공급자가 미사용 데이터 암호화를 구현하는 방법을 이해하려면 먼저 다양한 암호화 모델 및 장단점을 이해해야 합니다. 이러한 정의는 Azure의 모든 리소스 공급자에서 공유되어 공용 언어 및 분류를 보장합니다. 

서버 쪽 암호화에는 다음 세 가지 시나리오가 있습니다.

- 서비스 관리 키를 사용하여 서버 쪽 암호화
    - Azure 리소스 공급자에서 암호화 및 암호 해독 작업 수행
    - Microsoft에서 키 관리
    - 전체 클라우드 기능

- Azure Key Vault에서 고객 관리 키를 사용하여 서버 쪽 암호화
    - Azure 리소스 공급자에서 암호화 및 암호 해독 작업 수행
    - 고객이 Azure Key Vault를 통해 키 제어
    - 전체 클라우드 기능

- 고객 제어 하드웨어에서 고객 관리 키를 사용하여 서버 쪽 암호화
    - Azure 리소스 공급자에서 암호화 및 암호 해독 작업 수행
    - 고객이 고객 제어 하드웨어에서 키 제어
    - 전체 클라우드 기능

클라이언트 쪽 암호화의 경우 다음을 고려합니다.

- Azure 서비스에서 암호 해독된 데이터를 볼 수 없음
- 고객이 온-프레미스(또는 다른 보안 저장소)에서 키 관리 및 저장 - 키를 Azure 서비스에서 사용할 수 없습니다.
- 제한된 클라우드 기능

Azure에서 지원되는 암호화 모델은 두 가지 주요 그룹으로 나눕니다. 이전에 언급한 바와 같이 "클라이언트 암호화"와 "서버 측 암호화"입니다. 사용되는 저장 데이터 암호화 모델과는 관계없이 Azure 서비스에서는 항상 TLS 또는 HTTPS와 같은 보안 전송 프로토콜을 사용하는 것이 좋습니다. 따라서 암호화 전송은 전송 프로토콜을 통해 처리되어야 하며, 사용할 미사용 데이터 암호화 모델을 결정하는 주요 요소가 되어서는 안됩니다.

### <a name="client-encryption-model"></a>클라이언트 쪽 암호화 모델

클라이언트 쪽 암호화 모델은 서비스 또는 호출 애플리케이션이 리소스 공급자 또는 Azure 외부에서 수행되는 암호화를 나타냅니다. 이 암호화는 Azure의 서비스 애플리케이션이나 고객 데이터 센터에서 실행되는 애플리케이션에서 수행할 수 있습니다. 두 경우 모두 Azure 리소스 공급자에서 이 암호화 모델을 활용할 때 어떤 방식으로든 데이터의 암호를 해독할 수 없거나 암호화 키에 대한 액세스 권한이 없는 데이터의 암호화된 Blob을 받습니다. 이 모델에서는 키 관리가 호출 서비스/응용 프로그램에서 수행되며 Azure 서비스에 대해 불투명합니다.

![클라이언트](./media/azure-security-encryption-atrest/azure-security-encryption-atrest-fig2.png)

### <a name="server-side-encryption-model"></a>서버 쪽 암호화 모델

서버 쪽 암호화 모델은 Azure 서비스에서 수행되는 암호화를 참조합니다. 이 모델에서는 리소스 공급자에서 암호화 및 암호 해독 작업을 수행합니다. 예를 들어 Azure Storage는 일반 텍스트 작업으로 데이터를 받을 수 있으며, 암호화 및 암호 해독을 내부적으로 수행합니다. 리소스 공급자는 제공된 구성에 따라 Microsoft 또는 고객이 관리하는 암호화 키를 사용할 수도 있습니다. 

![서버](./media/azure-security-encryption-atrest/azure-security-encryption-atrest-fig3.png)

### <a name="server-side-encryption-key-management-models"></a>서버 쪽 암호화 키 관리 모델

서버 쪽 미사용 데이터 암호화 모델마다 키 관리의 고유한 특성을 나타냅니다. 여기에는 암호화 키를 만들고 저장하는 방법, 액세스 모델 및 키 회전 절차가 포함됩니다. 

#### <a name="server-side-encryption-using-service-managed-keys"></a>서비스 관리 키를 사용하여 서버 쪽 암호화

많은 고객에 대한 필수적 요구 사항은 데이터가 사용되고 있지 않을 때마다 암호화되도록 하는 것입니다. 서비스 관리 키를 사용하는 서버 쪽 암호화는 고객이 암호화를 위한 특정 리소스(Storage 계정, SQL DB 등)를 표시하고, 모든 키 관리 측면(예: 키 발급, 교체 및 백업)을 Microsoft에 맡길 수 있게 함으로써 이 모델을 가능하게 합니다. 일반적으로 미사용 데이터 암호화를 지원하는 Azure 서비스 대부분은 암호화 키 관리를 Azure로 오프로드하는 이 모델을 지원합니다. Azure 리소스 공급자는 키를 만들고, 안전한 저장소에 저장하고, 필요할 때 검색합니다. 이는 서비스에서 키에 대한 모든 권한을 가지며 자격 증명 수명 주기 관리를 완벽하게 제어할 수 있음을 의미합니다.

![관리](./media/azure-security-encryption-atrest/azure-security-encryption-atrest-fig4.png)

따라서 서비스 관리 키를 사용하는 서버 쪽 암호화는 고객에게 낮은 오버헤드로 저장 데이터 암호화를 유지해야 하는 요구 사항을 신속하게 해결합니다. 가능한 경우 고객은 일반적으로 대상 구독 및 리소스 공급자에 대한 Azure Portal을 열고 데이터 암호화를 원하는지를 나타내는 상자를 선택합니다. 일부 리소스 관리자에서는 기본적으로 서비스 관리 키를 사용하는 서버 쪽 암호화가 설정되어 있습니다.

Microsoft 관리 키를 사용하는 서버 쪽 암호화는 이 서비스에 키를 저장하고 관리하기 위한 모든 권한이 있음을 의미합니다. 일부 고객은 보안을 더 강화할 수 있다고 생각하기 때문에 키를 관리하려고 할 수 있지만, 이 모델을 평가할 때는 사용자 지정 키 저장소 솔루션과 관련된 비용과 위험을 고려해야 합니다. 대부분의 경우 조직에서는 온-프레미스 솔루션의 리소스 제약 또는 위험이 저장 데이터 암호화 키의 클라우드 관리 위험보다 클 수 있다고 결정할 수 있습니다.  그러나 이 모델은 암호화 키의 생성 또는 수명 주기를 제어하거나 다른 담당자가 서비스를 관리하는 대신 서비스의 암호화 키를 관리하도록(즉, 서비스에 대한 전체 관리 모델에서 키 관리 분리) 요구하는 조직에는 충분하지 않을 수 있습니다.

##### <a name="key-access"></a>키 액세스

서비스 관리 키를 사용하는 서버 쪽 암호화를 사용하면 키 생성, 저장 및 서비스 액세스가 모두 서비스에서 관리됩니다. 일반적으로 기본 Azure 리소스 공급자는 키 암호화 키를 안전한 내부 저장소에 저장하는 한편, 데이터에 가까운 저장소에 데이터 암호화 키를 저장하여 빠르게 사용하고 액세스할 수 있습니다.

**장점**

- 간단한 설정
- Microsoft에서 키 회전, 백업 및 중복성을 관리합니다.
- 고객은 구현 또는 사용자 지정 키 관리 체계의 위험과 관련된 비용을 부담하지 않습니다.

**단점**

- 고객이 암호화 키(키 사양, 수명 주기, 해지 등)를 제어할 수 없습니다.
- 서비스에 대한 전체 관리 모델에서 키 관리를 분리할 수 없습니다.

#### <a name="server-side-encryption-using-customer-managed-keys-in-azure-key-vault"></a>Azure Key Vault에서 고객 관리 키를 사용하여 서버 쪽 암호화 

미사용 데이터를 암호화하고 암호화 키를 제어해야 하는 요구 사항이 있는 시나리오의 경우 고객은 Key Vault에서 고객 관리 키를 사용하여 서버 쪽 암호화를 사용할 수 있습니다. 일부 서비스는 Azure Key Vault에 루트 키 암호화 키만 저장하고, 데이터에 가까운 내부 위치에는 암호화된 데이터 암호화 키를 저장할 수 있습니다. 이 시나리오에서는 고객이 자신의 키를 Key Vault(BYOK(Bring Your Own Key))로 가져오거나 새 키를 생성하여 원하는 리소스를 암호화하는 데 사용할 수 있습니다. 리소스 공급자에서 암호화 및 암호 해독 작업을 수행하는 동안 구성된 키를 모든 암호화 작업에 대한 루트 키로 사용합니다. 

##### <a name="key-access"></a>키 액세스

Azure Key Vault에서 고객 관리 키를 사용하는 서버 쪽 암호화 모델에는 필요에 따라 키를 액세스하여 암호화하고 암호 해독하는 서비스가 포함되어 있습니다. 미사용 데이터 암호화는 액세스 제어 정책을 통해 해당 서비스에 제공됩니다. 이 정책은 키를 받을 수 있는 서비스 ID 액세스 권한을 부여합니다. 연결된 구독을 대신하여 실행되는 Azure 서비스는 해당 구독의 ID를 사용하여 구성할 수 있습니다. 이 서비스는 Azure Active Directory 인증을 수행하고, 구독을 대신하여 작동하는 해당 서비스로 식별할 수 있는 인증 토큰을 받습니다. 그러면 해당 토큰을 Key Vault에 제공하여 액세스 권한이 부여된 키를 얻을 수 있습니다.

암호화 키를 사용하는 작업의 경우 암호 해독, 암호화, unwrapKey, wrapKey, 확인, 서명, 가져오기(get), 목록, 업데이트, 만들기, 가져오기(import), 삭제, 백업 및 복원과 같은 작업에 대한 액세스 권한을 서비스 ID에 부여할 수 있습니다.

미사용 데이터를 암호화하거나 암호 해독하는 데 사용할 키를 얻으려면, 리소스 관리자 서비스 인스턴스가 실행될 서비스 ID에 UnwrapKey(암호 해독 키를 가져옴) 및 WrapKey(새 키를 만들 때 키 자격 증명 모음에 키를 삽입함)가 있어야 합니다.


>[!NOTE] 
>Key Vault 권한 부여에 대한 자세한 내용은 [Azure Key Vault 설명서](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault)의 키 자격 증명 모음 보호 페이지를 참조하세요. 

**장점**

- 사용된 키에 대한 완전한 제어 - 암호화 키는 고객의 Key Vault에서 고객의 통제 하에 관리됩니다.
- 하나의 마스터에 여러 서비스를 암호화하는 기능
- 서비스에 대한 전체 관리 모델에서 키 관리를 분리할 수 있습니다.
- 지역 간 서비스 및 키 위치를 정의할 수 있습니다.

**단점**

- 키 액세스 관리에 대한 책임이 전적으로 고객에게 있습니다.
- 키 수명 주기 관리에 대한 책임이 전적으로 고객에게 있습니다.
- 추가 설치 및 구성 오버헤드

#### <a name="server-side-encryption-using-service-managed-keys-in-customer-controlled-hardware"></a>고객 제어 하드웨어에서 서비스 관리 키를 사용하여 서버 쪽 암호화

일부 Azure 서비스는 HYOK(Host Your Own Key) 키 관리 모델을 사용할 수 있습니다. 이 관리 모드는 미사용 데이터를 암호화하고 Microsoft의 제어를 벗어나는 전용 리포지토리의 키를 관리해야 하는 시나리오에서 유용합니다. 이 모델에서 서비스는 외부 사이트에서 키를 검색해야 합니다. 성능 및 가용성 보장은 영향을 받으며, 구성은 더 복잡합니다. 또한 암호화 및 암호 해독 작업 중에 DEK에 대한 액세스 권한이 서비스에 있으므로 이 모델에서는 고객이 Azure Key Vault에서 키를 관리하는 경우와 비슷한 방식으로 전반적인 보안을 보장합니다.  따라서 특정 키 관리 요구 사항이 필요하지 않는 한 대부분의 조직에는 이 모델이 적합하지 않습니다. 이러한 제한으로 인해 대부분의 Azure 서비스에서는 고객이 제어하는 하드웨어에서 서버 관리 키를 사용하는 서버 쪽 암호화를 지원하지 않습니다.

##### <a name="key-access"></a>키 액세스

고객 제어 하드웨어에서 서비스 관리 키를 사용하는 서버 쪽 암호화가 사용되는 경우 키는 고객이 구성한 시스템에서 유지됩니다. 이 모델을 지원하는 Azure 서비스에서는 고객이 제공한 키 저장소에 보안 연결을 설정하는 수단을 제공합니다.

**장점**

- 루트 키에 대한 모든 권한 사용 - 암호화 키는 고객이 제공한 저장소에서 관리됩니다.
- 하나의 마스터에 여러 서비스를 암호화하는 기능
- 서비스에 대한 전체 관리 모델에서 키 관리를 분리할 수 있습니다.
- 지역 간 서비스 및 키 위치를 정의할 수 있습니다.

**단점**

- 키 저장소, 보안, 성능 및 가용성에 대한 전적인 책임
- 키 액세스 관리에 대한 전적인 책임
- 키 수명 주기 관리에 대한 전적인 책임
- 중요한 설정, 구성 및 지속적인 유지 관리 비용
- 고객 데이터 센터와 Azure 데이터 센터 간의 네트워크 가용성에 대한 종속성 증가

## <a name="encryption-at-rest-in-microsoft-cloud-services"></a>Microsoft Cloud 서비스 내 미사용 데이터 암호화

Microsoft 클라우드 서비스는 다음 세 가지 클라우드 모델에서 사용됩니다. IaaS, PaaS, SaaS 각 모델에서 작동하는 방식에 대한 예는 다음과 같습니다.

- Office 365와 같이 클라우드에서 제공하는 애플리케이션이 있는 SaaS(Software as a Server)라는 소프트웨어 서비스
- 고객이 자신의 애플리케이션에서 클라우드를 활용하고 저장소, 분석 및 서비스 버스 기능과 같은 용도로 클라우드를 사용하는 플랫폼 서비스
- 고객이 클라우드에 호스팅되어 다른 클라우드 서비스를 활용할 수 있는 운영 체제 및 애플리케이션을 배포하는 인프라 서비스 또는 IaaS(Infrastructure as a Service)

### <a name="encryption-at-rest-for-saas-customers"></a>SaaS 고객에 대한 미사용 데이터 암호화

SaaS(Software as a Service) 고객은 일반적으로 각 서비스에서 미사용 데이터 암호화를 사용하도록 설정하거나 사용할 수 있습니다. Office 365에는 고객이 미사용 데이터 암호화를 확인하거나 사용하도록 설정할 수 있는 몇 가지 옵션이 있습니다. Office 365 서비스에 대한 자세한 내용은 [Office 365의 암호화](https://docs.microsoft.com/office365/securitycompliance/encryption)를 참조하세요.

### <a name="encryption-at-rest-for-paas-customers"></a>PaaS 고객에 대한 미사용 데이터 암호화

PaaS(Platform as a Service) 고객의 데이터는 일반적으로 애플리케이션 실행 환경 및 고객 데이터 저장에 사용되는 모든 Azure 리소스 공급자에 상주합니다. 저장 데이터 암호화 옵션을 사용할 수 있는지 확인하려면 아래 표에서 사용할 수 있는 저장소 및 응용 프로그램 플랫폼을 살펴보세요. 지원되는 경우 미사용 데이터 암호화를 사용하도록 설정하는 지침에 대한 리소스 공급자별 링크가 제공됩니다. 

### <a name="encryption-at-rest-for-iaas-customers"></a>IaaS 고객에 대한 미사용 데이터 암호화

IaaS(Infrastructure as a Service) 고객은 다양한 서비스와 애플리케이션을 사용할 수 있습니다. IaaS 서비스를 사용하면 Azure Disk Encryption을 사용하여 Azure 호스팅 가상 머신 및 VHD에서 미사용 데이터 암호화를 사용할 수 있습니다. 

#### <a name="encrypted-storage"></a>암호화된 저장소

PaaS와 마찬가지로 IaaS 솔루션은 암호화된 미사용 데이터를 저장하는 다른 Azure 서비스를 활용할 수 있습니다. 이 경우 사용된 각 Azure 서비스에서 제공하는 대로 미사용 데이터 암호화 지원을 사용하도록 설정할 수 있습니다. 아래 표에서는 주 저장소, 서비스 및 애플리케이션 플랫폼과 지원되는 미사용 데이터 암호화 모델을 나열합니다. 지원되는 경우 미사용 데이터 암호화를 사용하도록 설정하는 지침에 대한 링크가 제공됩니다. 

#### <a name="encrypted-compute"></a>암호화된 계산

미사용 데이터 암호화 솔루션을 완전하게 사용하려면 데이터가 암호화된 형식으로 저장되어야 합니다. 사용하고 있는 동안 메모리에 있는 데이터를 로드하는 서버에서 다양한 방법(예: Windows 페이지 파일, 크래시 덤프, 애플리케이션에서 수행할 수 있는 로깅 등)으로 데이터를 로컬에 유지할 수 있습니다. 이러한 미사용 데이터가 암호화되어 있는지 확인하려면 IaaS 응용 프로그램이 Azure IaaS 가상 머신(Windows 또는 Linux) 및 가상 디스크에서 Azure Disk Encryption을 사용할 수 있습니다. 

#### <a name="custom-encryption-at-rest"></a>사용자 지정 미사용 데이터 암호화

가능할 때마다 사용된 모든 Azure 서비스에서 제공하는 Azure Disk Encryption 및 미사용 데이터 암호화 옵션을 IaaS 애플리케이션에서 활용하는 것이 좋습니다. 불규칙한 암호화 요구 사항 또는 Azure 기반이 아닌 저장소와 같은 어떤 경우에는 IaaS 애플리케이션 개발자가 자체적으로 미사용 데이터 암호화를 구현해야 할 수도 있습니다. IaaS 솔루션 개발자는 특정 Azure 구성 요소를 활용하여 Azure 관리 및 고객 기대와 더 잘 통합될 수 있습니다. 특히 개발자는 Azure Key Vault 서비스를 사용하여 안전한 키 저장소를 제공할 뿐만 아니라 대부분의 Azure플랫폼 서비스에 있는 옵션과 함께 일관된 키 관리 옵션도 고객에게 제공해야 합니다. 또한 사용자 지정 솔루션은 Azure Managed Service Identities를 사용하여 서비스 계정에서 암호화 키에 액세스할 수 있도록 설정해야 합니다. Azure Key Vault 및 Managed Service Identities에 대한 개발자 정보는 해당 SDK를 참조하세요.

## <a name="azure-resource-providers-encryption-model-support"></a>Azure 리소스 공급자 암호화 모델 지원

Microsoft Azure 서비스마다 하나 이상의 미사용 데이터 암호화 모델을 지원합니다. 그러나 일부 서비스의 경우 하나 이상의 암호화 모델이 적용되지 않을 수 있습니다. 고객이 관리하는 키 시나리오를 지원하는 서비스의 경우, Azure Key Vault가 주요 암호화 키를 지원하는 키 유형의 하위 집합만 지원할 수 있습니다. 또한 서비스가 서로 다른 일정으로 이러한 시나리오 및 키 형식에 대한 지원을 해제할 수 있습니다. 이 섹션에서는 이 문서를 작성할 당시의 주요 Azure 데이터 저장소 서비스 각각에 대한 미사용 데이터 암호화에 대해 설명하고 있습니다.

### <a name="azure-disk-encryption"></a>Azure Disk Encryption

Azure IaaS(Infrastructure as a Service) 기능을 사용하는 모든 고객은 Azure Disk Encryption을 통해 IaaS VM 및 디스크에 대한 미사용 데이터 암호화를 달성할 수 있습니다. Azure Disk Encryption에 대한 자세한 내용은 [Azure Disk Encryption 설명서](https://docs.microsoft.com/azure/security/azure-security-disk-encryption)를 참조하세요.

#### <a name="azure-storage"></a>Azure 저장소

모든 Azure Storage 서비스(Blob Storage, Queue Storage, Table Storage 및 Azure Files)는 고객 관리 키와 클라이언트 측 암호화를 지원하는 일부 서비스와 함께 서버 쪽 정적 암호화를 지원합니다.  

- 서버 측: 모든 Azure Storage 서비스는 기본적으로 애플리케이션에 투명한 서비스 관리 키를 사용하여 서버 측 암호화를 활성화합니다. 자세한 내용은 [미사용 데이터에 대한 Azure Storage 서비스 암호화](https://docs.microsoft.com/azure/storage/storage-service-encryption)를 참조하세요. Azure Blob Storage 및 Azure Files도 Azure Key Vault의 RSA 2048비트 고객 관리 키를 지원합니다. 자세한 내용은 [Azure Key Vault의 고객 관리 키를 사용하는 Storage 서비스 암호화](https://docs.microsoft.com/azure/storage/common/storage-service-encryption-customer-managed-keys)를 참조하세요.
- 클라이언트 측: Azure Blob, Table 및 Queue에서 클라이언트 측 암호화를 지원합니다. 클라이언트 쪽 암호화를 사용하는 경우 고객은 데이터를 암호화하고 암호화된 Blob으로 데이터를 업로드합니다. 키 관리는 고객이 수행합니다. 자세한 내용은 [Microsoft Azure Storage용 클라이언트 쪽 암호화 및 Azure Key Vault](https://docs.microsoft.com/azure/storage/storage-client-side-encryption)를 참조하세요.


#### <a name="azure-sql-database"></a>Azure SQL Database

Azure SQL Database는 현재 Microsoft 관리 서비스 쪽 및 클라이언트 쪽 암호화 시나리오에 대한 저장 데이터 암호화를 지원합니다.

서버 암호화 지원은 현재 투명한 데이터 암호화라는 SQL 기능을 통해 제공됩니다. Azure SQL Database 고객이 TDE 키를 사용하도록 설정하면 해당 키가 자동으로 만들어지고 관리됩니다. 데이터베이스 및 서버 수준에서 미사용 데이터 암호화를 사용하도록 설정할 수 있습니다. 2017년 6월 현재 [TDE(투명한 데이터 암호화)](https://msdn.microsoft.com/library/bb934049.aspx)는 새로 만든 데이터베이스에서 기본적으로 사용하도록 설정되어 있습니다. Azure SQL Database는 Azure Key Vault의 RSA 2048비트 고객 관리 키를 지원합니다. 자세한 내용은 [Azure SQL Database 및 데이터 웨어하우스에 대한 BYOK(Bring Your Own Key) 지원을 사용한 투명한 데이터 암호화](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-byok-azure-sql?view=azuresqldb-current)를 참조하세요.

Azure SQL Database 데이터의 클라이언트 쪽 암호화는 [Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx) 기능을 통해 지원됩니다. Always Encrypted는 클라이언트에서 만들고 저장한 키를 사용합니다. 고객은 Windows 인증서 저장소, Azure Key Vault 또는 로컬 HSM(하드웨어 보안 모듈)에 마스터 키를 저장할 수 있습니다. SQL Server Management Studio를 사용하는 경우 SQL 사용자는 어떤 열을 암호화하는 데 사용하려는 키를 선택합니다.

|                                  |                    | **암호화 모델 및 키 관리** |                    |
|----------------------------------|--------------------|-----------------------------------------|--------------------|
|                                  | **서비스 관리 키를 사용하는 서버 쪽**     | **Key Vault에 고객 관리를 사용하는 서버 쪽**             | **클라이언트 관리를 사용하는 클라이언트 쪽**      |
| **저장소 및 데이터베이스**        |                    |                    |                    |
| 디스크(IaaS)                      | -                  | 예, RSA 2048비트  | -                  |
| SQL Server(IaaS)                | 예                | 예, RSA 2048비트  | 예                |
| Azure SQL(Database/Data Warehouse) | 예                | 예, RSA 2048비트  | 예                |
| Azure SQL(Database Managed Instance) | 예                | 미리 보기, RSA 2048비트  | 예                |
| Azure Storage(블록/페이지 Blob) | 예                | 예, RSA 2048비트  | 예                |
| Azure Storage(파일)            | 예                | 예, RSA 2048비트  | -                  |
| Azure Storage(테이블, 큐)   | 예                | -                  | 예                |
| Cosmos DB(Document DB)          | 예                | -                  | -                  |
| StorSimple                       | 예                | -                  | 예                |
| Backup                           | 예                | -                  | 예                |
| **인텔리전스 및 분석**   |                    |                    |                    |
| Azure Data Factory               | 예                | -                  | -                  |
| Azure Machine Learning           | -                  | 미리 보기, RSA 2048비트 | -                  |
| Azure Stream Analytics           | 예                | -                  | -                  |
| HDInsight(Azure Blob Storage)   | 예                | -                  | -                  |
| HDInsight(Data Lake Storage)    | 예                | -                  | -                  |
| HDInsight용 Apache Kafka       | 예                | 미리 보기, 모든 RSA 길이 | -                  |
| Azure Data Lake Store            | 예                | 예, RSA 2048비트  | -                  |
| Azure Data Catalog               | 예                | -                  | -                  |
| Power BI                         | 예                | -                  | -                  |
| **IoT 서비스**                 |                    |                    |                    |
| IoT Hub                          | -                  | -                  | 예                |
| Service Bus                      | 예                | -                  | 예                |
| Event Hubs                       | 예                | -                  | -                  |
| Event Grid                       | 예                | -                  | -                  |


## <a name="conclusion"></a>결론

Azure 서비스에 저장된 고객 데이터의 보호는 Microsoft에 매우 중요합니다. 모든 Azure 호스팅 서비스에서 미사용 데이터 암호화 옵션을 제공하도록 노력하고 있습니다. Azure Storage, Azure SQL Database 및 주요 분석 및 인텔리전스 서비스와 같은 기본 서비스에서는 이미 미사용 데이터 암호화 옵션을 제공하고 있습니다. 이러한 서비스 중 일부는 고객 관리 키와 클라이언트 쪽 암호화뿐만 아니라 서비스 관리 키와 암호화도 지원하고 있습니다. Microsoft Azure 서비스는 미사용 데이터 암호화 가용성을 광범위하게 향상시키고 있으며, 향후 몇 달 동안 미리 보기 및 일반 가용성을 위한 새 옵션을 계획하고 있습니다.
