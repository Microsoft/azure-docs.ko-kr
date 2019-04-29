---
title: Azure에서 지역 인공 지능 Virtual Machine 프로비전 - Azure | Microsoft Docs
description: 지역 AI Data Science Virtual Machine을 만들고 구성하는 방법을 알아봅니다. 지역 AI Data Science Virtual Machine은 지리적 데이터를 사용하여 AI 및 ML 솔루션을 만들 수 있는 도구를 제공합니다.
keywords: 심화 학습, AI, 데이터 과학 도구, 데이터 과학 가상 머신, 지리 공간적 분석
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.custom: seodec18
ms.assetid: ''
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/05/2018
ms.author: gokuma
ms.openlocfilehash: dde9b71c3615a592f8c08e040c5e9ba7bc756bd6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60578546"
---
# <a name="provision-a-geo-artificial-intelligence-virtual-machine-on-azure"></a>Azure에서 지역 인공 지능 Virtual Machine 프로비전 

지역 AI 데이터 과학 Virtual Machine(Geo-DSVM)은 널리 사용되는 [Azure 데이터 과학 Virtual Machine](https://aka.ms/dsvm)의 확장으로써 AI와 지리 공간적 분석을 결합하도록 특별하게 구성됩니다. VM의 지리 공간적 분석은 [ArcGIS Pro](https://www.arcgis.com/features/index.html)에 기반합니다. 데이터 과학 VM을 사용하면 기계 학습 모델을 신속하게 학습하고, 지리적 정보를 제공하는 데이터에 대해 심층적으로 학습할 수 있습니다. Windows 2016 DSVM에서만 지원됩니다. 

Geo-DSVM에는 다음을 비롯하여 AI에 대한 몇 가지 도구가 포함됩니다.

- Microsoft Cognitive Toolkit, TensorFlow, Keras, Caffe2, Chainer 등 널리 사용되는 심층 학습 프레임워크의 GPU 버전 
- 이미지 획득 및 전처리 도구, 텍스트 데이터, 
- Microsoft R Server Developer Edition, Anaconda Python, Python 및 R용 Jupyter Notebooks, Python 및 R용 IDE, SQL 데이터베이스 등 개발 활동을 위한 도구
- AI 애플리케이션에서 지리 공간적 데이터로 작업할 수 있는 Python 및 R 인터페이스를 사용하는 ESRI의 ArcGIS Pro 데스크톱 소프트웨어 
 

## <a name="create-your-geo-ai-data-science-vm"></a>지역 AI 데이터 과학 VM 만들기

지역 AI 데이터 과학 VM의 인스턴스를 만드는 절차는 다음과 같습니다. 


1. [Azure 포털](https://ms.portal.azure.com/#create/microsoft-ads.geodsvmwindows)에서 가상 머신 목록으로 이동합니다.
2. 아래쪽에 있는 **만들기** 단추를 선택하여 마법사로 이동합니다.
![create-geo-ai-dsvm](./media/provision-geo-ai-dsvm/Create-Geo-AI.png)
3. Geo-DSVM을 만드는 데 사용되는 마법사에서 이 그림의 오른쪽에 열거된 **네 단계** 각각에 대한 **입력**이 필요합니다. 다음은 이러한 각 단계를 구성하는 데 필요한 입력입니다.



   - **기본 사항**

      1. **이름**: 만들려는 데이터 과학 서버 이름입니다.

      2. **사용자 이름**: 관리자 계정 로그인 ID

      3. **암호**: 관리자 계정 암호

      4. **구독**: 둘 이상의 구독이 있는 경우 머신을 만들고 요금을 청구할 구독을 선택합니다.

      5. **리소스 그룹**: 새 그룹을 만들거나 구독에서 **비어 있는** 기존 Azure 리소스 그룹을 사용할 수 있습니다.

      6. **위치**: 가장 적합한 데이터 센터를 선택합니다. 이는 대개 대부분의 데이터가 저장되어 있거나 네트워크에 가장 빠르게 액세스하기 위해 물리적 위치와 가장 가까이 있는 데이터 센터입니다. GPU에 대한 심층 학습이 필요한 경우 NC 시리즈 GPU VM 인스턴스를 포함하는 Azure 위치 중 하나를 선택해야 합니다. 현재 GPU VM이 있는 위치는 다음과 같습니다. **미국 동부, 미국 중북부, 미국 중남부, 미국 서부 2, 유럽 북부, 유럽 서부**. 최신 목록은 [지역별 Azure 제품 페이지](https://azure.microsoft.com/regions/services/)를 확인하고 **Compute** 아래에서 **NC 시리즈**를 찾으세요. 


   - **설정**: Geo DSVM에서 GPU에 대한 딥러닝을 실행하려는 경우 NC 시리즈 GPU 가상 머신 크기 중 하나를 선택합니다. 그렇지 않으면, CPU 기반 인스턴스 중 하나를 선택할 수 있습니다.  VM에 대한 저장소 계정을 만듭니다. 
   
   - **요약**: 입력한 모든 정보가 올바른지 확인합니다.

   - **구입**: **구입**을 클릭하여 프로비전을 시작합니다. 서비스 사용 약관에 대한 링크가 제공됩니다. VM은 **크기** 단계에서 선택한 서버 크기에 대한 계산 이외에 추가 요금이 발생하지 않습니다. 
 
>[!NOTE]
> 프로비전에는 20~30분 정도가 걸립니다. 프로비전의 상태는 Azure 포털에 표시됩니다.

 
## <a name="how-to-access-the-geo-ai-data-science-virtual-machine"></a>지역 AI 데이터 과학 Virtual Machine에 액세스하는 방법

 VM이 만들어지면 여기에 설치 및 사전 구성되는 도구를 사용하여 시작할 준비가 되었습니다. 여러 도구에 대한 시작 메뉴 타일 및 데스크톱 아이콘이 있습니다. 이전의 **기본 사항** 섹션에서 구성한 관리자 계정 자격 증명을 사용하여 원격 데스크톱으로 액세스할 수 있습니다. 

 
## <a name="using-arcgis-pro-installed-in-the-vm"></a>VM에 설치된 ArcGIS Pro 사용

Geo-DSVM에는 ArcGIS Pro 데스크톱이 미리 설치되어 있고 DSVM의 모든 도구와 함께 작동하도록 환경이 미리 구성되어 있습니다. ArcGIS를 시작할 때 ArcGIS 계정에 로그인하라는 메시지가 표시됩니다. ArcGIS 계정이 이미 있고 소프트웨어에 대한 라이선스가 있는 경우 기존 자격 증명을 사용할 수 있습니다.  

![Arc-GIS-Logon](./media/provision-geo-ai-dsvm/ArcGISLogon.png)

그렇지 않으면, 새 ArcGIS 계정 및 라이선스에 등록하고 [평가판](https://www.arcgis.com/features/free-trial.html)을 사용할 수 있습니다. 

![ArcGIS-Free-Trial](./media/provision-geo-ai-dsvm/ArcGIS-Free-Trial.png)

중 하나를 유료 또는 무료 평가판 ArcGIS 계정에 대 한 등록 권한을 부여할 수 있습니다 ArcGIS Pro 사용자 계정에 대 한의 지침에 따라 [Getting Started with ArcGIS Pro](https://www.esri.com/library/brochures/getting-started-with-arcgis-pro.pdf)합니다. 

ArcGIS 계정을 사용하여 ArcGIS Pro 데스크톱에 로그인하면 지리 공간적 분석 및 기계 학습 프로젝트를 위해 VM에 설치되고 구성되는 데이터 과학 도구를 사용할 준비가 됩니다.

## <a name="next-steps"></a>다음 단계

다음 항목의 지침을 따라 지역 AI 데이터 과학 VM을 사용하기 시작합니다.

* [지역 AI 데이터 과학 VM 사용](use-geo-ai-dsvm.md)
