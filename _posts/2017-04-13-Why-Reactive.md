---
layout: post
title: Why Reactive?
---

    이 글은 Konrad Malawski가 2016년 11월 22일에 O'Reilly에 기고한 글입니다. Reactive에 대한 전반적인 배경 지식을 얻기에 도움이 되는 것 같아, 원작자의 허락을 받아 직접 번역하였습니다. 오역이 있을 수도 있으니 양해 부탁드립니다.

    원문 링크: https://www.oreilly.com/learning/why-reactive

# Why Reactive?

## 챕터1 - 소개

> 이전의 선형, 3-티어 아키텍쳐는 더 이상 쓸모없다는 것이 점점 분명해지고 있습니다.
>
> &nbsp; - 가트너 서밋 트랙 설명

비록 reactive라는 용어는 오랫동안 사용되어 왔지만, 최근에 들어서야 산업계에서 사실상 de facto 시스템 디자인으로 인정 받아 주류로 채택되고 있습니다. 2014년에 가트너는 그 동안 널리 사용되던 3-티어 아키텍처가 구식이 되고 있다고 썼습니다. 이 보고서의 목적은 과장된 광로로부터 한 걸음 물러서서 reactive가 실제로 무엇인지, 언제 채택해야 할지, 또, 채택하기 위해선 어떻게 해야 하는지를 분석하는 것입니다. 이 보고서는 되도록 특정 기술에 관계없이 reactive 어플리케이션과 시스템 설계의 기본 원칙에 중점을 두려고 합니다. 사실, Lightbend 또는 Netflix 스택과 같은 특정 기술은 reactive 시스템 개발에 훨씬 적합합니다. 그러나 이 보고서에선 맹목적인 추천만 하기 보단, 여러분이 직접 옳은 판단을 할 수 있도록 필요한 배경 지식과 이해를 제공할 것입니다.

이 보고서의 대상은 reactive가 무엇인지를 궁금해하는 CTO, 설계자 및 팀 리더 또는 기술 기반의 관리자입니다. 일부 챕터에서는 기술 측면으로 깊게 들어갈 것입니다. 어플리케이션 레벨에서의 reactive를 다룬 챕터2에선 프로그래밍 패러다임의 기술적 차이와 그 차이가 자원 활용에 미치는 영향을 이해해볼 것입니다. 시스템 레벨에서의 reactive를 다루는 다음 장에서는 조금 뒤로 물러나서 분산된 reactive 어플리케이션의 구조적 영향과 조직적 영향을 살펴 봅니다. 마지막으로 몇 가지 구성 요소를 제안하고 reactive에 대한 수많은 마케팅 과대 광고 중 실제로 적합한 요소를 찾아내는 방법을 제시하며 이 보고서를 마무리 할 것입니다.

그럼, reactive는 실제로 무엇을 의미할까? 그에 대한 핵심 정의는 Jonas Bonér가 분산 및 고성능 컴퓨팅 업계에서 가장 명석한 사람들, 알파벳 순서로, Dave Farley, Roland Kuhn, Martin Thompson과 협력하여 reactive 어플리케이션과 시스템을 개발하는데 있어 필요한 핵심 원칙을 세워 2013년에 발표한 Reactive 선언문에서 어느 정도 공식화되었습니다. 선언문의 목표는 reactive를 둘러싼 여러 혼란을 없애고 reactive가 실행 가능한 개발 스타일이 될 수 있는 단단한 기반을 구축하는 것이 었습니다. 여기선 그 선언문 자체를 깊게 다루진 않겠지만, 그것을 읽어볼 것을 강력히 권고합니다. 요즘 시스템 디자인에 사용되는 많은 용어들(에러와 실패의 차이점 등)이 잘 정의되어 있습니다.

용어에 대한 혼란을 명확하게 하기 위해 준비된 Reactive 선언문과 유사하게, 우리의 목표는 이 보고서를 통해 reactive화 된다는 것이 어떤 의미인지 대한 공통된 이해를 갖는 것입니다.

### 왜 Reactive 시스템을 구축해야 합니까?

> 어제로 돌아가는 것은 쓸모가 없습니다.
왜냐하면 그땐 제가 다른 사람이었기 때문입니다.
>
> &nbsp; - Lewis Carroll

Reactive 시스템 및 아키텍처의 기술적 측면에 뛰어 들기 전에 우리 스스로에게 질문을 던져봐야 합니다.
"왜 Reactive 시스템을 구축해야 합니까?"

왜 우리는 지금까지 우리가 응용 프로그램을 구축해 왔던 방식을 바꾸는 데 관심을 가져야 할까요? 또는 더 바람직하게, "우리 소프트웨어의 사용자에게 어떤 이점을 제공하려고 합니까?"라는 질문을 통해 토론을 시작할 수도 있습니다. 많은 가능한 답변 중에서 일반적으로 사람들이 Reactive 시스템 디자인을 들여다보도록 이끄는 답변들을 알아보겠습니다. 우리 시스템이 다음과 같이 작동해야 한다고 가정할 수 있습니다.

- 사용자와의 상호 작용에 반응해야 한다.
- 실패를 처리하고 장애 발생 시에도 사용이 가능해야 한다.
- 다양한 하중 조건에서 견딜 수 있어야 한다.
- 다양한 네트워크 조건에서 메시지를 주고 받으며 라우팅 할 수 있어야 한다.

이러한 답변들은 실제로 선언문에 정의된 reactive의 핵심 특성들과 같습니다. 반응성은 어플리케이션의 하드웨어 사용을 제어함으로써 달성되고, 이 경우 많은 reactive 기술이 훌륭한 도구입니다. 챕터2에서 어플리케이션 레벨에서의 reactive를 살펴볼 때 몇 가지 도구를 알아 보겠습니다. 한편, 시스템을 쉽게 확장 할 수 있는 좋은 방법은 각 요소를 분리하여 독립적으로 확장 할 수 있도록 하는 것입니다. 이런 방법들과 함께 시스템간에 동기식 통신을 피하도록 만들면 시스템을 더욱 탄력적으로 만들 수 있습니다. 비동기식 통신을 최대한 사용하면 요청 대상 호스트 라이프사이클에 엄격하게 묶이는 것을 피할 수 있습니다. 예를 들어 요청 대상의 라이프사이클이 느리게 실행되는 경우에도 우리는 영향을 받지 않아야 합니다. 시스템 레벨에서의 reactive에 대해 설명할 챕터3에서 동기식 요청-응답 통신 패턴과 비동기식 메세지 전달을 비교해보며 이 이슈에 대해 보다 자세히 알아볼 것입니다.

마지막으로 챕터4에선 사용가능한 다양한 도구를 나열하고 각 도구를 언제 어떻게 사용해야 하는지에 대해 설명합니다. 또, 이미 세상에 존재하는 수많은 시스템들이 가치있고 연동할 필요가 있음을 인정하고, 기존 코드베이스에 reactive를 도입할 수 있는 방법에 대해서도 논의합니다.

### 왜 지금?

> Internet of Things (IoT)는 2018년에 휴대 전화를 뛰어넘어, 연결된 장치들 중 가장 큰 카테고리가 될 것으로 예상됩니다.
>
> &nbsp; - 에릭슨 모바일 리포트

"왜"란 질문의 또 다른 흥미로운 면은 우리가 조금 더 나아가 다음과 같은 질문을 할 때 밝혀집니다. "왜 지금?"

곧 보게 될 듯이, reactive의 배경이 된 수많은 아이디어의 대부분은 그리 새로운 것은 아닙니다. 많은 아이디어들은 이미 수년 전에 기술되고 구현되었습니다. 예를 들어 Erlang의 액터 기반 프로그래밍 모델은 1980년대 초반부터 있었으며 최근에는 Akka를 통해 JVM으로 이식되었습니다. 그렇다면 질문은: 왜 이토록 오래된 아이디어들이 이제서야 주류 엔터프라이즈 소프트웨어 개발에 투입되게 된 것일까?

우리는 예전과 다른 매우 흥미로운 시점에 와있습니다. 단일 서버로 존재했거나 스케일아웃 또는 하드웨어 활용 없이도 존재할 수 있었던 많은 어플리케이션에서도 이제는 확장성과 분산 시스템이 아주 흔하게 고려되고 있습니다. Reactive 프로그래밍의 부상에는 여러가지가 기여했습니다. 특이 주목할만한 것으로는,

##### IoT 및 모바일

