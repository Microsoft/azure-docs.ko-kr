---
title: Microsoft 위협 모델링 도구 - Azure | Microsoft Docs
description: 위협 모델링 도구에서 사용할 수 있는 모든 기능에 대해 알아보기
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
ms.openlocfilehash: 601f3bf05388406c8f96a7351f7fb3aa4de2650a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60588802"
---
# <a name="threat-modeling-tool-feature-overview"></a>위협 모델링 도구 기능 개요

위협 모델링 도구는 위협 모델링 요구에 도움이 됩니다. 도구에 대한 기본적인 소개는 [위협 모델링 도구 시작](./azure-security-threat-modeling-tool-getting-started.md)을 참조하세요.

> [!NOTE]
>위협 모델링 도구는 자주 업데이트되므로 이 가이드를 자주 확인하여 최신 기능 및 향상된 기능을 확인하세요.

빈 페이지를 열려면 **모델 만들기**를 선택합니다.

![빈 페이지](./media/azure-security-threat-modeling-tool-feature-overview/tmtstart.png)

현재 이 도구에서 사용할 수 있는 모든 기능을 확인하려면 [시작](./azure-security-threat-modeling-tool-getting-started.md) 예제에서 당사 팀이 만든 위협 모델을 사용합니다.

![기본 위협 모델](./media/azure-security-threat-modeling-tool-feature-overview/basictmt.png)

## <a name="navigation"></a>탐색

기본 제공 기능을 알아보기 전에 도구에 있는 주요 구성 요소를 검토해 보겠습니다.

### <a name="menu-items"></a>메뉴 항목

환경은 다른 Microsoft 제품과 비슷합니다. 최상위 메뉴 항목을 검토해 보겠습니다.

![메뉴 항목](./media/azure-security-threat-modeling-tool-feature-overview/menuitems.png)

| 레이블                               | 세부 정보      |
| --------------------------------------- | ------------ |
| **파일** | <ul><li>파일 열기, 저장 및 닫기</li><li>OneDrive 계정에 로그인 및 로그아웃합니다.</li><li>링크를 공유합니다(보기 및 편집).</li><li>파일 정보를 봅니다.</li><li>기존 모델에 새 템플릿을 적용합니다.</li></ul> |
| **편집** | 작업을 실행 취소 및 다시 실행하고, 복사하고, 붙여넣고, 삭제합니다. |
| **보기** | <ul><li>**분석** 및 **디자인** 보기 사이를 전환합니다.</li><li>닫힌 창을(예: 스텐실, 요소 속성 및 메시지) 엽니다.</li><li>레이아웃을 기본 설정으로 다시 설정합니다.</li></ul> |
| **다이어그램** | 다이어그램을 추가하고 삭제하며, 다이어그램의 탭을 통해 이동합니다. |
| **보고서** | HTML 보고서를 만들어 다른 사용자와 공유합니다. |
| **도움말** | 도구를 사용할 수 있도록 지침을 찾습니다. |

아이콘은 최상위 메뉴에 대한 바로 가기입니다.

| 기호                               | 세부 정보      |
| --------------------------------------- | ------------ |
| **열기** | 새 파일을 엽니다. |
| **저장** | 현재 파일을 저장합니다. |
| **디자인** | 모델을 만들 수 있는 **디자인** 보기를 엽니다. |
| **분석** | 생성된 위협과 그 속성을 표시합니다. |
| **다이어그램 추가** | 새 다이어그램을 추가합니다(Excel의 새 탭과 유사). |
| **다이어그램 삭제** | 현재 다이어그램을 삭제합니다. |
| **복사/잘라내기/붙여넣기** | 요소를 복사하고, 잘라내고, 붙여넣습니다. |
| **실행 취소/다시 실행** | 작업을 실행 취소하고 다시 실행합니다. |
| **확대/축소** | 잘 볼 수 있도록 다이어그램을 확대하고 축소합니다. |
| **사용자 의견** | MSDN 포럼을 엽니다. |

### <a name="canvas"></a>캔버스

캔버스는 요소를 끌어서 놓는 공간입니다. 끌어서 놓기는 모델을 작성하는 신속하고 가장 효율적인 방법입니다. 그림과 같이 마우스 오른쪽 단추로 클릭하여 메뉴에서 항목을 선택하여 요소의 일반 버전을 추가할 수도 있습니다.

#### <a name="drop-the-stencil-on-the-canvas"></a>캔버스에 스텐실 놓기

![캔버스 놓기](./media/azure-security-threat-modeling-tool-feature-overview/canvasdrop1.png)

#### <a name="select-the-stencil"></a>스텐실 선택

