---
title: Python 및 Jupyter 노트북을 사용 하 여 데이터 과학을 교육 하는 랩 설정 | Microsoft Docs
description: Python 및 Jupyter 노트북을 사용 하 여 데이터 과학을 학습 하도록 랩을 설정 하는 방법을 알아봅니다.
services: lab-services
documentationcenter: na
author: emaher
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/19/2019
ms.author: enewman
ms.openlocfilehash: 695b87540178fb5c1012a095c2a86937a189ef24
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77133740"
---
# <a name="set-up-a-lab-to-teach-data-science-with-python-and-jupyter-notebooks"></a>Python 및 Jupyter 노트북을 사용 하 여 데이터 과학을 교육 하는 랩 설정

이 문서에서는 학생 들에 게 [Jupyter 노트북](http://jupyter-notebook.readthedocs.io)을 사용 하는 방법을 학습 하는 데 필요한 도구를 사용 하 여 랩 서비스에서 템플릿 컴퓨터를 설정 하는 방법을 설명 합니다.  Jupyter 노트북은 노트북 이라는 단일 캔버스에서 서식 있는 텍스트 및 실행 가능한 [Python](https://www.python.org/) 소스 코드를 쉽게 결합할 수 있는 오픈 소스 프로젝트입니다.  노트북을 실행 하면 입력 및 출력의 선형 레코드가 생성 됩니다.  이러한 출력에는 텍스트, 정보 테이블, 산 점도 등이 포함 될 수 있습니다.

## <a name="lab-configuration"></a>랩 구성

이 랩을 설정 하려면 시작 하려면 Azure 구독 및 랩 계정이 필요 합니다. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다. Azure 구독을 받은 후 Azure Lab Services에서 새 랩 계정을 만들 수 있습니다. 새 랩 계정을 만드는 방법에 대 한 자세한 내용은 자습서를 참조 [하 여 랩 계정 설정](tutorial-setup-lab-account.md)을 참조 하세요.  기존 랩 계정을 사용할 수도 있습니다.

### <a name="lab-account-settings"></a>랩 계정 설정

랩 계정에 대해 아래 표에 설명 된 설정을 사용 하도록 설정 합니다. Marketplace 이미지를 사용 하도록 설정 하는 방법에 대 한 자세한 내용은 [랩 작성자에 게 제공 되는 marketplace 이미지 지정](how-to-configure-lab-accounts.md#specify-marketplace-images-available-to-lab-creators)을 참조 하세요.

| 랩 계정 설정 | Instructions |
| ------------------- | ------------ |
| Marketplace 이미지 | 랩 계정 내에서 사용할 [Data Science Virtual Machine Windows 2016](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-windows) 이미지를 사용 하도록 설정 합니다. |

>[!TIP]
>이 문서에서는 Windows Server 운영 체제를 사용 하는 템플릿 컴퓨터를 구성 하는 방법을 집중적으로 설명 합니다.  Azure Marketplace에서 [linux 용 Data Science Virtual Machine (CentOS)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.linux-data-science-vm) 또는 [linux (Ubuntu) 이미지 Data Science Virtual Machine](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.linux-data-science-vm-ubuntu) 를 사용 하 여 Python 및 Jupyter 노트북을 사용 하 여 데이터 과학 클래스를 설정할 수도 있습니다.

### <a name="lab-settings"></a>랩 설정

클래스 룸 랩을 설정 하는 경우 아래 표의 설정을 사용 합니다.  교실 랩을 만드는 방법에 대 한 자세한 내용은 [교실 랩 설정 자습서](tutorial-setup-classroom-lab.md)를 참조 하세요.

| 랩 설정 | 값/지침 |
| ------------ | ------------------ |
|Virtual Machine 크기| 소형 GPU (계산). 이 크기는 인공 지능 및 심층 학습 같은 계산 집약적인 응용 프로그램 및 네트워크 집약적인 응용 프로그램에 가장 적합 합니다. |
|가상 컴퓨터 이미지| Data Science Virtual Machine-Windows 2016|

## <a name="template-machine"></a>템플릿 컴퓨터

[Data Science Virtual Machine-Windows 2016](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-windows) 이미지는 이러한 종류의 클래스에 필요한 심층 학습 프레임 워크 및 도구를 제공 합니다.  이미지에는 Jupyter 노트북 및 Visual Studio Code 포함 되어 있습니다.  [Jupyter 노트북](http://jupyter-notebook.readthedocs.io) 은 데이터 과학자에서 원시 데이터를 가져오고, 계산을 실행 하 고, 동일한 환경에서 결과를 모두 볼 수 있도록 하는 웹 응용 프로그램입니다.  템플릿 컴퓨터의 경우 웹 응용 프로그램이 로컬로 실행 됩니다.  [Visual Studio Code](https://code.visualstudio.com/) 은 노트북을 작성 하 고 테스트할 때 풍부한 대화형 환경을 제공 하는 IDE입니다.  자세한 내용은 [Visual Studio Code에서 Jupyter 노트북 작업](https://code.visualstudio.com/docs/python/jupyter-support)을 참조 하세요.

클래스를 설정 하는 나머지 작업은 로컬 노트북을 제공 하는 것입니다.  Azure Machine Learning 샘플을 사용 하는 방법에 대 한 지침은 [Jupyter 노트북을 사용 하 여 환경을 구성 하는 방법](../../machine-learning/how-to-configure-environment.md#jupyter)을 참조 하세요.  템플릿 컴퓨터에서 자신의 전자 필기장을 제공할 수도 있습니다.  템플릿이 게시 될 때 노트북은 모든 학생 컴퓨터에 복사 됩니다.

## <a name="cost-estimate"></a>예상 비용

이 클래스에 대해 가능한 예상 비용을 살펴보겠습니다.  25 명의 학생 클래스를 사용 합니다.  예약 된 클래스 시간은 20 시간입니다.  또한 각 학생은 예약 된 클래스 시간 외에도 과제 또는 배정에 대해 10 시간 할당량을 얻습니다.  선택한 가상 컴퓨터 크기는 작은 gpu (계산) 이며 랩 단위는 139입니다.

이 클래스에 대 한 예상 비용 예상 예는 다음과 같습니다.

25 명의 학생 \* (20 개의 예약 된 시간 + 10 할당량 시간) \* 139 lab 단위 \* 0.01 USD/시간 = 1042.5 USD

가격 책정에 대 한 추가 세부 정보는 [Azure Lab Services 가격 책정](https://azure.microsoft.com/pricing/details/lab-services/)을 참조 하세요.

## <a name="conclusion"></a>결론

이 문서에서는 Jupyter 노트북 클래스의 랩을 만드는 단계를 살펴보았습니다. 다른 기계 학습 클래스에 대해 유사한 설정을 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

다음 단계는 랩을 설정 하는 데 일반적입니다.

- [템플릿 만들기 및 관리](how-to-create-manage-template.md)
- [사용자 추가](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [할당량 설정](how-to-configure-student-usage.md#set-quotas-for-users)
- [일정 설정](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [학생에 대 한 전자 메일 등록 링크](how-to-configure-student-usage.md#send-invitations-to-users)
