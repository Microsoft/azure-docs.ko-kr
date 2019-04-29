---
title: 해결 방법 - Microsoft 위협 모델링 도구 - Azure | Microsoft Docs
description: Microsoft 위협 모델링 도구에 대한 해결 방법 페이지로, 생성되는 가장 큰 노출 위협에 대한 가능한 해결 방법을 강조 표시합니다.
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: rodsan
ms.openlocfilehash: 24aa49fd4ccccda372d2632ef4aee22bd5cb2bf6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60611312"
---
# <a name="microsoft-threat-modeling-tool-mitigations"></a>Microsoft 위협 모델링 도구 해결 방법

위협 모델링 도구는 Microsoft SDL(Security Development Lifecycle)의 핵심 요소입니다. 소프트웨어 설계자는 이 도구를 사용하여 비교적 쉽고 비용 효과적으로 문제를 해결할 수 있는 조기에 잠재적인 보안 문제를 식별하고 완화할 수 있습니다. 결과적으로 전체 개발 비용이 크게 절감됩니다. 또한 보안 전문가가 아닌 사용자를 염두에 두고 개발되었으므로 위협 모델을 만들고 분석하기 위한 정확한 지침을 제공하여 모든 개발자가 쉽게 위협 모델링을 수행할 수 있도록 합니다.

지금 바로 시작하려면 **[위협 모델링 도구](./azure-security-threat-modeling-tool.md)** 를 방문하세요!

## <a name="mitigation-categories"></a>해결 방법 범주

위협 모델링 도구 해결 방법은 다음으로 구성된 웹 애플리케이션 보안 프레임에 따라 분류됩니다.

| Category | 설명 |
| -------- | ----------- |
| **[감사 및 로깅](./azure-security-threat-modeling-tool-auditing-and-logging.md)** | 누가 언제 무엇을 했나요? 감사 및 로깅은 애플리케이션이 보안 관련 이벤트를 기록하는 방법을 나타냅니다. |
| **[인증](./azure-security-threat-modeling-tool-authentication.md)** | 당신은 누구인가요? 인증은 엔터티가 일반적으로 사용자 이름 및 암호와 같은 자격 증명을 통해 다른 엔터티의 ID를 증명하는 과정입니다. |
| **[권한 부여](./azure-security-threat-modeling-tool-authorization.md)** | 어떻게 해야 합니까? 권한 부여는 애플리케이션이 리소스 및 작업에 대한 액세스 제어를 제공하는 방법입니다. |
| **[통신 보안](./azure-security-threat-modeling-tool-communication-security.md)** | 누구에게 이야기하고 있나요? 통신 보안은 수행되는 모든 통신을 최대한 안전하게 유지합니다. |
| **[구성 관리](./azure-security-threat-modeling-tool-configuration-management.md)** | 애플리케이션이 어떤 권한으로 실행되고 있나요? 어떤 데이터베이스에 연결되나요? 애플리케이션이 어떤 방식으로 관리되나요? 이러한 설정은 어떻게 보호되나요? 구성 관리는 애플리케이션이 이러한 운영 문제를 처리하는 방법을 나타냅니다. |
| **[암호화](./azure-security-threat-modeling-tool-cryptography.md)** | 비밀(기밀성)은 어떻게 유지하나요? 데이터 또는 라이브러리의 변조를 방지하려면 어떻게 하나요(무결성)? 암호로 강력하게 보호해야 하는 임의 값에 대해 시드를 제공하려면 어떻게 해야 하나요? 암호화는 애플리케이션이 기밀성 및 무결성을 적용하는 방법을 나타냅니다. |
| **[예외 관리](./azure-security-threat-modeling-tool-exception-management.md)** | 애플리케이션의 메서드 호출이 실패하면 애플리케이션은 어떻게 하나요? 얼마나 많은 정보를 노출하고 있나요? 최종 사용자에게 오류 정보를 친절하게 반환하고 있나요? 중요한 예외 정보를 호출자에게 다시 제공하고 있나요? 애플리케이션이 정상적으로 실패하나요? |
| **[유효성 검사 입력](./azure-security-threat-modeling-tool-input-validation.md)** | 애플리케이션이 수신하는 입력이 유효하고 안전한지를 어떻게 알 수 있나요? 입력 유효성 검사는 애플리케이션이 입력을 추가로 처리하기 전에 필터링, 삭제 또는 거절하는 방법을 나타냅니다. 진입점을 통해 입력을 제한하고 종료 지점을 통과하는 출력을 인코딩하는 것이 좋습니다. 데이터베이스 및 파일 공유와 같은 원본의 데이터를 신뢰할 수 있나요? |
| **[중요 데이터](./azure-security-threat-modeling-tool-sensitive-data.md)** | 애플리케이션은 중요한 데이터를 어떻게 처리하나요? 중요한 데이터는 애플리케이션에서 메모리 내에, 네트워크를 통해 또는 영구 저장소에서 보호되어야 하는 모든 데이터를 처리하는 방법을 나타냅니다. |
| **[세션 관리](./azure-security-threat-modeling-tool-session-management.md)** | 애플리케이션은 사용자 세션을 어떻게 처리하고 보호하나요? 세션은 사용자와 웹 애플리케이션 간에 관련된 일련의 상호 작용입니다. |

세션을 다음을 파악하는 데 도움이 됩니다.

* 가장 일반적인 실수가 수행되는 위치
* 실행 가능한 개선 방법이 제공되는 위치

결과적으로 이러한 범주를 사용하여 보안 작업에 주력하고 우선 순위를 두고 있고, 입력 유효성 검사, 인증 및 권한 부여 범주에서 보안 문제가 가장 널리 발생한다는 사실을 알 수 있으므로 여기서부터 시작할 수 있습니다. 자세한 내용은 **[이 특허 링크](https://www.google.com/patents/US7818788)** 를 방문하세요.

## <a name="next-steps"></a>다음 단계

**[위협 모델링 도구 위협](./azure-security-threat-modeling-tool-threats.md)** 을 방문하여 도구에서 가능한 디자인 위협을 생성하는 데 사용하는 위협 범주에 대한 자세한 내용을 알아봅니다.