---
title: Linux 살펴보기
titleSuffix: Azure Data Science Virtual Machine
description: Linux Data Science Virtual Machine를 사용 하 여 몇 가지 일반적인 데이터 과학 작업을 완료 하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 07/16/2018
ms.openlocfilehash: 529e188d1a4ee00cee7f3d023ab45a48dd0d3c5f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75428382"
---
# <a name="data-science-with-a-linux-data-science-virtual-machine-in-azure"></a>Azure에서 Linux Data Science Virtual Machine를 사용 하는 데이터 과학

이 연습에서는 Linux Data Science Virtual Machine (DSVM)를 사용 하 여 몇 가지 일반적인 데이터 과학 작업을 완료 하는 방법을 보여 줍니다. Linux DSVM은 Azure에서 사용할 수 있는 가상 머신 이미지로, 데이터 분석 및 기계 학습에 일반적으로 사용 되는 도구 모음에 미리 설치 되어 있습니다. 핵심 소프트웨어 구성 요소는 [Linux Data Science Virtual Machine 프로 비전](linux-dsvm-intro.md)에서 항목별로 제공 됩니다. DSVM 이미지를 사용 하면 각 도구를 개별적으로 설치 하 고 구성할 필요 없이 몇 분 내에 데이터 과학을 쉽게 시작할 수 있습니다. 필요한 경우 DSVM을 쉽게 확장할 수 있으며 사용 하지 않을 때 중지할 수 있습니다. DSVM 리소스는 탄력적이 고 비용 효율성이 높습니다.

이 연습에서 설명 하는 데이터 과학 작업은 [팀 데이터 과학 프로세스 란?](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/overview) 에 설명 된 단계를 따릅니다. 팀 데이터 과학 프로세스는 데이터 과학자 팀이 지능형 응용 프로그램을 빌드하는 수명 주기를 효율적으로 공동 작업 하는 데 도움이 되는 데이터 과학에 대 한 체계적인 접근 방식입니다. 또한 데이터 과학 프로세스는 개별 사용자가 수행할 수 있는 데이터 과학을 위한 반복되는 프레임워크를 제공합니다.