* 모바일 부문은 2015년 1분기와 2016년 1분기 사이에 트래픽이 60% 증가했고, 에릭슨 모빌리티 리포트(Ericsson Mobility Report)에 따르면, 그 성장은 아직 전혀 느려질 조짐을 보이지 않고 있습니다. 또한, 이 부문은 이론적으로 서버 측이 수백만 개의 연결된 장치를 동시에 처리해야 한다는 것을 의미하고, 이러한 일은 디바이스와 같은 리소스를 가볍게 표현할 수 있는 비동기 처리방식으로 처리하는 것이 가장 적합합니다.

##### 클라우드와 컨테이너화

* 우리는 수년전 부터 클라우드 기반 인프라를 보유하고 있었지만, 가벼운 가상화 및 컨테이너와 컨테이너 중심의 스케줄러 및 PaaS 솔루션의 등장으로 보다 자유롭고 신속하게 세밀한 배포를 할 수 있게 되었습니다.

이 두 가지 움직임을 살펴보면, 동시 및 분산 응용 프로그램에 대한 필요성이 점차 커지는 시기임을 알 수 있습니다. 동시에, 큰 번거로움 없이 대규모 처리를 하기 위해 필요한 도구들도 드디어 그 수준이 올라오고 있습니다. 분산 응용 프로그램을 배포 할 때 배포 및 인프라 자동화 솔루션을 관리하는 전담 팀이 필요했던 수년 전과는 달라졌습니다.

Reactive라고 감싸져서 불려지는 많은 솔루션들이 1970년대 부터 존재했다는 것을 깨닫는 것도 중요합니다. Reactive가 이미 예전에 개념이 알려 졌음에도 불구하고, 왜 이제서야 주류가 되고 있는지는 여러 가지와 관련이 있습니다. 첫째, 자원 활용도 및 확장성에 대한 필요도가 높아져서, 이제 대다수의 프로젝트에서 해결책을 강구하게 되었습니다. Akka와 같은 클러스터 스케줄러, 메시지 기반 동시성 및 분산 처리 툴킷 등의 도구들도 사용할 수 있게 되었습니다. 또 다른 흥미로운 것은 모든 구현체들이 Reactive Streams와 같은 규약을 통해 좋은 상호 운용성을 제공하는 것을 목표로 하여, 특정 구현에 종속될 위험이 적어지고 있는 것입니다. Reactive Streams 표준에 대해서는 다음 챕터에서 보다 깊게 다룰 것입니다.

다시 말해, 배포 및 인프라의 자동화를 향한 지속적인 움직임으로 인해, 어플리케이션을 여러 전문 서비스와 노드에 분산시키는 것이 소규모 팀에게도 더 이상 불편이 되지 않는 수준에 이르렀습니다. 이러한 추세는 최근의 serverless 또는 ops-less 움직임과도 상통하는 것으로 보입니다. 이러한 움직임은 자체적으로 클라우드를 자동화하려는 모든 팀들의 논리적 다음 단계입니다. 그리고 reactive의 특성들은 당장의 성공을 위한 기반일뿐만 아니라 location-transparent와 ops-less 분산 서비스를 지향하는 업계의 방향과 잘 맞아 떨어진다는 걸 깨닫는 것이 중요합니다.

## 챕터2 - 어플리케이션 단계에서의 Reactive

> 할당 문은 프로그래밍 언어의 폰 노이만 병목 지점이며, 컴퓨터의 병목 지점과 유사하게 우리를 한 번에 한 단어씩만 생각하도록 제한합니다.
>
> &nbsp; - John Backus

Reactive Systems를 구축하기 위한 첫 번째 단계로 이러한 원칙들을 단일 어플리케이션에 적용하는 방법을 살펴 보겠습니다. 많은 원칙들이 이미 적용된 로컬 (어플리케이션) 단계의 reactive한 구성 요소들을 조합하여 시스템을 구성한다면, 똑같은 개념을 본격적인 분산 시스템으로 확장하는 것도 손쉬울 것입니다.

먼저, 두 개의 별개 커뮤니티가 "reactive"란 단어를 지금까지 다르게 사용해오면서 발생한 일반적인 오해를 바로잡아야 합니다. 최근에 들어서야 그 용어의 의미를 통일시키기로 합의했습니다.  오랫동안 업계는, 특히 ops 분야에서, 실패가 났을 때 회복될 수 있는 시스템이나 트래픽 증가/감소에 따라 스케일아웃이 가능한 시스템을 "Reactive Systems"이라고 칭해 왔습니다. 이것은 Reactive 선언문의 핵심 개념이기도 합니다. 한편, 학술 쪽에서는 "reactive"란 용어가 "functional reactive programming"(FRP), 보다 구체적으로는 "functional reactive activation" 때 부터 사용되어져 왔습니다. 이 용어는 1997년 Haskell과 Elm, .NET ("Reactive Extensions"라는 용어가 알려짐) 및 기타 언어들에서 소개되었습니다. 이 기술은 Reactive Systems에 매우 유용한 건 사실이지만, 오늘날에는 FRP 프레임워크 쪽에서도 잘못 통용되고 있습니다.

Reactive 프로그래밍의 핵심 요소 중 하나는 비동기식으로 일을 실행할 수 있다는 겁니다. 최근에 FRP 기반의 라이브러리들이 인기가 높아졌고, FRP의 reactive만 접해본 사람이 많은 상황이라, 그것이 Reactive Systems이 제공할 수 있는 모든 것이라고 추측하는 경우가 많습니다. 이벤트와 스트림 프로세싱이 reactive의 상당 부분을 차지하는 것은 맞지만, 그건 분명히 reactive의 요구조건도, 전체도 아니라고 전 주장합니다. 예를 들어 reactive 어플리케이션 및 프로그래밍에 매우 적합한 액터 모델(Akka 또는 Erlang에서 알려진)과 같은 다양한 프로그래밍 모델이 있습니다.

Reactive 라이브러리와 구현체의 일반적인 형태는 보통 어떤 이벤트 루프 또는 스레드풀을 기반으로 한 공유 디스패쳐 인프라를 사용하는 것입니다. 간단한 업무, 액터, 또는 콜백 시퀀스와 같은 저렴한 구조체가 공유 디스패쳐를 통해 스레드와 같은 비싼 리소스를 공유함으로써 하나의 어플리케이션을 여러 개의 코어로 확장할 수 있습니다. 이런 멀티플렉싱 기술은 라이브러리들이 하나의 서버에서 수백만 개의 엔티티를 처리할 수 있도록 해줍니다. 그리하여 우리는 한 명의 유저에 하나의 액터를 배정하여 보다 자연스러운 도메인 모델링이 가능해졌습니다. 스레드를 직접 사용하는 어플리케이션은 금방 무거워지기 때문에 깔끔하게 분리하기가 어렵습니다. 또한, 스레드를 직접 다루는 일은 전혀 간단하지 않고, 작성된 코드의 대부분은 실제 비지니스 로직을 구현하는 것 대신, 여러 스레드 간의 데이터를 동기화하는데 쓰여지게 됩니다.

단점은, 예전의 "누가 빌드를 깨트렸어?"를 대체할만한 "누가 이벤트루프를 블록했어?" 문구에서 알 수 있습니다. 블록킹이란 완료되기 까지 오랜 시간(무한할 수도 있음)이 걸리는 작업을 뜻합니다. 문제가 되는 블록킹의 대표적인 예로는 파일 IO 또는 블로킹 드라이버(대부분의 현재 DB 드라이버)를 사용한 DB 접근이 있습니다. 블록킹 문제를 설명하기 위해 그림 2-1을 살펴보겠습니다. 두 개의 싱글코어 프로세서가 있다고 가정해봅시다(단순화를 위해 하이퍼스레딩과 같은 기술을 사용하지 않는다고 가정). 그리고 우리가 처리하려고 하는 일이 3개의 큐에 들어있다고 합시다. 모든 큐는 동일하게 중요하므로 우리는 모두 최대한 공정하고 (빠르게) 처리하고자 합니다. 공정성 요구 사항은 우리가 블록킹 방법으로 프로그래밍할 땐 자주 생각하지 않는 것입니다. 하지만 비동기식 방법으로 가게 되면 매우 중요해집니다. 명확히하자면, 공정성이란 특정한 큐가 더 빠르지 않게 각각의 큐의 처리 시간이 대략 동일해야 한다는 특성입니다. 그림 2-1의 타임라인 색상은 어떤 프로세서가 해당 프로세스를 처리하고 있는지를 나타낸 것입니다. 우리가 가정한 것에 따르면, 단지 두 개의 프로세스만 병렬로 처리 가능합니다.

