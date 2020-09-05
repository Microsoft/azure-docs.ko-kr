---
title: GPU를 사용 하 여 Azure Stack Edge에서 인증서 문제 해결 | Microsoft Docs
description: Azure Stack Edge GPU 장치를 사용 하 여 인증서 오류 문제를 해결 하는 방법을 설명 합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: troubleshooting
ms.date: 08/28/2020
ms.author: alkohli
ms.openlocfilehash: 05338fb70bfa390ff3bd9e14e938edeb40938aeb
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89269440"
---
# <a name="troubleshooting-certificate-errors"></a>인증서 오류 문제 해결

이 문서에서는 Azure Stack Edge 장치에 인증서를 설치할 때 일반적인 인증서 오류 문제를 해결 하는 방법을 설명 합니다.

## <a name="common-certificate-errors"></a>일반적인 인증서 오류

다음 표에서는 일반적인 인증서 오류와 이러한 오류 및 가능한 해결 방법에 대 한 자세한 정보를 보여 줍니다.

> [!NOTE]
> &#8220;, {0} {1} , ..., {n} &#8221;는 위치 매개 변수를 표시 합니다. 위치 매개 변수는 사용 하는 인증서에 따라 값을 사용 합니다.

| 오류 코드 | Description |
|---|---|
| CertificateManagement_UntrustedCertificate | 주체 이름이 있는 인증서 {0} 의 인증서 체인이 끊어졌습니다. 이 인증서를 업로드 하기 전에 서명 체인 인증서를 업로드 하세요.|
| CertificateManagement_DeviceNotRegistered| 장치가 활성화 되지 않았습니다. 활성화 한 후에만 지원 인증서를 업로드할 수 있습니다.|
| CertificateManagement_EmptySAN | 주체 이름이 있는 인증서 {0} 에 주체 대체 이름이 없습니다. 인증서 속성을 확인 하 고 새 인증서를 가져옵니다.|
| CertificateManagement_ExpiredCertificate | 유형의 인증서 {0} 가 만료 되었거나 곧 만료 됩니다. 인증서 만료를 확인 하 고 필요한 경우 새 인증서를 가져옵니다.|
| CertificateManagement_FormatMismatch | 인증서 형식이 지원 되지 않습니다. 인증서 형식을 확인 하 고 필요한 경우 새 인증서를 가져옵니다.  {0}가 필요 {1} 합니다. |
| CertificateManagement_GenericError | 인증서 관리 작업을 수행할 수 없습니다. 몇 분 후에이 작업을 다시 시도 하세요. 문제가 지속되면 Microsoft 지원에 문의하세요. |
| CertificateManagement_HttpsBindingFailure | 주체 이름이 인 인증서가 {0} 보안 https 채널을 만들지 못했습니다. 업로드 한 인증서를 확인 하 고 필요한 경우 새 인증서를 가져옵니다. 이 오류는 장치 노드 인증서와 함께 발생 합니다.|
| CertificateManagement_IncorrectKeyCertSignKeyUsage | 주체 이름이 있는 인증서 {0} 에는 키 사용 인증서 서명이 없어야 합니다. 인증서의 키 사용을 확인 하 고 필요한 경우 새 인증서를 가져옵니다. 이 오류는 서명 체인 인증서에서 발생 합니다. |
| CertificateManagement_IncorrectKeyNumber | 주체 이름이 인 인증서 {0} 에 잘못 된 키 번호가 {1} 있습니다. 인증서의 키 번호를 확인 하 고 필요한 경우 새 인증서를 가져옵니다.|
| CertificateManagement_InvalidP7b | 업로드 된 인증서 파일이 잘못 되었습니다.  인증서 형식을 확인 하 고 필요한 경우 새 인증서를 가져옵니다.|
| CertificateManagement_KeyAlgorithmNotRSA | 이 인증서는 RSA 키 알고리즘을 사용 하지 않습니다. RSA 인증서만 지원 됩니다. |
| CertificateManagement_KeySizeNotSufficient | 주체 이름이 있는 인증서 {0} 의 키 크기가 충분 하지 않습니다 {1} . 최소 키 크기는 4096입니다.|
| CertificateManagement_MissingClientOid | 주체 이름이 인 인증서에 {0} 클라이언트 인증 OID가 없습니다. 인증서 속성을 확인 하 고 필요한 경우 새 인증서를 가져옵니다.|
| CertificateManagement_MissingDigitalSignatureKeyUsage | 주체 이름이 있는 인증서 {0} 의 키 사용에 디지털 서명이 없습니다. 인증서 속성을 확인 하 고 필요한 경우 새 인증서를 가져옵니다. |
| CertificateManagement_MissingEntryInSAN | 주체 이름이 있는 인증서 {0} 의 주체 대체 이름에 주체 이름 항목이 없습니다. 인증서 속성을 확인 하 고 새 인증서를 가져옵니다. |
| CertificateManagement_MissingKeyCertSignKeyUsage | 주체 이름이 인 인증서 {0} 에 키 사용에 대 한 인증서 서명이 없습니다. 인증서 속성을 확인 하 고 필요한 경우 새 인증서를 가져옵니다.|
| CertificateManagement_MissingKeyEnciphermentKeyUsage | 주체 이름이 있는 인증서 {0} 의 키 암호화는 키 사용에 포함 되지 않습니다. 인증서 속성을 확인 하 고 필요한 경우 새 인증서를 가져옵니다. |
| CertificateManagement_MissingServerOid | 주체 이름이 인 인증서에는 {0} 서버 인증 OID가 없습니다. 인증서 속성을 확인 하 고 필요한 경우 새 인증서를 가져옵니다.|
| CertificateManagement_NameMismatch | 인증서 유형이 일치 하지 않습니다. 예상 범위: {0} , 개 찾음 {1} 적절 한 인증서를 업로드 합니다.|
| CertificateManagement_NoPrivateKeyPresent | 주체 이름이 있는 인증서 {0} 에 개인 키가 없습니다. 개인 키가 있는 .pfx 인증서를 업로드 합니다.|
| CertificateManagement_NotSelfSignedCertificate | 주체 이름이 있는 인증서 {0} 는 자체 서명 되지 않습니다. 루트 인증서는 자체 서명 되어야 합니다. |
| CertificateManagement_NotSupportedOnVirtualAppliance | 가상 장치에서는이 작업이 지원 되지 않습니다. 이 오류는 전략적 클라우드 어플라이언스에서 실행 되는 Data Box Gateway에만 서명이 발생 함을 나타냅니다. 이 오류는 Windows PowerShell을 통해 장치를 관리 하는 동안 발생 합니다.|
| CertificateManagement_SelfSignedCertificate | 주체 이름이 있는 인증서 {0} 는 자체 서명 됩니다. 올바르게 서명 된 인증서를 업로드 합니다.|
| CertificateManagement_SignatureAlgorithmSha1 | 주체 이름이 인 인증서 {0} 에 지원 되지 않는 서명 알고리즘이 있습니다. SHA1-RSA는 지원 되지 않습니다. |
| CertificateManagement_SubjectNamesInvalid | 주체 이름이 있는 인증서 {0} 의 주체 이름이 잘못 되었거나 인증서의 주체 대체 이름이 잘못 되었습니다 {1} . 업로드 한 인증서를 확인 하 고 필요한 경우 새 인증서를 가져옵니다. 또한 DNS 이름을 확인 하 여 SAN 이름과 일치 해야 합니다.|
| CertificateManagement_UnreadableCertificate | 형식을 가진 인증서를 {0} 읽을 수 없습니다. 이 오류는 인증서를 읽을 수 없거나 손상 된 경우에 발생 합니다. 새 인증서를 가져옵니다.|
| CertificateSubjectNotFound | 주체 이름이 인 인증서를 찾을 수 없습니다 {0} . 새 인증서를 가져옵니다.|

## <a name="next-steps"></a>다음 단계

5\. 인증서가 RD 게이트웨이에 대한 요구 사항을 충족하면 [설치](azure-stack-edge-j-series-certificate-requirements.md)를 클릭합니다.