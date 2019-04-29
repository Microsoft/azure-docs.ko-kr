---
title: 딥러닝 Data Science Virtual Machine 만들기
titleSuffix: Azure
description: 분석 및 기계 학습을 수행하기 위해 Azure에서 심층 학습 데이터 과학 Virtual Machine을 구성하고 만듭니다.
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.custom: seodec18
ms.assetid: e1467c0f-497b-48f7-96a0-7f806a7bec0b
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.devlang: na
ms.topic: conceptual
ms.date: 03/16/2018
ms.author: gokuma
ms.openlocfilehash: 42430c847149f7eda2f0dbed1cff006a92f372ee
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60578608"
---
# <a name="provision-a-deep-learning-virtual-machine-on-azure"></a>Azure에서 심층 학습 Virtual Machine 프로비전 

DLVM(딥 러닝 Virtual Machine)은 심층 학습 모델을 빠르게 학습하기 위해 GPU 기반 VM 인스턴스를 더 쉽게 사용할 수 있도록 특별히 구성된 [DSVM(데이터 과학 Virtual Machine)](https://aka.ms/dsvm) 변형입니다. Windows 2016 또는 Ubuntu DSVM을 기반으로 하여 지원됩니다. DLVM은 동일한 코어 VM 이미지를 공유하므로 DSVM에서 사용할 수 있는 풍부한 도구 집합을 모두 공유합니다. 

DLVM에는 몇 가지 AI용 도구가 포함되어 있습니다. 즉 인기 있는 심층 학습 프레임워크의 GPU 버전(예: Microsoft Cognitive Toolkit, TensorFlow, Keras, Caffe2, Chainer), 이미지와 텍스트 데이터를 획득하고 전처리하는 도구, 데이터 과학 모델링 및 개발 작업용 도구(예: Microsoft R Server Developer Edition, Anaconda Python, Python 및 R용 Jupyter 노트북, Python 및 R용 IDE, SQL 데이터베이스 및 기타 여러 데이터 과학) 및 ML 도구 등입니다. 

## <a name="create-your-deep-learning-virtual-machine"></a>심층 학습 Virtual Machine 만들기
심층 학습 Virtual Machine의 인스턴스를 만드는 단계는 다음과 같습니다. 

1. [Azure 포털](https://portal.azure.com/#create/microsoft-ads.dsvm-deep-learningtoolkit
)에서 가상 머신 목록으로 이동합니다.
2. 아래쪽에 있는 **만들기** 단추를 선택하여 ![create-dlvm](./media/dlvm-provision-wizard.PNG) 마법사로 이동합니다.
3. DLVM을 만드는 데 사용되는 마법사에서 이 그림의 오른쪽에 열거된 **네 단계** 각각에 대한 **입력**이 필요합니다. 다음은 이러한 각 단계를 구성하는 데 필요한 입력입니다.
   
   1. **기본 사항**
      
      1. **이름**: 만들려는 데이터 과학 서버 이름
      2. **딥러닝 VM의 OS 유형 선택**: Windows 또는 Linux 선택(Windows 2016 및 Ubuntu Linux 기반 DSVM용)
      2. **사용자 이름**: 관리자 계정 로그인 ID
      3. **암호**: 관리자 계정 암호
      4. **구독**: 둘 이상의 구독이 있는 경우 머신을 만들고 요금을 청구할 구독을 선택합니다.
      5. **리소스 그룹**: 새 그룹을 만들거나 구독에서 **비어 있는** 기존 Azure 리소스 그룹을 사용할 수 있습니다.
      6. **위치**: 가장 적합한 데이터 센터를 선택합니다. 이는 대개 대부분의 데이터가 저장되어 있거나 네트워크에 가장 빠르게 액세스하기 위해 물리적 위치와 가장 가까이 있는 데이터 센터입니다. 
      
> [!NOTE]
> DLVM은 모든 NC 및 ND 시리즈 GPU VM 인스턴스를 지원합니다. DLVM을 프로비전할 때 Azure에서 GPU가 있는 위치 중 하나를 선택해야 합니다. [지역별 Azure 제품](https://azure.microsoft.com/regions/services/) 페이지에서 사용 가능한 위치를 확인하고 **계산** 아래에서 **NC 시리즈**, **NCv2 시리즈**, **NCv3 시리즈** 또는 **ND 시리즈**를 찾습니다. 

1. **설정**: 기능 요구 사항 및 비용 제약 조건을 충족하는 NC 시리즈(NC, NCv2, NCv3) 또는 ND 시리즈 GPU 가상 머신 크기 중 하나를 선택합니다. VM에 대한 저장소 계정을 만듭니다.  ![dlvm-settings](./media/dlvm-provision-step-2.PNG)
   
1. **요약**: 입력한 모든 정보가 올바른지 확인합니다.
1. **구입**: **구입**을 클릭하여 프로비전을 시작합니다. 트랜잭션의 조건에는 링크가 제공됩니다. VM은 **크기** 단계에서 선택한 서버 크기에 대한 계산 이외에 추가 요금이 발생하지 않습니다. 

> [!NOTE]
> 프로비전은 약 10-20분 정도 소요됩니다. 프로비전의 상태는 Azure 포털에 표시됩니다.
> 


## <a name="how-to-access-the-deep-learning-virtual-machine"></a>심층 학습 Virtual Machine에 액세스하는 방법

### <a name="windows-edition"></a>Windows 버전
VM이 만들어지면 이전의 **기본 사항** 섹션에서 구성한 관리자 계정 자격 증명을 사용하여 원격 데스크톱으로 액세스할 수 있습니다. 

### <a name="linux-edition"></a>Linux 버전

VM을 만든 후 SSH를 사용하여 해당 VM에 로그인할 수 있습니다. 3단계의 **기본 사항** 섹션에서 만든 계정 자격 증명을 텍스트 셸 인터페이스용으로 사용합니다. Windows 클라이언트에서는 [Putty](https://www.putty.org)와 같은 SSH 클라이언트 도구를 다운로드할 수 있습니다. 그래픽 데스크톱(X Windows 시스템)을 사용하려는 경우 Putty에서 X11 전달을 사용하거나 X2Go 클라이언트를 설치할 수 있습니다.

> [!NOTE]
> 테스트한 결과 X2Go 클라이언트의 성능이 X11 전달보다 더 우수했습니다. 그래픽 데스크톱 인터페이스에서는 X2Go 클라이언트를 사용하는 것이 좋습니다.
> 
> 

#### <a name="installing-and-configuring-x2go-client"></a>X2Go 클라이언트 설치 및 구성
Linux DLVM은 이미 X2Go 서버를 통해 프로비전되었고 클라이언트 연결을 허용할 준비가 되었습니다. Linux VM 그래픽 데스크톱에 연결하려면 클라이언트에서 다음 절차를 완료합니다.

1. 사용 중인 클라이언트 플랫폼용 X2Go 클라이언트를 [X2Go](https://wiki.x2go.org/doku.php/doc:installation:x2goclient)에서 다운로드하여 설치합니다.    
2. X2Go 클라이언트를 실행하고 **새 세션**을 선택합니다. 여러 탭이 있는 구성 창이 열립니다. 다음 구성 매개 변수를 입력합니다.
   * **세션 탭**:
     * **호스트**: Linux Data Science VM의 호스트 이름 또는 IP 주소
     * **로그인**: Linux VM의 사용자 이름
     * **SSH 포트**: 기본값 22를 그대로 사용합니다.
     * **세션 유형**: 값을 **XFCE**로 변경합니다. Linux DSVM은 현재 XFCE 데스크톱만 지원합니다.
   * **미디어 탭**: 사운드 지원 및 클라이언트 인쇄를 사용하지 않으려면 해제할 수 있습니다.
   * **공유 폴더**: 클라이언트 머신의 디렉터리를 Linux VM에 탑재하려면 이 탭에서 VM과 공유하려는 클라이언트 머신 디렉터리를 추가합니다.

X2Go 클라이언트를 통해 XFCE 그래픽 데스크톱 또는 SSH 클라이언트를 사용하여 VM에 로그인하고 나면 VM에 설치 및 구성된 도구를 사용할 수 있습니다. XFCE에는 다양한 도구에 대한 애플리케이션 메뉴 바로 가기와 바탕 화면 아이콘이 표시됩니다.

VM이 만들어지고 프로비전되면 여기에 설치 및 구성되는 도구를 사용하여 시작할 준비가 되었습니다. 여러 도구에 대한 시작 메뉴 타일 및 데스크톱 아이콘이 있습니다. 
