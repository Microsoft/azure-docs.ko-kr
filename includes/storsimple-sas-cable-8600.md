---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 51e1fd18b52d7e215ba43be540156199fb41778e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60482733"
---
#### <a name="to-attach-the-sas-cables"></a>SAS 케이블을 연결하려면
1. 기본 인클로저와 EBOD 인클로저를 찾아냅니다. 두 엔클로저는 해당 백플레인을 보고 식별할 수 있습니다. 지침은 다음 이미지를 참조하세요. 
   
    ![기본 엔클로저 및 EBOD 엔클로저 백플레인](./media/storsimple-sas-cable-8600/HCSBackplaneofprimaryandEBODenclosure.png)
   
    **기본 엔클로저 및 EBOD 엔클로저의 뒷면 보기**
   
   | 레이블 | 설명 |
   |:--- |:--- |
   | 1 |기본 인클로저 |
   | 2 |EBOD 인클로저 |
2. 기본 엔클로저 및 EBOD 엔클로저에서 일련번호를 찾습니다. 일련 번호 스티커는 각 엔클로저의 뒷면 귀에 붙어 있습니다. 일련 번호는 두 인클로저 모두에서 동일해야 합니다. [Microsoft 지원에 문의](../articles/storsimple/storsimple-contact-microsoft-support.md) 하세요. 다음 그림을 참조하여 일련 번호를 찾습니다.
   
    ![일련 번호가 표시된 엔클로저의 뒷면 보기](./media/storsimple-sas-cable-8600/HCSRearviewofenclosureindicatinglocationofserialnumbersticker.png)
   
    **일련 번호 스티커의 위치**
   
   | 레이블 | 설명 |
   |:--- |:--- |
   | 1 |인클로저의 귀 |
3. 제공된 SAS 케이블을 사용하여 다음과 같이 EBOD 엔클로저를 기본 엔클로저에 연결합니다.
   
   1. 기본 엔클로저 및 EBOD 엔클로저에서 4개의 SAS 포트를 식별합니다. 아래 SAS 케이블 연결 그림과 같이 SAS 포트는 기본 엔클로저에서 EBOD로 레이블이 지정되며 EBOD 엔클로저의 포트 A에 해당합니다.
   2. 제공된 SAS 케이블을 사용하여 EBOD 포트를 포트 A에 연결합니다.
   3. 컨트롤러 0의 EBOD 포트를 EBOD 컨트롤러 0의 포트 A에 연결해야 합니다. 컨트롤러 1의 EBOD 포트를 EBOD 컨트롤러 1의 포트 A에 연결해야 합니다. 지침은 다음 그림을 참조하세요. 
      
      ![디바이스에 대한 SAS 케이블 연결](./media/storsimple-sas-cable-8600/HCSSAScablingforyourdevice.png)
      
      **SAS 케이블 연결**
      
      | 레이블 | 설명 |
      |:--- |:--- |
      | 문자열(UTF-8 형식) 또는 |기본 인클로저 |
      | b |EBOD 인클로저 |
      | 1 |컨트롤러 0 |
      | 2 |컨트롤러 1 |
      | 3 |EBOD 컨트롤러 0 |
      | 4 |EBOD 컨트롤러 1 |
      | 5, 6 |기본 인클로저(EBOD 레이블)에서 SAS 포트 |
      | 7, 8 |EBOD 엔클로저의 SAS 포트(포트 A) |

