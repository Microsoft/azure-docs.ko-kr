---
title: Avere vFXT 지원 사용 - Azure
description: Avere vFXT for Azure에서 클러스터에 대 한 지원 데이터의 자동 업로드를 사용 하도록 설정 하 여 고객 서비스 제공을 지원 하도록 설정 하는 방법을 알아봅니다.
author: ekpgh
ms.service: avere-vfxt
ms.topic: how-to
ms.date: 12/14/2019
ms.author: rohogue
ms.openlocfilehash: 93b99aa624a21d9312297e4279b1dcf053c79ae3
ms.sourcegitcommit: 2bab7c1cd1792ec389a488c6190e4d90f8ca503b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2020
ms.locfileid: "88272731"
---
# <a name="enable-support-uploads"></a>지원 업로드 사용

Avere vFXT for Azure는 클러스터에 대 한 지원 데이터를 자동으로 업로드할 수 있습니다. 이러한 업로드를 통해 지원 담당자는 가능한 최고의 고객 서비스를 제공합니다.

## <a name="steps-to-enable-uploads"></a>업로드를 사용하도록 설정하는 단계

다음 단계에 따라 Avere 제어판에서 지원을 활성화합니다. ( [VFXT 클러스터에 액세스](avere-vfxt-cluster-gui.md) 하 여 제어판을 여는 방법을 알아보세요.)

1. 위쪽에서 **설정** 탭으로 이동합니다.
1. 왼쪽의 **지원** 링크를 클릭하고 개인 정보 취급 방침에 동의합니다.

   ![개인정보처리방침에 동의하는 확인 단추가 있는 Avere 제어판 및 팝업 창을 보여주는 스크린샷](media/avere-vfxt-privacy-policy.png)

1. 지원 구성 페이지에서 왼쪽에 있는 삼각형을 클릭 하 여 **고객 정보** 섹션을 엽니다.
1. **업로드 정보 유효성 재검사** 단추를 클릭합니다.
1. 클러스터의 지원 이름을 **고유한 클러스터 이름**으로 설정 합니다. 이 이름이 클러스터를 지원 담당자에 게 고유 하 게 식별 하는지 확인 합니다.
1. **통계 모니터링**, **일반 정보 업로드** 및 **크래시 정보 업로드** 확인란을 선택합니다.
1. **제출**을 클릭합니다.

   ![지원 설정 페이지의 완료된 고객 정보 섹션을 포함하는 스크린샷](media/avere-vfxt-support-info.png)

1. **SPS(Secure Proactive Support)** 왼쪽의 삼각형을 클릭하여 섹션을 확장합니다.
1. **SPS 링크 사용** 확인란을 선택합니다.
1. **제출**을 클릭합니다.

   ![지원 설정 페이지의 완료된 보안 주도적 지원 섹션을 포함하는 스크린샷](media/avere-vfxt-support-sps.png)

## <a name="next-steps"></a>다음 단계

클러스터에 온-프레미스 또는 기존 클라우드 저장소 시스템을 추가 해야 하는 경우 [저장소 구성](avere-vfxt-add-storage.md)의 지침을 따르세요.

클러스터에 클라이언트를 연결할 준비가 되었으면 [Avere vFXT 클러스터 탑재](avere-vfxt-mount-clients.md)를 읽어보세요.
