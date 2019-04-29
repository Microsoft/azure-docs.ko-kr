---
title: Excel을 사용하여 Azure Analysis Services에 연결 | Microsoft Docs
description: Excel을 사용하여 Azure Analysis Services 서버에 연결하는 방법에 대해 알아봅니다.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 5c46d4e4d23744cf07ccf7857a33990bf405a6a1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61023275"
---
# <a name="connect-with-excel"></a>Excel로 연결

서버를 만들고 테이블 형식의 모델을 배포하면 클라이언트가 데이터에 연결하여 탐색을 시작할 수 있습니다. 

## <a name="before-you-begin"></a>시작하기 전에

로그인에 사용하는 계정은 읽기 이상의 권한이 있는 모델 데이터베이스 역할에 속해야 합니다. 자세한 내용은 [인증 및 사용자 권한](analysis-services-manage-users.md)을 참조하세요. 

## <a name="connect-in-excel"></a>Excel에서 연결

Excel 2016 이상에서 Get Data를 사용하여 Excel에서 서버에 연결합니다. 파워 피벗에서 테이블 가져오기 마법사를 사용하여 연결하는 작업은 지원되지 않습니다. 

1. Excel의 **데이터** 리본 메뉴에서 **외부 데이터 가져오기** > **다른 원본에서** > **Analysis Services에서**를 클릭합니다.

2. 데이터 연결 마법사에서 **서버 이름**에 프로토콜 및 URI를 포함하여 서버 이름을 입력합니다. 예를 들어 asazure://westcentralus.asazure.windows.net/advworks 같이 입력합니다. 그런 다음 **로그온 자격 증명**에서 **다음 사용자 이름 및 암호 사용**을 선택하고 조직 사용자 이름(예: nancy@adventureworks.com 및 암호)을 입력합니다.

    > [!IMPORTANT]
    > Microsoft 계정, Live ID, Yahoo, Gmail 등을 사용하여 로그인하려고 하거나 다단계 인증을 사용하여 로그인해야 하는 경우 암호 필드를 비워 둡니다. 다음을 클릭하면 암호를 묻는 메시지가 표시됩니다. 

    ![Excel 로그온에서 연결](./media/analysis-services-connect-excel/aas-connect-excel-logon.png)

3. **데이터베이스 및 테이블 선택**에서 데이터베이스, 모델 또는 큐브 뷰를 선택한 다음 **마침**을 클릭합니다.
   
    ![Excel 선택 모델에서 연결](./media/analysis-services-connect-excel/aas-connect-excel-select.png)


## <a name="see-also"></a>참고 항목

[클라이언트 라이브러리](analysis-services-data-providers.md)   
[서버 관리](analysis-services-manage.md)     


