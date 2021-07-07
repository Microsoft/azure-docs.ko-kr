---
title: Azure Key Vault에서 프라이빗 링크 구성 문제 진단
description: Key Vault에 대 한 일반적인 개인 링크 문제를 해결 하 고 구성에 대해 자세히 알아봅니다.
author: msfcolombo
ms.author: fcolombo
ms.date: 09/30/2020
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.openlocfilehash: 03abe4e4e098d46060e33ba114872905e54a443f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96317065"
---
# <a name="diagnose-private-links-configuration-issues-on-azure-key-vault"></a>Azure Key Vault에서 프라이빗 링크 구성 문제 진단

## <a name="introduction"></a>소개

이 문서는 사용자가 Key Vault 및 개인 링크 기능과 관련 된 문제를 진단 하 고 해결 하는 데 도움이 됩니다. 이 가이드는 개인 링크를 처음으로 작동 하는 것과 같은 구성 측면에서 나 개인 링크가 일부 변경으로 인해 작동을 멈춘 상황을 해결 하는 데 도움이 됩니다.

이 기능을 처음 사용 하는 경우 [Azure 개인 링크와 Key Vault 통합](private-link-service.md)을 참조 하세요.

### <a name="problems-covered-by-this-article"></a>이 문서에서 다루는 문제

- DNS 쿼리는 개인 링크 기능을 사용 하는 것으로 간주 되는 개인 IP 주소 대신 키 자격 증명 모음에 대 한 공용 IP 주소를 계속 반환 합니다.
- 개인 링크를 사용 하는 지정 된 클라이언트에서 수행 하는 모든 요청에는 시간 초과 또는 네트워크 오류로 인해 오류가 발생 하 고 문제가 간헐적으로 발생 하지 않습니다.
- Key vault는 개인 IP 주소를 갖지만 요청은 여전히 `403` 내부 오류 코드와 함께 응답을 받습니다 `ForbiddenByFirewall` .
- 개인 링크를 사용 하 고 있지만 키 자격 증명 모음은 여전히 공용 인터넷의 요청을 수락 합니다.
- 키 자격 증명 모음에는 두 개의 개인 끝점이 있습니다. 하나를 사용 하는 요청은 정상적으로 작동 하지만 다른 요청은 실패 합니다.
- 개인 링크를 사용 하는 다른 구독, 주요 자격 증명 모음 또는 가상 네트워크가 있습니다. 비슷한 배포를 새로 만들려고 하지만 여기에서 작업 하기 위한 개인 링크를 가져올 수 없습니다.

### <a name="problems-not-covered-by-this-article"></a>이 문서에서 다루지 않는 문제

- 간헐적 연결 문제가 있습니다. 지정 된 클라이언트에서 일부 요청이 작동 중이 고 일부는 작동 하지 않는 것을 볼 수 있습니다. *일시적인 문제는 일반적으로 개인 링크 구성의 문제로 인해 발생 하지 않습니다. 네트워크 또는 클라이언트 오버 로드의 부호입니다.*
- BYOK (Bring Your Own Key), CMK (고객 관리 키) 또는 Key vault에 저장 된 암호에 대 한 액세스를 지 원하는 Azure 제품을 사용 하 고 있습니다. Key vault 설정에서 방화벽을 사용 하도록 설정 하면 해당 제품에서 키 자격 증명 모음에 액세스할 수 없습니다. *제품별 설명서를 확인 합니다. 방화벽이 사용 하도록 설정 된 key vault에 대 한 지원이 명시적으로 명시 되어 있는지 확인 합니다. 필요한 경우 해당 특정 제품에 대 한 지원에 문의 하세요.*

### <a name="how-to-read-this-article"></a>이 문서를 읽는 방법

개인 링크를 처음 접하는 경우 나 복잡 한 배포를 평가 하는 경우 전체 문서를 읽는 것이 좋습니다. 그렇지 않으면 사용자가 직면 한 문제에 더 적합 한 섹션을 자유롭게 선택할 수 있습니다.

그럼 시작하겠습니다.

## <a name="1-confirm-that-you-own-the-client-connection"></a>1. 클라이언트 연결을 소유 하 고 있는지 확인 합니다.

### <a name="confirm-that-your-client-runs-at-the-virtual-network"></a>가상 네트워크에서 클라이언트가 실행 되는지 확인

이 가이드는 응용 프로그램 코드에서 발생 하는 key vault에 대 한 연결을 수정 하는 데 도움을 주기 위해 작성 되었습니다. 예제는 Azure Virtual Machines, Azure Service Fabric 클러스터, Azure App Service, Azure Kubernetes Service (AKS) 등에서 실행 되는 응용 프로그램 및 스크립트입니다. 이 가이드는 브라우저에서 키 자격 증명 모음에 직접 액세스 하는 Azure Portal 웹 기반 사용자 인터페이스에서 수행 되는 액세스에도 적용 됩니다.

개인 링크의 정의에 따라 응용 프로그램, 스크립트 또는 포털이 [개인 끝점 리소스가](../../private-link/private-endpoint-overview.md) 배포 된 Virtual Network에 연결 된 컴퓨터, 클러스터 또는 환경에서 실행 되어야 합니다.

