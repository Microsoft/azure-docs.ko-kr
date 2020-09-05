---
title: 휴지 상태의 데이터에 대 한 음성 서비스 암호화
titleSuffix: Azure Cognitive Services
description: Microsoft는 Microsoft에서 관리 하는 암호화 키를 제공 하며, CMK (고객이 관리 하는 키) 라고 하는 고유한 키를 사용 하 여 Cognitive Services 구독을 관리할 수도 있습니다. 이 문서에서는 Speech service에 대 한 미사용 데이터 암호화에 대해 설명 합니다.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: egeaney
ms.openlocfilehash: b9b76b2eb5e9536561f73a92b6911a2f82122a1b
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89078098"
---
# <a name="speech-service-encryption-of-data-at-rest"></a>휴지 상태의 데이터에 대 한 음성 서비스 암호화

Speech Service는 데이터를 클라우드에 보관할 때 데이터를 자동으로 암호화 합니다. 음성 서비스 암호화는 데이터를 보호 하 고 조직의 보안 및 규정 준수 약정을 충족 하는 데 도움을 줍니다.

## <a name="about-cognitive-services-encryption"></a>Cognitive Services 암호화 정보

데이터는 [FIPS 140-2](https://en.wikipedia.org/wiki/FIPS_140-2) 규격 [256 비트 AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) 암호화를 사용 하 여 암호화 및 암호 해독 됩니다. 암호화 및 암호 해독은 투명 합니다. 즉, 암호화 및 액세스를 관리 합니다. 데이터는 기본적으로 안전하며 암호화를 활용하기 위해 코드 또는 애플리케이션을 수정할 필요가 없습니다.

## <a name="about-encryption-key-management"></a>암호화 키 관리 정보

Custom Speech 및 사용자 지정 음성을 사용 하는 경우 Speech service는 다음 데이터를 클라우드에 저장할 수 있습니다.  

* 음성 추적 데이터-사용자 지정 끝점에 대해 추적을 설정 하는 경우에만
* 업로드 된 학습 및 테스트 데이터

기본적으로 데이터는 Microsoft 저장소에 저장 되 고 구독은 Microsoft에서 관리 하는 암호화 키를 사용 합니다. 또한 사용자 고유의 저장소 계정을 준비 하는 옵션도 있습니다. 저장소에 대 한 액세스는 관리 되는 Id를 통해 관리 되며, speech service는 음성 추적 데이터, 사용자 지정 학습 데이터 및 사용자 지정 모델과 같은 고유한 데이터에 직접 액세스할 수 없습니다.

관리 Id에 대 한 자세한 내용은 [관리 되는 Id 정의](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)를 참조 하세요.

## <a name="bring-your-own-storage-byos-for-customization-and-logging"></a>사용자 지정 및 로깅에 대 한 사용자 고유의 저장소 (BYOS) 가져오기

사용자 고유의 저장소를 가져오기 위해 액세스를 요청 하려면 [음성 서비스-사용자 고유의 저장소 (BYOS) 요청 양식을](https://aka.ms/cogsvc-cmk)작성 하 고 제출 합니다. 승인 되 면 사용자 지정 및 로깅에 필요한 데이터를 저장 하기 위해 고유한 저장소 계정을 만들어야 합니다. 저장소 계정을 추가 하는 경우 Speech service 리소스는 시스템 할당 관리 id를 사용 하도록 설정 합니다. 시스템 할당 관리 id를 사용 하도록 설정 하면이 리소스가 AAD (Azure Active Directory)에 등록 됩니다. 등록 한 후에는 관리 id에 저장소 계정에 대 한 액세스 권한이 부여 됩니다. 여기에서 관리 되는 Id에 대해 자세히 알아볼 수 있습니다. 관리 Id에 대 한 자세한 내용은 [관리 되는 Id 정의](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)를 참조 하세요.

> [!IMPORTANT]
> 시스템 할당 관리 id를 사용 하지 않도록 설정 하면 저장소 계정에 대 한 액세스가 제거 됩니다. 이렇게 하면 저장소 계정에 대 한 액세스를 필요로 하는 음성 서비스 부분이 작동을 중지 하 게 됩니다.  

음성 서비스는 현재 고객 Lockbox을 지원 하지 않습니다. 그러나 BYOS를 사용 하 여 고객 데이터를 저장 하 여 [고객 Lockbox](../../security/fundamentals/customer-lockbox-overview.md)하는 비슷한 데이터 제어를 달성할 수 있습니다. 음성 서비스 데이터는 음성 리소스가 생성 된 지역에서 유지 되 고 처리 됩니다. 이는 미사용 데이터 및 전송 중인 데이터에 적용 됩니다. 사용자 지정 기능을 사용 하는 경우 (예: Custom Speech 및 사용자 지정 음성) 모든 고객 데이터는 BYOS (사용 되는 경우) 및 음성 서비스 리소스가 있는 동일한 지역에서 전송, 저장 및 처리 됩니다.

> [!IMPORTANT]
> Microsoft **는** 고객 데이터를 사용 하 여 음성 모델을 개선 하지 않습니다. 또한 끝점 로깅을 사용 하지 않도록 설정 하 고 사용자 지정을 사용 하지 않은 경우에는 고객 데이터가 저장 되지 않습니다. 

## <a name="next-steps"></a>다음 단계

* [Speech service-사용자 고유의 저장소 (BYOS) 요청 양식 가져오기](https://aka.ms/cogsvc-cmk)
* [관리 Id 란?](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)
