---
author: baanders
description: Azure Digital Twins 자습서에 대한 파일 포함 - 인스턴스를 설정하기 위한 필수 구성 요소
ms.service: digital-twins
ms.topic: include
ms.date: 5/25/2020
ms.author: baanders
ms.openlocfilehash: 5c41f7516cecdb6bbc42a66d118a90986dd7de56
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/06/2020
ms.locfileid: "87827342"
---
### <a name="prepare-an-azure-digital-twins-instance"></a>Azure Digital Twins 인스턴스 준비하기

이 자습서를 완료하려면 프로그래밍할 **Azure Digital Twins 인스턴스**가 필요합니다. 이전 작업에서 이미 Azure Digital Twins 인스턴스가 설정된 경우 해당 인스턴스를 사용할 수 있습니다.

* 그렇지 않으면 [*방법: 인스턴스 및 인증 설정*](../articles/digital-twins/how-to-set-up-instance-scripted.md)의 지침을 사용하여 **인스턴스 및 인증을 설정**합니다. 지침에는 각 단계를 성공적으로 완료했으며 새 인스턴스를 사용할 준비가 되었는지 확인하는 단계도 포함되어 있습니다.

이 자습서에서는 인스턴스를 설정할 때 다음 값이 필요합니다. 이러한 값을 다시 수집해야 하는 경우 설정 문서의 해당 섹션에 대한 아래 링크를 사용하여 [Azure Portal](https://portal.azure.com)에서 찾을 수 있습니다.
* Azure Digital Twins 인스턴스 **_이름_**([포털에서 찾기](../articles/digital-twins/how-to-set-up-instance-portal.md#verify-success-and-collect-important-values))
* Azure Digital Twins 인스턴스 **_호스트 이름_**([포털에서 찾기](../articles/digital-twins/how-to-set-up-instance-portal.md#verify-success-and-collect-important-values))
* Azure AD 앱 등록 **_애플리케이션(클라이언트) ID_**([포털에서 찾기](../articles/digital-twins/how-to-set-up-instance-portal.md#collect-important-values))
* Azure AD 앱 등록 **_디렉터리(테넌트) ID_**([포털에서 찾기](../articles/digital-twins/how-to-set-up-instance-portal.md#collect-important-values))