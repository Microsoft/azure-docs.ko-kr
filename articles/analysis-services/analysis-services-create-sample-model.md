---
title: 자습서 - 샘플 모델 추가 - Azure Analysis Services | Microsoft Docs
description: 이 자습서 단원에서는 Azure Analysis Services에서 샘플 모델을 추가하는 방법을 알아봅니다.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: tutorial
ms.date: 05/19/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 88ed58ea1eea80b4b890c31260fc65153377eaaa
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/22/2020
ms.locfileid: "83697510"
---
# <a name="tutorial-add-a-sample-model-from-the-portal"></a>자습서: 포털에서 샘플 모델 추가

이 자습서에서는 샘플 Adventure Works 표 형식 모델 데이터베이스를 서버에 추가합니다. 샘플 모델은 Adventure Works 인터넷 판매(1200) 샘플 데이터 모델의 완성된 버전입니다. 샘플 모델은 모델 관리를 테스트하고, 도구 및 클라이언트 애플리케이션과 연결하며, 모델 데이터를 쿼리하는 데 유용합니다. 이 자습서에서는 [Azure Portal](https://portal.azure.com) 및 SSMS([SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms))를 사용합니다. 

> [!div class="checklist"]
> * 서버에 완성된 샘플 표 형식 데이터 모델 추가 
> * SSMS로 모델에 연결

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="before-you-begin"></a>시작하기 전에

이 자습서를 완료하려면 다음이 필요합니다.

- Azure Analysis Services 서버. 자세한 내용은 [서버 만들기 - 포털](analysis-services-create-server.md)을 참조하세요.
- 서버 관리자 권한
- [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)


## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인

[포털](https://portal.azure.com/)에 로그인합니다.

## <a name="add-a-sample-model"></a>샘플 모델 추가

1. 서버 **개요**에서 **새 모델**을 클릭합니다.

    ![샘플 모델 만들기](./media/analysis-services-create-sample-model/aas-create-sample-new-model.png)

2. **새 모델** > **데이터 원본 선택**에서 **샘플 데이터**가 선택되어 있는지 확인한 다음, **추가**를 클릭합니다.

    ![샘플 데이터 선택](./media/analysis-services-create-sample-model/aas-create-sample-data.png)

3. **개요**에서 `adventureworks` 샘플 모델이 추가되었는지 확인합니다.

    ![샘플 데이터 선택](./media/analysis-services-create-sample-model/aas-create-sample-verify.png)


## <a name="clean-up-resources"></a>리소스 정리

샘플 모델은 캐시 메모리 리소스를 사용합니다. 따라서 샘플 모델을 테스트하는 데 사용하지 않는 경우 서버에서 이 모델을 제거해야 합니다.

다음 단계에서는 SSMS를 사용하여 서버에서 모델을 삭제하는 방법을 설명합니다.

1. SSMS > **개체 탐색기**에서 **연결** > **Analysis Services**를 클릭합니다.

2. **서버에 연결**에서 서버 이름을 붙여넣고, **인증**에서 **Active Directory - MFA 지원을 통한 유니버설 인증**을 선택하고, 사용자 이름을 입력한 다음, **연결**을 클릭합니다.

    ![로그인](./media/analysis-services-create-sample-model/aas-create-sample-cleanup-signin.png)

3. **개체 탐색기**에서 `adventureworks` 샘플 데이터베이스를 마우스 오른쪽 단추로 클릭한 다음, **삭제**를 클릭합니다.

    ![샘플 데이터베이스 삭제](./media/analysis-services-create-sample-model/aas-create-sample-cleanup-delete.png)

## <a name="next-steps"></a>다음 단계 

이 자습서에서는 서버에 기본, 샘플 모델을 추가하는 방법을 배웠습니다. 이제 모델 데이터베이스가 있으므로 SQL Server Management Studio에서 연결하고 사용자 역할을 추가할 수 있습니다. 자세한 내용은 다음 자습서로 계속 진행하세요.

> [!div class="nextstepaction"]
> [자습서: 서버 관리자 및 사용자 역할 구성](tutorials/analysis-services-tutorial-roles.md)


