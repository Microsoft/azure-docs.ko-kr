---
title: 포함 파일
description: 포함 파일
services: virtual-network
author: asudbring
ms.service: virtual-network
ms.topic: include
ms.date: 03/01/2020
ms.author: allensu
ms.custom: include file
ms.openlocfilehash: 81db46b52c9b4fe800f2fbfeadad966995d66e12
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "78244960"
---
## <a name="create-the-virtual-network"></a>가상 네트워크 만들기

이 섹션에서는 가상 네트워크 및 서브넷을 만듭니다.

1. 화면의 왼쪽 위에서 **리소스 만들기 > 네트워킹 > 가상 네트워크**를 차례로 선택하거나, 검색 상자에서 **가상 네트워크**를 검색합니다.

2. **가상 네트워크 만들기**의 **기본** 탭에서 다음 정보를 입력하거나 선택합니다.

    | **설정**          | **값**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **프로젝트 세부 정보**  |                                                                 |
    | Subscription     | Azure 구독 선택                                  |
    | 리소스 그룹   | **새로 만들기**를 선택하고, **\<resource-group-name>** 을 입력한 다음, 확인을 선택하거나 매개 변수를 기반으로 기존 **\<resource-group-name>** 을 선택합니다. |
    | **인스턴스 세부 정보** |                                                                 |
    | 속성             | **\<virtual-network-name>** 를 입력합니다.                                    |
    | 지역           | **\<region-name>** 선택 |

3. **IP 주소** 탭을 선택하거나 페이지 하단의 **다음: IP 주소** 단추를 선택합니다.

4. **IP 주소** 탭에서 다음 정보를 입력합니다.

    | 설정            | 값                      |
    |--------------------|----------------------------|
    | IPv4 주소 공간 | **\<IPv4-address-space>** 를 입력합니다. |

5. **서브넷 이름** 아래에서 **기본값**이라는 단어를 선택합니다.

6. **서브넷 편집**에서 다음 정보를 입력합니다.

    | 설정            | 값                      |
    |--------------------|----------------------------|
    | 서브넷 이름 | **\<subnet-name>** 를 입력합니다. |
    | 서브넷 주소 범위 | **\<subnet-address-range>** 를 입력합니다.

7. **저장**을 선택합니다.

8. **검토 + 만들기** 탭을 선택하거나 **검토 + 만들기** 단추를 선택합니다.

9. **만들기**를 선택합니다.