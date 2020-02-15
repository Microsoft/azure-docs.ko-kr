---
title: Azure Lab Services를 사용 하 여 심층 학습에 집중 된 랩 설정 Microsoft Docs
description: Linux에서 셸 스크립팅을 학습 하도록 랩을 설정 하는 방법에 대해 알아봅니다.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/30/2019
ms.author: spelluru
ms.openlocfilehash: 21a11863bd2bc9882d4336f5c5d2bf1d7b7f49da
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77133758"
---
# <a name="set-up-a-lab-focused-on-deep-learning-in-natural-language-processing-using-azure-lab-services"></a>Azure Lab Services를 사용 하 여 자연어 처리의 심층 학습에 초점을 맞춘 랩 설정
이 문서에서는 Azure Lab Services를 사용 하 여 NLP (자연어 처리)의 심층 학습에 초점을 맞춘 랩을 설정 하는 방법을 보여 줍니다. NLP(자연어 처리)는 번역, 음성 인식 및 기타 언어 이해 기능을 갖춘 컴퓨터를 가능케하는 AI(인공 지능) 형태입니다.  

NLP 클래스를 수강하는 학생은 Linux VM(가상 머신)을 통해 작성된 인간 언어 분석에 사용되는 딥 러닝 모델을 개발하는 신경망 알고리즘을 적용하는 방법을 알아봅니다. 

## <a name="lab-configuration"></a>랩 구성
이 랩을 설정 하려면 시작 하려면 Azure 구독이 필요 합니다. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다. Azure 구독이 있으면 Azure Lab Services에서 새 랩 계정을 만들거나 기존 랩 계정을 사용할 수 있습니다. 새 랩 계정 만들기에 대 한 자습서: [랩 계정 설정 자습서](tutorial-setup-lab-account.md)를 참조 하세요.
 
랩 계정을 만든 후 랩 계정에서 다음 설정을 사용 하도록 설정 합니다. 

| 랩 계정 설정 | Instructions |
| ----------- | ------------ |  
| Marketplace 이미지 | 랩 계정 내에서 사용할 Ubuntu (Linux 용 Data Science Virtual Machine) 이미지를 사용 하도록 설정 합니다.  지침: [랩 작성자에 게 제공 되는 마켓플레이스 이미지 지정](how-to-configure-lab-accounts.md#specify-marketplace-images-available-to-lab-creators)문서를 참조 하세요. | 

[이 자습서](tutorial-setup-classroom-lab.md) 에 따라 새 랩을 만들고 다음 설정을 적용 합니다.

| 랩 설정 | 값/지침 | 
| ------------ | ------------------ |
| VM (가상 머신) 크기 | 소형 GPU (계산). 이 크기는 인공 지능 및 심층 학습 같은 계산 집약적인 응용 프로그램 및 네트워크 집약적인 응용 프로그램에 가장 적합 합니다. |
| VM 이미지 | [Linux (Ubuntu)의 Data Science Virtual Machine](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.linux-data-science-vm-ubuntu)입니다. 이 이미지는 기계 학습 및 데이터 과학을 위한 심층 학습 프레임 워크 및 도구를 제공 합니다. 이 이미지에 설치 된 도구에 대 한 전체 목록을 보려면 [DSVM에 포함 된 항목](../../machine-learning/data-science-virtual-machine/overview.md#whats-included-on-the-dsvm)을 참조 하세요. |
| 원격 데스크톱 연결 사용 | 사용. <p>이 설정을 사용 하면 교사와 학생이 RDP (원격 데스크톱)를 사용 하 여 해당 Virtual Machines (VM)에 연결할 수 있습니다.</p><p>**중요**: RDP는 Linux 이미지에 대 한 Data Science Virtual Machine에 이미 설치 되 고 구성 되어 있습니다. 따라서 교사/학생은 추가 단계 없이 RDP를 통해 Vm에 연결할 수 있습니다. 또한 그래픽 데스크톱에 연결 해야 하는 경우이 이미지에는 가상 머신에 [X2Go 서버가](https://wiki.x2go.org/doku.php/doc:newtox2go) 이미 설치 되어 있습니다. 학생은 로컬 컴퓨터에 X2Go client를 설치 해야 하며 연결에 클라이언트를 사용 해야 합니다. 자세한 내용은 다음 가이드를 참조 하세요. <ul><li>[Linux에 대 한 Data Science Virtual Machine 액세스 하는 방법](../../machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)</li><li>[템플릿 VM에 연결 하 여 RDP 및 GUI 패키지 설치](how-to-enable-remote-desktop-linux.md#connect-to-the-template-vm)</li></ul></p>   |

Linux 이미지에 대 한 Data Science Virtual Machine는이 클래스 형식에 필요한 심층 학습 프레임 워크 및 도구를 제공 합니다. 따라서 템플릿 컴퓨터를 만든 후에는이를 추가로 사용자 지정할 필요가 없습니다. 학생 들이 사용할 수 있도록 게시할 수 있습니다. 템플릿 페이지에서 **게시** 단추를 선택 하 여 랩에 템플릿을 게시 합니다.  

## <a name="cost"></a>비용
이 랩의 비용을 예측 하려는 경우 다음 예제를 사용할 수 있습니다. 

20 시간의 예약 된 클래스 시간 및 숙제 또는 할당에 대 한 10 시간의 할당량을 포함 하는 25 명의 학생의 경우 랩 가격은-25 학생 * (20 + 10) 시간 * 139 랩 단위 * 0.01 USD/시간 = 1042.5 USD입니다.

가격 책정에 대 한 추가 세부 정보는 [Azure Lab Services 가격 책정](https://azure.microsoft.com/pricing/details/lab-services/)을 참조 하세요.

## <a name="conclusion"></a>결론
이 문서에서는 자연어 처리 클래스에 대 한 랩을 만드는 단계를 안내 합니다. 다른 심층 학습 클래스에 대해 유사한 설정을 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계
다음 단계는 모든 랩을 설정 하는 데 일반적입니다.

- [사용자 추가](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [할당량 설정](how-to-configure-student-usage.md#set-quotas-for-users)
- [일정 설정](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab) 
- [학생에 대 한 전자 메일 등록 링크](how-to-configure-student-usage.md#send-invitations-to-users). 