![요소 속성](./media/azure-security-threat-modeling-tool-feature-overview/canvasdrop2.png)

### <a name="stencils"></a>스텐실

선택한 템플릿에 따라 사용 가능한 모든 스텐실을 찾을 수 있습니다. 적합한 요소를 찾을 수 없는 경우 다른 템플릿을 사용합니다. 또는 필요에 맞게 템플릿을 수정할 수 있습니다. 일반적으로 다음과 같은 범주의 조합을 찾을 수 있습니다.

| 스텐실 이름                               | 세부 정보      |
| --------------------------------------- | ------------ |
| **프로세스** | 애플리케이션, 브라우저 플러그 인, 스레드, 가상 머신 |
| **외부 작용** | 인증 공급자, 브라우저, 사용자, 웹 애플리케이션 |
| **데이터 저장소** | 캐시, 저장소, 구성 파일, 데이터베이스, 레지스트리 |
| **데이터 흐름** | 이진, ALPC, HTTP, HTTPS/TLS/SSL, IOCTL, IPSec, 명명된 파이프, RPC/DCOM, SMB, UDP |
| **신뢰 선/경계** | 회사 네트워크, 인터넷, 컴퓨터, 샌드박스, 사용자/커널 모드 |

### <a name="notesmessages"></a>참고 사항/메시지

| 구성 요소                               | 세부 정보      |
| --------------------------------------- | ------------ |
| **메시지** | 요소 간 데이터 흐름 없음과 같은 오류가 발생할 때마다 사용자에게 알리는 내부 도구 논리입니다. |
| **참고 사항** | 수동 참고 사항은 디자인 및 검토 프로세스에 걸쳐 엔지니어링 팀에 의해 파일에 추가됩니다. |

### <a name="element-properties"></a>요소 속성

요소 속성은 선택한 요소에 따라 다릅니다. 신뢰 경계 외에도 다른 모든 요소에는 세 가지 일반 선택 항목이 포함되어 있습니다.

| 요소 속성                               | 세부 정보      |
| --------------------------------------- | ------------ |
| **Name** | 프로세스, 저장소, 인자 및 흐름을 쉽게 인식하도록 이름을 지정하는 데 유용합니다. |
| **범위 외** | 선택한 경우 요소는 위협 생성 매트릭스에서 제거됩니다(권장하지 않음). |
| **범위 외에 대한 이유** | 범위 외가 선택된 이유를 사용자에게 알리는 맞춤 필드입니다. |

속성은 각 요소 범주 아래에서 변경됩니다. 각 요소를 선택하여 사용 가능한 옵션을 검사합니다. 또는 템플릿을 열어 자세히 알아볼 수 있습니다. 기능을 검토해 보겠습니다.

## <a name="welcome-screen"></a>시작 화면

앱을 열면 **시작** 화면이 표시됩니다.

### <a name="open-a-model"></a>모델 열기

**모델 열기** 위로 마우스를 가져가 두 옵션, **이 컴퓨터에서 열기** 및 **OneDrive에서 열기**를 표시합니다. 첫 번째 옵션은 **파일 열기** 화면이 열립니다. 두 번째 옵션은 OneDrive에 대한 로그인 프로세스로 이동합니다. 인증이 성공하면 파일과 폴더를 선택할 수 있습니다.

![모델 열기](./media/azure-security-threat-modeling-tool-feature-overview/openmodel.png)

![컴퓨터 또는 OneDrive에서 열기](./media/azure-security-threat-modeling-tool-feature-overview/openmodel2.png)

### <a name="feedback-suggestions-and-issues"></a>피드백, 제안 및 문제

**피드백, 제안 및 문제**를 선택하는 경우 SDL 도구에 관한 MSDN 포럼으로 이동합니다. 해결 방법 및 새로운 아이디어를 포함하여 도구에 대한 다른 사용자의 의견을 읽을 수 있습니다.

![사용자 의견](./media/azure-security-threat-modeling-tool-feature-overview/feedback.png)

## <a name="design-view"></a>디자인 보기

새 모델을 열거나 만들 때 **디자인** 보기가 열립니다.

### <a name="add-elements"></a>요소 추가

두 가지 방법으로 그리드에서 요소를 추가할 수 있습니다.

- **끌어서 놓기**: 원하는 요소를 그리드로 끌어옵니다. 그런 다음 요소 속성을 사용하여 추가 정보를 제공합니다.
- **마우스 오른쪽 단추로 클릭**: 그리드의 아무 곳을 마우스 오른쪽 단추로 클릭하여 드롭다운 메뉴에서 항목을 선택합니다. 선택한 요소의 일반적인 표현이 화면에 표시됩니다.

### <a name="connect-elements"></a>요소 연결

