---
title: Azure Security Center 인시던트-경고의 스마트 상관 관계
description: 이 항목에서는 fusion에서 클라우드 스마트 경고 상관 관계를 사용 하 여 Azure Security Center에서 보안 인시던트를 생성 하는 방법을 설명 합니다.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: e9d5a771-bfbe-458c-9a9b-a10ece895ec1
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/02/2019
ms.author: memildin
ms.openlocfilehash: b26f0bab073ce248ca23bb8a815fa3e293ddba51
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73686482"
---
# <a name="cloud-smart-alert-correlation-in-azure-security-center-incidents"></a>Azure Security Center의 클라우드 스마트 경고 상관 관계 (인시던트)

Azure Security Center는 고급 분석 및 위협 인텔리전스를 사용 하 여 악의적인 작업에 대해 경고 하는 하이브리드 클라우드 워크 로드를 지속적으로 분석 합니다.

위협 범위 범위가 증가 하 고 있습니다. Slightest 손상을 검색 해야 하는 것은 중요 하며, 보안 분석가가 다른 경고를 심사 하 고 실제 공격을 식별 하는 데 어려움이 있을 수 있습니다. Security Center 분석가가이 경고를 피로 하는 데 도움이 됩니다. 서로 다른 경고와 낮은 정확도 신호를 보안 인시던트에 상관 관계를 통해 발생 하는 공격을 진단 하는 데 도움이 됩니다.

Fusion analytics는 인시던트를 Security Center 하 여 서로 다른 경고와 상황별 신호를 서로 연결할 수 있도록 하는 기술 및 분석 백 엔드입니다. Fusion은 리소스에서 구독에 대해 보고 된 다양 한 신호를 확인 합니다. 퓨전은 공유 컨텍스트 정보를 사용 하 여 공격 진행 또는 신호를 표시 하는 패턴을 검색 하 여 통합 된 응답 절차를 사용 해야 함을 나타냅니다.

Fusion analytics는 보안 도메인 정보를 AI와 결합 하 여 경고를 분석 하 고 발생 하는 새로운 공격 패턴을 검색 합니다. 

Security Center는 MITRE 공격 매트릭스를 활용 하 여 경고를 인식 된 도와 연결 하 고 보안 도메인 지식을 공식화 하는 데 도움을 줍니다. 또한 공격의 각 단계에 대해 수집 된 정보를 사용 하 여 공격의 단계로 표시 되는 작업을 규칙 할 수 Security Center 있지만 실제로는 그렇지 않습니다.

공격은 여러 테넌트에서 자주 발생 하므로 AI 알고리즘을 결합 하 여 각 구독에 대해 보고 되는 공격 순서를 분석할 수 Security Center. 이 기술은 공격 시퀀스를 서로 연결 하는 것이 아니라 널리 사용 되는 경고 패턴으로 식별 합니다.

인시던트를 조사 하는 동안 분석가는 위협에 대 한 특성 및이를 완화 하는 방법에 대 한 verdict에 도달 하는 데 추가 컨텍스트가 필요 하기도 합니다. 예를 들어 네트워크에서 발생 하는 다른 작업을 이해 하거나 대상 리소스와 관련 하 여 네트워크 변칙이 검색 된 경우에도 다음에 수행할 작업을 이해 하기 어렵습니다. 이를 돕기 위해 보안 인시던트에는 아티팩트, 관련 이벤트 및 정보가 포함 될 수 있습니다. 보안 인시던트에 사용할 수 있는 추가 정보는 검색 된 위협 유형 및 사용자 환경의 구성에 따라 달라 집니다. 

![보안 인시던트의 검색 된 보고서의 스크린샷](./media/security-center-alerts-cloud-smart/security-incident.png)

보안 인시던트를 더 잘 이해 하려면 [Azure Security Center에서 보안 인시던트를 처리 하는 방법](https://docs.microsoft.com/azure/security-center/security-center-incident)을 참조 하세요.

