---
title: Power BI 앱 제안 게시 - Azure Marketplace | Microsoft Docs
description: Microsoft AppSource marketplace에서 Power BI 앱 제품을 게시 합니다.
services: Azure, AppSource, Marketplace, Cloud Partner Portal, Power BI
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: pbutlerm
ms.openlocfilehash: 0e82041dab1932ed7c129b598740b2441aa59315
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60725717"
---
# <a name="publish-a-power-bi-app-offer"></a>Power BI 앱 제품을 게시

마지막 단계, 클라우드 파트너 포털에서 제품을 정의 하 고 관련된 기술 자산을 만든 후 게시에 대 한 제품을 제출 하는 것입니다. 왼쪽된 창에서이 프로세스를 시작 하는 **새 제품** 창에서 **게시**합니다. 자세한 내용은 [Azure Marketplace 및 AppSource 제안 게시](../manage-offers/cpp-publish-offer.md)를 참조하세요.


## <a name="publishing-steps"></a>게시 단계

다음은 게시 프로세스의 주요 단계입니다.

![Power BI 앱에 대 한 게시 프로세스 단계 제공](./media/publishing-process-steps.png)

이 표에서 각 단계를 설명 하 고 예상된 완료 시간을 제공 합니다.

|   게시 단계            |   Time     |   설명                                                                  |
| --------------------         |------------| ----------------                                                               |
| 필수 구성 요소 유효성 검사       | 15분     | 제안 정보 및 제안 설정의 유효성이 검사됩니다.                            |
| 인증                | 1-7일   | Power BI 인증 팀은 제품을 분석합니다. 팀은 제공 된 설치 URL 통해 앱을 설치 하 여 수동 확인 테스트를 통해 Power BI 앱을 실행 합니다. 기본 유효성 검사 (이 문서의 뒷부분에서 설명) 앱 인증 프로세스의 일부로 수행 됩니다.         |
| 패키징                    | \< 1시간 미만  | 고객 사용에 대 한 제품의 기술 자산 패키지 됩니다.                        |
| 리드 생성 등록 | \< 1시간 미만  | 잠재 고객 시스템이 구성 및 배포됩니다.                                      |
| 게시자 확인            | \-         | 제품 라이브 전에 최종 검토 및 확인을 완료 합니다. 이제 귀하의 제품을 미리 보려면 링크를 해야 합니다. 미리 보기 표시 하는 방법에 만족할 선택 **Go Live** 에 **상태** 탭 합니다. 그러면 온 보 딩 팀 AppSource에 앱 목록에 요청을 보냅니다.    |
| 라이브                         | \< 3시간 미만 | 제품 이제 공개적으로 나열 됩니다 ("라이브"), AppSource에서 및 고객에 게 앱 보기를 업데이트 하 고 Power BI 구독에 배포할 수 있습니다. 또한 확인 전자 메일을 받게 됩니다. 오른쪽 열에 **모든 제품** 탭 모든 제품의 상태를 확인할 수 있습니다. 에 **상태** 탭 제품에 대 한 자세한 게시 흐름 상태를 확인할 수 있습니다. |
|   |   |

이 프로세스를 완료 하려면 최대 8 일 수 있습니다. Power BI 앱 제품에 표시 되는 다음 게시 단계를 완료 합니다 [AppSource](https://appsource.microsoft.com/marketplace/apps?product=power-bi%20) Power BI 앱 섹션입니다.


### <a name="app-certification-process"></a>앱 인증 프로세스

Microsoft 온 보 딩 팀이이 프로세스를 사용 하 여 Power BI 앱 제안을 제출의 유효성을 검사 하려면:

1. 법률 문서 및 도움말 링크를 검토 합니다.
2. 지원 연락처 정보를 확인 합니다.
3. 적절 한 설치를 확인 하려면 설치 관리자 URL을 사용 합니다.
4. 맬웨어 및 기타 악성 콘텐츠 용 앱을 검색 합니다.
5. 표시 된 앱의 설명과 일치 하는 것을 확인 합니다.
6. 앱 관련 작업 Power BI에서 예상 대로 작동 하는지 확인 합니다. 팀은 사용자 지정 데이터 원본에 연결, 데이터 및 등을 새로 고칩니다 샘플 데이터를 사용 하 여 보고서 및 대시보드를 엽니다.

문제가 발견되면 인증 팀에서 피드백을 제공합니다.  Power BI 앱 요구 사항에 대 한 자세한 내용은 참조는 [Power BI 앱 설명서](https://go.microsoft.com/fwlink/?linkid=2028636)합니다.


## <a name="next-steps"></a>다음 단계

앱에서 정기적으로 모니터링 하는 것이 좋습니다 합니다 [AppSource marketplace](https://appsource.microsoft.com)합니다.  사용 해야는 [Seller Insights](../../cloud-partner-portal-orig/si-getting-started.md) 기능의 [클라우드 파트너 포털](https://cloudpartner.azure.com/#insights) marketplace 고객 및 앱 사용에 대 한 통찰력을 제공 합니다. 마지막으로, 있습니다 [제품 업데이트](./cpp-update-existing-offer.md)합니다.