이 연습에서는 [spambase](https://archive.ics.uci.edu/ml/datasets/spambase) 데이터 집합을 분석 합니다. Spambase는 스팸 또는 ham (스팸이 아님)로 표시 된 전자 메일 집합입니다. Spambase에도 전자 메일의 내용에 대 한 일부 통계가 포함 되어 있습니다. 이 연습의 뒷부분에서 통계에 대해 설명 합니다.

## <a name="prerequisites"></a>필수 조건

Linux DSVM을 사용 하려면 먼저 다음과 같은 필수 구성 요소가 있어야 합니다.

* **Azure 구독**. Azure 구독을 얻으려면 [지금 무료 azure 계정 만들기](https://azure.microsoft.com/free/)를 참조 하세요.
* [**Linux Data Science Virtual Machine**](https://azure.microsoft.com/marketplace/partners/microsoft-ads/linux-data-science-vm). 가상 컴퓨터를 프로 비전 하는 방법에 대 한 자세한 내용은 [Linux Data Science Virtual Machine 프로 비전](linux-dsvm-intro.md)을 참조 하세요.
* [**X2Go**](https://wiki.x2go.org/doku.php) 는 열려 있는 XFCE 세션을 사용 하 여 컴퓨터에 설치 됩니다. 자세한 내용은 [Install and configure The X2Go client](linux-dsvm-intro.md#x2go)을 참조 하세요.
* 더 부드러운 스크롤 환경을 위해 DSVM의 Firefox 웹 브라우저에서 `about:config`의 `gfx.xrender.enabled` 플래그를 전환 합니다. [자세히 알아보기](https://www.reddit.com/r/firefox/comments/4nfmvp/ff_47_unbearable_slow_over_remote_x11/). 또한 `False``mousewheel.enable_pixel_scrolling`을 설정 하는 것이 좋습니다. [자세히 알아보기](https://support.mozilla.org/questions/981140).
* **Azure Machine Learning 계정**. 계정이 아직 없는 경우 [Azure Machine Learning 홈 페이지](https://azure.microsoft.com/free/services/machine-learning//)에서 새 계정을 등록 합니다.

## <a name="download-the-spambase-dataset"></a>spambase 데이터 세트 다운로드

[Spambase](https://archive.ics.uci.edu/ml/datasets/spambase) 데이터 집합은 4601 예를 포함 하는 비교적 작은 데이터 집합입니다. 데이터 집합은 리소스 요구 사항을 별로 유지 하므로 DSVM의 몇 가지 주요 기능을 보여 줄 수 있는 편리한 크기입니다.

> [!NOTE]
> 이 연습은 D2 v2 size Linux DSVM (CentOS Edition)을 사용 하 여 만들었습니다. 이 크기의 DSVM을 사용 하 여이 연습에서 설명 하는 절차를 완료할 수 있습니다.

저장소 공간이 더 필요한 경우 추가 디스크를 만들어 DSVM에 연결할 수 있습니다. 디스크는 영구 Azure storage를 사용 하므로 서버를 크기 조정으로 인해 다시 프로 비전 하거나 종료 하는 경우에도 해당 데이터가 보존 됩니다. 디스크를 추가 하 여 DSVM에 연결 하려면 [LINUX VM에 디스크 추가](../../virtual-machines/linux/add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)의 단계를 완료 합니다. 디스크를 추가 하는 단계는 DSVM에 이미 설치 되어 있는 Azure CLI를 사용 합니다. DSVM 자체에서 전체 단계를 완료할 수 있습니다. 저장소를 늘리는 또 다른 옵션은 [Azure Files](../../storage/files/storage-how-to-use-files-linux.md)를 사용 하는 것입니다.

데이터를 다운로드 하려면 터미널 창을 열고 다음 명령을 실행 합니다.

    wget https://archive.ics.uci.edu/ml/machine-learning-databases/spambase/spambase.data

다운로드 한 파일에 머리글 행이 없습니다. 헤더를 포함 하는 다른 파일을 만들어 보겠습니다. 다음 명령을 실행하여 적합한 머리글이 있는 파일을 만듭니다.

    echo 'word_freq_make, word_freq_address, word_freq_all, word_freq_3d,word_freq_our, word_freq_over, word_freq_remove, word_freq_internet,word_freq_order, word_freq_mail, word_freq_receive, word_freq_will,word_freq_people, word_freq_report, word_freq_addresses, word_freq_free,word_freq_business, word_freq_email, word_freq_you, word_freq_credit,word_freq_your, word_freq_font, word_freq_000, word_freq_money,word_freq_hp, word_freq_hpl, word_freq_george, word_freq_650, word_freq_lab,word_freq_labs, word_freq_telnet, word_freq_857, word_freq_data,word_freq_415, word_freq_85, word_freq_technology, word_freq_1999,word_freq_parts, word_freq_pm, word_freq_direct, word_freq_cs, word_freq_meeting,word_freq_original, word_freq_project, word_freq_re, word_freq_edu,word_freq_table, word_freq_conference, char_freq_semicolon, char_freq_leftParen,char_freq_leftBracket, char_freq_exclamation, char_freq_dollar, char_freq_pound, capital_run_length_average,capital_run_length_longest, capital_run_length_total, spam' > headers

그런 다음 두 파일을 함께 연결 합니다.

    cat spambase.data >> headers
    mv headers spambaseHeaders.data

데이터 집합에는 각 전자 메일에 대 한 여러 유형의 통계가 있습니다.

* Word **\_freq\__word_**  와 같은 열은 전자 메일에서 *단어*와 일치 하는 단어의 비율을 표시 합니다. 예를 들어 **word\_freq\_** 이 **1**이면 전자 메일에 있는 모든 단어의 1%가 *설정*된 것입니다.
* Char **\_freq\__char_**  와 같은 열은 전자 메일에서 *char*인 모든 문자의 비율을 표시 합니다.
* **capital\_run\_length\_longest**는 대문자 시퀀스의 가장 긴 길이입니다.
* **capital\_run\_length\_average**는 모든 대문자 시퀀스의 평균 길이입니다.
* **capital\_run\_length\_total**는 모든 대문자 시퀀스의 총 길이입니다.
* **spam** 은 메일이 스팸으로 간주되는지 여부를 나타냅니다(1 = 스팸, 0 = 스팸이 아님).

## <a name="explore-the-dataset-by-using-r-open"></a>R Open을 사용 하 여 데이터 집합 탐색

R을 사용 하 여 데이터를 검토 하 고 몇 가지 기본 기계 학습을 수행 해 보겠습니다. DSVM은 [Microsoft R Open](https://mran.revolutionanalytics.com/open/) 이 미리 설치 된 상태로 제공 됩니다. 사전 설치 된 R 버전의 다중 스레드 수학 라이브러리는 단일 스레드 버전 보다 더 나은 성능을 제공 합니다. R Open은 CRAN 패키지 리포지토리의 스냅숏을 통해 재현 가능성도 제공 합니다.

이 연습에서 사용 되는 코드 샘플의 복사본을 가져오려면 git를 사용 하 여 Azure 기계 학습-데이터 과학 리포지토리를 복제 합니다. Git은 DSVM에 사전 설치 됩니다. Git 명령줄에서 다음을 실행 합니다.

    git clone https://github.com/Azure/Azure-MachineLearning-DataScience.git

터미널 창을 열고 R 대화형 콘솔에서 새 R 세션을 시작 합니다. DSVM에 사전 설치 된 RStudio를 사용할 수도 있습니다.

데이터를 가져오고 환경을 설정 하려면 다음을 수행 합니다.

    data <- read.csv("spambaseHeaders.data")
    set.seed(123)

각 열에 대한 요약 통계를 보려면

    summary(data)

데이터를 다른 보기로 보려면

    str(data)

이 보기에는 각 변수의 형식과 데이터 집합의 처음 몇 개 값이 표시 됩니다.

**spam** 열은 정수로 읽었지만 실제로는 범주 변수(또는 요소)입니다. 해당 형식을 설정하려면

    data$spam <- as.factor(data$spam)

일부 예비 분석을 수행 하려면 DSVM에 사전 설치 된 R에 대해 인기 있는 그래프 라이브러리인 [ggplot2](https://ggplot2.tidyverse.org/) 패키지를 사용 합니다. 앞에 표시 된 요약 데이터를 기준으로 느낌표의 빈도에 대 한 요약 통계가 있습니다. 다음 명령을 실행 하 여 이러한 빈도를 표시 해 보겠습니다.

    library(ggplot2)
    ggplot(data) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)

0 막대는 플롯을 기울이기 때문에 제거 하겠습니다.

    email_with_exclamation = data[data$char_freq_exclamation > 0, ]
    ggplot(email_with_exclamation) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)

매우 흥미로운 밀도가 있습니다. 해당 데이터만 살펴보겠습니다.

    ggplot(data[data$char_freq_exclamation > 1, ]) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)

그런 다음 스팸 및 ham로 분할 합니다.

    ggplot(data[data$char_freq_exclamation > 1, ], aes(x=char_freq_exclamation)) +
    geom_density(lty=3) +
    geom_density(aes(fill=spam, colour=spam), alpha=0.55) +
    xlab("spam") +
    ggtitle("Distribution of spam \nby frequency of !") +
    labs(fill="spam", y="Density")

이러한 예는 비슷한 플롯을 만들고 다른 열의 데이터를 탐색 하는 데 도움이 됩니다.

## <a name="train-and-test-a-machine-learning-model"></a>기계 학습 모델 학습 및 테스트

몇 가지 기계 학습 모델을 학습 하 여 데이터 집합의 전자 메일을 스팸 또는 ham 포함 하는 것으로 분류 해 보겠습니다. 이 섹션에서는 의사 결정 트리 모델 및 임의 포리스트 모델을 학습 합니다. 그런 다음 예측의 정확도를 테스트 합니다.

> [!NOTE]
> 다음 코드에 사용 된 *rpart* (재귀적 분할 및 회귀 트리) 패키지는 dsvm에 이미 설치 되어 있습니다.

먼저 데이터 집합을 학습 집합과 테스트 집합으로 분할 하겠습니다.

    rnd <- runif(dim(data)[1])
    trainSet = subset(data, rnd <= 0.7)
    testSet = subset(data, rnd > 0.7)

그런 다음 전자 메일을 분류 하는 의사 결정 트리를 만듭니다.

    require(rpart)
    model.rpart <- rpart(spam ~ ., method = "class", data = trainSet)
    plot(model.rpart)
    text(model.rpart)

결과:

![만든 의사 결정 트리의 다이어그램](./media/linux-dsvm-walkthrough/decision-tree.png)

학습 집합에 대해 얼마나 잘 수행하는지를 확인하려면 다음 코드를 사용합니다.

    trainSetPred <- predict(model.rpart, newdata = trainSet, type = "class")
    t <- table(`Actual Class` = trainSet$spam, `Predicted Class` = trainSetPred)
    accuracy <- sum(diag(t))/sum(t)
    accuracy

테스트 집합에 대해 얼마나 잘 수행하는지를 확인하려면

    testSetPred <- predict(model.rpart, newdata = testSet, type = "class")
    t <- table(`Actual Class` = testSet$spam, `Predicted Class` = testSetPred)
    accuracy <- sum(diag(t))/sum(t)
    accuracy

임의 포리스트 모델도 살펴보겠습니다. 임의 포리스트는 수많은 의사 결정 트리를 학습 하 고 모든 개별 의사 결정 트리에서 분류의 모드인 클래스를 출력 합니다. It는 의사 결정 트리 모델의 경향이 학습 데이터 집합을 초과 하기 때문에 보다 강력한 기계 학습 방법을 제공 합니다.

    require(randomForest)
    trainVars <- setdiff(colnames(data), 'spam')
    model.rf <- randomForest(x=trainSet[, trainVars], y=trainSet$spam)

    trainSetPred <- predict(model.rf, newdata = trainSet[, trainVars], type = "class")
    table(`Actual Class` = trainSet$spam, `Predicted Class` = trainSetPred)

    testSetPred <- predict(model.rf, newdata = testSet[, trainVars], type = "class")
    t <- table(`Actual Class` = testSet$spam, `Predicted Class` = testSetPred)
    accuracy <- sum(diag(t))/sum(t)
    accuracy


## <a name="deploy-a-model-to-azure-machine-learning-studio-classic"></a>Azure Machine Learning Studio에 모델 배포 (클래식)

[Azure Machine Learning Studio (클래식)](https://studio.azureml.net/) 은 예측 분석 모델을 쉽게 빌드하고 배포할 수 있도록 하는 클라우드 서비스입니다. Azure Machine Learning Studio (클래식)의 유용한 기능은 R 함수를 웹 서비스로 게시 하는 기능입니다. Azure Machine Learning Studio (클래식) R 패키지를 사용 하 여 DSVM의 R 세션에서 바로 배포를 쉽게 수행할 수 있습니다.

이전 섹션에서 의사 결정 트리 코드를 배포 하려면 Azure Machine Learning Studio (클래식)에 로그인 합니다. 로그인할 작업 영역 ID와 권한 부여 토큰이 필요합니다. 이러한 값을 찾고 Azure Machine Learning 변수를 초기화 하려면 다음 단계를 완료 합니다.

1. 왼쪽 메뉴에서 **설정**을 선택 합니다. **작업 영역 ID**의 값을 확인 합니다.

   ![Azure Machine Learning Studio (클래식) 작업 영역 ID](./media/linux-dsvm-walkthrough/workspace-id.png)

1. **권한 부여 토큰** 탭을 선택 합니다. **기본 권한 부여 토큰**에 대 한 값을 확인 합니다.

   ![Azure Machine Learning Studio (클래식) 기본 권한 부여 토큰](./media/linux-dsvm-walkthrough/workspace-token.png)
1. **AzureML** 패키지를 로드 한 다음 Dsvm의 R 세션에서 토큰 및 작업 영역 ID를 사용 하 여 변수 값을 설정 합니다.

        if(!require("AzureML")) install.packages("AzureML")
        require(AzureML)
        wsAuth = "<authorization-token>"
        wsID = "<workspace-id>"

1. 이 데모를 보다 쉽게 구현할 수 있도록 모델을 단순화해 보겠습니다. 루트와 가장 가까운 의사 결정 트리에서 세 개의 변수를 선택 하 고 다음 세 변수만 사용 하 여 새 트리를 작성 합니다.

        colNames <- c("char_freq_dollar", "word_freq_remove", "word_freq_hp", "spam")
        smallTrainSet <- trainSet[, colNames]
        smallTestSet <- testSet[, colNames]
        model.rpart <- rpart(spam ~ ., method = "class", data = smallTrainSet)

1. 입력으로 기능을 사용하고 예측된 값을 반환 하는 예측 함수가 필요합니다.

        predictSpam <- function(newdata) {
        predictDF <- predict(model.rpart, newdata = newdata)
        return(colnames(predictDF)[apply(predictDF, 1, which.max)])
        }


1. **Predictspam** 함수를 사용 하 여 AzureML에 **predictspam** 함수를 게시 합니다.

        spamWebService <- publishWebService(ws, fun = predictSpam, name="spamWebService", inputSchema = smallTrainSet, data.frame=TRUE)

1. 이 함수는 **Predictspam** 함수를 사용 하 여 입력 및 출력을 정의 하는 **spamwebservice** 라는 웹 서비스를 만든 다음 새 엔드포인트에 대 한 정보를 반환 합니다.

    이 명령을 사용 하 여 API 엔드포인트 및 액세스 키를 비롯 한 최신 게시 된 웹 서비스의 세부 정보를 볼 수 있습니다.

        s<-tail(services(ws, name = "spamWebService"), 1)
        ep <- endpoints(ws,s)
        ep

1. 테스트 집합의 처음 10개의 행에 대해 작업을 수행하려면

        consume(ep, smallTestSet[1:10, ])

<a name="deep-learning"></a>

## <a name="deep-learning-tutorials-and-walkthroughs"></a>심층 학습 자습서 및 연습

프레임 워크 기반 샘플 외에도 일련의 포괄적인 연습도 제공 됩니다. 이러한 연습을 통해 이미지 및 텍스트/언어 이해와 같이 도메인에서 딥 러닝 애플리케이션의 개발을 빠르게 시작할 수 있습니다.

- 여러 프레임 워크에서 [신경망 실행](https://github.com/ilkarman/DeepLearningFrameworks): 한 프레임 워크에서 다른 프레임 워크로 코드를 마이그레이션하는 방법을 보여 주는 포괄적인 연습입니다. 또한 프레임 워크 간에 모델 및 런타임 성능을 비교 하는 방법을 보여 줍니다. 

- 이미지 [내에서 제품을 검색 하는 종단 간 솔루션을 빌드하는 방법 가이드](https://github.com/Azure/cortana-intelligence-product-detection-from-images): 이미지 검색은 이미지 내에서 개체를 찾고 분류할 수 있는 기술입니다. 이 기술은 많은 실제 비즈니스 도메인에서 상당한 보상을 가져올 수 있습니다. 예를 들어 소매업체는 이 기술을 사용하여 고객이 선택한 제품을 확인할 수 있습니다. 또한 이 정보는 매장의 제품 재고를 관리하는 데 도움이 됩니다. 

- [오디오에 대 한 심층 학습](https://blogs.technet.microsoft.com/machinelearning/2018/01/30/hearing-ai-getting-started-with-deep-learning-for-audio-on-azure/):이 자습서에서는 [도시 소리 데이터 집합](https://urbansounddataset.weebly.com/)에서 오디오 이벤트 검색을 위한 심층 학습 모델을 학습 하는 방법을 보여 줍니다. 이 자습서에서는 오디오 데이터로 작업 하는 방법에 대 한 개요를 제공 합니다.

- [텍스트 문서 분류](https://github.com/anargyri/lstm_han):이 연습에서는 두 가지 신경망 아키텍처를 빌드하고 학습 하는 방법을 보여 줍니다. 이러한 신경망은 Keras API를 딥 러닝에 사용하여 텍스트 문서를 분류합니다. Keras는 가장 인기 있는 딥 러닝 프레임워크인 Microsoft Cognitive Toolkit, TensorFlow 및 Theano의 프런트 엔드입니다.

## <a name="other-tools"></a>기타 도구

나머지 섹션에서는 Linux DSVM에 설치 된 일부 도구를 사용 하는 방법을 보여 줍니다. 이러한 도구에 대해 설명 합니다.

* XGBoost
* Python
* JupyterHub
* Rattle
* PostgreSQL 및 SQuirreL SQL
* SQL Server 데이터 웨어하우스

### <a name="xgboost"></a>XGBoost

[Xgboost](https://xgboost.readthedocs.org/en/latest/) 는 빠르고 정확한 승격 된 트리 구현을 제공 합니다.

    require(xgboost)
    data <- read.csv("spambaseHeaders.data")
    set.seed(123)

    rnd <- runif(dim(data)[1])
    trainSet = subset(data, rnd <= 0.7)
    testSet = subset(data, rnd > 0.7)

    bst <- xgboost(data = data.matrix(trainSet[,0:57]), label = trainSet$spam, nthread = 2, nrounds = 2, objective = "binary:logistic")

    pred <- predict(bst, data.matrix(testSet[, 0:57]))
    accuracy <- 1.0 - mean(as.numeric(pred > 0.5) != testSet$spam)
    print(paste("test accuracy = ", accuracy))

XGBoost는 Python 또는 명령줄에서 호출할 수도 있습니다.

### <a name="python"></a>Python

Python 개발의 경우 Anaconda Python 배포판 3.5 및 2.7이 DSVM에 설치 됩니다.

> [!NOTE]
> Anaconda 분포는 [Conda](https://conda.pydata.org/docs/index.html)를 포함 합니다. Conda를 사용 하 여 다른 버전 또는 패키지를 설치 하는 사용자 지정 Python 환경을 만들 수 있습니다.

Spambase 데이터 집합 중 일부를 읽고 Scikit에서 지원 벡터 컴퓨터를 사용 하 여 전자 메일을 분류 해 보세요. 알아보기:

    import pandas
    from sklearn import svm
    data = pandas.read_csv("spambaseHeaders.data", sep = ',\s*')
    X = data.ix[:, 0:57]
    y = data.ix[:, 57]
    clf = svm.SVC()
    clf.fit(X, y)

예측하려면

    clf.predict(X.ix[0:20, :])

Azure Machine Learning 엔드포인트을 게시 하는 방법을 보여 주기 위해 더 기본적인 모델을 만들어 보겠습니다. 이전에 R 모델을 게시할 때 사용한 세 가지 변수를 사용 합니다.

    X = data[["char_freq_dollar", "word_freq_remove", "word_freq_hp"]]
    y = data.ix[:, 57]
    clf = svm.SVC()
    clf.fit(X, y)

모델을 Azure Machine Learning에 게시 하려면 다음을 수행 합니다.

    # Publish the model.
    workspace_id = "<workspace-id>"
    workspace_token = "<workspace-token>"
    from azureml import services
    @services.publish(workspace_id, workspace_token)
    @services.types(char_freq_dollar = float, word_freq_remove = float, word_freq_hp = float)
    @services.returns(int) # 0 or 1
    def predictSpam(char_freq_dollar, word_freq_remove, word_freq_hp):
        inputArray = [char_freq_dollar, word_freq_remove, word_freq_hp]
        return clf.predict(inputArray)

    # Get some info about the resulting model.
    predictSpam.service.url
    predictSpam.service.api_key

    # Call the model
    predictSpam.service(1, 1, 1)


> [!NOTE]
> 이 옵션은 Python 2.7에 대해서만 사용할 수 있습니다. Python 3.5에서는 아직 지원 되지 않습니다. 실행 하려면 **/anaconda/bin/python2.7**를 사용 합니다.

### <a name="jupyterhub"></a>JupyterHub

DSVM의 Anaconda 배포는 Python, R 또는 줄리아 코드 및 분석을 공유 하기 위한 플랫폼 간 환경인 Jupyter Notebook와 함께 제공 됩니다. JupyterHub를 통해 Jupyter Notebook에 액세스할 수 있습니다. Https://\<DSVM DNS 이름 또는 IP 주소\>: 8000/에서 로컬 Linux 사용자 이름 및 암호를 사용 하 여 로그인 합니다. JupyterHub에 대 한 모든 구성 파일은/etc/jupyterhub.에 있습니다.

> [!NOTE]
> 현재 커널의 Jupyter Notebook에서 `pip` 명령을 통해 Python 패키지 관리자를 사용 하려면 코드 셀에서 다음 명령을 사용 합니다.
>
>   ```python
>    import sys
>    ! {sys.executable} -m pip install numpy -y
>   ```
> 
> 현재 커널의 Jupyter Notebook에서 `conda` 명령을 통해 Conda 설치 관리자를 사용 하려면 코드 셀에 다음 명령을 사용 합니다.
>
>   ```python
>    import sys
>    ! {sys.prefix}/bin/conda install --yes --prefix {sys.prefix} numpy
>   ```

여러 샘플 노트북은 DSVM에 이미 설치 되어 있습니다.

* 샘플 Python 노트북:
  * [IntroToJupyterPython](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IntroToJupyterPython.ipynb)
  * [IrisClassifierPyMLWebService](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IrisClassifierPyMLWebService.ipynb)
* 샘플 R 노트북:
  * [IntroTutorialinR](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IntroTutorialinR.ipynb) 

> [!NOTE]
> 또한 줄리아 언어는 Linux DSVM의 명령줄에서 사용할 수 있습니다.

### <a name="rattle"></a>Rattle

[Rattle](https://cran.r-project.org/web/packages/rattle/index.html) (*R* *A*nalytical *T*ool *t*o *L*획득 *E*asily)는 데이터 마이닝을 위한 그래픽 R 도구입니다. Rattle에는 데이터를 쉽게 로드, 탐색 및 변환 하 고 모델을 작성 및 평가할 수 있게 해 주는 직관적인 인터페이스가 있습니다. [Rattle: R 용 데이터 마이닝 GUI는](https://journal.r-project.org/archive/2009-2/RJournal_2009-2_Williams.pdf) Rattle의 기능을 보여 주는 연습을 제공 합니다.

다음 명령을 실행 하 여 Rattle을 설치 하 고 시작 합니다.

    if(!require("rattle")) install.packages("rattle")
    require(rattle)
    rattle()

> [!NOTE]
> DSVM에 Rattle를 설치할 필요가 없습니다. 그러나 Rattle가 열리면 추가 패키지를 설치 하 라는 메시지가 표시 될 수 있습니다.

Rattle은 탭 기반 인터페이스를 사용합니다. 대부분의 탭은 데이터를 로드 하거나 데이터를 탐색 하는 것과 같은 [팀 데이터 과학 프로세스](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/)의 단계에 해당 합니다. 데이터 과학 프로세스는 탭을 통해 왼쪽에서 오른쪽으로 진행됩니다. 마지막 탭에는 Rattle에서 실행 한 R 명령의 로그가 포함 되어 있습니다.

데이터 세트를 로드하고 구성하려면

1. 파일을 로드 하려면 **데이터** 탭을 선택 합니다.
1. **Filename**옆의 선택기를 선택 하 고 **Spambaseheaders. data**를 선택 합니다.
1. 파일을 로드하려면 **실행**을 선택 합니다. 식별 된 데이터 형식을 포함 하 여 각 열에 대 한 요약이 표시 되어야 합니다. 입력, 대상 또는 다른 유형의 변수 인지 여부 고유 값의 수를 표시 합니다.
1. Rattle은 **spam** 열을 대상으로 제대로 식별합니다. **스팸** 열을 선택 하 고 **대상 데이터 형식을** **Categoric**로 설정 합니다.

데이터를 탐색하려면

1. **탐색** 탭을 선택합니다.
1. 변수 형식 및 일부 요약 통계에 대 한 정보를 보려면 **요약** > **실행**을 선택 합니다.
1. 각 변수에 대 한 다른 종류의 통계를 보려면 **설명** 또는 **기본 사항**등의 기타 옵션을 선택 합니다.

**탐색** 탭을 사용 하 여 통찰력 있는 플롯을 생성할 수도 있습니다. 데이터의 히스토그램을 나타내려면

1. **배포**를 선택합니다.
1. **Word_freq_remove** 및 **word_freq_you**에 대해서는 **히스토그램**을 선택 합니다.
1. **실행**을 선택합니다. 단일 그래프 창에 밀도 플롯이 모두 표시 되어야 합니다. _여기서 단어는_ _제거_하는 것 보다 훨씬 자주 전자 메일에 표시 되는 것을 알 수 있습니다.

**상관 관계** 플롯도 흥미롭습니다. 플롯을 만들려면 다음을 수행 합니다.

1. **유형**에서 **상관 관계**를 선택 합니다.
1. **실행**을 선택합니다.
1. 권장 최대 변수는 40개라는 경고 메시지가 표시됩니다. 도표를 보려면 **예** 를 선택합니다.

몇 가지 흥미로운 상관 관계를 제공 합니다. 예를 들어 _기술이_ _HP_ 및 _labs_와 밀접 하 게 연관 되어 있습니다. 또한 데이터 집합 기부자의 지역 코드는 650 이므로 _650_ 와 밀접 하 게 연관 됩니다.

단어 간의 상관 관계에 대 한 숫자 값은 **탐색** 창에서 사용할 수 있습니다. 예를 들어이 _기술은_ 및 _money_ _와 부정적인_ 상관 관계를 두고 있습니다.

Rattle은 몇 가지 일반적인 문제를 처리하기 위해 데이터 세트를 변환할 수 있습니다. 예를 들어 기능을 다시 크기 조정 하 고, 누락 된 값을 돌립니다 하 고, 이상 값을 처리 하 고, 누락 된 데이터가 있는 변수나 관찰을 제거할 수 있습니다 Rattle는 관찰 및 변수 간의 연결 규칙을 식별할 수도 있습니다. 이러한 탭은이 소개 연습에서 다루지 않습니다.

Rattle는 클러스터 분석도 실행할 수 있습니다. 출력을 더 쉽게 읽을 수 있도록 일부 기능을 제외하겠습니다. **데이터** 탭에서 다음 10 개 항목을 제외 하 고 각 변수 옆의 **무시** 를 선택 합니다.

* word_freq_hp
* word_freq_technology
* word_freq_george
* word_freq_remove
* word_freq_your
* word_freq_dollar
* word_freq_money
* capital_run_length_longest
* word_freq_business
* spam

**클러스터** 탭으로 돌아갑니다. **Kmeans**을 선택 하 고 **클러스터 수** 를 **4**로 설정 합니다. **실행**을 선택합니다. 결과가 출력 창에 표시됩니다. 한 클러스터는 _george_ 및 _hp_의 빈도가 높고 합법적인 비즈니스 전자 메일 일 수 있습니다.

기본 의사 결정 트리 기계 학습 모델을 빌드하려면 다음을 수행 합니다.

1. **모델** 탭을 선택합니다.
1. **형식**에서 **트리**를 선택 합니다.
1. **실행** 을 선택하여 출력 창에 텍스트 형식으로 트리를 표시합니다.
1. **그리기** 단추를 선택하여 그래픽 버전을 봅니다. 의사 결정 트리는 이전에 rpart를 사용 하 여 가져온 트리와 비슷합니다.

Rattle의 유용한 기능은 여러 기계 학습 방법을 실행 하 고 신속 하 게 평가할 수 있는 기능입니다. 단계는 다음과 같습니다.

1. **유형**에 대해 **모두**를 선택 합니다.
1. **실행**을 선택합니다.
1. Rattle 실행이 완료 되 면 **SVM**와 같은 **유형** 값을 선택 하 고 결과를 볼 수 있습니다.
1. **평가** 탭을 사용 하 여 유효성 검사 집합에서 모델의 성능을 비교할 수도 있습니다. 예를 들어 **오류 행렬** 선택은 유효성 검사 집합의 각 모델에 대 한 혼동 행렬, 전체 오류 및 평균 클래스 오류를 보여 줍니다. ROC 곡선을 플롯 하 고 민감도 분석을 실행 하 고 다른 유형의 모델 평가를 수행할 수도 있습니다.

모델 작성을 완료 한 후 **로그** 탭을 선택 하 여 세션 중 Rattle에서 실행 한 R 코드를 확인 합니다. **내보내기** 단추를 선택하여 저장할 수 있습니다.

> [!NOTE]
> 현재 Rattle 릴리스에는 버그가 포함 되어 있습니다. 스크립트를 수정 하거나 나중에 단계를 반복 하는 데 사용 하려면 로그 텍스트에서 *이 로그 내보내기* ... 앞에 **#** 문자를 삽입 해야 합니다.

### <a name="postgresql-and-squirrel-sql"></a>PostgreSQL 및 SQuirreL SQL

DSVM은 PostgreSQL이 설치된 상태로 제공됩니다. PostgreSQL은 정교한 오픈 소스 관계형 데이터베이스입니다. 이 섹션에서는 PostgreSQL에 spambase 데이터 집합을 로드 한 다음 쿼리 하는 방법을 보여 줍니다.

데이터를 로드 하려면 먼저 localhost에서 암호 인증을 허용 해야 합니다. 명령 프롬프트에서 다음을 실행합니다.

    sudo gedit /var/lib/pgsql/data/pg_hba.conf

구성 파일의 아래쪽 몇 줄은 허용되는 연결을 자세히 설명하는 줄입니다.

    # "local" is only for Unix domain socket connections:
    local   all             all                                     trust
    # IPv4 local connections:
    host    all             all             127.0.0.1/32            ident
    # IPv6 local connections:
    host    all             all             ::1/128                 ident

사용자 이름 및 암호를 사용 하 여 로그인 할 수 있도록 **IPv4 로컬 연결** 줄에서 **ident**대신 **md5** 를 사용 하도록 변경 합니다.

    # IPv4 local connections:
    host    all             all             127.0.0.1/32            md5

그런 다음 PostgreSQL 서비스를 다시 시작 합니다.

    sudo systemctl restart postgresql

*Psql* (PostgreSQL의 대화형 터미널)을 기본 제공 postgres user로 시작 하려면 다음 명령을 실행 합니다.

    sudo -u postgres psql

로그인 하는 데 사용한 Linux 계정의 사용자 이름을 사용 하 여 새 사용자 계정을 만듭니다. 암호 만들기:

    CREATE USER <username> WITH CREATEDB;
    CREATE DATABASE <username>;
    ALTER USER <username> password '<password>';
    \quit

Psql에 로그인 합니다.

    psql

새 데이터베이스로 데이터 가져오기:

    CREATE DATABASE spam;
    \c spam
    CREATE TABLE data (word_freq_make real, word_freq_address real, word_freq_all real, word_freq_3d real,word_freq_our real, word_freq_over real, word_freq_remove real, word_freq_internet real,word_freq_order real, word_freq_mail real, word_freq_receive real, word_freq_will real,word_freq_people real, word_freq_report real, word_freq_addresses real, word_freq_free real,word_freq_business real, word_freq_email real, word_freq_you real, word_freq_credit real,word_freq_your real, word_freq_font real, word_freq_000 real, word_freq_money real,word_freq_hp real, word_freq_hpl real, word_freq_george real, word_freq_650 real, word_freq_lab real,word_freq_labs real, word_freq_telnet real, word_freq_857 real, word_freq_data real,word_freq_415 real, word_freq_85 real, word_freq_technology real, word_freq_1999 real,word_freq_parts real, word_freq_pm real, word_freq_direct real, word_freq_cs real, word_freq_meeting real,word_freq_original real, word_freq_project real, word_freq_re real, word_freq_edu real,word_freq_table real, word_freq_conference real, char_freq_semicolon real, char_freq_leftParen real,char_freq_leftBracket real, char_freq_exclamation real, char_freq_dollar real, char_freq_pound real, capital_run_length_average real, capital_run_length_longest real, capital_run_length_total real, spam integer);
    \copy data FROM /home/<username>/spambase.data DELIMITER ',' CSV;
    \quit

이제 JDBC 드라이버를 통해 데이터베이스와 상호 작용 하는 데 사용할 수 있는 그래픽 도구인 SQuirreL SQL을 사용 하 여 데이터를 탐색 하 고 일부 쿼리를 실행 해 보겠습니다.

시작 하려면 **응용 프로그램** 메뉴에서 SQuirreL SQL을 엽니다. 드라이버를 설정하려면

1. **Windows** > **드라이버 보기**를 선택 합니다.
1. **PostgreSQL** 를 마우스 오른쪽 단추로 클릭 하 고 **드라이버 수정**을 선택 합니다.
1. 추가 **클래스 경로** 를 선택 하 > **추가**를 선택 합니다.
1. **파일 이름**에 **/usr/share/java/jdbcdrivers/postgresql-9.4.1208.jre6.jar**을 입력 합니다.
1. **열기**를 선택합니다.
1. **드라이버 나열**을 선택 합니다. **클래스 이름**에 대해 **postgresql**를 선택 하 고 **확인**을 선택 합니다.

로컬 서버에 연결을 설정하려면

1. **Windows** > **별칭 보기를 선택 합니다.**
1. **+** 단추를 선택 하 여 새 별칭을 만듭니다. 새 별칭 이름에 대해 **스팸 데이터베이스**를 입력 합니다. 
1. **드라이버**에 대해 **PostgreSQL**를 선택 합니다.
1. URL을 **jdbc:postgresql://localhost/spam**으로 설정합니다.
1. 사용자 이름 및 암호를 입력합니다.
1. **확인**을 선택합니다.
1. **연결** 창을 열려면 별칭 **스팸 데이터베이스**를 두 번 클릭합니다.
1. **연결**을 선택합니다.

일부 쿼리를 실행하려면

1. **SQL** 탭을 선택합니다.
1. **SQL** 탭의 맨 위에 있는 쿼리 상자에 `SELECT * from data;`와 같은 기본 쿼리를 입력 합니다.
1. Ctrl + Enter를 눌러 쿼리를 실행 합니다. 기본적으로 SQuirreL SQL은 쿼리에서 처음 100 개 행을 반환 합니다.

더 많은 쿼리를 실행 하 여이 데이터를 탐색할 수 있습니다. 예를 들어 스팸과 햄 간에 *make* 라는 단어의 빈도가 얼마나 차이가 있을까요?

    SELECT avg(word_freq_make), spam from data group by spam;

또는 *3d*가 자주 포함 되는 전자 메일의 특징은 무엇 인가요?

    SELECT * from data order by word_freq_3d desc;

*3d* 가 많이 발생 하는 대부분의 메일은 스팸입니다. 이 정보는 전자 메일을 분류 하는 예측 모델을 작성 하는 데 유용할 수 있습니다.

PostgreSQL 데이터베이스에 저장 된 데이터를 사용 하 여 기계 학습을 수행 하려면 [MADlib](https://madlib.incubator.apache.org/)를 사용 하는 것이 좋습니다.

### <a name="sql-data-warehouse"></a>SQL Data Warehouse

Azure SQL Data Warehouse는 관계형 데이터와 비관계형 데이터를 모두 처리할 수 있는 클라우드 기반 스케일 아웃 데이터베이스입니다. 자세한 내용은 [Azure SQL Data Warehouse란?](../../sql-data-warehouse/sql-data-warehouse-overview-what-is.md)

데이터 웨어하우스에 연결하고 테이블을 만들려면 명령 프롬프트에서 다음 명령을 실행합니다.

    sqlcmd -S <server-name>.database.windows.net -d <database-name> -U <username> -P <password> -I

Sqlcmd 프롬프트에서 다음 명령을 실행 합니다.

    CREATE TABLE spam (word_freq_make real, word_freq_address real, word_freq_all real, word_freq_3d real,word_freq_our real, word_freq_over real, word_freq_remove real, word_freq_internet real,word_freq_order real, word_freq_mail real, word_freq_receive real, word_freq_will real,word_freq_people real, word_freq_report real, word_freq_addresses real, word_freq_free real,word_freq_business real, word_freq_email real, word_freq_you real, word_freq_credit real,word_freq_your real, word_freq_font real, word_freq_000 real, word_freq_money real,word_freq_hp real, word_freq_hpl real, word_freq_george real, word_freq_650 real, word_freq_lab real,word_freq_labs real, word_freq_telnet real, word_freq_857 real, word_freq_data real,word_freq_415 real, word_freq_85 real, word_freq_technology real, word_freq_1999 real,word_freq_parts real, word_freq_pm real, word_freq_direct real, word_freq_cs real, word_freq_meeting real,word_freq_original real, word_freq_project real, word_freq_re real, word_freq_edu real,word_freq_table real, word_freq_conference real, char_freq_semicolon real, char_freq_leftParen real,char_freq_leftBracket real, char_freq_exclamation real, char_freq_dollar real, char_freq_pound real, capital_run_length_average real, capital_run_length_longest real, capital_run_length_total real, spam integer) WITH (CLUSTERED COLUMNSTORE INDEX, DISTRIBUTION = ROUND_ROBIN);
    GO

Bcp를 사용 하 여 데이터를 복사 합니다.

    bcp spam in spambaseHeaders.data -q -c -t  ',' -S <server-name>.database.windows.net -d <database-name> -U <username> -P <password> -F 1 -r "\r\n"

> [!NOTE]
> 다운로드 한 파일에는 Windows 스타일 줄 끝이 포함 됩니다. Bcp 도구에는 Unix 스타일의 줄 끝이 필요 합니다. -R 플래그를 사용 하 여 bcp에 지시 합니다.

그런 다음 sqlcmd를 사용 하 여 쿼리 합니다.

    select top 10 spam, char_freq_dollar from spam;
    GO

SQuirreL SQL을 사용 하 여 쿼리할 수도 있습니다. SQL Server JDBC 드라이버를 사용 하 여 PostgreSQL와 유사한 단계를 수행 합니다. JDBC 드라이버는/usr/share/java/jdbcdrivers/sqljdbc42.jar 폴더에 있습니다.

## <a name="next-steps"></a>다음 단계

Azure에서 데이터 과학 프로세스를 구성 하는 작업을 안내 하는 문서에 대 한 개요는 [팀 데이터 과학 프로세스](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/overview)를 참조 하세요.

특정 시나리오에 대 한 팀 데이터 과학 프로세스의 단계를 설명 하는 종단 간 연습에 대 한 설명은 [팀 데이터 과학 프로세스 연습](../team-data-science-process/walkthroughs.md)을 참조 하세요. 또한 이 연습에서는 클라우드 및 온-프레미스 도구와 서비스를 워크플로 또는 파이프라인에 결합하여 지능형 애플리케이션을 만드는 방법을 설명합니다.