![](https://d3ansictanv2wj.cloudfront.net/whyr_0201-e10d2a09c516b2741465e8efab297dca.png)
<그림 2-1> 블록킹 작업(회색)이 리소스를 낭비하여 전체 시스템의 공정성을 저해하고 특정(불운한) 유저들의 체감 응답 속도를 늦춘다.

회색 영역은 동작한 액터가 블록킹 API를 사용하여 파일이나 네트워크에 블록킹 작업을 실행했음을 나타냅니다. 세 번째 액터가 CPU 리소스를 전혀 쓰지 않고 블록킹 콜의 결과가 리턴되기만을 기다리고 있어서 CPU 리소스가 낭비되고 있느 것을 알 수 있습니다. Reactive Systems에서는 이러한 작업을 수행 할 때 스레드를 다시 풀로 반환하여 중간 액터가 메시지 처리를 시작할 수 있게 합니다. 블록킹 작업으로 인해 중간 큐에 기아가 발생하고 있으며 중간 시스템이 처리하는 요청의 응답 속도 지연과 함께 전체 시스템의 공정성을 침해하게 되는 것을 볼 수 있습니다.

일부 사람들은 관찰결과와 다이어그램을 보며 "블록킹은 순수한 악이며 모든 것이 파멸되었다!"라고 잘못 해석합니다. 때로는 reactive 기술에 반대하는 사람들이 현대의 reactive 기술 스택에 대한 두려움(fear), 불확실성(uncertainty) 및 의심(doubt) (FUD, 적극적인 마케팅 방법론)을 전파하기 위해 이 문구를 인용하기도 합니다. 진정한 메시지는 (언제나 그래왔듯이) 블록킹은 신중한 관리가 필요하다는 것입니다!

많은 reative 툴킷(Netty, Akka, Play, RxJava 등)은 블록킹 작업을 처리하기 위한 해결책으로 블록킹 작업을 블록킹 작업만을 위한 스레드풀로 분리시킵니다. 우리는 이러한 기법을 샌드박싱(sandboxing) 또는 벌크헤딩(bulkheading)이라고 합니다. 그림 2-2에서는 프로세서는 이제 실제 코어를 나타내고, 스레드풀을 나타내도록 갱신된 다이어그램을 볼 수 있습니다. 디폴트 풀은 노랑색, 블록킹 콜을 위해 새로 생긴 풀은 회색으로, 두 개의 스레드풀이 있습니다. 블록킹 콜을 실행할 땐 새로 만든 풀에 요청을 넣습니다. 세번째 프로세스가 블록킹 작업으로부터 응답을 기다리는 동안 응용 프로그램의 나머지 부분은 디폴트 풀에서 계속 메세지를 처리할 수 있습니다. 얻게되는 분명한 이점은 블록킹 작업이 메인 이벤트 루프나 디스패쳐를 지연시키지 않는다는 것입니다.

그러나, 이러한 분리 구조에 어찌보면 덜 명백한 이점도 있습니다. 그 중 하나는 비동기식 응용 프로그램 작업을 더 많이 해보기 전까진 그 유용성을 알기 어려울 수도 있습니다. 이제 서로 다른 유형의 작업을 각각의 풀로 분리했기 때문에 특정 풀에 과부하가 걸리기 시작하면 우리의 응용 프로그램의 병목 지점이 어디인지 직감적으로 알 수가 있습니다. 또한, 허용된 횟수를 초과하여 무거운 작업을 실행하지 않도록 스레드풀에 대해 엄격한 상한 제한을 걸 수 있게 됩니다. 예를 들어, 우리가 CPU 집약적인 작업 용 디스패쳐를 설정할 때, 코어가 4개인 환경에서 20개의 작업을 동시에 실행시키는 건 적절하지 않을 것입니다.

![](https://d3ansictanv2wj.cloudfront.net/whyr_0202-5171648d9c6628b2e856b5fe7b090023.png)
<그림 2-2> 블록킹 작업들은 별도의 디스패쳐를 통해 스케줄링 된다(회색). 일반 reactive 작업은 무관하게 기본 디스패쳐에서 진행된다(노란색).

### 최적의 이용률 찾기

이전 섹션에서는 비동기식 API 및 프로그래밍 기술을 사용하면 하드웨어 사용률을 높일 수 있음을 알게 되었습니다. 이것은 바람직해 보이고, 실제로 우리도 우리가 지불하는 하드웨어를 최대한으로 사용하고 싶어 합니다. 그러나 특정 지점을 초과하여 이용률을 높이려 하면 이득은 오히려 줄어들(더 많이 초과하면 손해로 까지) 것입니다. 이 관찰은 1993년 Neil J. Gunther에 의해 공식화되었으며 Universal Scalability Law (USL)라고 불립니다.

USL, Amdahl의 법칙과 큐잉(queueing) 이론 사이의 관계는 그 자체만으로도 별도의 논문이 필요할 정도의 가치가 있는 주제이므로 이 보고서에서는 간단한 개념에 대해서만 설명합니다. 이 섹션을 읽고 흥미가 느껴지고 더 많은 것을 배우고 싶다면 Baron Schwartz (O'Reilly)가 작성한 백서 "Practical Scalability Analysis with the Universal Scalability Law"를 찾아보십시오.

USL은 Gene Amdahl이 1967년에 최초로 정의한 Amdahl의 법칙 - 병렬로 실행할 수 있는 알고리즘의 분량에 따라 속도 향상이 이루어진다는 이론적 설명 - 보다 더 실용적인 모델로 볼 수 있습니다. USL은 커뮤니케이션 비용, 데이터 동기 비용을 공식에 추가하여 한 단계 더 나아갔으며, 시스템이 최적의 이용률을 초과하면 속도가 더 이상 높아지지 않을 뿐만 아니라, 백그라운드에서 처리되는 온갖 조정 작업 때문에 시스템의 총 처리량은 오히려 감소함을 제시하였습니다. 이러한 조정 작업은 하드웨어 단계(프로세서 수와 무관한 메모리 대역폭 등) 또는 네트워크 단계(대역폭 또는 재전송 문제 등)와 상관있을 수 있습니다.

초과 사용 문제는 CPU 뿐만 아니라 네트워크 리소스와 같은 여러 리소스에도 해당됨을 알아야 합니다. 일부 고처리 메세징 라이브러리의 경우, 일반적인 클라우드 셋업에 사용되는 1Gbps 네트워크 대역폭을 모두 사용해버리는 것도 가능합니다(10Gbps 네트워크 인터페이스를 제공하는 Amazon EC2의 high-end 인스턴스와 같이 특수한 네트워크/노드 설정이 가능한 경우는 제외). 따라서 USL은 로컬과 분산 환경 모두에 적용될 수 있지만, 지금은 어플리케이션 단계에 초점을 맞추어 보겠습니다.

### Back-Pressure를 사용하여 최적의 이용률 유지

동기식 API를 사용할 때, 시스템은 블록킹 작업에 의해 "자동으로" back-pressure 됩니다. 블록킹 작업이 완료 될 때 까지 다른 작업을 수행하지 않으므로 기다리면서 많은 자원을 낭비합니다. 하지만 비동기식 API를 사용하면 로직을 최대한으로 수행할 수 있지만 다른 (느린) 시스템이나 어플리케이션의 다른 부분을 압도해버릴 위험이 있습니다. 이 부분에서 back-pressure(또는 flow 컨트롤) 메커니즘이 활용됩니다.

Reactive Manifesto와 마찬가지로 Reactive Streams는 동시 및 분산 응용 프로그램을 개발하는 업계 선두 기업들간의 협업 과정에서 계획되었습니다. 한정된 메모리 기반의 스트림 처리를 위한 상호 운용 가능한 프로토콜의 표준화의 필요성이 대두되었기 때문입니다. 초기에는 Lightbend, Netflix와 Pivotal이 포함되었지만 이후에는 보다 성장하여 RedHat 및 Oracle의 개발자들도 포함되었습니다. 스펙은 다양한 스트리밍 라이브러리 간의 저수준 상호운용성을 목표로 하고 있으며, back-pressure를 라이브러리 사용자들 모르게 적용할 수 있어야 합니다. 1년 이상 스펙을 다듬은 결과, Reactive Streams에 대한 사양, Reactive Streams의 TCK 및 세부 사항은 OpenJDK의 JEP-266의 일부인 "More Concurrency Update" 제안서로 통합되었습니다. 이런 인터페이스들과 헬퍼 함수들이 JDK에 포함되므로써 자바 생태계의 일부가 되었고, Reactive Streams를 구현한 라이브러리들이 이제 JDK에 포함된 인터페이스들을 구현하여 JDK9 출시 이후에도 계속 호환될 것이라고 예상이 됩니다.

주의해야할 점은, back-pressure, Reactive Streams, 또는 다른 어떤 구성 요소도 시스템을 탄력적이고 확장성이 높으며, 반응성이 좋도록 만들기엔 부족하다는 겁니다. 완전히 reactive한 시스템을 구축하려면 여기 언급된 기술들의 조합이 필요합니다. 비동기와 back-pressure가 적용된 API로 우리는 시스템을 초과하지 않는 범위 내에서 한계치까지 끌어올릴 수 있습니다. 얼마큼이 최적의 한계치인지는 트래픽의 갑작스런 튐현상과 리소스의 낭비 간의 균형을 맞춰야 하기 때문에 늘 대답하기가 까다롭습니다. 또한 시스템이 수행하는 작업에 따라 크게 달라집니다. 시작을 위한 기본적인 규칙은(이후에 요구 사항에 맞춰 최적화함) 시스템 이용률을 80% 이하로 유지하는 것입니다.이용률을 최적화하기 위해 활발하게 이뤄졌던 토론과 그 외의 다른 것들은 우수한 Google Maglev 문서에서 읽을 수 있습니다.

이러한 "스스로 제한하기"가 동기식 버전에 비해 전반적인 성능을 저하시킬 수 있지 않겠냐는 의문이 있을 수 있습니다. 이는 유효한 질문이며, 싱글 스레드, raw 출력 벤치마크에서 동기식 버전이 종종 비동기식 버전을 능가합니다. 하지만 현실에 존재하는 데이터는 그런 모습이 아닙니다. Netflix에서 실시한 RxNetty와 Tomcat의 성능 비교 분석에서, Brendan Gregg와 Ben Christensen은 비동기식의 오버헤드와 flow 컨트롤에도 불구하고, 비동기식 서버 구현이 동기식(고도로 튜닝된) Tomcat 서버에 비해 대용량 데이터 처리 시에 훨씬 좋은 응답 지연 속도를 보여줌을 알아냈습니다.

### 스트리밍 API 및 한정된 메모리 Stream 처리의 증가

> 우리는 동일한 강에 두 번 발을 담그는 것은 불가능하다.
>
> &nbsp; - 헤라클리투스

Reactive와 마찬가지로, 스트리밍도 사람들이 "스트림"이라는 단어의 실제 의미가 무엇인지 정의하려는 단계에 와있습니다. 슬프게도, 사람들은 "reactive" 단어 보다도 더 혼란스러워 합니다. 이렇게 혼란스러워진 이유로는, 매우 다른 요구사항을 위해 있는 여러 라이브러리들이 이 단어를 사용하고 있기 때문입니다. 예를 들며, Spark Streaming과 Flink는 대규모 데이터 변환을 다루지만 중소 규모에는 적합하지 않고, 또한, 잘 알려진 Twitter Streaming과 같이 무한 데이터 스트림을 사용하는 HTTP 응답 API의 데이터 소스로 사용할 수도 없습니다.

이 장에서는 스트리밍이 실제로 무엇을 의미하는지, 왜 중요하며, 앞으로 어떻게 될 것인지에 초점을 맞출 것입니다. 같은 동전에는 두 면이 존재합니다: 스트리밍 API의 소비와 생산. API가 다양한 시스템 사이의 통합 레이어 역할을 함에도 불구하고, 시스템 단계가 아닌 어플리케이션 단계를 다루는 챕터에서 이 주제에 대해 논의를 하는 이유가 있습니다. 스트리밍 API 및 한정된 메모리 처리방법이 우리에게 주는 흥미로운 능력과 관련이 있습니다. 특히, 실제 필요한 양보다 더 많은 데이터를 메모리에 불러들이지 못하게 하는 라이브러리들과 API들을 사용하는 것은 우리가 한정된 메모리 파이프라인을 구축할 수 있게 합니다. 이는 용량 계획을 세우기 위한 매우 흥미롭고 유용한 속성으로, 이제 특정 연결이나 스트림이 얼마만큼의 메모리를 사용할지 보장할 수 있고, 용량 추산 계획을 산정할 때 이러한 수치들을 사용할 수 있습니다.

이러한 기능을 Twitter Firehose API와 연관지어 논의해 보겠습니다. Twitter Firehose API는 Twittersphere에서 들어오는 모든 트윗을 수집하고 분석하기 위해 사용하는 API입니다. 당연한 말이지만, 이러한 높은 트래픽의 스트림을 소비하면 수신 측에서도 많은 시스템 자원이 소비됩니다. 만약, 다운스트림(Firehose API를 사용하는 고객)이 소비하는 속도가 방출되는 속도를 따라가지 못한다면 어떻게 될까요?

서버의 관점에서 이 문제를 살펴 보겠습니다. 우리는 백엔드로부터 들어오는 라이브 데이터 스트림을 가지고 있으며, 이들을 해당 서비스의 다운스트림 클라이언트로 보내야합니다. 클라이언트들 중 일부는 느리거나 스트림을 전혀 소비할 수 없을 정도로 문제가 있을 수 있습니다. 이런 경우엔 어떻게 해야할까요? 일반적인 답은 클라이언트가 돌아올 때까지 버퍼링하는 것입니다. 이 답은 맞으면서도 동시에 무섭습니다. 물론 우리는 약간의 버퍼링을 하여 클라이언트가 느려진 속도에서 복귀하여 스트림을 계속 소비하게 되는 것을 원합니다. 그러나 우리는 무한대의 메모리를 가지고 있지 않기 때문에 이러한 트윗들을 무한정 버퍼링할 수 있을 거라 기대할 수 없습니다. 해답은 간단합니다: 한정된 버퍼를 사용 합니다. 예를 들어, 클라이언트가 우리가 이벤트를 발생시키는 속도를 감당할 수 없어 보이면, 클라이언트에게 "우리는 전송할 메세지들을 큐에 쌓아놓고 있습니다. 당신의 큐는 현재 60% 넘게 차있습니다"라는 경고를 보냅니다(실제로 Twitter의 모든 스트리밍 API에 옵션으로 제공됩니다). 이런 전략은 우리의 한정된 크기의 큐들과 우리가 다룰 수 있는 느린 클라이언트의 수에 따라 예측하여 우리의 서비스 레벨과 노드의 이용률을 조절할 수 있으므로 매우 유용합니다.

API의 클라이언트 큐 및 버퍼 크기를 모니터링하는 것은 매우 흥미로운 척도이며 고객과의 상호 작용을 유발할 수도 있습니다. 예를 들어 추가 처리 능력을 제공하거나 클라이언트를 특정 방식으로 최적화하는 것을 제안할 수도 있습니다. 물론 버퍼가 가득 차면 우리의 어플리케이션을 문제없이 유지하기 위해 조치를 취해야합니다. Twitter 예제에서의 해답은 간단합니다: 클라이언트와의 연결을 끊습니다(경고 메시지는 항상 이에 대한 세부 정보를 포함합니다). 그러나 이것이 유일한 방법은 아닙니다. 가장 오래된 요소나 최신 요소를 스트림에서 제외시킬 수도 있습니다. 특히 오래된 요소가 만료되었을 경우 더욱 그렇습니다. 말할 필요도 없이 이러한 결정은 업무 중심적이어야 하지만, 이러한 것을 간단하게 만들 수 있는 핵심 방법은 Akka Streams/HTTP와 같이 overflow 전략을 선택하는 수준으로 간단히 처리 가능한 스트리밍 최우선 라이브러리들을 사용하는 것입니다.

### Reactive는 단일 라이브러리가 아닌, 설계 및 디자인 원칙이다.

> 전체는 각 부분의 합과 다르다.
>
> &nbsp; - Kurt Koffka

당신은 아마 전체가 각 부분들의 합보다 "더 크다"라고 표현한 이 문구를 들어봤을 것입니다. 그러나 실제로 Koffka가 의미한 것은 더 크거나 나은 것이 아닌, 다르다는 것이었음이 밝혀졌습니다(그림 2-3의 "보이지 않는" 삼각형과 같습니다). 이 문구는 우리가 "reactive"란 용어에 처한 우리의 상황에 부합합니다. "Reactive"란 접두어가 붙은 수많은 라이브러리를 쉽게 찾을 수 있고, 그 라이브러리가 수행하는 일(예: 스트림 처리)이 reactive의 전부인 양 reactive의 뜻을 바꾸려 드는 경우도 흔하게 볼 수 있습니다. 우리가 주의해야할 점은, 각각은 매우 뛰어난 구성 요소이지만, 전체 문제를 다루는 경우가 거의 없다는 것입니다(주로 탄력성이나 유연성 처리를 빠트립니다). Reactive Systems에 관한 다음 챕터에서는 reactive에 단순히 어플리케이션 속의 멋진 프로그래밍 모델이 아닌 그 이상의 것이 무엇이 있는지 알아볼 것입니다. 어플리케이션들이 어떻게 서로 통신하고 요구에 맞게 확장 및 축소하는지 그 방법을 살펴 볼 것입니다.

![](https://d3ansictanv2wj.cloudfront.net/whyr_0203-1d04826cffbaf606df117e09540d2fd7.png)
<그림 2-3> 삼각형의 선은 아직 그려지지 않았지만 삼각형의 모서리는 볼 수 있다.

## 챕터3 - 시스템 단계에서의 Reactive

> 단일 액터는 액터가 아닙니다.
> 액터는 시스템으로 구성됩니다.
>
> &nbsp; - 동시 계산의 액터 모델 창시자 Carl Hewitt

> 조직의 문제 중 95% 이상이 개별 직원이 아닌 시스템, 프로세스 및 방법에서 파생됩니다.
> [...]
> 사람들은 최선을 다하고 있지만 최선의 노력으로는 당신의 부적절하고 기능 장애가 있는 시스템을 보완할 수 없습니다.
>
> 시스템을 변경하면 사람들이 하는 일이 바뀝니다. 사람들이 하는 일을 변경해도 시스템이 변경되지는 않습니다.
>
> &nbsp; - Peter R. Scholtes, The Leaders Handbook

우리는 실제로 단일 응용 프로그램의 단일 인스턴스에 대해 거의 이야기하지 않습니다. 대신 다양한 기술로 구현되고 각각 다른 지연 조건과 가동시간 조건을 갖고 있는 여러 가지 서비스로 구성된 시스템에 대해 이야기합니다. 이 장에서 살펴 보겠지만 이전 장에서 다뤘던 많은 개념과 요구 사항은 시스템 단계에도 바로 적용됩니다.

우리는 종종 이것이 단지 시스템의 기술적 측면이라고 생각하지만, 저는 그것이 전체 이야기의 일부일 뿐이라고 생각합니다. 어플리케이션은 기술적인 의미 뿐만 아니라 조직의 의미에서도 확장해야 합니다. 분산 시스템(좋은 예로, 마이크로 서비스)을 사용하면 역할을 여러 어플리케이션으로 나눌 수 있으며, 각각은 잘 정의 된 역할을 가진 자체 팀을 보유합니다. 따라서 분산 시스템을 사용하면 조직의 규모를 확장하는 데 도움이 됩니다. 이들은 독립적으로 개발하고 배포 할 수 있게되므로 프로젝트 사이의 강한 종속성을 없애줍니다. 이러한 독립성 덕분에 서로 다른 팀(이러한 서비스가 사용되는 지구 반대편에서 까지도)에서 서비스를 구축 할 수 있습니다. 분산되는 순간, 시간적으로나 공간적으로나 팀 또는 어플리케이션이 얼만큼 떨어져있는지는 중요하지 않아집니다.

Reactive Systems와 메시징 측면에서의 사고 방식은 실제로 매우 단순하지만, 그것이 쉽다는 것을 뜻하지는 않습니다. 이 두 개념의 차이와 이 둘을 섞지 않는 것의 중요성은 RailsConf 2012 기조 연설[Rich Hickey, “Simplicity Matters” (Speech presented at Rails Conf 2012, Austin, Texas, May 1, 2012), https://www.youtube.com/watch?v=rI8tNMsozo0]에서 Clojure 프로그래밍 언어를 만든 Rich Hickey가 설명했습니다. 그는 이 발표에서 "쉽다"라는 것은 우리에게 익숙한 것이라는 것이 차이라고 주장했습니다. 예를 들어, 지난 매우 복잡한 시스템의 다양한 모듈 사이의 숨겨진 의존성과 깊은 내부 구조를 아는 것도 만약 당신이 지난 10년간 작업해왔을 경우 쉽게 느껴질 것입니다. 뭔가가 당신에게 쉽다는 것이 현재의 복잡한 모습이 더 단순해지는 것은 아닙니다. 한편, 어떤 것의 핵심 개념과 아이디어는 막상 깨닫고 나면 "단순"할 수 있지만, 그것을 배우는 것이 어렵지 않다는 것을 의미하지는 않습니다 .

따라서 우리는 단순과 복잡을 대조하고, 쉬움과 어려움을 대조해야 합니다. 분산 시스템은 어떤 형태로 이루어져있든 어렵습니다. 하나 이상의 컴퓨터 또는 조직 내에서 하나 이상의 팀을 처리해야하는 순간, 하나를 다룰 때보다 훨씬 어려워집니다. 사실, 마이크로 서비스 는 분산 시스템이므로 사소하게 생각해서는 안됩니다. 그러나 통신에 사용할 수 있는 방법은 단순(예: 메시징) 또는 복잡(예 : 풀링, pipelining, circuit-breaking, 죽은 연결을 중단하기 위한 타임아웃 등을 사용하는 요청/응답) 할 수 있습니다.

다음 섹션에서는 시스템 내의 커뮤니케이션 스타일을 살펴보고 확장성(기술적 및 조직적 모두)을 검토 한 다음 기존 코드 베이스에 Reactive Services를 도입하는 방법을 제안할 것입니다.

### 단순히 요청-응답-HTTP를 통한 JSON 뿐만이 아닙니다.

> HTTP/2는 주로 브라우저에서의 웹사이트 문서를 가져오는데 HTTP/1.1 보다 낫도록 만들어졌습니다.
> 우리는 어플리케이션 용으로 HTTP/2 보다 더 잘 할 수 있습니다.
> [Ben Christensen, “Reactivesocket/Motivations.md,” ReactiveSocket on GitHub, https://github.com/reactiveSocket/reactivesocket/blob/master/Motivations.md]
>
> &nbsp; - Ben Christensen, ReactiveSocket

우리는 너무 자주 동기식 요청-응답 패턴이 우리 애플리케이션의 통신 패턴을 지배한다는 것을 볼 수 있습니다. REST가 나쁜 것이라는 말은 아닙니다. 실제로 RESTful 원칙을 사용하여 비동기 통신 패턴을 구현할 수 있습니다. 문제는 실제로 거의 존재하지 않는다는 것입니다. 최근 몇 년 동안 많은 조직이 REST 이념에만 집중하지 않고 "HTTP를 통한 JSON 요청-응답" 방식의 사고를 하고 있는 것으로 나타났습니다. 다행이도 많은 팀이 최근의 Twitter의 Finagle RPC, Google의 새로운 GRPC (Google RPC) 라이브러리 및 Facebook과 Netflix가 연구한 ReactiveSocket 등과 같은 새로운 프로토콜이나 메시징 패턴이 어떤 경우에는 더 적합하다는 것을 천천히 깨닫고 있습니다. "REST"라는 용어는 지난 몇 년간 HTTP를 통한 JSON이란 뜻으로 저하되었지만, 꼭 그렇게 작동할 필요는 없습니다. 제가 이렇게 구분짓는 이유는, REST가 등장한 원 출판물[Roy Thomas Fielding. “Architectural Styles and the Design of Network-based Software Architectures.” (PhD diss., University of California, Irvine, 2000), https://www.ics.uci.edu/~elding/pubs/dissertation/rest_arch_style.htm]에는 HTTP가 전혀 언급되고 있지 않지만, 개발자들과 설계자들은 REST가 꼭 HTTP 내에서 동작하는 설계 방식이라고 이해하고 있습니다. 부분적인 이유로는 Fielding이 URI, HTTP, HTML IETF 워킹 그룹에 참여했었기 때문에 그의 작업이 어떤식으로든 관련이 된 것입니다.

서비스 간의 통신 패턴에 대해 이야기하기 전에 단일 서비스 가 반드시 해당 서비스의 한 인스턴스 에서 처리된다는 것을 의미하지는 않는다는 것을 상기시켜야 합니다. 예를 들어, 이미지 크기 조정 서비스를 고려해보겠습니다. 엔트리 포인트는 단일 API 엔드 포인트이며, 서비스 사용자는 이를 "이미지 스케일링 서비스" ("이미지 스케일링 서비스가 또 다운 되었습니까?"에서와 같이)로 참조합니다. 그러나 내부에서는 동일한 서비스의 여러 인스턴스를 실행하고, 들어오는 이미지들을 내부 클러스터에서 균형을 맞추고 있을 수 있습니다. 외부 API 및 서비스의 상호 작용에 대해 우리가 겪는 트레이드오프가 서비스의 내부 통신을 위한 것과 다소 다를 수 있음을 인식하는 것이 중요합니다. 이것은 DDD 용어의 제한된 컨텍스트에 직접 연결됩니다. 단일 제한된 컨텍스트 내에서는 더 자유롭게 통신할 수 있습니다. 하지만, 컨텍스트 경계를 통과해야 할 때는 우리가 누구와 어떻게 의사소통하느냐에 따라 변화가 필요합니다. 그 차이는, 무엇보다도 서비스가 보통 하나의 팀에 속해있다는 사실 때문입니다. 즉, 우리는 캡슐화를 재발견했지만, 이것은 조직의 경계와 각 팀은 내부적으로 약간씩 다르게 동작한다는 사실에 대한 결과입니다. 회사 내의 모든 통신을 동일한 RESTful 통신 스타일로 강제하는 대신, 각 서비스의 내부 통신에 대한 제약을 완화하는 것이 유리합니다. 단일 팀이 제어하면 더 빠르게 진행될 수 있고 공개API 용 기술과 다른 기술도 사용할 수 있기 때문입니다. 그림 3-1은 커뮤니케이션 스타일에 있어서 외부와 내부의 다른 요구 조건을 나타냅니다.

"Data on the outside versus Data on the inside"란 뛰어는 논문에서 Pat Helland는 데이터가 시스템의 "내부"(즉, 마이크로 서비스가 소유한 내부 데이터 저장소)에서 직면하는 요구 사항과 "외부"(즉, 이 서비스의 소비자에게 데이터를 전송하는 경우)에서 직면하는 요구 사항에는 고유한 차이가 있다고 주장합니다. [Pat Helland, “Data on the Outside versus Data on the Inside,” Proceedings of 2005 CIDR Conference, Asilomar, California, http://cidrdb.org/cidr2005/papers/P12.pdf] 특히, 그는 우리가 보다 효율적인 데이터 저장 방식을 찾을 수 있어야 하기 때문에 내부 데이터 표현은 변할 수 있어야 한다고 합니다(SQL 스키마를 변경하는 등). 하지만, 데이터의 외부 표현은 불변이어야 합니다. 사용자가 연락처 목록을 가져올 수 있는 한, 내부적으로 JOIN을 구현했는지, 종종 갱신되는 미리 계산 된 캐시에서 가져 왔는지는 문제가 되지 않습니다. 저는 같은 생각이 커뮤니케이션 패턴에도 적용된다고 생각합니다. 외부 적으로는 회사 내부(또는 외부)의 다른 팀이 사용할 수 있는 안정적이고 명확한 RESTful API를 공개하거나 다른 사람들이 자유롭게 사용할 수 있는 이벤트를 게시 할 수도 있습니다 (결과적으로 이벤트 기반 아키텍처의 형태). 그러나 내부적으로 우리는 우리의 요구사항에 가장 적합한 통신 프로토콜이 필요합니다. 예를 들어, 내부적으로 바이너리 메시지 형식을 사용한 단방향[메시지에 대한 즉시 응답을 기대하지 않는 메세지 패턴; 요청-응답 프로토콜의 반대] 메세징 형식을 택할 수 있습니다.

![](https://d3ansictanv2wj.cloudfront.net/whyr_0301-4380d2ad2cddef7bfc96160a6d6415cc.png)
<그림 3-1> 데이터는 외부와 내부에 따라 요구사항이 다르다. 통신 패턴과 API를 만드는 것도 마찬가지이다.

RESTful API를 비롯한 엄격한 요청-응답 API의 문제는 개발자가 할 수 있는 일을 제한시키는 것입니다. SOAP도 매우 유사합니다. 예를 들어, HTTP 환경에서 이벤트 스트림을 구독하는 방법은 다소 해킹스럽습니다. 보통 한 줄씩 JSON 데이터로 전송하는 "무한" HTTP 응답을 반환하여 구현됩니다. 그러한 API의 좋은 예가 Twitter Streaming API 입니다.

이 주제에 관해 논의 할 때 종종 다음과 같은 반론을 듣습니다. "나는 완전히 REST로 비동기를 수행 할 수 있습니다." 이 말은 기술적으로 말하면 맞습니다. 그러나 구현 비용을 메시징을 기본적으로 지원하는 솔루션과 비교해볼 때 기하급수적입니다.

RESTful 스타일로 구현 될 때 비동기 작업 API가 어떻게 보일 지 살펴 보겠습니다. 먼저, 제어 흐름을 분석 한 다음 전체 라이프사이클 동안 할당해야 하는 리소스의 수를 살펴 보겠습니다. 요즘 세상에서 REST는 일반적으로 HTTP를 기반으로 구현됩니다(최초의 REST는 HTTP에 대해 아무런 언급도 하지 않은 만큼 꼭 그럴 필요는 없습니다.). 하나의 작업을 생성하기 위해선 /jobs에 PUT 요청을 보낼 수 있고, 그러면 작업 생성 요청이 받아들여졌다는 201 Created 또는 202 Accepted 응답이 올 것입니다. 또, 작업이 완료되었을 때 결과값이 저장된 위치가 Location 헤더에 포함되어 있을 것입니다. 비-reactive 클라이언트는 작업의 상태를 알기 위해 그 결과값 위치를 poll 해야합니다. 또한, 요청과 polling을 하는 프로세스 간의 매핑을 위해 리소스를 유지해야 합니다. 초기의 요청/응답 주기가 우리가 진정 원하는 내용(작업의 결과나 상태)에 대해 아무것도 말해주지 않지만, 응답 없이는 작업의 결과를 찾을 수 없기 때문에 무조건 기다려야만 하는 것에 주목해야 합니다.

Akka 또는 Erlang과 같은 메시징 기반 시스템에서는 동일한 작업을 손쉽게 수행 할 수 있습니다. 메세지에 작업의 상태에 대해 알려달라는 플래그값을 넣고 보내는 것으로 송신측의 일은 끝납니다. 작업이 끝나면 결과를 포함한 메세지를 받게 됩니다. 그 작업이 어디서 끝나고 언제 끝나는지는 상관이 없습니다. 이 방법에서 유지해야하는 데이터나 상태값에 대해 알아보면, 단지 메세지를 받을 주소값이 있는 엔티티가 필요합니다(Erlang에선 프로세스, Akka에선 액터). 그것들은 약 400바이트 정도로 매우 가볍습니다. 결국 우리는 그 메시지에 진전 상황에 대해 알려주고 자하는 깃발을 넣을 수 있으며, 우리는 보내는 쪽에서 끝난다 . 작업이 완료되면 결과와 함께 메시지를 받게됩니다. 비록 구현체에 따라 크기는 다를 수 있지만, 중요한 것은 매우 작다는 것입니다. 다른 시스템과의 reactive 상호작용에서 필요한 오버헤드는 이것이 전부입니다. 커넥션 유지, 왕복 통신, polling 등은 필요치 않습니다.

![](https://d3ansictanv2wj.cloudfront.net/whyr_0302-35974ce1ad34674a7368027942180b5e.png)
<그림 3-2> 동기식 HTTP 콜-스타일 API와 순수 메세징 프로토콜과의 비교. Reactive 버전의 스레드 사용량이 훨씬 적음을 볼 수 있다.

이러한 종류의 문제를 해결하기 위해 HTTP를 사용한 대체 패턴이 수년간 개발되었지만 매우 무거우며 간단히 말해 수많은 작업과 클라이언트가 존재하는 현대 인터넷에서 생존 할 수 없을 것입니다. 이러한 패턴들이 확장성에서 어떤 약점이 있는지 알아보겠습니다. HTTP의 지지자는 우리가 최초에 장기 실행되는 PUT 호출을 해놓고, 작업이 완료될 때 까지 "열어"둘 수 있다고 제안할 수 있습니다.

또한 확장성만 문제가 아니라는 걸 인식하는 것도 중요합니다. 일반 HTTP로 이러한 API를 설계 할 때 결정해야 하는 항목의 수는 기하급수적으로 늘어납니다. 요청이 왔을 때 블록해야 할까요? 어떤 방법을 사용해야 할까요? 클라이언트에게 어떻게 polling하라고 알려줘야 할까요? 차라리 WebSocket을 사용할까요? Server-sent-event는 어떨까요? 메시징을 사용하면 단순해집니다. 뭔가를 원할 때 메시지를 보내면 완료 될 때 메시지를 받게됩니다. 따라서 이러한 관점에서 볼 때 시스템을 설계 할 때 큰 단순화와 적은 두통을 얻을 수 있고. 구현 세부 사항을 논하기보다는 실제 작업에 집중할 수 있습니다.

### 부하를 견디고... 요금을 줄이자!

Reactive Systems의 가장 흥미로운 면은 들어오는 트래픽의 양이 다양할 때 탄력적으로 확장 할 수 있는 능력입니다. 일반적으로 확장은 두 가지 목적 중 하나를 제공합니다: 스케일 아웃(더 많은 기계를 추가) 또는 스케일 업(더 고사양 기계를 추가). 또한, 어플리케이션에 의해 점유된 자원의 수를 감소시켜야 할 때도 있습니다.

"탄력성(elastic)"이라는 단어가 점점 더 중요해지고 있습니다. 그 의미는 우리가 이미 잘 알고 사랑하고 있는 용어인 "확장성(scalability)"과 매우 유사하지만, 이 변화에 주목해야합니다. 클라우드 초기에는 실제 처리하는 부하에 비해 over-provisioning할 때가 많았습니다. 이는 클라우드에서 새로운 노드를 추가, 클러스터로 묶기, 로드밸런서 뒤에 감추기 등의 작업이 상대적으로 단순했기 때문입니다. 알고보니 단순하지 않은 일은 늘어난 노드들의 합리적인 이용률을 회복하는 것이었습니다. 우리는 지난 몇 년간 우리의 어플리케이션에 over-provisioning해왔고, 이는 최근에 컨테이너 및 클러스터 관리(Mesos 등)의 추세가 상승하고 있는 이유입니다. 우리는 지난 몇 년 동안 응용 프로그램을 과도하게 제공했습니다. Over-provisioning 자체는 나쁜 것이 아닙니다. 우리가 클러스터에 더 많은 노드를 추가(새로운 마케팅 캠페인이 매우 성공적이었을 경우 등)하는 동안 트래픽의 갑작스러운 증가를 견딜 수 있도록 "버퍼"를 제공합니다. 그러나 심한 over-provisioning은 우리가 실제로 사용하지 않은 것에 대한 커다란 비용을 내게 되는 큰 문제입니다.

우리는 시스템의 확장성에 대해 이야기 할 때 개발자들이 스케일 아웃 또는 스케일 업에 고정되어 스케일 다운 부분을 놓쳐버리는 것을 발견했습니다. 저는 스케일 다운이 스케일 아웃/업 만큼 중요하다고 생각합니다. 극심한 트래픽 요청이 잠잠해지면 더 이상 비싼 요금을 낼 필요도 없고, 에너지를 아끼고 환경친화적이 되는 것도 중요합니다. Mesos 및 Kubernetes와 같은 새로운 도구는 컨테이너 중심의 어플리케이션 배포 방식과 함께 어플리케이션 일반적인 운영이 더욱 더 ops-less한 미래로 나아가고 있습니다.

Netflix와 Gilt(인기있는 핫딜 사이트)가 사용하는 흥미로운 스케일링 패턴은 예측 확장입니다. 높은 부하가 발생할 시기를 알기 때문에 사전에 서버를 provisioning 할 수 있고 트래픽이 줄어들면 다시 클러스터 크기를 점차 감소시킵니다. 그럼 "reactive 스케일링" 보다 "능동적(proactive) 스케일링"이 더 좋을가요? 아닙니다. Reactive Services는 서비스 확장을 위한 원동력이기 때문에 그렇지 않습니다. 반면, 능동적 스케일링은 스케일링 할 시기를 결정할 때 사용할 수 있는 기술입니다. 예를 들어 Gilt(provisioning 전략에 대해 매우 개방적이었던)와 같은 핫딜 사이트는 대낮에, 정확히 핫딜이 활성화 될 때 트래픽이 급증합니다. [Daniel Bryant, “Scaling Microservices at Gilt with Scala, Docker and AWS,” InfoQ, April 26, 2015, https://www.infoq.com/news/2015/04/scaling-microservices-gilt] 여기서 능동적 확장의 뜻은 예측 가능한 트래픽 증가를 가지고 부하가 서버에 도달하기 전에 노드를 증가시킬 수 있는 것입니다. 이 것은 이 서비스들이 reactive한 것과 밀접히 연관되어 있습니다. 결국 앱을 확장할 수 없다면 능동적으로 확장하는 것 자체가 불가능하기 때문입니다.

### 탄력성이 없다면, 다른 사안은 중요하지 않습니다.

> 탄력성은 물질이나 물체가 다시 원래 모습으로 복귀하는 능력입니다.
> 어려움에서 빨리 회복 할 수 있는 역량.
>
> &nbsp; - Merriam Webster

우리는 성능, 확장성, 메시징 패턴 및 reactive의 다양한 측면에 관해 많이 논의했습니다. 마지막으로, 가장 중요한 특성인 탄력성으로 다시 돌아가 보겠습니다. 결국, 그 어떤 초고속 확장형 시스템이어도 무언가 잘못되었을 때 완전히 사용할 수 없게 되면 도움이 되지 않기 때문입니다.

결국, 시스템을 사용할 수 없는 경우 시스템이 얼마나 빠르고 멋진지는 중요하지 않습니다. Reactive Systems는 일반적으로 장애가 발생한 노드를 새로운 노드로 대체하거나, 시스템에 과부하가 걸릴 경우 더 많은 노드를 추가함으로써 장애를 처리합니다. 그런 의미에서, 이러한 시스템은 스트레스를 견뎌내는 것 뿐만 아니라 실제로 개선이 되는 antifragile이라고 생각할 수 있습니다. [Nassim Nicholas Taleb, Antifragile:  ings  at Gain from Disorder (New York: Ran‐ dom House, 2014)]

우리가 탄력성에 관한 챕터에서 암묵적으로 논의한 또 다른 안티 패턴은 레거시 시스템이 때때로 값 비싼 공통 자원에 대한 접근을 공유하는 경향이 있다는 것입니다. 어플리케이션 단계에서는 변경 가능한 상태에 대해 공유된 동시 접근으로 나타납니다. 시스템 단계에서는 단순히 공유 데이터베이스 또는 이와 유사한 리소스이므로 찾기가 훨씬 쉽습니다. 이러한 리소스의 공통된 주제는 모든 서비스가 작동하기 위해선 공유 상태에 접근을 해야한다는 것입니다. 사실, 2016년에 발생한 Reddit 다운은 정확히 그런 공유 서비스의 오작동으로 인해 발생했습니다. [Gooeyblob, “Why Reddit Was down on Aug 11,” Reddit, August 11, 2016] 즉, 한 곳의 잠재적 장애점이 사이트 전체를 내려가게 만들었습니다.

Reactive Systems는 "자신의 데이터를 직접 소유하자" 패턴을 따르며, 액터에 관해서는 얘기했을 때 이미 논의했습니다. 동일한 패턴이지만 이제 시스템 단계에서 적용됩니다. 만약 시스템이 내부적으로 ZooKeeper에 의존하지 않고 주변 환경에 대해 더 많은 상태를 유지했다면 장애를 견뎌 낼 수 있었을 것입니다 (예를 들어, 공유 리소스 접근이 실패했을 경우 "불확실한" 타임아웃이 트리거 될 수 있고, 서버들이 다운되기 전에 운영 팀이 대응할 시간을 벌어 줄 수 있습니다.) Peer-to-peer 및 masterless 시스템은 고유한 단일 자원 문제를 피하도록 본질적으로 구조화되어 있기 때문에 더욱 좋습니다.

## 챕터4 - Reactive Systems의 구성 요소

> 우리는 너무 많은 벽을 짓고, 충분치 않은 다리를 짓습니다.
> [While often misattributed to Isaac Newton, the origin of this quote goes back to Joseph Fort Newton’s “The One Great Church: Adventures of Faith” (1948), according to Wiki‐ pedia]
>
> &nbsp; - Joseph Fort Newton

시스템은 결코 홀로 존재하지 않으며 동일한 부품으로도 구성되지 않습니다. 현실 세계는 훨씬 더 다양하며, 이 사실을 무시하려고 하면 필연적으로 실망, 또는 최악의 경우, 실패로 이어집니다.

대신, 우리는 이 다양성을 인정하고 시스템의 다양한 각 부분이 자기가 구축 된 분야에 특화 될 수 있는 힘으로 전환시켜야 합니다. 챕터3에서 설명한 것처럼, 이러한 구성 요소를 하나씩 추가하여 시스템 아키텍처를 점진적으로 reactive 원칙 쪽으로 이동시킬 수 있습니다. Reactive System으로의 여행은 길 수도 있지만, 이 보고서에서 논의된 방식으로 아키텍처를 개선할 것이기 때문에 충분히 가치가 있습니다. 또한 낙담하지 않고, 시스템의 어떤 부분이 움직일 가치가 있고, 어떤 부분이 그럴 필요가 없는 지 신중하게 판단하는 것이 중요합니다. 예를 들어, 새로운 기능을 구축하라는 임무가 주어지면, 레거시 코드베이스를 확장하는 대신 reactive 마이크로 서비스로 구축하고 기존 시스템과 통합할 수 있는지 생각해봐야 합니다. 전체 시스템을 새로 구축 할 수 없는 경우, Strangler 패턴을 적용하고 시스템의 해당 특정 부분에 필요한 구성 요소를 선택하면 됩니다.

그럼, Reactive Systems를 구축하는 데 사용할 수 있는 구성 요소는 무엇입니까? 우리는 그 중 몇 가지를 이미 암시적으로 논의했지만, 다양한 기술 모두를 접할 기회가 없었습니다.

예를 들어 스트리밍의 경우에 Akka Streams 및 Akka HTTP 또는 RxJava를 사용하여 스트리밍 API를 만들고 소비하는 방법에 대해 논의했습니다. 그러나 야간 배치 작업에서 스트리밍 데이터 분석의 reactive한 파이프라인으로 이동하는 것에 관해서는 이야기하지 않았습니다. Apache Spark , Flink 또는 Gear Pump로 대표되는 이러한 기술을 사용하면 어플리케이션의 응답성을 향상시킬 수 있습니다. [Apache Gearpump, mostly backed and driven by Intel, the Google MillWheel inspired large scale real-time stream processing engine] 고객에게 보고서를 보내기 위해 야간 배치가 완료되기를 기다리는 대신, 고객의 데이터를 임의로 또는 스트리밍 방식으로 처리하여 고객에게 보다 빠른 응답을 제공 할 수 있습니다.

우리가 언급할 공간이 거의 없었던 다른 주제로는 Mesos와 Mesosphere DC/OS와 같은 클러스터 스케줄러와 플랫폼이 있습니다. Reactive Services를 구축하고 나면 어딘가에 배치해야 할 것입니다. Reactive Services가 제공하는 가능성을 활용하려면 이러한 기능을 지원하는 도구가 필요합니다. 좋은 예로 Mesos가 있습니다. Mesos는 사내 또는 클라우드에 배포 할 수 있으며 어플리케이션의 인스턴스를 클라우드의 리소스에 할당하는 방식을 표준화 해줍니다. 확장성 및 탄력성을 염두에 두고 개발된 reactive 어플리케이션 덕분에 Mesos와 같은 스케줄러는 시스템에 가해지는 압력에 따라 앱의 인스턴스를 중지하고 시작할 수 있습니다.

### 실제 시스템에 Reactive 적용하기

> 완전한 비동기식 아키텍처는 [측정 가능한] 이점을 가지고 있습니다. 그러나 나는 그런 소프트웨어 시스템을 보게 될 것이라 기대하지는 않습니다. 대신, 우리는 혼합된 코드베이스를 다룹니다.
> [Ben Christensen, “Applying reactive Programming with Rx” (Presented at GOTO Chi‐ cago 2015, Jul 15, 2015), https://www.youtube.com/watch?v=8OcCSQS0tug]
>
> &nbsp; - Ben Christensen

우리의 일상 업무의 현실에선 완전히 새로운 것을 시작할 기회가 거의 없습니다. 독립된 프로젝트가 있어도 어느 정도 수준에서는 기존 시스템과 통합해야 하고, 그 프로젝트들은 오늘날 우리가 원하는 탄력성과 의미론 수준으로 구축되지 않았을 수 있습니다. 우리는 기존 시스템을 낮춰 보는 것이 바람직한 접근이라고 믿지 않습니다. 레거시 시스템은 무언가 중요한 것을 전달하는 데 성공했기 때문에 현재까지 작동되고 있는 것입니다. 이 단순한 진리를 받아 들일 수 있게 되면, 앞으로 나아갈 수 있는 생산적인 방법을 찾고 reactive 아키텍쳐와 같은 새로운 기술을 도입할 수 있을 것입니다. 이 섹션에서는 기존의 생태계에 새로운 기술을 도입하기 위한 성공적이고 검증된 접근법을 설명하고자 합니다.

기존 코드베이스에 변화를 주는(특히 다른 패러다임 또는 언어로 이동할 때) 방법 중 하나는 "Ivy 패턴"(때로는 "Strangler 패턴"이라고 지칭[Martin Fowler, “StranglerApplication,” Martinfowler.com, June 29, 2004, http://martinfowler.com/bliki/StranglerApplication.html])을 사용하는 것입니다. 이 패턴은 업계의 여러 프로젝트에 사용되었지만 많은 경우에 알지 못하는 상태로 적용되었습니다. 개념은 그림4-1에서 처럼 다소 단순합니다.

![](https://d3ansictanv2wj.cloudfront.net/whyr_0401-85685c11fd22debfa3e75188855a4ef1.png)
<그림 4-1> Strangler 패턴을 사용하여 Reactive API 뒤에 기존 구현체를 숨기고, 필요할 때만 기존 기능을 신규 기능으로 이전시킨다.

핵심 아이디어는 실제로 문제가 발생할 때까진 작동하는 레거시 코드를 다시 구현하는 것을 피하는 것입니다. 문제가 발생하면 새로운 스타일로 다시 구현할 것을 고려할 수 있습니다(그러나 강제는 아닙니다). 이 패턴의 이름은 담쟁이 식물이 나무를 조이면서 자라는 모습에서 따왔습니다. 아이디어는 동일합니다. 새 시스템은 기존 시스템의 재구현 없이 기존 시스템을 둘러서 구축됩니다. 새 시스템은 더 reactive한 스타일로 개발될 수 있으며 필요한 경우에만 기존 시스템을 재구현 할 수 있습니다. 이 방법은 대게 비참한 실패로 끝나는 위험한 "빅뱅(big bang)" 재구현을 피하는 데 도움이 됩니다. 주로 이전 코드베이스를 과소 평가하거나 이해하려 하지 않기 때문입니다.

### Reactive, 현재와 미래를 위한 아키텍쳐 스타일

> 알아, 당신은 날 미래로 돌려 보내줬어.
하지만 난 돌아 왔어. 난 미래에서 돌아왔어 .
>
> &nbsp; - Marty McFly, Back to the Future

Reactive가 단일 기술이나 라이브러리에 관한 것이 아니라는 것을 일찍 깨닫는 것이 중요합니다. 특정 라이브러리에 대해 설명하는 대신, 우리는 그 배경에 있는 핵심 개념에 대해 설명하였습니다. 이 지식을 통해 여러분은 여정을 계속하고 자신에게 가장 적합하고 이 새로운 프로그래밍 패러다임으로 다가갈 수 있는 도구를 결정할 수 있을 것입니다. 또한, 분산 시스템을 마치 특별한 로컬 실행인 것 처럼 숨겼던 과거의 실수들[Steve Vinoski, “Convenience Over Correctness”, IEEE Internet Computing IEEE Internet Comput. 12, no. 4 (2008), doi:10.1109/mic.2008.75; Waldo et al.; “A Note on Distributed Computing”, IEEE Micro, 1994; and Anne Thomas Manes, “SOA Is Dead; Long Live Services”, Application Platform Strategies Blog, January 5, 2009]로부터 깨달은 점들을 보는 것도 흥미롭습니다. CORBA와 무거운 SOA 프로세스와 같은 동기식 RPC 시스템이 그것입니다. 우리는 이러한 실험들에서 좋은 교훈을 얻었으며 지금은 그 어느 때 보다도 네트워크를 잘 포용하고 있다고 생각합니다. 또, 어떻게든 실패를 피하려 하는 대신, 우리의 시스템 내에서 포용하고, 그에 맞춰 스케일링하고 수용하려 합니다.

물론 유용한 도구들이 존재합니다. 하지만 잘못 사용한다면 최상의 도구나 라이브러리도 문제를 해결해줄 수는 없습니다. 애자일이 IT 업계를 덮쳤을 때와도 비슷합니다. 당시에 팀들은 팀에 어떤 도움이 될지 큰 고민 없이 "책에 쓰여진 대로" "매일 스탠드업 미팅"을 적용했습니다. 그리고 그것이 그들이 갖고 있던 망가진 프로세스들을 해결하지 못하자 애자일 전체를 비난했습니다. 오늘날엔, 어느 정도의 애자일과 린(lean) 방식이 없는 소프트웨어 개발은 상상하기 힘들지만, 어떠한 방법론과도 마찬가지로 여전히 오해의 여지가 있습니다.

따라서 reactive를 적용하기 전에 여러 생각을 하고, 원칙을 이해하고 앞으로 나아가는 것이 중요합니다. 이 보고서가 정확하고 유용한 정보를 제공하여 여러분과 여러분의 팀이 구축하는 시스템에 대해 최상의 결정을 내리는 데 도움이 되기를 바랍니다.