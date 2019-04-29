---
title: 분산 심층 학습을 위해 Azure HDInsight Spark에서 Caffe 사용
description: 분산 심층 학습을 위해 Azure HDInsight Spark에서 Caffe 사용
author: hrasheed-msft
ms.author: hrasheed
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/17/2017
ms.openlocfilehash: d0d68263485c5ab6e57a349317b1975862470cc2
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62124393"
---
# <a name="use-caffe-on-azure-hdinsight-spark-for-distributed-deep-learning"></a>분산 심층 학습을 위해 Azure HDInsight Spark에서 Caffe 사용


## <a name="introduction"></a>소개

심층 학습은 의료, 교통, 제조 등에 이르는 모든 분야에 영향을 줍니다. 기업은 [이미지 분류](https://blogs.microsoft.com/next/2015/12/10/microsoft-researchers-win-imagenet-computer-vision-challenge/), [음성 인식](https://googleresearch.blogspot.jp/2015/08/the-neural-networks-behind-google-voice.html), 개체 인식 및 기계 번역과 같은 어려운 문제를 해결하기 위해 딥 러닝으로 전환하고 있습니다. 

[Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/research/product/cognitive-toolkit/), [Tensorflow](https://www.tensorflow.org/), [Apache MXNet](https://mxnet.apache.org/), Theano 등 [널리 사용되는 수많은 프레임워크](https://en.wikipedia.org/wiki/Comparison_of_deep_learning_software)가 있습니다. [Caffe](https://caffe.berkeleyvision.org/)는 가장 많이 사용되는 비기호(명령적) 신경망 프레임워크 중 하나로, Computer Vision을 비롯한 많은 영역에서 널리 사용 됩니다. 또한 [CaffeOnSpark](https://yahoohadoop.tumblr.com/post/139916563586/caffeonspark-open-sourced-for-distributed-deep)는 Caffe를 Apache Spark와 결합하여 기존 Hadoop 클러스터에서 심층 학습을 쉽게 사용하도록 할 수 있습니다. Spark ETL 파이프라인과 함께 심층 학습을 사용하여 시스템 복잡성을 줄이고 완전한 솔루션 학습에 따른 대기 시간을 줄일 수 있습니다.

[HDInsight](https://azure.microsoft.com/services/hdinsight/)는 Apache Spark, Apache Hive, Apache Hadoop, Apache HBase, Apache Storm, Apache Kafka 및 ML Services에 최적화된 오픈 소스 분석 클러스터를 제공하는 클라우드 Apache Hadoop 솔루션입니다. HDInsight는 99.9% SLA를 보장합니다. 이러한 각 빅 데이터 기술과 ISV 애플리케이션은 엔터프라이즈용 보안과 모니터링으로 관리 클러스터 형태로 쉽게 배포 가능합니다.

이 문서에서는 HDInsight 클러스터에 대해 [Spark의 Caffe](https://github.com/yahoo/CaffeOnSpark)를 설치하는 방법을 보여줍니다. 또한 이 문서는 기본 제공 MNIST 데모를 사용하여 CPU에서 HDInsight Spark를 통해 분산 심층 학습을 사용하는 방법을 보여줍니다.

다음 4단계로 작업을 수행합니다.

1. 모든 노드에 필요한 종속성 설치
2. 헤드 노드에서 HDInsight용 Spark에 Caffe 빌드
3. 모든 작업자 노드에 필요한 라이브러리 배포
4. Caffe 모델을 구성하고 분산 방식으로 실행합니다.

HDInsight는 PaaS 솔루션으로, 뛰어난 플랫폼 기능을 제공하므로 일부 작업을 쉽게 수행할 수 있습니다. 이 블로그 게시물에서 사용하는 기능 중 하나는 [스크립트 작업](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux)이라고 하며 셸 명령을 실행하여 클러스터 노드(헤드 노드, 작업자 노드 또는 가장자리 노드)를 사용자 지정할 수 있습니다.

## <a name="step-1--install-the-required-dependencies-on-all-the-nodes"></a>1단계:  모든 노드에 필요한 종속성 설치

시작하려면 종속성을 설치해야 합니다. Caffe 사이트 및 [CaffeOnSpark 사이트](https://github.com/yahoo/CaffeOnSpark/wiki/GetStarted_yarn)는 YARN 모드에서 Spark에 대한 종속성을 설치하기 위한 몇 가지 유용한 wiki를 제공합니다. 또한 HDInsight는 YARN 모드에서 Spark를 사용합니다. 그러나 HDInsight 플랫폼에 대한 몇 가지 종속성을 추가해야 합니다. 이렇게 하려면 스크립트 동작을 사용하고 모든 헤드 노드 및 작업자 노드에서 실행합니다. 해당 종속성은 다른 패키지에도 종속되므로 이 스크립트 동작은 약 20분이 소요됩니다. HDInsight 클러스터에 액세스할 수 있는 일부 위치(예: GitHub 위치 또는 기본 Blob Storage 계정)에 배치해야 합니다.

    #!/bin/bash
    #Please be aware that installing the below will add additional 20 mins to cluster creation because of the dependencies
    #installing all dependencies, including the ones mentioned in https://caffe.berkeleyvision.org/install_apt.html, as well a few packages that are not included in HDInsight, such as gflags, glog, lmdb, numpy
    #It seems numpy will only needed during compilation time, but for safety purpose you install them on all the nodes

    sudo apt-get install -y libprotobuf-dev libleveldb-dev libsnappy-dev libopencv-dev libhdf5-serial-dev protobuf-compiler maven libatlas-base-dev libgflags-dev libgoogle-glog-dev liblmdb-dev build-essential  libboost-all-dev python-numpy python-scipy python-matplotlib ipython ipython-notebook python-pandas python-sympy python-nose

    #install protobuf
    wget https://github.com/google/protobuf/releases/download/v2.5.0/protobuf-2.5.0.tar.gz
    sudo tar xzvf protobuf-2.5.0.tar.gz -C /tmp/
    cd /tmp/protobuf-2.5.0/
    sudo ./configure
    sudo make
    sudo make check
    sudo make install
    sudo ldconfig
    echo "protobuf installation done"


스크립트 동작에는 두 단계가 있습니다. 첫 번째 단계는 필요한 모든 라이브러리를 설치하는 것입니다. 이러한 라이브러리로는 Caffe를 컴파일(예: gflags, glog)하고 Caffe를 실행(예: numpy)하는 데 필요한 라이브러리가 포함됩니다. CPU 최적화를 위한 libatlas를 사용 중이지만 MKL 또는 CUDA(GPU용)와 같은 다른 최적화 라이브러리를 설치하는 데는 항상 CaffeOnSpark wiki를 따를 수 있습니다.

두 번째 단계는 런타임 중에 Caffe용 protobuf 2.5.0을 다운로드, 컴파일 및 설치하는 것입니다. Protobuf 2.5.0이 [필요](https://github.com/yahoo/CaffeOnSpark/issues/87)하지만 이 버전은 Ubuntu 16에서 패키지로 제공되지 않으므로 소스 코드에서 컴파일해야 합니다. 컴파일하는 방법에 대한 몇 가지 리소스가 인터넷에 제공됩니다. 자세한 내용은 [여기](https://jugnu-life.blogspot.com/2013/09/install-protobuf-25-on-ubuntu.html)를 참조하세요.

시작하기 위해 모든 작업자 노드 및 헤드 노드 (HDInsight 3.5용)에 대한 클러스터에 대해 이 스크립트 동작을 실행할 수 있습니다. 기존 클러스터에 대해 스크립트 동작을 실행하거나 클러스터 생성 동안 스크립트 동작을 사용할 수 있습니다. 스크립트 동작에 대한 자세한 내용은 [여기](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux) 설명서를 참조하세요.

![종속성 설치를 위한 스크립트 동작](./media/apache-spark-deep-learning-caffe/Script-Action-1.png)


## <a name="step-2-build-caffe-on-apache-spark-for-hdinsight-on-the-head-node"></a>2단계: 헤드 노드에서 HDInsight용 Apache Spark에 Caffe 빌드

두 번째 단계는 헤드 노드에 Caffe를 빌드한 후 컴파일된 라이브러리를 모든 작업자 노드에 배포하는 것입니다. 이 단계에서는 [헤드 노드에 대해 ssh를 수행](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix)해야 합니다. 그런 후에 [CaffeOnSpark 빌드 프로세스](https://github.com/yahoo/CaffeOnSpark/wiki/GetStarted_yarn)를 따라야 합니다. 다음은 CaffeOnSpark 빌드에 사용할 수 있는 스크립트와 몇 가지 추가 단계입니다. 

    #!/bin/bash
    git clone https://github.com/yahoo/CaffeOnSpark.git --recursive
    export CAFFE_ON_SPARK=$(pwd)/CaffeOnSpark

    pushd ${CAFFE_ON_SPARK}/caffe-public/
    cp Makefile.config.example Makefile.config
    echo "INCLUDE_DIRS += ${JAVA_HOME}/include" >> Makefile.config
    #Below configurations might need to be updated based on actual cases. For example, if you are using GPU, or using a different BLAS library, you may want to update those settings accordingly.
    echo "CPU_ONLY := 1" >> Makefile.config
    echo "BLAS := atlas" >> Makefile.config
    echo "INCLUDE_DIRS += /usr/include/hdf5/serial/" >> Makefile.config
    echo "LIBRARY_DIRS += /usr/lib/x86_64-linux-gnu/hdf5/serial/" >> Makefile.config
    popd

    #compile CaffeOnSpark
    pushd ${CAFFE_ON_SPARK}
    #always clean up the environment before building (especially when rebuiding), or there will be errors such as "failed to execute goal org.apache.maven.plugins:maven-antrun-plugin:1.7:run (proto) on project caffe-distri: An Ant BuildException has occurred: exec returned: 2"
    make clean 
    #the build step usually takes 20~30 mins, since it has a lot maven dependencies
    make build 
    popd
    export LD_LIBRARY_PATH=${CAFFE_ON_SPARK}/caffe-public/distribute/lib:${CAFFE_ON_SPARK}/caffe-distri/distribute/lib

    hadoop fs -mkdir -p wasb:///projects/machine_learning/image_dataset

    ${CAFFE_ON_SPARK}/scripts/setup-mnist.sh
    hadoop fs -put -f ${CAFFE_ON_SPARK}/data/mnist_*_lmdb wasb:///projects/machine_learning/image_dataset/

    ${CAFFE_ON_SPARK}/scripts/setup-cifar10.sh
    hadoop fs -put -f ${CAFFE_ON_SPARK}/data/cifar10_*_lmdb wasb:///projects/machine_learning/image_dataset/

    #put the already compiled CaffeOnSpark libraries to wasb storage, then read back to each node using script actions. This is because CaffeOnSpark requires all the nodes have the libarries
    hadoop fs -mkdir -p /CaffeOnSpark/caffe-public/distribute/lib/
    hadoop fs -mkdir -p /CaffeOnSpark/caffe-distri/distribute/lib/
    hadoop fs -put CaffeOnSpark/caffe-distri/distribute/lib/* /CaffeOnSpark/caffe-distri/distribute/lib/
    hadoop fs -put CaffeOnSpark/caffe-public/distribute/lib/* /CaffeOnSpark/caffe-public/distribute/lib/

CaffeOnSpark에 대한 설명서에 나와 있는 것보다 더 많은 것을 수행해야 할 수 있습니다. 변경 내용은 다음과 같습니다.
- CPU 전용으로 변경하고 특정 목록으로 libatlas를 사용합니다.
- 나중에 사용할 수 있도록 데이터 세트를 모든 작업자 노드에 액세스할 수 있는 공유 위치인 Blob Storage에 배치합니다.
- 컴파일된 Caffe 라이브러리를 Blob Storage에 배치하고 나중에 스크립트 동작을 사용하여 해당 라이브러리를 모든 노드에 복사하여 추가 컴파일 시간이 발생하지 않도록 합니다.


### <a name="troubleshooting-an-ant-buildexception-has-occurred-exec-returned-2"></a>문제 해결: Ant BuildException 발생: exec 반환됨: 2

CaffeOnSpark 빌드를 처음으로 시도할 때 이 메시지가 발생하는 경우가 있습니다.

    failed to execute goal org.apache.maven.plugins:maven-antrun-plugin:1.7:run (proto) on project caffe-distri: An Ant BuildException has occurred: exec returned: 2

"make clean"으로 코드 리포지토리를 정리한 후 "make build"를 실행하면 이 문제가 해결됩니다(단, 올바른 종속성이 있어야 함).

### <a name="troubleshooting-maven-repository-connection-time-out"></a>문제 해결: Maven 리포지토리 연결 시간 초과

maven에서 다음 코드 조각과 유사한 연결 시간 초과 오류가 발생하는 경우가 있습니다.

    Retry:
    [INFO] Downloading: https://repo.maven.apache.org/maven2/com/twitter/chill_2.11/0.8.0/chill_2.11-0.8.0.jar
    Feb 01, 2017 5:14:49 AM org.apache.maven.wagon.providers.http.httpclient.impl.execchain.RetryExec execute
    INFO: I/O exception (java.net.SocketException) caught when processing request to {s}->https://repo.maven.apache.org:443: Connection timed out (Read failed)

몇 분 후에 다시 시도해야 합니다.


### <a name="troubleshooting-test-failure-for-caffe"></a>문제 해결: Caffe 테스트 실패

CaffeOnSpark에 대한 최종 확인을 수행할 때 테스트 실패가 표시됩니다. UTF-8 인코딩과 관련될 수 있지만 Caffe 사용에는 영향을 주지 않아야 합니다.

    Run completed in 32 seconds, 78 milliseconds.
    Total number of tests run: 7
    Suites: completed 5, aborted 0
    Tests: succeeded 6, failed 1, canceled 0, ignored 0, pending 0
    *** 1 TEST FAILED ***

## <a name="step-3-distribute-the-required-libraries-to-all-the-worker-nodes"></a>3단계: 모든 작업자 노드에 필요한 라이브러리 배포

다음 단계는 모든 노드에 라이브러리(기본적으로 CaffeOnSpark/caffe-public/distribute/lib/ 및 CaffeOnSpark/caffe-distri/distribute/lib/의 라이브러리)를 배포하는 것입니다. 2단계에서는 라이브러리를 Blob Storage에 배치했으며 이 단계에서는 스크립트 동작을 사용하여 라이브러리를 모든 헤드 노드 및 작업자 노드에 복사합니다.

이 작업을 수행하려면 다음 코드 조각에 나와 있는 것처럼 스크립트 동작을 실행합니다.

    #!/bin/bash
    hadoop fs -get wasb:///CaffeOnSpark /home/changetoyourusername/

클러스터에 해당하는 적절한 위치를 가리켜야 합니다.

2단계에서 모든 노드에 액세스할 수 있는 Blob Storage에 배치했으므로 이 단계에서는 모든 노드에 복사하기만 하면 됩니다.

## <a name="step-4-compose-a-caffe-model-and-run-it-in-a-distributed-manner"></a>4단계: Caffe 모델 구성 및 분산 방식으로 실행

이전 단계를 실행하기 전에 Caffe가 설치됩니다. 다음 단계는 Caffe 모델을 작성하는 것입니다. 

Caffe는 "표현 아키텍처"를 사용 중이며 여기서는 모델을 구성하기 위해 대부분의 경우 코딩 없이 구성 파일을 정의해야 합니다. 따라서 이에 대해 살펴보겠습니다. 

학습할 모델은 MNIST 학습을 위한 샘플 모델입니다. 필기 숫자의 MNIST 데이터베이스에는 학습 집합 예제 60,000개와 테스트 집합 예제 10,000개가 포함됩니다. NIST에서 제공되는 큰 집합의 하위 집합입니다. 이 숫자는 크기를 표준화하였고 고정 크기 이미지로 중앙에 배치됩니다. CaffeOnSpark에는 데이터 세트를 다운로드하고 적절한 형식으로 변환할 수 있는 몇 가지 스크립트가 포함됩니다.

CaffeOnSpark는 MNIST 학습을 위한 몇 가지 네트워크 토폴로지를 제공합니다. 네트워크 아키텍처(네트워크의 토폴로지)를 분할하고 최적화하기 위한 훌륭한 설계를 포함합니다. 이 경우 다음 두 파일이 필요합니다. 

"Solver" 파일(${CAFFE_ON_SPARK}/data/lenet_memory_solver.prototxt)은 최적화를 감독하고 매개 변수 업데이트를 생성하는 데 사용됩니다. 예를 들어 CPU 또는 GPU를 사용할지 여부, 모멘텀, 반복 횟수 등을 정의합니다. 또한 프로그램에서 사용하는 신경망 토폴로지도 정의합니다(필요한 두 번째 파일). Solver에 대한 자세한 내용은 [Caffe 설명서](https://caffe.berkeleyvision.org/tutorial/solver.html)를 참조하세요.

이 예제의 경우 GPU보다는 CPU를 사용하므로 마지막 줄을 다음으로 변경해야 합니다.

    # solver mode: CPU or GPU
    solver_mode: CPU

![Caffe 구성](./media/apache-spark-deep-learning-caffe/Caffe-1.png)

필요에 따라 다른 줄을 변경할 수 있습니다.

두 번째 파일(${CAFFE_ON_SPARK}/data/lenet_memory_train_test.prototxt)은 신경망의 모습과 관련 입력 및 출력 파일을 정의합니다. 또한 학습 데이터 위치를 반영하도록 파일을 업데이트해야 합니다. lenet_memory_train_test.prototxt에서 다음 부분을 변경합니다(클러스터에 대한 적절한 위치를 가리켜야 함).

- "file:/Users/mridul/bigml/demodl/mnist_train_lmdb"를 "wasb:///projects/machine_learning/image_dataset/mnist_train_lmdb"로 변경합니다.
- "file:/Users/mridul/bigml/demodl/mnist_test_lmdb/"를 "wasb:///projects/machine_learning/image_dataset/mnist_test_lmdb"로 변경합니다.

![Caffe 구성](./media/apache-spark-deep-learning-caffe/Caffe-2.png)

네트워크를 정의하는 방법에 대한 자세한 내용은 [MNIST 데이터 세트에 대한 Caffe 설명서](https://caffe.berkeleyvision.org/gathered/examples/mnist.html)를 확인하세요.

이 문서의 목적에 맞게 다음 MNIST 예제를 사용합니다. 헤드 노드에서 다음 명령을 실행합니다.

    spark-submit --master yarn --deploy-mode cluster --num-executors 8 --files ${CAFFE_ON_SPARK}/data/lenet_memory_solver.prototxt,${CAFFE_ON_SPARK}/data/lenet_memory_train_test.prototxt --conf spark.driver.extraLibraryPath="${LD_LIBRARY_PATH}" --conf spark.executorEnv.LD_LIBRARY_PATH="${LD_LIBRARY_PATH}" --class com.yahoo.ml.caffe.CaffeOnSpark ${CAFFE_ON_SPARK}/caffe-grid/target/caffe-grid-0.1-SNAPSHOT-jar-with-dependencies.jar -train -features accuracy,loss -label label -conf lenet_memory_solver.prototxt -devices 1 -connection ethernet -model wasb:///mnist.model -output wasb:///mnist_features_result

앞의 명령은 각 YARN 컨테이너에 필요한 파일(lenet_memory_solver.prototxt 및 lenet_memory_train_test.prototxt)를 배포합니다. 또한 이 명령은 각 Spark 드라이버/실행기의 관련 PATH를 LD_LIBRARY_PATH로 설정합니다. LD_LIBRARY_PATH는 이전 코드 조각에 정의되어 있으며 CaffeOnSpark 라이브러리가 포함된 위치를 가리킵니다. 

## <a name="monitoring-and-troubleshooting"></a>모니터링 및 문제 해결

YARN 클러스터 모드를 사용 중이고 이 경우 임의 컨테이너(및 임의 작업자 노드)에 대해 Spark 드라이버가 예약되므로 콘솔에 다음과 같은 항목만 표시됩니다.

    17/02/01 23:22:16 INFO Client: Application report for application_1485916338528_0015 (state: RUNNING)

어떤 작업이 발생했는지 알고 싶은 경우에는 일반적으로 자세한 정보가 포함된 Spark 드라이버 로그를 가져와야 합니다. 이 경우 YARN UI로 이동하여 관련 YARN 로그를 찾아야 합니다. 다음 URL로 YARN UI를 가져올 수 있습니다. 

    https://yourclustername.azurehdinsight.net/yarnui
   
![YARN UI](./media/apache-spark-deep-learning-caffe/YARN-UI-1.png)

이 특정 애플리케이션에 대해 얼마나 많은 리소스가 할당되는지를 살펴볼 수 있습니다. "Scheduler" 링크를 클릭하면 이 애플리케이션에 대해 9개의 컨테이너가 실행 중인 것을 볼 수 있습니다. YARN에 8개의 실행기를 제공하도록 요청하고 다른 컨테이너는 드라이버 프로세스용입니다. 

![YARN Scheduler](./media/apache-spark-deep-learning-caffe/YARN-Scheduler.png)

오류가 있는 경우 드라이버 로그 또는 컨테이너 로그를 확인하려 할 수 있습니다. 드라이버 로그의 경우 YARN UI에서 애플리케이션 ID를 클릭한 후 [로그] 단추를 클릭하면 됩니다. 드라이버 로그는 stderr에 기록됩니다.

![YARN UI 2](./media/apache-spark-deep-learning-caffe/YARN-UI-2.png)

예를 들어 드라이버 로그에서 아래와 같이 너무 많은 실행기를 할당했음을 나타내는 몇 가지 오류가 표시될 수 있습니다.

    17/02/01 07:26:06 ERROR ApplicationMaster: User class threw exception: java.lang.IllegalStateException: Insufficient training data. Please adjust hyperparameters or increase dataset.
    java.lang.IllegalStateException: Insufficient training data. Please adjust hyperparameters or increase dataset.
        at com.yahoo.ml.caffe.CaffeOnSpark.trainWithValidation(CaffeOnSpark.scala:261)
        at com.yahoo.ml.caffe.CaffeOnSpark$.main(CaffeOnSpark.scala:42)
        at com.yahoo.ml.caffe.CaffeOnSpark.main(CaffeOnSpark.scala)
        at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
        at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
        at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
        at java.lang.reflect.Method.invoke(Method.java:498)
        at org.apache.spark.deploy.yarn.ApplicationMaster$$anon$2.run(ApplicationMaster.scala:627)

경우에 따라 드라이버보다는 실행기에서 문제가 발생할 수 있습니다. 이 경우 컨테이너 로그를 확인해야 합니다. 항상 컨테이너 로그를 가져온 다음, 실패한 컨테이너를 가져올 수 있습니다. 예를 들어 Caffe를 실행할 때 이 오류가 발생할 수 있습니다.

    17/02/01 07:12:05 WARN YarnAllocator: Container marked as failed: container_1485916338528_0008_05_000005 on host: 10.0.0.14. Exit status: 134. Diagnostics: Exception from container-launch.
    Container id: container_1485916338528_0008_05_000005
    Exit code: 134
    Exception message: /bin/bash: line 1: 12230 Aborted                 (core dumped) LD_LIBRARY_PATH=/usr/hdp/current/hadoop-client/lib/native:/usr/hdp/current/hadoop-client/lib/native/Linux-amd64-64:/home/xiaoyzhu/CaffeOnSpark/caffe-public/distribute/lib:/home/xiaoyzhu/CaffeOnSpark/caffe-distri/distribute/lib /usr/lib/jvm/java-8-openjdk-amd64/bin/java -server -Xmx4608m '-Dhdp.version=' '-Detwlogger.component=sparkexecutor' '-DlogFilter.filename=SparkLogFilters.xml' '-DpatternGroup.filename=SparkPatternGroups.xml' '-Dlog4jspark.root.logger=INFO,console,RFA,ETW,Anonymizer' '-Dlog4jspark.log.dir=/var/log/sparkapp/${user.name}' '-Dlog4jspark.log.file=sparkexecutor.log' '-Dlog4j.configuration=file:/usr/hdp/current/spark2-client/conf/log4j.properties' '-Djavax.xml.parsers.SAXParserFactory=com.sun.org.apache.xerces.internal.jaxp.SAXParserFactoryImpl' -Djava.io.tmpdir=/mnt/resource/hadoop/yarn/local/usercache/xiaoyzhu/appcache/application_1485916338528_0008/container_1485916338528_0008_05_000005/tmp '-Dspark.driver.port=43942' '-Dspark.history.ui.port=18080' '-Dspark.ui.port=0' -Dspark.yarn.app.container.log.dir=/mnt/resource/hadoop/yarn/log/application_1485916338528_0008/container_1485916338528_0008_05_000005 -XX:OnOutOfMemoryError='kill %p' org.apache.spark.executor.CoarseGrainedExecutorBackend --driver-url spark://CoarseGrainedScheduler@10.0.0.13:43942 --executor-id 4 --hostname 10.0.0.14 --cores 3 --app-id application_1485916338528_0008 --user-class-path file:/mnt/resource/hadoop/yarn/local/usercache/xiaoyzhu/appcache/application_1485916338528_0008/container_1485916338528_0008_05_000005/__app__.jar > /mnt/resource/hadoop/yarn/log/application_1485916338528_0008/container_1485916338528_0008_05_000005/stdout 2> /mnt/resource/hadoop/yarn/log/application_1485916338528_0008/container_1485916338528_0008_05_000005/stderr

    Stack trace: ExitCodeException exitCode=134: /bin/bash: line 1: 12230 Aborted                 (core dumped) LD_LIBRARY_PATH=/usr/hdp/current/hadoop-client/lib/native:/usr/hdp/current/hadoop-client/lib/native/Linux-amd64-64:/home/xiaoyzhu/CaffeOnSpark/caffe-public/distribute/lib:/home/xiaoyzhu/CaffeOnSpark/caffe-distri/distribute/lib /usr/lib/jvm/java-8-openjdk-amd64/bin/java -server -Xmx4608m '-Dhdp.version=' '-Detwlogger.component=sparkexecutor' '-DlogFilter.filename=SparkLogFilters.xml' '-DpatternGroup.filename=SparkPatternGroups.xml' '-Dlog4jspark.root.logger=INFO,console,RFA,ETW,Anonymizer' '-Dlog4jspark.log.dir=/var/log/sparkapp/${user.name}' '-Dlog4jspark.log.file=sparkexecutor.log' '-Dlog4j.configuration=file:/usr/hdp/current/spark2-client/conf/log4j.properties' '-Djavax.xml.parsers.SAXParserFactory=com.sun.org.apache.xerces.internal.jaxp.SAXParserFactoryImpl' -Djava.io.tmpdir=/mnt/resource/hadoop/yarn/local/usercache/xiaoyzhu/appcache/application_1485916338528_0008/container_1485916338528_0008_05_000005/tmp '-Dspark.driver.port=43942' '-Dspark.history.ui.port=18080' '-Dspark.ui.port=0' -Dspark.yarn.app.container.log.dir=/mnt/resource/hadoop/yarn/log/application_1485916338528_0008/container_1485916338528_0008_05_000005 -XX:OnOutOfMemoryError='kill %p' org.apache.spark.executor.CoarseGrainedExecutorBackend --driver-url spark://CoarseGrainedScheduler@10.0.0.13:43942 --executor-id 4 --hostname 10.0.0.14 --cores 3 --app-id application_1485916338528_0008 --user-class-path file:/mnt/resource/hadoop/yarn/local/usercache/xiaoyzhu/appcache/application_1485916338528_0008/container_1485916338528_0008_05_000005/__app__.jar > /mnt/resource/hadoop/yarn/log/application_1485916338528_0008/container_1485916338528_0008_05_000005/stdout 2> /mnt/resource/hadoop/yarn/log/application_1485916338528_0008/container_1485916338528_0008_05_000005/stderr

        at org.apache.hadoop.util.Shell.runCommand(Shell.java:933)
        at org.apache.hadoop.util.Shell.run(Shell.java:844)
        at org.apache.hadoop.util.Shell$ShellCommandExecutor.execute(Shell.java:1123)
        at org.apache.hadoop.yarn.server.nodemanager.DefaultContainerExecutor.launchContainer(DefaultContainerExecutor.java:225)
        at org.apache.hadoop.yarn.server.nodemanager.containermanager.launcher.ContainerLaunch.call(ContainerLaunch.java:317)
        at org.apache.hadoop.yarn.server.nodemanager.containermanager.launcher.ContainerLaunch.call(ContainerLaunch.java:83)
        at java.util.concurrent.FutureTask.run(FutureTask.java:266)
        at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1142)
        at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:617)
        at java.lang.Thread.run(Thread.java:745)


    Container exited with a non-zero exit code 134

이 경우 실패한 컨테이너 ID(위의 경우 container_1485916338528_0008_05_000005)를 가져와야 합니다. 그런 다음 헤드 노드에서 다음을 

    yarn logs -containerId container_1485916338528_0008_03_000005

실행해야 합니다. 컨테이너 오류를 확인하면 lenet_memory_solver.prototxt에서 GPU 모드를 사용했기 때문입니다(여기서는 대신 CPU 모드를 사용해야 함).

    17/02/01 07:10:48 INFO LMDB: Batch size:100
    WARNING: Logging before InitGoogleLogging() is written to STDERR
    F0201 07:10:48.309725 11624 common.cpp:79] Cannot use GPU in CPU-only Caffe: check mode.


## <a name="getting-results"></a>결과 가져오기

8개의 실행기를 할당하고 네트워크 토폴로지가 간단하므로 결과를 실행하는 데 약 30분만 소요됩니다. 명령줄에서 wasb:///mnist.model에 모델을 배치하고 wasb:///mnist_features_result라는 폴더에 결과를 배치한 것을 확인할 수 있습니다.

다음을 실행하여 결과를 가져올 수 있습니다.

    hadoop fs -cat hdfs:///mnist_features_result/*

결과는 다음과 유사하게 표시됩니다.

    {"SampleID":"00009597","accuracy":[1.0],"loss":[0.028171852],"label":[2.0]}
    {"SampleID":"00009598","accuracy":[1.0],"loss":[0.028171852],"label":[6.0]}
    {"SampleID":"00009599","accuracy":[1.0],"loss":[0.028171852],"label":[1.0]}
    {"SampleID":"00009600","accuracy":[0.97],"loss":[0.0677709],"label":[5.0]}
    {"SampleID":"00009601","accuracy":[0.97],"loss":[0.0677709],"label":[0.0]}
    {"SampleID":"00009602","accuracy":[0.97],"loss":[0.0677709],"label":[1.0]}
    {"SampleID":"00009603","accuracy":[0.97],"loss":[0.0677709],"label":[2.0]}
    {"SampleID":"00009604","accuracy":[0.97],"loss":[0.0677709],"label":[3.0]}
    {"SampleID":"00009605","accuracy":[0.97],"loss":[0.0677709],"label":[4.0]}

SampleID는 MNIST 데이터 세트에서 ID를 나타내며 레이블은 모델에서 식별하는 숫자입니다.


## <a name="conclusion"></a>결론

이 설명서에서는 간단한 예를 실행하여 CaffeOnSpark를 설치하려고 시도했습니다. HDInsight는 완전히 관리되는 클라우드 분산된 계산 플랫폼으로, 분산 딥 러닝을 위해 대규모 데이터 세트에 대해 Machine Learning 및 고급 분석 워크로드를 실행하기 위한 최적의 장소이며 HDInsight Spark에서 Caffe를 사용하여 딥 러닝 작업을 수행할 수 있습니다.


## <a name="seealso"></a>참고 항목
* [개요: Azure HDInsight의 Apache Spark](apache-spark-overview.md)

### <a name="scenarios"></a>시나리오
* [Machine Learning과 Apache Spark: HDInsight의 Spark를 사용하여 HVAC 데이터로 건물 온도 분석](apache-spark-ipython-notebook-machine-learning.md)
* [Machine Learning과 Apache Spark: 음식 검사 결과를 예측하는 데 HDInsight의 Spark 사용](apache-spark-machine-learning-mllib-ipython.md)

### <a name="manage-resources"></a>리소스 관리
* [Azure HDInsight에서 Apache Spark 클러스터에 대한 리소스 관리](apache-spark-resource-manager.md)