응용 프로그램, 스크립트 또는 포털이 임의의 인터넷 연결 네트워크에서 실행 되는 경우이 가이드는 적용 되지 않으며 개인 링크를 사용할 수 없습니다. 이러한 제한은 사용자 브라우저 대신 요청 시 제공 되는 원격 Azure 컴퓨터에서 실행 되기 때문에 Azure Cloud Shell 실행 되는 명령에도 적용 됩니다.

### <a name="if-you-use-a-managed-solution-refer-to-specific-documentation"></a>관리 솔루션을 사용 하는 경우 특정 문서를 참조 하세요.

이 가이드는 Microsoft에서 관리 하는 솔루션에 적용할 수 없습니다. 여기서 주요 자격 증명 모음은 고객 Virtual Network와 독립적으로 존재 하는 Azure 제품에 의해 액세스 됩니다. 이러한 시나리오의 예로는 저장소 데이터 암호화를 위해 구성 된 azure SQL, Azure Event Hub는 고객이 제공한 키로 데이터를 암호화 하 고, key Azure Pipelines vault에 저장 된 서비스 자격 증명에 액세스 Azure Data Factory, key vault에서 비밀을 검색 하는 경우, 기타 유사한 시나리오를 Azure Storage 합니다. 이러한 경우에 *는 방화벽이 사용 하도록 설정 된 주요 자격 증명 모음을 제품에서 지원 하는지 확인 해야* 합니다. 이 지원은 일반적으로 Key Vault 방화벽의 [신뢰할 수 있는 서비스](overview-vnet-service-endpoints.md#trusted-services) 기능을 사용 하 여 수행 됩니다. 그러나 다양 한 이유로 많은 제품이 신뢰할 수 있는 서비스 목록에 포함 되지 않습니다. 이 경우 제품별 지원에 도달 합니다.

적은 수의 Azure 제품은 *vnet 주입* 의 개념을 지원 합니다. 간단히 말해, 제품은 네트워크 장치를 고객 Virtual Network에 추가 하 여가 Virtual Network에 배포 된 것 처럼 요청을 보낼 수 있게 합니다. 주목할 만한 예는 [Azure Databricks](/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject)합니다. 이와 같은 제품은 개인 링크를 사용 하 여 주요 자격 증명 모음에 대 한 요청을 수행할 수 있습니다 .이 문제 해결 가이드는 도움이 될 수 있습니다.

## <a name="2-confirm-that-the-connection-is-approved-and-succeeded"></a>2. 연결이 승인 되었고 성공 했는지 확인 합니다.

다음 단계는 개인 끝점 연결이 승인 되 고 성공 했는지 확인 합니다.

1. Azure Portal를 열고 키 자격 증명 모음 리소스를 엽니다.
2. 왼쪽 메뉴에서 **네트워킹** 을 선택 합니다.
3. **개인 끝점 연결** 탭을 클릭 합니다. 그러면 모든 개인 끝점 연결과 해당 상태가 표시 됩니다. 연결이 없거나 Virtual Network에 대 한 연결이 없는 경우 새 개인 끝점을 만들어야 합니다. 이 내용은 나중에 설명 합니다.
4. 여전히 **개인 끝점 연결** 상태에서 진단 하는 중 하나를 찾고 "연결 상태"가 **승인** 되었으며 "프로 비전 상태"가 **성공** 했는지 확인 합니다.
    - 연결이 "보류 중" 상태인 경우에만 승인할 수 있습니다.
    - 연결이 "거부 됨", "실패", "오류", "연결 끊김" 또는 기타 상태가 아닌 경우에는 새 개인 끝점 리소스를 만들어야 합니다.

작업을 정리 하기 위해 비효율적인 연결을 삭제 하는 것이 좋습니다.

## <a name="3-confirm-that-the-key-vault-firewall-is-properly-configured"></a>3. key vault 방화벽이 제대로 구성 되어 있는지 확인 합니다.

>[!IMPORTANT]
> 방화벽 설정을 변경 하면 여전히 개인 링크를 사용 하지 않는 합법적인 클라이언트에서 액세스를 제거할 수 있습니다. 방화벽 구성의 각 변경 내용에 대 한 의미를 알고 있는지 확인 합니다.

중요 한 개념은 개인 링크 기능이 데이터 exfiltration을 방지 하기 위해 종결 된 Virtual Network에서 키 자격 증명 모음에 대 한 액세스 권한만 *제공* 한다는 것입니다. 기존 액세스는 *제거* 되지 않습니다. 공용 인터넷에서 액세스를 효과적으로 차단 하려면 키 자격 증명 모음 방화벽을 명시적으로 사용 하도록 설정 해야 합니다.

1. Azure Portal를 열고 키 자격 증명 모음 리소스를 엽니다.
2. 왼쪽 메뉴에서 **네트워킹** 을 선택 합니다.
3. 위에서 **방화벽 및 가상 네트워크** 탭이 선택 되어 있는지 확인 합니다.
4. **개인 끝점 및 선택한 네트워크** 옵션이 선택 되어 있는지 확인 합니다. **모든 네트워크** 를 검색 하는 경우 외부 클라이언트가 여전히 키 자격 증명 모음에 액세스할 수 있는 이유를 설명 하는를 선택 합니다.

다음 문은 방화벽 설정에도 적용 됩니다.

- 개인 링크 기능에는 키 자격 증명 모음 방화벽 설정에서 "가상 네트워크"를 지정할 필요가 없습니다. 키 자격 증명 모음 방화벽 설정에 가상 네트워크가 지정 되지 않은 경우에도 key vault의 개인 IP 주소를 사용 하는 모든 요청 (다음 섹션 참조)이 작동 해야 합니다.
- 개인 링크 기능에서는 키 자격 증명 모음 방화벽 설정에 IP 주소를 지정할 필요가 없습니다. 키 자격 증명 모음의 개인 IP 주소를 사용 하는 모든 요청은 방화벽 설정에서 IP 주소가 지정 되지 않은 경우에도 작동 해야 합니다.

개인 링크를 사용 하는 경우 key vault 방화벽에서 가상 네트워크 또는 IP 주소를 지정 하는 유일한 동기는 다음과 같습니다.

- 일부 클라이언트는 개인 링크를 사용 하 고 일부는 서비스 끝점을 사용 하 고 일부는 공용 IP 주소를 사용 하는 하이브리드 시스템이 있습니다.
- 개인 링크로 전환 하 고 있습니다. 이 경우 모든 클라이언트에서 개인 링크를 사용 하 고 있는지 확인 한 후에는 key vault 방화벽 설정에서 가상 네트워크 및 IP 주소를 제거 해야 합니다.

## <a name="4-make-sure-the-key-vault-has-a-private-ip-address"></a>4. key vault에 개인 IP 주소가 있는지 확인 합니다.

### <a name="difference-between-private-and-public-ip-addresses"></a>개인 및 공용 IP 주소 간의 차이

개인 IP 주소는 항상 다음 형식 중 하나입니다.

- 10. x. x. x: 예: `10.1.2.3` , `10.56.34.12` .
- 172.16. x. x. x. x. x: 예: `172.20.1.1` , `172.31.67.89` .
- 192.168. x. x: `192.168.0.1` , `192.168.100.7`

특정 IP 주소 및 범위는 예약 되어 있습니다.

- 224. x. x: 멀티 캐스트
- 255.255.255.255: 브로드캐스트
- 127. x. x. x: 루프백
- 169.254: 링크-로컬
- 168.63.129.16: 내부 DNS

다른 모든 IP 주소는 공용입니다.

포털을 찾아보거나 IP 주소를 표시 하는 명령을 실행 하는 경우 IP 주소가 개인, 공용 또는 예약 인지를 식별할 수 있는지 확인 합니다. 개인 링크가 작동 하려면 키 자격 증명 모음 호스트 이름이 Virtual Network 주소 공간에 속하는 개인 IP 주소로 확인 되어야 합니다.

### <a name="find-the-key-vault-private-ip-address-in-the-virtual-network"></a>가상 네트워크에서 키 자격 증명 모음 개인 IP 주소 찾기

호스트 이름 확인을 진단 해야 하며, 개인 링크가 사용 하도록 설정 된 key vault의 정확한 개인 IP 주소를 알아야 합니다. 해당 주소를 찾으려면 다음 절차를 따르세요.

1. Azure Portal를 열고 키 자격 증명 모음 리소스를 엽니다.
2. 왼쪽 메뉴에서 **네트워킹** 을 선택 합니다.
3. **개인 끝점 연결** 탭을 클릭 합니다. 그러면 모든 개인 끝점 연결과 해당 상태가 표시 됩니다.
4. 진단 하 고 있는 항목을 찾아 "연결 상태"가 **승인** 되었고 프로 비전 상태가 **성공** 인지 확인 합니다. 이 내용이 표시 되지 않으면이 문서의 이전 섹션으로 돌아갑니다.
5. 올바른 항목을 찾으면 **개인 끝점** 열의 링크를 클릭 합니다. 이렇게 하면 개인 끝점 리소스가 열립니다.
6. 개요 페이지에는 **사용자 지정 DNS 설정** 이라는 섹션이 표시 될 수 있습니다. 키 자격 증명 모음 호스트 이름과 일치 하는 항목이 하나만 있는지 확인 합니다. 이 항목은 키 자격 증명 모음 개인 IP 주소를 표시 합니다.
7. **네트워크 인터페이스** 에서 링크를 클릭 하 고 개인 IP 주소가 이전 단계에 표시 된 것과 동일한 지 확인할 수도 있습니다. 네트워크 인터페이스는 키 자격 증명 모음을 나타내는 가상 장치입니다.

IP 주소는 Vm 및 *동일한 Virtual Network에서 실행* 되는 다른 장치에서 키 자격 증명 모음에 연결 하는 데 사용 합니다. 추가 조사를 수행 하는 동안 IP 주소를 기록 하거나 브라우저 탭을 열어 둡니다.

>[!NOTE]
> 키 자격 증명 모음에 여러 개인 끝점이 있는 경우 개인 IP 주소를 여러 개 포함 합니다. 이는 각각 고유한 개인 끝점을 통해 동일한 키 자격 증명 모음에 액세스 하는 여러 가상 네트워크가 있는 경우에만 유용 합니다 (개인 끝점이 단일 Virtual Network에 속함). 올바른 Virtual Network에 대 한 문제를 진단 하 고 위의 절차에서 올바른 개인 끝점 연결을 선택 했는지 확인 합니다. 또한 동일한 Virtual Network 동일한 Key Vault에 대해 여러 개인 끝점을 만들지 **마십시오** . 이는 필요 하지 않으며 혼동의 원인입니다.

## <a name="5-validate-the-dns-resolution"></a>5. DNS 확인의 유효성을 검사 합니다.

DNS 확인은 주요 자격 증명 모음 호스트 이름 (예:)을 `fabrikam.vault.azure.net` IP 주소 (예:)로 변환 하는 프로세스입니다 `10.1.2.3` . 다음 하위 섹션에서는 각 시나리오에서 DNS 확인의 예상 결과를 보여 줍니다.

### <a name="key-vaults-without-private-link"></a>개인 링크 없는 키 자격 증명 모음

이 섹션은 학습 목적으로 작성 되었습니다. 키 자격 증명 모음에 승인 된 상태의 개인 끝점 연결이 없는 경우 호스트 이름을 확인 하면 다음과 유사한 결과를 제공 합니다.

Windows:

```console
C:\> nslookup fabrikam.vault.azure.net
```

```output
Non-authoritative answer:
Address:  52.168.109.101
Aliases:  fabrikam.vault.azure.net
          data-prod-eus.vaultcore.azure.net
          data-prod-eus-region.vaultcore.azure.net
```

Linux:

```console
joe@MyUbuntu:~$ host fabrikam.vault.azure.net
```

```output
fabrikam.vault.azure.net is an alias for data-prod-eus.vaultcore.azure.net.
data-prod-eus.vaultcore.azure.net is an alias for data-prod-eus-region.vaultcore.azure.net.
data-prod-eus-region.vaultcore.azure.net has address 52.168.109.101
```

이름이 공용 IP 주소로 확인 되 고 별칭이 없는 것을 볼 수 있습니다 `privatelink` . 별칭은 나중에 설명 합니다. 지금은 걱정 하지 마세요.

위의 결과는 컴퓨터에 연결 되어 있거나 인터넷에 연결 되어 있는 임의의 컴퓨터에 Virtual Network에 관계 없이 예상 됩니다. 주요 자격 증명 모음에는 승인 된 상태에서 개인 끝점 연결이 없기 때문에 발생 하므로 key vault에서 개인 링크를 지원할 필요가 없습니다.

### <a name="key-vault-with-private-link-resolving-from-arbitrary-internet-machine"></a>임의의 인터넷 컴퓨터에서 확인 되는 개인 링크가 있는 Key vault

키 자격 증명 모음에 하나 이상의 개인 끝점 연결이 승인 된 상태에 있고 인터넷에 연결 된 임의의 컴퓨터에서 호스트 이름을 확인 하는 경우 (개인 끝점이 있는 Virtual Network에 연결 *되지* 않은 컴퓨터) 다음을 찾을 수 있습니다.

Windows:

```console
C:\> nslookup fabrikam.vault.azure.net
```

```output
Non-authoritative answer:
Address:  52.168.109.101
Aliases:  fabrikam.vault.azure.net
          fabrikam.privatelink.vaultcore.azure.net
          data-prod-eus.vaultcore.azure.net
          data-prod-eus-region.vaultcore.azure.net
```

Linux:

```console
joe@MyUbuntu:~$ host fabrikam.vault.azure.net
```

```output
fabrikam.vault.azure.net is an alias for fabrikam.privatelink.vaultcore.azure.net.
fabrikam.privatelink.vaultcore.azure.net is an alias for data-prod-eus.vaultcore.azure.net.
data-prod-eus.vaultcore.azure.net is an alias for data-prod-eus-region.vaultcore.azure.net.
data-prod-eus-region.vaultcore.azure.net has address 52.168.109.101
```

이전 시나리오에서 주목할 만한 차이점은 값이 포함 된 새 별칭이 있다는 것입니다 `{vaultname}.privatelink.vaultcore.azure.net` . 즉, key vault 데이터 평면이 개인 링크의 요청을 받을 준비가 된 것입니다.

Virtual Network *외부* 의 컴퓨터에서 수행 된 요청 (예: 방금 사용한 요청)은 개인 링크를 사용 하는 것을 의미 하지 않습니다. 호스트 이름이 여전히 공용 IP 주소로 확인 된다는 사실을 알 수 있습니다. *Virtual Network에 연결* 된 컴퓨터 에서만 개인 링크를 사용할 수 있습니다. 이에 대 한 자세한 내용은 다음을 참조 하세요.

별칭이 표시 되지 않으면 `privatelink` 키 자격 증명 모음에 상태에서 0 개인 끝점 연결이 있음을 의미 `Approved` 합니다. 다시 시도 하기 전에 [이 섹션](#2-confirm-that-the-connection-is-approved-and-succeeded) 으로 돌아갑니다.

### <a name="key-vault-with-private-link-resolving-from-virtual-network"></a>Virtual Network에서 확인 되는 개인 링크가 있는 Key vault

키 자격 증명 모음이 승인 된 상태에 있는 하나 이상의 개인 끝점 연결을 포함 하 고 개인 끝점이 생성 된 Virtual Network에 연결 된 컴퓨터에서 호스트 이름을 확인 하는 경우 예상 되는 응답은 다음과 같습니다.

Windows:

```console
C:\> nslookup fabrikam.vault.azure.net
```

```output
Non-authoritative answer:
Address:  10.1.2.3
Aliases:  fabrikam.vault.azure.net
          fabrikam.privatelink.vaultcore.azure.net
```

Linux:

```console
joe@MyUbuntu:~$ host fabrikam.vault.azure.net
```

```output
fabrikam.vault.azure.net is an alias for fabrikam.privatelink.vaultcore.azure.net.
fabrikam.privatelink.vaultcore.azure.net has address 10.1.2.3
```

두 가지 주목할 만한 차이점이 있습니다. 먼저 이름이 개인 IP 주소로 확인 됩니다. 이는이 문서의 [해당 섹션](#find-the-key-vault-private-ip-address-in-the-virtual-network) 에서 찾을 수 있는 IP 주소 여야 합니다. 둘째, 그 뒤에 다른 별칭이 없습니다 `privatelink` . 이는 Virtual Network DNS 서버가 별칭 체인을 *가로채서* 이름에서 직접 개인 IP 주소를 반환 하기 때문에 발생 합니다 `fabrikam.privatelink.vaultcore.azure.net` . 이 항목은 실제로 `A` 사설 DNS 영역에 있는 레코드입니다. 이에 대 한 자세한 내용은 다음을 참조 하세요.

>[!NOTE]
> 위의 결과는 개인 끝점이 생성 된 Virtual Network에 연결 된 가상 컴퓨터 에서만 발생 합니다. 개인 끝점을 포함 하는 Virtual Network에 배포 된 VM이 없는 경우에는 VM을 배포 하 고 원격으로 연결한 다음 명령을 실행 합니다 `nslookup` (Windows) 또는 `host` 위의 명령 (Linux).

개인 끝점이 생성 된 Virtual Network에 연결 된 가상 컴퓨터에서 이러한 명령을 실행 하 고 키 자격 증명 모음 개인 IP 주소를 표시 **하지 않는** 경우 다음 섹션에서 문제를 해결 하는 데 도움이 될 수 있습니다.

## <a name="6-validate-the-private-dns-zone"></a>6. 사설 DNS 영역 유효성 검사

이전 섹션에 설명 된 대로 DNS 확인이 작동 하지 않는 경우 사설 DNS 영역에 문제가 있을 수 있으며이 섹션에서 도움이 될 수 있습니다. DNS 확인에 올바른 키 자격 증명 모음 개인 IP 주소가 표시 되 면 사설 DNS 영역이 올바른 것일 수 있습니다. 이 전체 섹션을 건너뛸 수 있습니다.

### <a name="confirm-that-the-required-private-dns-zone-resource-exists"></a>필요한 사설 DNS 영역 리소스가 있는지 확인 합니다.

Azure 구독에는 정확한 이름을 가진 [사설 DNS 영역](../../dns/private-dns-privatednszone.md) 리소스가 있어야 합니다.

`privatelink.vaultcore.azure.net`

포털의 구독 페이지로 이동 하 고 왼쪽 메뉴에서 "리소스"를 선택 하 여이 리소스가 있는지 확인할 수 있습니다. 리소스 이름은 여야 `privatelink.vaultcore.azure.net` 하 고 리소스 형식은 **사설 DNS 영역** 이어야 합니다.

일반적으로이 리소스는 일반 프로시저를 사용 하 여 개인 끝점을 만들 때 자동으로 만들어집니다. 그러나이 리소스가 자동으로 생성 되지 않으며 수동으로 수행 해야 하는 경우도 있습니다. 이 리소스는 실수로 삭제 되었을 수도 있습니다.

이 리소스가 없으면 구독에 새 사설 DNS 영역 리소스를 만듭니다. 이름은 `privatelink.vaultcore.azure.net` 공백이 나 추가 점이 없는 정확 하 게 사용 해야 합니다. 잘못 된 이름을 지정 하는 경우이 문서에서 설명 하는 이름 확인이 작동 하지 않습니다. 이 리소스를 만드는 방법에 대 한 자세한 내용은 [Azure Portal를 사용 하 여 Azure 개인 DNS 영역 만들기](../../dns/private-dns-getstarted-portal.md)를 참조 하세요. 해당 페이지를 따르는 경우이 시점에서 Virtual Network 만들기를 건너뛸 수 있습니다. Virtual Machines를 사용 하 여 유효성 검사 프로시저를 건너뛸 수도 있습니다.

### <a name="confirm-that-the-private-dns-zone-is-linked-to-the-virtual-network"></a>사설 DNS 영역이 Virtual Network에 연결 되어 있는지 확인 합니다.

사설 DNS 영역이 충분 하지 않습니다. 또한 개인 끝점을 포함 하는 Virtual Network에 연결 되어 있어야 합니다. 사설 DNS 영역이 올바른 Virtual Network에 연결 되지 않은 경우 해당 Virtual Network의 DNS 확인은 사설 DNS 영역을 무시 합니다.

사설 DNS 영역 리소스를 열고 왼쪽 메뉴에서 **가상 네트워크 연결** 옵션을 클릭 합니다. 그러면 링크 목록이 표시 되 고 각 링크에는 구독의 Virtual Network 이름이 표시 됩니다. 개인 끝점 리소스를 포함 하는 Virtual Network 여기에 나열 되어야 합니다. 나열되지 않으면 추가합니다. 자세한 단계는 [가상 네트워크 연결](../../dns/private-dns-getstarted-portal.md#link-the-virtual-network)을 참조 하세요. "자동 등록 사용"을 선택 하지 않은 상태로 둘 수 있습니다 .이 기능은 개인 링크와 관련이 없습니다.

사설 DNS 영역이 Virtual Network에 연결 되 면 Virtual Network에서 시작 된 DNS 요청은 사설 DNS 영역에서 이름을 찾습니다. 이는 개인 끝점이 생성 된 Virtual Network에 연결 된 Virtual Machines에서 수행 되는 올바른 주소 확인에 필요 합니다.

>[!NOTE]
> 방금 링크를 저장 한 경우 포털에서 작업이 완료 된 후에도이 작업이 적용 되는 데 다소 시간이 걸릴 수 있습니다. DNS 확인을 테스트 하는 데 사용 하는 VM을 다시 부팅 해야 할 수도 있습니다.

### <a name="confirm-that-the-private-dns-zone-contains-the-right-a-record"></a>사설 DNS 영역에 올바른 A 레코드가 포함 되어 있는지 확인 합니다.

포털을 사용 하 여 이름이 인 사설 DNS 영역을 엽니다 `privatelink.vaultcore.azure.net` . 개요 페이지에는 모든 레코드가 표시 됩니다. 기본적으로 이름 및 형식이 있는 레코드 하나 `@` `SOA` (권한 시작)가 있습니다. 그거 만지지 마세요.

키 자격 증명 모음 이름 확인이 작동 하려면 `A` 접미사가 나 점이 없는 간단한 자격 증명 모음 이름을 가진 레코드가 있어야 합니다. 예를 들어 호스트 이름이 인 경우 `fabrikam.vault.azure.net` `A` `fabrikam` 접미사가 나 점 없이 이름이 있는 레코드가 있어야 합니다.

또한 `A` 레코드 값 (IP 주소)은 [키 자격 증명 모음 개인 IP 주소](#find-the-key-vault-private-ip-address-in-the-virtual-network)여야 합니다. `A`레코드를 찾았지만 잘못 된 ip 주소를 포함 하는 경우 잘못 된 ip 주소를 제거 하 고 새 주소를 추가 해야 합니다. 전체 레코드를 제거 하 `A` 고 새 레코드를 추가 하는 것이 좋습니다.

>[!NOTE]
> 레코드를 제거 하거나 수정할 때마다 `A` TTL (Time To Live) 값이 아직 만료 되지 않았기 때문에 컴퓨터가 여전히 이전 IP 주소로 확인 될 수 있습니다. TTL 값은 항상 60 초 (1 분) 보다 작은 값으로 지정 하 고 600 초 (10 분) 보다 크게 지정 하는 것이 좋습니다. 너무 긴 값을 지정 하는 경우 클라이언트는 가동 중단에서 복구 하는 데 너무 오래 걸릴 수 있습니다.

### <a name="dns-resolution-for-more-than-one-virtual-network"></a>두 개 이상의 Virtual Network에 대 한 DNS 확인

여러 가상 네트워크가 있고 각 네트워크에 동일한 키 자격 증명 모음을 참조 하는 고유한 개인 끝점 리소스가 있는 경우 키 자격 증명 모음 호스트 이름은 네트워크에 따라 다른 개인 IP 주소로 확인 되어야 합니다. 즉, 서로 다른 Virtual Network에 연결 되 고 레코드의 다른 IP 주소를 사용 하 여 여러 사설 DNS 영역도 필요 `A` 합니다.

고급 시나리오에서는 가상 네트워크에서 피어 링을 사용 하도록 설정할 수 있습니다. 이 경우에는 하나의 Virtual Network 전용 끝점 리소스가 필요 하지만 둘 다 사설 DNS 영역 리소스에 연결 해야 할 수 있습니다. 이 시나리오는이 문서에서 직접 다루지 않습니다.

### <a name="understand-that-you-have-control-over-dns-resolution"></a>DNS 확인을 제어할 수 있음을 이해 합니다.

[이전 섹션](#key-vault-with-private-link-resolving-from-arbitrary-internet-machine)에서 설명한 것 처럼 개인 링크를 포함 하는 주요 자격 증명 모음에는 `{vaultname}.privatelink.vaultcore.azure.net` *공용* 등록에 별칭이 있습니다. Virtual Network에서 사용 하는 DNS 서버는 공용 등록을 사용 하지만 *개인* 등록에 대 한 모든 별칭을 확인 하 고, 검색 한 경우 공용 등록 시 정의 된 다음 별칭을 중지 합니다.

이 논리는 Virtual Network 이름이 있는 사설 DNS 영역에 연결 되 `privatelink.vaultcore.azure.net` 고 키 자격 증명 모음에 대 한 공용 DNS 등록에 별칭이 있는 경우 `fabrikam.privatelink.vaultcore.azure.net` (키 자격 증명 모음 접미사는 사설 DNS 영역 이름과 정확 하 게 일치) DNS 쿼리는 `A` `fabrikam` *사설 DNS 영역에서* 이름이 있는 레코드를 검색 함을 의미 합니다. `A`레코드가 발견 되 면 DNS 쿼리에서 해당 IP 주소가 반환 되 고 공용 dns 등록에서 추가 조회가 수행 되지 않습니다.

여기에서 볼 수 있듯이 이름 확인은 사용자의 제어를 받고 있습니다. 이 디자인의 rationales은 다음과 같습니다.

- 사용자 지정 DNS 서버와 온-프레미스 네트워크와의 통합을 포함 하는 복잡 한 시나리오가 있을 수 있습니다. 이 경우 이름을 IP 주소로 변환 하는 방법을 제어 해야 합니다.
- 개인 링크 없이 키 자격 증명 모음에 액세스 해야 할 수 있습니다. 이 경우 Virtual Network에서 호스트 이름을 확인 하는 것은 공용 IP 주소를 반환 해야 하며, 개인 링크가 없는 키 자격 증명 모음에는 `privatelink` 이름 등록에 별칭이 없는 경우에 발생 합니다.

## <a name="7-validate-that-requests-to-key-vault-use-private-link"></a>7. key vault에 대 한 요청이 개인 링크를 사용 하는지 확인

### <a name="query-the-healthstatus-endpoint-of-the-key-vault"></a>`/healthstatus`Key vault의 끝점 쿼리

키 자격 증명 모음은 `/healthstatus` 진단에 사용할 수 있는 끝점을 제공 합니다. 응답 헤더에는 주요 자격 증명 모음 서비스에 표시 되는 원본 IP 주소가 포함 됩니다. 다음 명령을 사용 하 여 해당 끝점을 호출할 수 있습니다.**key vault 호스트 이름을 사용** 해야 합니다.

Windows (PowerShell):

```powershell
PS C:\> $(Invoke-WebRequest -UseBasicParsing -Uri https://fabrikam.vault.azure.net/healthstatus).Headers
```

```output
Key                           Value
---                           -----
Pragma                        no-cache
x-ms-request-id               3729ddde-eb6d-4060-af2b-aac08661d2ec
x-ms-keyvault-service-version 1.2.27.0
x-ms-keyvault-network-info    addr=10.4.5.6;act_addr_fam=InterNetworkV6;
Strict-Transport-Security     max-age=31536000;includeSubDomains
Content-Length                4
Cache-Control                 no-cache
Content-Type                  application/json; charset=utf-8
```

Linux 또는 다음을 포함 하는 최신 버전의 Windows 10 `curl`

```console
joe@MyUbuntu:~$ curl -i https://fabrikam.vault.azure.net/healthstatus
```

```output
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Type: application/json; charset=utf-8
x-ms-request-id: 6c090c46-0a1c-48ab-b740-3442ce17e75e
x-ms-keyvault-service-version: 1.2.27.0
x-ms-keyvault-network-info: addr=10.4.5.6;act_addr_fam=InterNetworkV6;
Strict-Transport-Security: max-age=31536000;includeSubDomains
Content-Length: 4
```

이와 유사한 출력이 표시 되지 않거나 네트워크 오류가 발생 하는 경우에는 사용자가 지정한 호스트 이름 (예:)을 통해 키 자격 증명 모음에 액세스할 수 없음을 의미 `fabrikam.vault.azure.net` 합니다. 호스트 이름이 올바른 IP 주소로 확인 되지 않거나 전송 계층에 연결 문제가 있습니다. 라우팅 문제, 패키지 삭제 및 기타 이유로 인해 발생할 수 있습니다. 자세히 조사 해야 합니다.

응답은 다음 헤더를 포함 해야 합니다 `x-ms-keyvault-network-info` .

```console
x-ms-keyvault-network-info: addr=10.4.5.6;act_addr_fam=InterNetworkV6;
```

`addr`헤더의 필드는 `x-ms-keyvault-network-info` 요청 원본에 대 한 IP 주소를 표시 합니다. 이 IP 주소는 다음 중 하나일 수 있습니다.

- 요청을 수행 하는 컴퓨터의 개인 IP 주소입니다. 이는 요청이 개인 링크 또는 서비스 끝점을 사용 하 고 있음을 나타냅니다. 이는 개인 링크에 대해 예상 되는 결과입니다.
- 요청을 수행 하는 컴퓨터가 *아닌* 다른 개인 IP 주소 이는 일부 사용자 지정 라우팅이 적용 됨을 나타냅니다. 여전히 요청이 개인 링크 또는 서비스 끝점을 사용 하 고 있음을 나타냅니다.
- 일부 공용 IP 주소입니다. 이는 요청이 게이트웨이 (NAT) 장치를 통해 인터넷으로 라우팅되는 것을 나타냅니다. 이는 요청이 개인 링크를 사용 하지 않고 일부 문제를 해결 해야 함을 나타냅니다. 이에 대 한 일반적인 이유는 1) 개인 끝점이 승인 및 성공 상태가 아닌 경우입니다. 및 2) 호스트 이름이 key vault의 개인 IP 주소를 확인 하지 않습니다. 이 문서에는 두 경우 모두에 대 한 문제 해결 작업이 포함 되어 있습니다.

>[!NOTE]
> 요청이 `/healthstatus` 작동 하지만 `x-ms-keyvault-network-info` 헤더가 없으면 끝점이 키 자격 증명 모음에서 제공 되지 않을 수 있습니다. 위의 명령은 HTTPS 인증서의 유효성도 검사 하므로 누락 된 헤더는 변조의 것일 수 있습니다.

### <a name="query-the-key-vault-ip-address-directly"></a>키 자격 증명 모음 IP 주소 직접 쿼리

>[!IMPORTANT]
> HTTPS 인증서 유효성 검사 없이 키 자격 증명 모음에 액세스 하는 것은 위험 하며 학습 목적 으로만 사용할 수 있습니다. 프로덕션 코드는이 클라이언트 쪽 유효성 검사 없이 키 자격 증명 모음에 액세스 해서는 안 됩니다. 문제를 진단 하는 경우에도 키 자격 증명 모음에 대 한 요청에서 HTTPS 인증서 유효성 검사를 자주 사용 하지 않도록 설정 하는 경우에는 변조 시도의 영향을 받을 수 있습니다.

최신 버전의 PowerShell을 설치한 경우를 사용 `-SkipCertificateCheck` 하 여 HTTPS 인증서 검사를 건너뛸 수 있습니다. 그러면 [키 자격 증명 모음 IP 주소](#find-the-key-vault-private-ip-address-in-the-virtual-network) 를 직접 대상으로 지정할 수 있습니다.

```powershell
PS C:\> $(Invoke-WebRequest -SkipCertificateCheck -Uri https://10.1.2.3/healthstatus).Headers
```

를 사용 하는 경우 인수를 사용 하 여 `curl` 동일한 작업을 수행할 수 있습니다 `-k` .

```console
joe@MyUbuntu:~$ curl -i -k https://10.1.2.3/healthstatus
```

응답은 이전 섹션과 동일 해야 합니다. 즉, `x-ms-keyvault-network-info` 같은 값을 가진 헤더를 포함 해야 합니다. `/healthstatus`키 자격 증명 모음 호스트 이름 또는 IP 주소를 사용 하는 경우에는 끝점이 필요 하지 않습니다.

`x-ms-keyvault-network-info`Key vault 호스트 이름 및 IP 주소를 사용 하는 요청에 대 한 다른 값을 사용 하 여 요청에 대 한 값을 반환 하는 것이 확인 되 면 각 요청은 다른 끝점을 대상으로 합니다. 오류가 `addr` `x-ms-keyvault-network-info` 발생 하 고 수정 해야 하는 경우를 결정 하려면 이전 섹션에서의 필드에 대 한 설명을 참조 하세요.

## <a name="8-other-changes-and-customizations-that-cause-impact"></a>8. 영향을 일으키는 기타 변경 내용 및 사용자 지정

다음 항목은 비 포괄적인 조사 작업입니다. 추가 문제를 찾을 수 있는 위치를 알려 주지만 이러한 시나리오에서 문제를 해결 하려면 고급 네트워크 기술 자료가 있어야 합니다.

### <a name="diagnose-custom-dns-servers-at-virtual-network"></a>Virtual Network에서 사용자 지정 DNS 서버 진단

포털에서 Virtual Network 리소스를 엽니다. 왼쪽 메뉴에서 **DNS 서버** 를 엽니다. "Custom"을 사용 하는 경우 DNS 확인이이 문서에 설명 된 것과 다를 수 있습니다. DNS 서버에서 주요 자격 증명 모음 호스트 이름을 확인 하는 방법을 진단 해야 합니다.

Azure에서 제공 하는 기본 DNS 서버를 사용 하는 경우이 문서 전체를 적용할 수 있습니다.

### <a name="diagnose-hosts-overriding-or-custom-dns-servers-at-virtual-machine"></a>가상 머신에서 호스트를 재정의 하거나 사용자 지정 DNS 서버를 진단 합니다.

대부분의 운영 체제에서는 일반적으로 파일을 변경 하 여 호스트 이름에 따라 명시적인 고정 IP 주소를 설정할 수 `hosts` 있습니다. DNS 서버를 재정의 하도록 허용할 수도 있습니다. 이러한 시나리오 중 하나를 사용 하는 경우 시스템 관련 진단 옵션을 진행 합니다.

### <a name="promiscuous-proxies-fiddler-etc"></a>무차별 프록시 (Fiddler 등)

명시적으로 명시 된 경우를 제외 하 고이 문서의 진단 옵션은 환경에 무차별 프록시가 없는 경우에만 작동 합니다. 이러한 프록시는 진단 중인 컴퓨터에 단독으로 설치 되는 경우가 많지만 (Fiddler는 가장 일반적인 예제) 고급 관리자는 루트 Ca (인증 기관)를 덮어쓰고 네트워크에서 여러 컴퓨터를 제공 하는 게이트웨이 장치에 무차별 프록시를 설치할 수 있습니다. 이러한 프록시는 보안과 안정성에 실제로 영향을 줄 수 있습니다. Microsoft는 이러한 제품을 사용 하는 구성을 지원 하지 않습니다.

### <a name="other-things-that-may-affect-connectivity"></a>연결에 영향을 줄 수 있는 기타 항목

다음은 고급 또는 복잡 한 시나리오에서 찾을 수 있는 항목에 대 한 완전 하지 않은 목록입니다.

- 방화벽 설정, Virtual Network에 연결 된 Azure 방화벽 또는 Virtual Network 또는 컴퓨터에 배포 하는 사용자 지정 방화벽 솔루션이 있습니다.
- 네트워크 피어 링-사용 되는 DNS 서버와 트래픽을 라우팅하는 방법에 영향을 줄 수 있습니다.
- DNS 쿼리에서의 트래픽을 포함 하 여 트래픽이 라우팅되는 방법에 영향을 줄 수 있는 NAT (사용자 지정 게이트웨이) 솔루션.