두 가지 방법으로 요소를 연결할 수 있습니다.

- **끌어서 놓기**: 원하는 데이터 흐름을 그리드로 끌어오고 양쪽 끝을 적절한 요소에 연결합니다.
- **클릭 + Shift**: 첫 번째 요소(데이터 전송)를 클릭하고, Shift 키를 누른 다음 두 번째 요소(데이터 수신)를 선택합니다. 마우스 오른쪽 단추로 클릭하고 **연결**을 선택합니다. 양방향 데이터 흐름을 사용하는 경우 순서는 중요하지 않습니다.

### <a name="properties"></a>properties

 스텐실에서 수정할 수 있는 속성을 보려면 스텐실을 선택하면 그에 따른 정보가 채워집니다. 아래 예제에서는 **데이터베이스** 스텐실을 다이어그램으로 끌어오기 전과 후를 보여줍니다.

#### <a name="before"></a>이전

![이전](./media/azure-security-threat-modeling-tool-feature-overview/properties1.png)

#### <a name="after"></a>이후

![이후](./media/azure-security-threat-modeling-tool-feature-overview/properties2.png)

### <a name="messages"></a>메시지

위협 모델을 만들고 데이터 흐름을 요소에 연결하는 것을 잊은 경우 알림이 나타납니다. 메시지를 무시하거나 지침을 따라 문제를 해결할 수 있습니다. 

![메시지](./media/azure-security-threat-modeling-tool-feature-overview/messages.png)

### <a name="notes"></a>메모

다이어그램에 메모를 추가하려면 **메시지** 탭에서 **메모** 탭으로 전환합니다.

## <a name="analysis-view"></a>분석 보기

다이어그램을 구성한 후, 바로 가기 도구 모음에서 **분석** 기호(돋보기)를 선택하여 **분석** 보기로 전환합니다.

![분석 보기](./media/azure-security-threat-modeling-tool-feature-overview/analysisview.png)

### <a name="generated-threat-selection"></a>생성된 위협 선택

위협을 선택하면 세 가지 고유 함수를 사용할 수 있습니다.

| 기능                               | 정보      |
| --------------------------------------- | ------------ |
| **읽음 표시기** | <p>위협이 읽음으로 표시되며, 검토한 항목을 계속 추적할 수 있습니다.</p><p>![읽음/읽지 않음 표시기](./media/azure-security-threat-modeling-tool-feature-overview/readmode.png)</p> |
| **상호 작용 포커스** | <p>위협에 속한 다이어그램의 상호 작용이 강조 표시됩니다.</p><p>![상호 작용 포커스](./media/azure-security-threat-modeling-tool-feature-overview/interactionfocus.png)</p> |
| **위협 속성** | <p>위협에 대한 추가 정보가 **위협 속성** 창에 나타납니다.</p><p>![위협 속성](./media/azure-security-threat-modeling-tool-feature-overview/threatproperties.png)</p> |

### <a name="priority-change"></a>우선 순위 변경

생성된 각 위협의 우선 순위 수준을 변경할 수 있습니다. 서로 다른 색상을 통해 높은, 중간, 낮은 우선 순위 위협을 쉽게 구분할 수 있습니다.

![우선 순위 변경](./media/azure-security-threat-modeling-tool-feature-overview/prioritychange.png)

### <a name="threat-properties-editable-fields"></a>위협 속성 편집 가능한 필드

이전 그림처럼 도구를 통해 생성된 정보를 변경할 수 있습니다. 맞춤 등 특정 필드에 정보를 추가할 수도 있습니다. 이러한 필드는 템플릿에서 생성됩니다. 각 위협에 대한 자세한 정보가 필요한 경우 수정할 수 있습니다.

![위협 속성](./media/azure-security-threat-modeling-tool-feature-overview/threatproperties.png)

## <a name="reports"></a>보고서

생성된 각 위협 상태의 우선 순위 변경 및 업데이트가 완료되면 파일을 저장하거나 보고서를 출력할 수 있습니다. **보고서** > **전체 보고서 만들기**로 이동합니다. 보고서에 이름을 지정하면 다음 이미지와 비슷하게 표시됩니다.

![보고서](./media/azure-security-threat-modeling-tool-feature-overview/report.png)

## <a name="next-steps"></a>다음 단계

- 질문, 의견 및 문제를 tmtextsupport@microsoft.com에 보냅니다. 시작하려면 위협 모델링 도구를 **[다운로드](https://aka.ms/threatmodelingtool)** 하세요.
- 커뮤니티에 템플릿을 제공하려면 [GitHub](https://github.com/Microsoft/threat-modeling-templates) 페이지로 이동하세요.