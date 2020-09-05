---
title: Azure Stack Edge를 사용 하 여 인증서 요구 사항 및 문제 해결 | Microsoft Docs
description: 인증서 요구 사항 및 Azure Stack Edge 장치의 인증서 오류 문제 해결에 대해 설명 합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/12/2020
ms.author: alkohli
ms.openlocfilehash: b24b745a53b632ce32cda37058363bf974d400b3
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89268265"
---
# <a name="certificate-requirements"></a>인증서 요구 사항

이 문서에서는 Azure Stack Edge 장치에 인증서를 설치 하기 전에 충족 해야 하는 인증서 요구 사항을 설명 합니다. 요구 사항은 PFX 인증서, 발급 기관, 인증서 주체 이름 및 주체 대체 이름, 지원 되는 인증서 알고리즘과 관련 됩니다.

## <a name="certificate-issuing-authority"></a>인증서 발급 기관

인증서 발급 요구 사항은 다음과 같습니다.

* 내부 인증 기관 또는 공용 인증 기관에서 인증서를 발급 해야 합니다.

* 자체 서명 된 인증서의 사용은 지원 되지 않습니다.

* 인증서의 *발급 대상:* 필드는 루트 CA 인증서를 제외 하 고 *발급자* : 필드와 같지 않아야 합니다.



## <a name="certificate-algorithms"></a>인증서 알고리즘

인증서 알고리즘은 다음과 같은 요구 사항을 충족 해야 합니다.

* 인증서는 RSA 키 알고리즘을 사용 해야 합니다.

* 인증서 서명 알고리즘은 SHA1 일 수 없습니다.

* 최소 키 크기는 4096입니다.

## <a name="certificate-subject-name-and-subject-alternative-name"></a>인증서 주체 이름 및 주체 대체 이름

인증서의 주체 이름 및 주체 대체 이름 요구 사항은 다음과 같아야 합니다.

* 인증서의 SAN (주체 대체 이름) 필드에 모든 이름 공간을 포함 하는 단일 인증서를 사용할 수 있습니다. 또는 각 네임 스페이스에 개별 인증서를 사용할 수 있습니다. 두 방법 모두 필요한 경우 Blob (binary large object)와 같이 와일드 카드를 사용 하 여 끝점을 사용 해야 합니다.

* 주체 이름 (주체 이름의 일반 이름)이 주체 대체 이름 확장의 주체 대체 이름에 포함 되어 있는지 확인 합니다.

* 인증서의 SAN 필드에 모든 이름 공간을 포함 하는 단일 와일드 카드 인증서를 사용할 수 있습니다.

* 끝점 인증서를 만들 때 다음 표를 사용 합니다.

    |유형 |주체 이름 (SN)  |SAN (주체 대체 이름)  |주체 이름 예 |
    |---------|---------|---------|---------|
    |Azure 리소스 관리자|`management.<Device name>.<Dns Domain>`|`login.<Device name>.<Dns Domain>`<br>`management.<Device name>.<Dns Domain>`|`management.mydevice1.microsoftdatabox.com` |
    |Blob Storage|`*.blob.<Device name>.<Dns Domain>`|`*.blob.< Device name>.<Dns Domain>`|`*.blob.mydevice1.microsoftdatabox.com` |
    |로컬 UI| `<Device name>.<DnsDomain>`|`<Device name>.<DnsDomain>`| `mydevice1.microsoftdatabox.com` |
    |두 끝점 모두에 대 한 다중 SAN 단일 인증서|`<Device name>.<dnsdomain>`|`<Device name>.<dnsdomain>`<br>`login.<Device name>.<Dns Domain>`<br>`management.<Device name>.<Dns Domain>`<br>`*.blob.<Device name>.<Dns Domain>`|`mydevice1.microsoftdatabox.com` |
    |노드|`<NodeSerialNo>.<DnsDomain>`|`*.<DnsDomain>`<br><br>`<NodeSerialNo>.<DnsDomain>`|`mydevice1.microsoftdatabox.com` |
    |VPN|`AzureStackEdgeVPNCertificate.<DnsDomain>`<br><br> * AzureStackEdgeVPNCertificate는 하드 코딩 됩니다.  | `*.<DnsDomain>`<br><br>`<AzureStackVPN>.<DnsDomain>` | `edgevpncertificate.microsoftdatabox.com`|
    
## <a name="pfx-certificate"></a>PFX 인증서

Azure Stack Edge 장치에 설치 된 PFX 인증서는 다음 요구 사항을 충족 해야 합니다.

* SSL 기관에서 인증서를 가져오면에서 인증서에 대 한 전체 서명 체인을 얻을 수 있는지 확인 합니다.

* PFX 인증서를 내보낼 때 **가능한 경우 체인의 모든 인증서 포함 옵션을** 선택 했는지 확인 합니다.

* Azure Stack Edge에 대 한 공개 키와 개인 키가 모두 필요 하므로 끝점, 로컬 UI, 노드, VPN 및 Wi-fi에 PFX 인증서를 사용 합니다. 개인 키에는 로컬 컴퓨터 키 특성 집합이 있어야 합니다.

* 인증서의 PFX 암호화는 3DES 여야 합니다. 이는 Windows 10 클라이언트 또는 Windows Server 2016 인증서 저장소에서 내보낼 때 사용 되는 기본 암호화입니다. 3DES와 관련 된 자세한 내용은 [TRIPLE DES](https://en.wikipedia.org/wiki/Triple_DES)를 참조 하세요.

* 인증서 PFX 파일의 *키 사용* 필드에 유효한 *디지털 서명* 및 *keyencipherment* 값이 있어야 합니다.

* 인증서 PFX 파일에는 *확장 된 키 사용* 필드의 *서버 인증 (1.3.6.1.5.5.7.3.1)* 및 *클라이언트 인증 (1.3.6.1.5.5.7.3.2)* 값이 있어야 합니다.

* Azure Stack 준비 검사기 도구를 사용 하는 경우 배포 시 모든 인증서 PFX 파일에 대 한 암호는 동일 해야 합니다. 자세한 내용은 [Azure Stack 허브 준비 검사 도구를 사용 하 여 Azure Stack에 지에 대 한 인증서 만들기](azure-stack-edge-j-series-create-certificates-tool.md)를 참조 하세요.

* 인증서 PFX에 대 한 암호는 복잡 한 암호 여야 합니다. 이 암호는 배포 매개 변수로 사용 되므로 기록해 둡니다.

자세한 내용은 [개인 키를 사용 하 여 PFX 인증서 내보내기](azure-stack-edge-j-series-manage-certificates.md#export-certificates-as-pfx-format-with-private-key)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

[Azure Stack Edge에서 인증서 사용](azure-stack-edge-j-series-manage-certificates.md)

[Azure Stack 허브 준비 검사 도구를 사용 하 여 Azure Stack에 지에 대 한 인증서 만들기](azure-stack-edge-j-series-create-certificates-tool.md)

[개인 키를 사용 하 여 PFX 인증서 내보내기](azure-stack-edge-j-series-manage-certificates.md#export-certificates-as-pfx-format-with-private-key)

[인증서 오류 문제 해결](azure-stack-edge-j-series-certificate-troubleshooting.md)