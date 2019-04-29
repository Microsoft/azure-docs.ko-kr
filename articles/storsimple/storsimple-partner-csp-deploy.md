---
title: Microsoft Azure StorSimple 및 클라우드 솔루션 공급자 프로그램 개요 | Microsoft Docs
description: StorSimple 파트너용 StorSimple 및 CSP에 대한 개요입니다.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/08/2017
ms.author: alkohli
ms.openlocfilehash: 0dac86a696599a391cb243ad11e16931e00b8921
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60629991"
---
# <a name="deploy-storsimple-virtual-array-for-cloud-solution-provider-program"></a>클라우드 솔루션 공급자 프로그램용 StorSimple 가상 배열 배포

## <a name="overview"></a>개요

고객에게 CSP(클라우드 솔루션 공급자) 파트너를 통해 StorSimple 가상 배열을 배포할 수 있습니다. CSP 파트너는 StorSimple 디바이스 관리자 서비스를 만들 수 있습니다. StorSimple 가상 배열 및 연결된 공유, 볼륨 및 백업을 배포하고 관리하는 데 이 서비스를 사용할 수 있습니다.

이 문서에서는 CSP 파트너가 고객을 추가하거나 기존 고객에 새 구독을 추가한 다음 CSP에서 StorSimple 가상 배열을 배포하는 서비스를 만들 수 있는 방법을 설명합니다.

## <a name="prerequisites"></a>필수 조건

시작하기 전에 다음 사항을 확인합니다.

- CSP 프로그램에서 등록됩니다.
- 올바른 [파트너 센터](https://partnercenter.microsoft.com/) 로그인 자격 증명입니다. 자격 증명을 사용하여 파트너 포털에 로그인하여 새 고객을 추가하고 고객을 검색하거나 파트너 대시보드에서 고객 계정으로 이동할 수 있습니다. CSP는 Azure Portal에서 고객을 대신하여 StorSimple 관리자로 작동할 수 있습니다.
                             
## <a name="add-a-customer"></a>고객 추가

고객을 추가하는 경우 구독이 자동으로 만들어집니다. 고객을 추가하려면(및 구독을 자동으로 만들려면) 파트너 포털에서 다음 단계를 수행합니다.

1. [파트너 센터](https://partnercenter.microsoft.com/)로 이동하고 CSP 자격 증명을 사용하여 로그인합니다. **대시보드**를 클릭합니다.

     ![파트너 센터의 대시보드](./media/storsimple-partner-csp-deploy/image1.png)
                              
2. 왼쪽 창에서 **고객**을 클릭합니다. 오른쪽 창에서 **고객 추가**를 클릭합니다. 고객의 세부 정보를 입력합니다. **다음: 구독** 고객 구독을 만듭니다.

    ![고객 추가](./media/storsimple-partner-csp-deploy/image2.png)

3.  **Microsoft Azure** 제품을 선택합니다. 페이지의 아래쪽으로 스크롤하고 **검토**를 클릭합니다.

    ![구독 정보 검토](./media/storsimple-partner-csp-deploy/image3.png)
                              
4. 정보를 검토하고 **제출**을 클릭합니다.

    ![구독 제출](./media/storsimple-partner-csp-deploy/image4.png)

5. 나중에 참조할 수 있도록 확인 정보를 저장합니다.

    ![확인 저장](./media/storsimple-partner-csp-deploy/image5.png)

6. 방금 추가한 고객을 찾거나 고객으로 이동합니다. **회사 이름**을 클릭하여 세부 정보로 드릴다운합니다.

    ![고객 검색](./media/storsimple-partner-csp-deploy/image6.png)  

7. 왼쪽 창에서 **서비스 관리**를 선택합니다. 오른쪽 창의 **서비스 관리** 아래에서 **Microsoft Azure 관리 포털**을 클릭하여 고객에 대한 Azure 관리자로 로그인합니다.

    ![Azure Portal에 로그인](./media/storsimple-partner-csp-deploy/image9.png)

8. StorSimple 디바이스 관리자를 만들려면 **+ 새로 만들기**를 클릭하고 **StorSimple 가상 디바이스 시리즈**를 검색하거나 이동합니다. 자세한 내용은 [StorSimple 디바이스 관리자 서비스 배포](storsimple-virtual-array-manage-service.md)로 이동하세요.

    ![StorSimple 디바이스 관리자 서비스 만들기](./media/storsimple-partner-csp-deploy/image8.png)


## <a name="add-a-subscription"></a>구독 추가

일부 경우에는 기존 고객이 있을 수 있으며 구독을 추가해야 합니다. 기존 고객에 구독을 추가하려면 파트너 포털에서 다음 단계를 수행합니다.

1. [파트너 센터](https://partnercenter.microsoft.com/)로 이동하고 CSP 자격 증명을 사용하여 로그인합니다. **대시보드**를 클릭합니다.

     ![파트너 센터의 대시보드](./media/storsimple-partner-csp-deploy/image1.png)
                              
2. 왼쪽 창에서 **고객**을 클릭합니다. 구독을 추가하려는 고객을 찾거나 고객으로 이동합니다. ![확인 아이콘 확장](./media/storsimple-partner-csp-deploy/expand_pane_icon.png) 아이콘을 클릭하여 고객의 회사 이름에 대한 행을 확장합니다. 세부 정보에서 **구독 추가**를 클릭합니다.

    ![고객](./media/storsimple-partner-csp-deploy/image10.png)

3. 구독에서 **주요 제안**에 대해 **Microsoft Azure**를 선택하고 **제출**을 클릭합니다. 그러면 새 구독이 생성됩니다.

    ![새 구독 추가](./media/storsimple-partner-csp-deploy/image11.png)

6. 새 구독을 만든 후 왼쪽 창에서 **<-- 고객**을 클릭하여 **고객** 페이지로 돌아갑니다. 방금 구독을 만든 고객을 검색합니다. **회사 이름**을 클릭하여 세부 정보로 드릴다운합니다.

    ![고객 검색](./media/storsimple-partner-csp-deploy/image6.png)  

7. 왼쪽 창에서 **서비스 관리**를 선택합니다. 오른쪽 창의 **서비스 관리** 아래에서 **Microsoft Azure 관리 포털**을 클릭하여 고객에 대한 Azure 관리자로 로그인합니다.

    ![Azure Portal에 로그인](./media/storsimple-partner-csp-deploy/image9.png)

8. StorSimple 디바이스 관리자를 만들려면 **+ 새로 만들기**를 클릭하고 **StorSimple 가상 디바이스 시리즈**를 검색하거나 이동합니다. 자세한 내용은 [StorSimple 디바이스 관리자 서비스 배포](storsimple-virtual-array-manage-service.md)로 이동하세요.

    ![StorSimple 디바이스 관리자 서비스 만들기](./media/storsimple-partner-csp-deploy/image8.png)

## <a name="next-steps"></a>다음 단계

- CSP에서 StorSimple에 대 한 추가 질문을 해야 하는 경우 이동 [CSP의 StorSimple. 질문과 대답](storsimple-partner-csp-faq.md)을 참조하세요.
- StorSimple을 배포할 준비가 되면 [CSP에서 StorSimple 배포](storsimple-partner-csp-deploy.md)로 이동합니다.
