# Opening / 오프닝

## Code of Conduuct

1. Be Agile
2. Be Kind to others
3. Behave Professionally

## Key note

에자일이 대부분을 차지하고 도움이 되는 것이 증명되었지만 성숙도는 낮은 상태

여튼 기쁘다. 

thoughtworks는 대형 기업에 대한 에자일 적용에 집중하고 있다.. 

프로세스 측면

- continuous delivery
- ui 협업 툴
- 데브 옵스 문화

프로덕트 측면

- 뭐... 협업 도구도 개발한다.
- go cd

책도 많이 쓴다.

- 소프트웨어 우수성, it 혁신을 이끌려고 노력한다.
- 끊임 없이 좋은 소프트웨어에 대해 이야기하려고 한다.
- 무료이당
- 이것저것 겁나 한다

개발자 단위

- Agile을 잘하려면 engineering practice 도 잘되어야 한다.(TDD, Test pyramid)
- CLI Build가 필요(뭐 빨리 되고 자주 할 수 이썽야 한다는 뜻)
- 커밋 자주

에자일 팀 레벨

- PP(Agile의 기초)
- 한사람이 하는걸 둘이서 하면 편하당
- 근데 ROI는 증명이 어렵당, 근데 많은 연구들에서 좋다고 한다
    - 지식 공유, 코드 퀄리티, 문제 해결 능력
- CI(코드를 로컬에 방치하거나 브랜칭을 넘나 많이 하면 문제가 생길 수 있당.)
- Trunk-based development
    - 많은 기업에서 branching이 많이 일어나고 있음
    - 용도는 다양함
    - branch는 무료가 아니다. merge하는데 고통을 주기도 한다.
    - 좋은 프로젝트 관리 대신 branch를 사용하는 경향이 있기 때문에 남용할 경우 좋지 않을 수 있당
    - feature flag를 써라
- CD
    - CI랑은 다르다.
    - IT 측면에서 괜찮다고 여겨지면, 배포 가능한 상태로 두고, 간다

Agile Software Architecture

- 에자일이라고 해서 아키텍처가 필요 없는 것은 아니다.
- 아키텍처는 기술적 의사결정자라고 보는 것이 좋겠다
    - 에자일에서는 누군가가 의사 결정을 해주는 것이 중요함
- Consistency, Flexibility는 trade-off 관계인거 같다.

성공적인 Architecture

- 팀과 팀의 비전을 확인하고, 소프트웨어를 잘 전달하는 사람
- 아마존은 6명 이상의 팀을 만들지 않는다.(2 피자 팀)
    - 그걸 넘어가면 더 작은 규모로 분리하고 API/CDC(Consumer-driven Contracts)를 통해 팀간은 커뮤니 케이션을 하도록

CDC 개념에 대해 설명해줌

### microservice

conway's law

- 시스템을 만드는 회사의 조직은 마치 그 조직의 커뮤니케이션 구조를 그대로 가져다 둔 듯한 구조로 제품을 만들게 된다.
- siloed functional team으로 분리되어 개발하면, 아키텍처에도 당연히 그 구조가 반영된다

microservice에서는 의도적으로 팀을 쪼개서 그 커뮤니케이션 구조가 제품으로 이어지도록 유도할 수도 있다.

이렇게 되면 팀이 프로덕트 기준으로 발전될 수 있다

devops = dev + ops

개발자들이 운용에 대해 이해하고 운용 담당자가 개발에 이해할 수 있는 구조, 

- 근데 대부분의 기업에서 데브옵스가 하나의 또 사일로 팀이 되려고 하고 있다.
- 별도의 팀이 아닌 문화가 되어야 한다.
- 디플로이 제대로 못하면서 devops화되면 오히려 좋지 않다.

### 큰 기업으로 넘어가봅시다.

platform이란, SAP, Oracle, Infra, 등등, 다른 뭔가를 하기위한 것이 platform

### Produce, Project

우리 it에서는 project 단위로 돈을 받고 사람을 구한다.

Product에 중심을 두고 생각을 해보면,더 장기적인 관점을 가질 필요가 있당

### Hypothesis-Driven development

1. 문제에 대한 statement를 만들고 가설을 만들고, 실험을 하고, 개선, 
2. 즉 실패를 두려워하지 않고 많은 가설을 세우고 시작허는 것이 실리콘벨리 흐름

회사의 기술이나 문화가 입사하고 싶은 인상을 줘야 함.

### 결론

잘 도입하면 성공한다.
아니면 똥망





