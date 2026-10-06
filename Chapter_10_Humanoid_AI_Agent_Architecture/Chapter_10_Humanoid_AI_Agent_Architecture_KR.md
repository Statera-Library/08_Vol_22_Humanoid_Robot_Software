**Volume 22. Humanoid Robot Software**

# Chapter 10. Humanoid AI Agent Architecture

## 10.01. AI Agent Architecture for Humanoid Perception Act

![](images/image1.png){width="7.268055555555556in" height="7.268055555555556in"}

![](images/image2.png){width="7.268055555555556in" height="7.268055555555556in"}

휴머노이드 인공지능 에이전트(Humanoid AI Agent)는 기존 로봇 소프트웨어를 확장하여 인식(Perception), 추론(Reasoning), 메모리(Memory), 계획(Planning), 물리적 실행(Physical Execution)을 연속적인 폐루프 지능 아키텍처(Closed-Loop Intelligence Architecture)로 구성한다. 센서 관측을 사전에 정의된 행동으로 직접 변환하는 대신, 에이전트는 현재 상황을 해석하고 이를 작업 목표와 연계하며 적절한 행동 전략을 선택한다. 이후 물리적 결과를 관찰하고 목표가 달성될 때까지 내부 상태를 반복적으로 갱신한다. 이러한 인식-행동(Perception-to-Action) 구조는 휴머노이드 인공지능 에이전트 스택(Humanoid AI Agent Stack)의 작업 계획, 메모리, 그라운딩(Grounding), 재계획(Replanning), 안전 기능을 위한 아키텍처적 기반을 제공한다.

인식 계층(Perception Layer)은 머리 카메라(Head Camera), 손목 카메라(Wrist Camera), 마이크로폰(Microphone), 힘-토크 센서(Force-Torque Sensor), 촉각 센서(Tactile Sensor), 고유수용감각(Proprioception), 로봇 상태 추정(Robot State Estimation)으로부터 얻은 이종 관측 정보를 에이전트가 추론할 수 있는 표현으로 변환한다. 휴머노이드의 조작, 보행, 의사소통, 물리적 상호작용은 동시에 발생하므로 인식은 본질적으로 다중모달(Multimodal)이다. 따라서 각 센싱 모달리티(Sensing Modality)를 독립적인 추론 파이프라인으로 처리하기보다 시각 객체, 사람 자세, 언어 명령, 접촉 상태, 로봇 구성, 환경 기하 정보의 시간적·공간적 정렬이 필요하다.

세계 상태 표현(World-State Representation)은 원시 인식(Raw Perception)과 인지적 추론(Cognitive Reasoning) 사이에 위치한다. 이는 사람, 객체, 도구, 표면, 컨테이너, 장애물, 조작 가능 영역, 이동 목표와 같은 개체(Entity)를 해당 속성 및 관계와 함께 표현한다. 관절 상태(Joint State), 균형 상태(Balance Condition), 손 점유 상태(Hand Occupancy), 도달 가능 작업공간(Reachable Workspace), 보행 모드(Locomotion Mode), 현재 행동 상태(Current Action Status)와 같은 로봇 중심 정보도 함께 표현되어야 한다. 이렇게 형성된 상태는 관측 정보가 유입되고 행동이 환경을 변화시킴에 따라 지속적으로 갱신되는 물리적 환경의 운용 추상화(Operational Abstraction) 역할을 한다.

인지 에이전트(Cognitive Agent)는 언어, 사전에 정의된 임무, 운영자 명령 또는 다른 소프트웨어 서비스에서 제공되는 목표와 연계하여 세계 상태를 해석한다. 고수준 추론(High-Level Reasoning)은 액추에이터 명령(Actuator Command)을 직접 생성하는 것이 아니라 무엇이 수행되어야 하는지를 결정한다. 예를 들어 객체를 컨테이너 안에 넣으라는 요청이 주어지면 에이전트는 객체 탐색, 객체 접근, 조작 자세 설정, 파지, 운반, 목적지 식별, 객체 놓기의 과정이 필요하다고 추론할 수 있다. 이러한 분해는 의미론적 작업 추론(Semantic Task Reasoning)을 각 단계를 물리적으로 구현하는 저수준 제어 메커니즘(Low-Level Control Mechanism)과 분리한다.

작업 계획(Task Planning)은 추론된 의도(Intent)를 실행 가능한 스킬(Skill)의 순서로 변환하면서 작업 간 의존성과 물리적 사전조건(Physical Precondition)을 고려한다. 따라서 에이전트 행동은 이동(Navigate), 탐색(Search), 검사(Inspect), 도달(Reach), 파지(Grasp), 들어 올리기(Lift), 운반(Carry), 배치(Place), 전달(Handover), 열기(Open), 닫기(Close), 말하기(Speak), 대기(Wait)와 같은 매개변수화된 기능(Parameterized Capability)으로 표현할 수 있다. 각 기능은 호출 조건, 필요한 매개변수, 예상 결과, 실패 정보를 제공한다. 이러한 스킬 추상화(Skill Abstraction)는 추론 시스템이 관절 수준 명령을 직접 다루는 것을 방지하고 인지 지능과 로봇 실행 사이에 안정적인 인터페이스를 형성한다.

실행 관리자(Execution Manager)는 상대적으로 느리고 비결정적인 추론 과정과 결정론적 로봇 하위 시스템(Deterministic Robot Subsystem)을 연결한다. 스킬이 선택되면 실행 관리자는 내비게이션(Navigation), 보행(Locomotion), 조작(Manipulation), 전신 제어(Whole-Body Control), 인식, 인간-로봇 상호작용(Human-Robot Interaction) 구성요소에 요청을 전달하고 진행 상태를 감시한다. 예를 들어 파지 명령은 객체 자세 추정(Object Pose Estimation), 접근 계획(Approach Planning), 팔 동작, 손 폐쇄, 힘 모니터링(Force Monitoring), 파지 검증(Grasp Verification)을 활성화할 수 있으며, 고수준 에이전트는 모든 중간 궤적을 직접 제어하는 대신 구조화된 완료 결과를 기다린다.

휴머노이드의 신체성(Embodiment)은 이러한 인터페이스를 기존 소프트웨어 에이전트의 도구 호출(Tool Invocation)보다 훨씬 복잡하게 만든다. 모든 기호적 행동(Symbolic Action)은 궁극적으로 도달 가능성(Reachability), 균형(Balance), 충돌 회피(Collision Avoidance), 접촉 안정성(Contact Stability), 액추에이터 제한(Actuator Limit), 사용 가능한 지지 다각형(Support Polygon), 주변 사람과 관련된 물리적 제약을 만족해야 한다. 따라서 의미론적으로 타당한 계획이라도 물리적으로 실행 불가능할 수 있다. 에이전트 아키텍처는 어떤 행동이 바람직한지를 결정하는 과정과 현재 로봇 구성 및 제어 스택에서 그 행동을 안전하게 실행할 수 있는지를 판단하는 과정 사이의 명확한 경계를 유지해야 한다.

메모리(Memory)는 순간적인 인식을 넘어 작업의 연속성을 제공한다. 단기 작업 메모리(Short-Term Working Memory)는 현재 작업, 최근 관측, 활성 객체, 수행된 단계, 중간 결과, 해결되지 않은 조건을 유지한다. 보다 장기적인 의미 메모리(Semantic Memory) 또는 에피소드 메모리(Episodic Memory)는 객체 위치, 환경 지식, 이전에 성공한 절차, 사용자 선호 또는 반복되는 실패 패턴을 보존할 수 있다. 메모리를 통해 에이전트는 더 이상 시야에 존재하지 않는 사건에 대해서도 추론할 수 있으며, 여러 장소를 이동하거나 여러 조작 단계를 수행하는 장기 작업(Long-Horizon Task)에서 동일한 정보를 불필요하게 다시 탐색하는 것을 방지할 수 있다.

물리적 행동은 완벽하게 예측 가능한 결과를 거의 생성하지 않기 때문에 인식-행동 루프(Perception-Action Loop)는 명시적으로 피드백 기반(Feedback-Driven)이어야 한다. 의미 있는 동작이 수행된 이후 로봇은 명령이 완료되었다는 사실만으로 성공을 가정하지 않고 결과 상태를 검증해야 한다. 파지는 시각, 촉각, 힘 또는 손 상태 정보를 통해 확인하며, 이동은 위치 추정(Localization)과 목적지 조건을 통해 검증하고, 배치는 객체가 의도된 위치에 존재하는지를 관찰하여 확인한다. 이러한 검증 과정은 개방루프(Open-Loop) 명령 시퀀스를 예측 결과와 실제 관측 결과의 차이를 감지할 수 있는 에이전트 구조로 전환한다.

따라서 실패 처리(Failure Handling)는 예외적인 소프트웨어 경로가 아니라 아키텍처의 핵심 기능(First-Class Architectural Function)이다. 실행이 실패하면 에이전트는 시도한 행동, 관측된 조건, 신뢰도(Confidence), 관련 오류 상태를 설명하는 구조화된 정보를 전달받아야 한다. 이후 수정된 매개변수로 재시도하거나, 인식을 다시 수행하거나, 대체 스킬을 선택하거나, 작업 순서를 수정하거나, 사람의 지원을 요청하거나, 안전하게 작업을 종료할 수 있다. 이러한 메커니즘은 접촉이 많은 조작(Contact-Rich Manipulation)과 동적 환경(Dynamic Environment)의 불확실성을 계획 단계에서 완전히 제거할 수 없는 휴머노이드 시스템에서 특히 중요하다.

하나의 아키텍처 내부에는 서로 다른 여러 시간 척도(Time Scale)가 공존해야 한다. 전신 제어 및 액추에이터 제어는 수백 헤르츠에서 킬로헤르츠(kHz) 범위로 동작할 수 있고, 인식 파이프라인은 영상 또는 센서 주기로 갱신되며, 학습 기반 정책(Learned Policy)은 수십 헤르츠 수준으로 실행될 수 있다. 반면 언어 추론과 작업 계획은 수백 밀리초에서 수 초의 시간 척도로 동작할 수 있다. 따라서 인공지능 에이전트는 실시간 제어(Real-Time Control)를 대체하는 것이 아니라 감독해야 한다. 빠른 안전 필수 안정화(Safety-Critical Stabilization)는 결정론적 제어 계층에 유지하고, 상대적으로 느린 인지 과정은 목표, 제약조건, 스킬 선택, 작업 수준 적응을 제공한다.

안전 권한(Safety Authority)은 인지 에이전트와 독립적으로 유지되어야 한다. 에이전트가 생성한 행동은 충돌 위험, 관절 및 토크 한계, 균형 실행 가능성, 작업공간 제한, 사람과의 거리, 금지된 행동, 운용 정책을 검사하는 검증 경계(Validation Boundary)를 통과해야 하는 요청으로 취급된다. 비상 정지(Emergency Stop), 보호 행동(Protective Behavior), 낙상 완화(Fall Mitigation)와 같은 핵심 안전 기능은 언어 모델 추론의 성공 여부에 의존해서는 안 된다. 이러한 분리를 통해 인공지능 계획 능력이 향상되더라도 물리적 액추에이터에 대한 무제한 권한을 부여하지 않고 보다 강력한 AI 계획 기능을 도입할 수 있다.

실용적인 휴머노이드 인공지능 에이전트는 하나의 지능 모델(Single Intelligent Model)이 아니라 오케스트레이션 아키텍처(Orchestration Architecture)로 이해하는 것이 적절하다. 인식 모델은 무엇이 존재하는지를 파악하고, 다중모달 그라운딩(Multimodal Grounding)은 명령을 물리적 개체와 연결하며, 메모리는 작업 맥락을 보존한다. 추론은 의도와 의존성을 결정하고, 계획은 스킬을 구성하며, 실행 서비스는 신체화된 기능(Embodied Capability)을 호출하고, 제어기는 실제 동작을 구현하며, 검증 과정은 전체 루프를 닫는다. 이러한 구성은 작업 계획, 인지 메모리, 도구 사용, 장면 메모리(Scene Memory), 실패 인지 재계획(Failure-Aware Replanning), 다중모달 그라운딩, 안전 경계, 평가로 확장되는 휴머노이드 AI 에이전트 구조의 기반을 형성한다.

이러한 모듈형 구성(Modular Organization)은 서로 다른 지능 메커니즘이 하나의 시스템 안에서 공존할 수 있도록 한다. 결정론적 상태 머신(Deterministic State Machine)은 안전 필수 상태 전이를 관리하고, 고전적 계획기(Classical Planner)는 명시적인 작업 제약조건을 적용하며, 학습 정책(Learned Policy)은 정교한 조작 스킬을 수행할 수 있다. 비전-언어-행동 모델(Vision-Language-Action Model)은 범용 행동(Generalist Behavior)을 제공하고, 대규모 언어 모델(Large Language Model)은 의미론적 추론과 작업 분해에 기여할 수 있다. 에이전트 계층은 모든 문제를 단일 모델에 강제로 통합하는 대신 각 구성요소의 강점에 따라 이를 조정함으로써 예측 가능한 제어와 적응 가능한 고수준 지능을 결합한다.

결과적으로 인식-행동 아키텍처(Perception-Act Architecture)는 관찰(Observe), 해석(Interpret), 추론(Reason), 계획(Plan), 실행(Execute), 검증(Verify), 적응(Adapt)이 반복되는 순환 구조를 형성한다. 그 목적은 단순히 휴머노이드가 명령에 지능적으로 반응하도록 만드는 것이 아니라 실제 작업 수행 중 물리적 세계가 변화하는 상황에서도 일관된 행동을 유지하도록 하는 것이다. 의미론적 이해(Semantic Understanding)를 신체화된 상태(Embodied State) 및 검증된 물리적 결과와 지속적으로 연결함으로써, 인공지능 에이전트는 분리되어 있던 인식, 보행, 조작, 비전-언어-행동(VLA), 인간-로봇 상호작용(HRI) 기능을 하나의 통합 자율 휴머노이드 시스템(Integrated Autonomous Humanoid System)으로 전환하는 감독 지능(Supervisory Intelligence)이 된다.

## 10.02. LLM Based Task Planner for Humanoid [w/Code]

![](images/image3.png){width="7.268055555555556in" height="7.268055555555556in"}

대규모 언어 모델 기반 작업 계획(LLM-Based Task Planning)은 자연어로 표현된 목표를 휴머노이드가 실행할 수 있는 구조화된 스킬 시퀀스(Structured Skill Sequence)로 변환하는 의미론적 추론 계층(Semantic Reasoning Layer)을 제공한다. 운영자가 모든 내비게이션, 인식, 조작, 상호작용 명령을 개별적으로 지정하는 대신, 계획기(Planner)는 원하는 결과를 해석하고 이를 중간 행동으로 분해한다. 휴머노이드 인공지능 에이전트 아키텍처(Humanoid AI Agent Architecture)에서 이러한 기능은 고수준 의도(High-Level Intent)를 로봇이 보유한 신체화된 기능(Embodied Capability)과 연결함으로써 인식-행동 프레임워크(Perception-Action Framework)를 확장한다.

대규모 언어 모델 계획기(LLM Planner)는 관절 위치(Joint Position), 토크(Torque), 발걸음(Footstep), 제어 궤적(Control Trajectory)을 직접 생성하지 않는다. 주요 역할은 기호적·의미론적 표현(Symbolic and Semantic Representation)을 기반으로 작업 수준 추론(Task-Level Reasoning)을 수행하는 것이다. 예를 들어 "주방에 있는 병을 테이블 근처의 사람에게 가져다줘"라는 명령은 주방 위치 확인, 주방으로 이동, 병 탐색, 객체 접근, 파지, 대상 사람에게 복귀, 안전한 전달(Handover) 과정으로 분해될 수 있다. 각각의 선택된 스킬을 실제로 구현하는 것은 저수준 로봇 모듈(Lower-Level Robot Module)의 역할로 유지된다.

따라서 실용적인 계획기는 명확하게 정의된 스킬 인터페이스(Skill Interface)를 필요로 한다. 휴머노이드는 이동(Navigate), 탐색(Search), 감지(Detect), 검사(Inspect), 도달(Reach), 파지(Grasp), 배치(Place), 열기(Open), 닫기(Close), 운반(Carry), 전달(Handover), 말하기(Speak), 대기(Wait), 지원 요청(Request Assistance)과 같은 기능을 제공할 수 있다. 각 스킬은 필요한 매개변수(Parameter), 사전조건(Precondition), 예상 효과(Expected Effect), 실행 제약조건(Execution Constraint), 가능한 실패 상태(Failure State)를 정의해야 한다. 대규모 언어 모델은 물리적 로봇이 실행할 수 없는 임의의 액추에이터 동작을 만들어내는 대신 제한된 행동 어휘(Bounded Action Vocabulary)를 기반으로 추론한다.

계획 과정은 사용자 명령을 현재 세계 상태(Current World State)에 그라운딩(Grounding)하는 것에서 시작한다. "저 상자", "왼쪽 선반", "나를 부른 사람"과 같은 표현은 언어만으로 신뢰성 있게 해석할 수 없다. 계획기는 인식(Perception), 객체 추적(Object Tracking), 의미론적 장면 메모리(Semantic Scene Memory), 인간 상호작용(Human Interaction), 로봇 상태 추정(Robot State Estimation)으로부터 문맥 정보를 제공받아야 한다. 다중모달 그라운딩(Multimodal Grounding)은 언어적 개체를 관측 가능한 사람, 객체, 장소, 행동과 연결하여 생성된 작업 단계가 로봇이 식별하고 조작할 수 있는 물리적 대상을 참조하도록 한다.

계획 문맥(Planning Context)은 로봇의 현재 신체화 상태(Embodied State)도 포함해야 한다. 이미 객체를 들고 있거나, 계단 위에 서 있거나, 하중을 운반하거나, 한쪽 팔을 사용할 수 없거나, 배터리 용량이 감소한 휴머노이드는 제약이 없는 초기 상태의 로봇과 동일한 계획을 실행할 수 없다. 관련 문맥에는 로봇 자세(Robot Pose), 손 점유 상태(Hand Occupancy), 도달 가능 영역(Reachable Region), 보행 모드(Locomotion Mode), 활성 접촉 상태(Active Contact), 배터리 상태(Battery Status), 안전 조건(Safety Condition), 현재 사용 가능한 스킬 등이 포함될 수 있다. 이러한 정보는 언어 기반 추론(Language-Based Reasoning)을 물리적 현실의 제약조건과 연결한다.

장기 작업 명령(Long-Horizon Command)은 하나의 명령 안에 상호 의존적인 여러 하위 작업(Subtask)이 포함될 수 있으므로 계층적 분해(Hierarchical Decomposition)가 필요하다. 대규모 언어 모델은 먼저 고수준 작업 시퀀스(High-Level Task Sequence)를 구성한 다음 실제 실행 시점이 가까워지면 개별 단계를 구체화할 수 있다. 예를 들어 "작업대를 준비하라"는 명령은 작업공간 검사, 필요한 도구 식별, 누락된 객체 수집, 지정된 위치에 객체 배치, 완료 상태 검증으로 확장될 수 있다. 계층적 계획(Hierarchical Planning)은 불필요한 세부사항을 제한하면서 환경 정보가 확보되는 시점에 보다 구체적인 행동을 생성할 수 있도록 한다.

사전조건(Precondition)과 사후조건(Postcondition)은 확률적인 언어 추론(Probabilistic Language Reasoning)과 결정론적인 작업 실행(Deterministic Task Execution)을 연결하는 중요한 역할을 한다. 파지(grasp(object))를 실행하기 전에 시스템은 객체가 감지되었는지, 객체 자세의 신뢰도가 충분한지, 로봇이 도달 가능한 구성에 있는지, 목표 손을 사용할 수 있는지를 확인할 수 있다. 실행 이후에도 계획기는 객체가 성공적으로 획득되었다고 단순히 가정해서는 안 된다. 다음 행동으로 진행하기 전에 인식, 촉각 센싱(Tactile Sensing), 힘 정보(Force Information), 조작기 상태(Manipulator State)를 통해 객체-손 내부 상태(Object-in-Hand)와 같은 검증된 사후조건이 확립되어야 한다.

제한되지 않은 자연어 응답보다 구조화된 계획기 출력(Structured Planner Output)을 사용하는 것이 적합하다. 대규모 언어 모델은 행동 식별자(Action Identifier), 대상 개체(Target Entity), 매개변수, 예상 결과, 제약조건, 복구 대안(Recovery Alternative)을 포함하는 중간 표현(Intermediate Representation)을 생성할 수 있다. 별도의 검증 구성요소(Validation Component)는 이 표현을 파싱하고 알려지지 않은 행동, 잘못된 인수(Argument), 접근 불가능한 자원, 금지된 동작을 거부한다. 이러한 아키텍처는 대규모 언어 모델을 추론 구성요소로 활용하면서 인지 계층과 실행 가능한 휴머노이드 제어 스택 사이에 소프트웨어로 정의된 인터페이스를 유지한다.

작업 계획(Task Plan)은 순차적(Sequential), 조건부(Conditional), 병렬적(Parallel) 관계를 결합할 수 있다. 일부 행동은 반드시 정해진 순서대로 실행되어야 하지만, 인식 또는 모니터링 프로세스는 이동과 동시에 수행될 수 있다. 문이 열려 있는지, 요청한 객체가 존재하는지, 사람이 전달을 수락했는지와 같이 관측 결과에 따라 다음 행동이 달라지는 경우에는 조건 분기(Conditional Branch)가 필요하다. 따라서 계획기는 단순한 선형 명령 목록에만 의존하기보다 행동 트리(Behavior Tree), 작업 그래프(Task Graph), 유한 상태 구조(Finite-State Structure), 기호 계획(Symbolic Plan)과 유사한 표현을 활용하는 것이 효과적이다.

실행 피드백(Execution Feedback)은 계획 문맥을 지속적으로 변경한다. 의미 있는 행동이 수행될 때마다 실행 관리자(Execution Manager)는 해당 스킬이 성공했는지, 실패했는지, 시간 초과(Time-Out)가 발생했는지, 예상하지 못한 결과가 발생했는지를 보고한다. 계획기는 관측된 상태를 예상된 사후조건과 비교하고 기존 계획이 여전히 유효한지를 판단할 수 있다. 이를 통해 언어 추론이 처음 한 번 완전한 계획을 생성한 뒤 세계가 변하지 않는다고 가정하는 대신, 물리적 증거에 반복적으로 그라운딩되는 계획-실행-관찰-재계획 루프(Plan-Execute-Observe-Replan Loop)가 형성된다.

실패 인지 재계획(Failure-Aware Replanning)은 조작과 보행이 불확실한 환경에서 수행되는 휴머노이드에게 특히 중요하다. 객체를 파지할 수 없는 경우 계획기는 다른 시점을 요청하거나, 신체 위치를 변경하거나, 반대쪽 손을 사용하거나, 대체 파지 방법을 선택하거나, 사람에게 지원을 요청할 수 있다. 이동 경로가 차단된 경우에도 전체 임무를 다시 생성하지 않고 해당 하위 작업만 교체할 수 있다. 따라서 복구(Recovery)는 이미 성공적으로 완료된 작업 진행 상태를 보존하면서 새로운 관측으로 인해 무효화된 부분만 수정해야 한다.

메모리(Memory)는 대규모 언어 모델 계획기가 장시간 지속되는 작업에서도 일관성을 유지하도록 한다. 작업 메모리(Working Memory)는 현재 목표, 완료된 단계, 해결되지 않은 조건, 최근 관측된 개체, 이전 실패 정보를 보존할 수 있다. 의미론적 장면 메모리(Semantic Scene Memory)는 알려진 객체 위치와 환경 관계에 관한 정보를 제공하며, 에피소드 메모리(Episodic Memory)는 이전 상호작용에서 성공한 전략을 유지할 수 있다. 이러한 메모리 시스템은 반복적인 추론을 줄이고 이후의 계획 결정이 이전 실행 단계에서 획득한 정보를 활용할 수 있도록 한다.

도구 사용(Tool Use)은 계획기의 능력을 순수한 언어적 추론을 넘어 확장한다. 로봇 인식 서비스(Robot Perception Service), 지도 질의(Map Query), 객체 데이터베이스(Object Database), 동작 실행 가능성 검사(Motion Feasibility Check), 파지 계획기(Grasp Planner), 내비게이션 시스템(Navigation System), 진단 인터페이스(Diagnostic Interface)를 호출 가능한 도구로 제공할 수 있다. 정보가 불확실할 경우 대규모 언어 모델은 결과를 임의로 생성하는 대신 적절한 서비스를 요청해야 한다. 예를 들어 객체 감지를 호출하여 물체를 확인하고, 의미 지도(Semantic Map)를 질의하여 목적지를 찾거나, 조작 단계를 결정하기 전에 도달 가능성 검사(Reachability Test)를 요청할 수 있다.

안전 제약조건(Safety Constraint)은 계획기의 프롬프트 내부에 텍스트로만 존재하는 것이 아니라 계획기 외부를 둘러싸는 독립적인 구조로 구현되어야 한다. 생성된 행동은 충돌 위험(Collision Risk), 사람과의 거리(Human Proximity), 제한 구역(Restricted Area), 관절 및 토크 한계(Joint and Torque Limit), 균형 실행 가능성(Balance Feasibility), 탑재하중 한계(Payload Limit), 운용 권한(Operational Permission)에 대한 독립적인 검사를 통과해야 한다. 언어 모델은 목표와 의미론적으로 일치하지만 물리적으로 위험한 행동을 제안할 수 있으므로 안전 아키텍처(Safety Architecture)는 계획기의 신뢰도와 관계없이 에이전트 요청을 수정, 거부, 일시정지 또는 종료할 권한을 유지해야 한다.

지연시간(Latency)과 계산 비용(Computational Cost) 역시 아키텍처에 영향을 미친다. 고수준 대규모 언어 모델 추론은 작업 전환(Task Transition)의 시간 척도에서 수행할 수 있지만, 균형 제어, 전신 제어, 충돌 대응, 액추에이터 조절은 훨씬 빠른 결정론적 루프(Deterministic Loop)를 요구한다. 따라서 모든 센서 프레임마다 재계획을 수행하기보다 의미 있는 상태 전이, 실패, 모호성(Ambiguity), 목표 변경이 발생할 때 재계획을 실행해야 한다. 반복적으로 수행되는 물리적 행동은 캐시된 스킬(Cached Skill) 또는 학습 정책(Learned Policy)에 유지하고, 대규모 언어 모델은 보다 넓은 문맥을 필요로 하는 의미론적 의사결정에 집중할 수 있다.

계획기의 신뢰성(Planner Reliability)은 의미론적 수준과 물리적 수준 모두에서 평가해야 한다. 논리적으로 일관된 작업 분해라 하더라도 행동을 실제로 실행할 수 없거나, 필요한 사전조건이 누락되거나, 생성된 행동이 비용이 높은 복구 절차를 반복적으로 호출한다면 충분하지 않다. 따라서 평가는 작업 완료(Task Completion), 계획 유효성(Plan Validity), 불필요한 행동 수(Unnecessary Action Count), 실행 효율성(Execution Efficiency), 복구 성공률(Recovery Success), 그라운딩 정확도(Grounding Accuracy), 안전 위반(Safety Violation), 변화하는 환경에 대한 강건성(Robustness)을 포함할 수 있다. 이러한 지표는 유창한 언어 생성과 효과적인 신체화 계획(Embodied Planning)을 구분한다.

궁극적으로 대규모 언어 모델 기반 작업 계획기(LLM-Based Task Planner)는 인간 수준의 목표(Human-Level Objective)와 휴머노이드 수준의 스킬(Humanoid-Level Skill)을 연결하는 숙고 계층(Deliberative Layer)으로 기능한다. 인식은 현재 상황을 파악하고, 그라운딩은 언어를 물리적 개체와 연결하며, 메모리는 관련 이력을 보존하고, 대규모 언어 모델은 목표를 분해하여 행동을 선택한다. 실행 서비스는 로봇 기능을 호출하고 피드백은 그 결과를 검증한다. 이러한 폐루프 구조(Closed-Loop Organization)를 통해 언어 모델은 로봇을 직접 제어하는 제어기가 아니라 인식, 보행, 조작, 상호작용, 복구 기능을 조정하는 제약된 작업 추론기(Constrained Task Reasoner)로서 신체화된 휴머노이드 에이전트(Embodied Humanoid Agent)의 핵심 구성요소가 된다.

## 10.03. Cognitive Architecture Perception Memory Action [w/Code]

![](images/image4.png){width="7.268055555555556in" height="7.268055555555556in"}

휴머노이드 로봇을 위한 인지 아키텍처(Cognitive Architecture)는 인식(Perception), 메모리(Memory), 추론(Reasoning), 행동(Action) 사이의 지속적인 상호작용으로 지능을 구성한다. 인식은 환경과 로봇 내부에서 현재 무엇이 발생하고 있는지를 추정하고, 메모리는 현재 관측만으로 복원할 수 없는 정보를 보존하며, 행동은 물리적 세계를 변화시킨다. 이러한 기능은 독립적인 모듈로 동작하는 것이 아니라 모든 행동이 새로운 관측을 생성하고 로봇의 내부 상태를 갱신하는 반복적인 인지 루프(Cognitive Loop)를 형성한다.

인식(Perception)은 물리적 환경과 인지 시스템(Cognitive System)을 연결하는 인터페이스를 형성한다. 카메라(Camera), 마이크로폰(Microphone), 촉각 센서(Tactile Sensor), 힘-토크 센서(Force-Torque Sensor), 고유수용감각(Proprioception), 상태 추정(State Estimation)은 서로 다른 주기와 추상화 수준에서 관측 정보를 생성한다. 인지 아키텍처는 모든 원시 측정값(Raw Measurement)을 고수준 추론에 전달할 필요가 없다. 대신 센서 스트림을 작업 의사결정을 지원할 수 있는 개체(Entity), 사건(Event), 공간적 관계(Spatial Relationship), 사람의 활동, 접촉 상태(Contact Condition), 신뢰도 추정(Confidence Estimate)으로 변환한다.

이렇게 생성된 지각 상태(Perceptual State)는 관측(Observation)과 해석(Interpretation)을 구분해야 한다. 카메라는 특정 영상 영역에 객체가 존재한다는 증거를 제공할 수 있고, 인식 모델은 객체의 정체성과 자세(Pose)를 추정하며, 인지 계층은 이를 현재 작업에 필요한 도구로 해석할 수 있다. 이러한 구분을 유지하면 시스템이 불확실성(Uncertainty)을 명시적으로 표현할 수 있다. 신뢰도가 충분하지 않은 경우 에이전트는 다른 시점을 확보하거나, 객체에 접근하거나, 추가 설명을 요청하거나, 행동하기 전에 전문화된 인식 기능을 호출할 수 있다.

작업 메모리(Working Memory)는 현재 활성화된 작업에 필요한 정보를 유지한다. 여기에는 사용자의 명령, 작업 목표(Task Goal), 현재 계획(Current Plan), 최근 감지된 객체, 선택된 대상, 완료된 행동, 해결되지 않은 조건, 실행 실패 등이 포함될 수 있다. 이러한 표현은 각 추론 주기가 아무런 문맥 없이 처음부터 시작되는 것을 방지한다. 휴머노이드가 다른 방에 있는 객체를 가져오기 위해 현재 공간을 떠나는 경우에도 작업 메모리는 이동한 이유와 객체를 획득한 이후 무엇을 수행해야 하는지를 보존한다.

의미 메모리(Semantic Memory)는 객체, 장소, 스킬(Skill), 관계에 관한 비교적 지속적인 지식을 표현한다. 예를 들어 캐비닛에 도구가 들어 있다는 사실, 작업대에 지정된 자재 영역이 있다는 사실, 특정 객체를 양손으로 조작해야 한다는 사실, 특정 이름의 장소가 의미 지도(Semantic Map)의 특정 영역에 대응한다는 정보를 표현할 수 있다. 일시적인 센서 관측과 달리 의미 지식(Semantic Knowledge)은 시간에 걸친 추론을 지원하며, 휴머노이드가 미래의 행동을 계획할 때 이전에 구축된 환경 구조를 활용할 수 있도록 한다.

에피소드 메모리(Episodic Memory)는 작업별 경험(Task-Specific Experience)을 보존함으로써 의미 메모리를 보완한다. 하나의 에피소드(Episode)는 초기 상황, 선택된 행동, 중요한 관측, 실패, 복구(Recovery), 상호작용의 최종 결과를 기록할 수 있다. 이후 유사한 상황이 발생하면 에이전트는 관련 경험을 검색하여 계획에 활용할 수 있다. 그러나 환경은 변화할 수 있으므로 에피소드 메모리를 의심할 수 없는 절대적인 사실로 취급해서는 안 된다. 검색된 경험은 현재 인식 결과와 다시 조정되어야 하는 문맥적 증거(Contextual Evidence)로 활용된다.

통합 인지 상태(Unified Cognitive State)는 선택된 지각 정보와 관련 메모리 그리고 로봇의 신체화 상태(Embodied Condition)를 결합한다. 여기에는 객체의 정체성과 자세, 사람과 그들의 활동, 공간적 관계, 활성 작업 목표, 손 점유 상태(Hand Occupancy), 균형 상태(Balance Condition), 도달 가능 영역(Reachable Region), 보행 모드(Locomotion Mode), 최근 행동, 안전 제약조건(Safety Constraint)이 포함될 수 있다. 이러한 표현은 인식, 계획, 실행, 메모리 서비스가 모든 하위 시스템에서 원시 센서 데이터를 개별적으로 해석하지 않고도 정보를 교환할 수 있는 공유 문맥(Shared Context) 역할을 한다.

전체 센서 및 메모리 이력은 매 추론 주기마다 처리하기에는 지나치게 크기 때문에 주의(Attention)와 문맥 선택(Context Selection)이 필요하다. 인지 시스템은 예상하지 못한 사건을 감지할 수 있는 기능을 유지하면서 현재 목표와 관련된 정보를 우선적으로 처리해야 한다. 예를 들어 객체 전달(Handover) 과정에서는 대상 사람, 들고 있는 객체, 손 자세, 접촉력(Contact Force), 주변 장애물이 우선적으로 처리될 수 있으며 관련성이 낮은 객체는 낮은 세부 수준으로 유지할 수 있다. 따라서 주의는 환경 인식을 제거하지 않으면서 계산 자원(Computational Resource)을 관리한다.

추론(Reasoning)은 이렇게 선택된 인지 상태를 기반으로 다음에 무엇을 수행해야 하는지를 결정한다. 작업에 따라 대규모 언어 모델(LLM), 기호 계획기(Symbolic Planner), 행동 트리(Behavior Tree), 학습 기반 정책 선택기(Learned Policy Selector), 또는 이들을 결합한 하이브리드 메커니즘(Hybrid Mechanism)이 참여할 수 있다. 추론 계층은 목표, 현재 조건, 기억된 정보, 스킬의 사전조건(Precondition), 예상 결과를 평가한다. 그 출력은 일반적으로 액추에이터 명령을 직접 지정하는 것이 아니라 실행 가능한 스킬 또는 계획 결정을 식별하여 인지와 실시간 물리 제어 사이의 분리를 유지해야 한다.

행동 선택(Action Selection)은 인지적 의사결정을 로봇의 신체화된 기능(Embodied Robot Capability)에 대한 요청으로 변환한다. 선택된 동작은 내비게이션(Navigation), 보행(Locomotion), 도달(Reach), 파지(Grasp), 양손 조작(Bimanual Manipulation), 발화(Speaking), 검사(Inspection) 또는 사전에 정의된 다른 스킬을 호출할 수 있다. 각각의 스킬은 운동학적(Kinematic), 동역학적(Dynamic), 접촉(Contact), 균형(Balance) 제약조건을 적용하는 저수준 동작 계획 및 제어 구성요소를 통해 실행된다. 이러한 계층 구조를 통해 인지 추론은 의미 있는 행동을 사용하고 결정론적 제어기(Deterministic Controller)는 안전한 실행에 필요한 빠른 물리적 프로세스를 관리할 수 있다.

행동 결과가 다시 인식되고 해석되기 전까지 인지 루프는 완성되지 않는다. 명령이 완료되었다는 사실만으로 의도한 상태가 달성되었다고 판단할 수 없다. 파지 후에는 객체가 실제로 안정적으로 확보되었는지를 판단해야 하며, 객체를 배치한 후에는 최종 위치를 검증해야 한다. 또한 사람에게 말을 한 이후에는 상대방이 반응했는지를 관찰해야 할 수도 있다. 검증된 결과(Verified Outcome)는 작업 메모리와 인지 상태를 갱신하며, 계획을 계속 진행할지, 다시 시도할지, 수정할지를 결정하는 데 필요한 증거를 제공한다.

메모리 기록(Memory Writing) 역시 메모리 검색과 마찬가지로 선택 과정이 필요하다. 모든 센서 샘플과 중간 상태를 인지 메모리에 저장하면 불필요하게 규모가 커지고 검색 품질이 저하될 수 있다. 따라서 아키텍처는 성공적인 스킬 완료, 변경된 객체 관계, 중요한 사람의 지시, 반복되는 실패, 새로운 환경 정보, 복구 결과와 같이 작업과 관련된 사건을 선택적으로 보존해야 한다. 상세한 센서 로그(Sensor Log)는 별도의 데이터 시스템에 유지할 수 있으며, 인지 메모리는 미래의 추론에 유용한 압축된 표현(Compact Representation)을 유지한다.

시간적 추론(Temporal Reasoning)은 작업이 연속적으로 변화하는 물리적 상태를 거쳐 진행되기 때문에 휴머노이드에서 특히 중요하다. 에이전트는 현재 참인 상태, 과거에 참이었던 상태, 행동 이후 예상되는 상태를 구분해야 한다. 테이블 위에 있다고 기억된 객체가 이동되었을 수 있고, 사람이 작업공간을 떠났을 수 있으며, 이전에 닫혀 있던 문이 현재는 열려 있을 수 있다. 시간 정보가 포함된 관측(Time-Stamped Observation)과 메모리 갱신은 오래된 정보(Stale Information)가 현재의 물리적 현실로 잘못 취급되는 것을 방지한다.

불확실성(Uncertainty)은 모듈 경계에서 사라지는 것이 아니라 인식, 메모리, 행동 선택 전반으로 전달되어야 한다. 객체의 정체성이 불확실할 수 있고, 기억된 위치 정보가 오래되었을 수 있으며, 예측된 행동 결과의 신뢰도가 제한적일 수도 있다. 인지 아키텍처는 이러한 불확실성을 활용하여 즉각적인 실행보다 추가 관측이 필요한 시점을 결정할 수 있다. 따라서 능동 인식(Active Perception)은 인지의 일부가 되며, 로봇은 보다 안전한 의사결정에 필요한 정보를 획득하기 위해 센서 또는 자신의 신체를 의도적으로 움직인다.

실패(Failure)는 메모리와 행동 사이의 또 다른 중요한 상호작용을 형성한다. 실행 시스템이 파지, 이동 시도 또는 조작 스킬의 실패를 보고하면 인지 상태는 실패 자체뿐만 아니라 실패가 발생한 주변 조건도 함께 기록한다. 이후 추론 시스템은 재시도, 매개변수 수정, 다른 스킬 선택, 인식 재수행 또는 사람에게 지원 요청 중 적절한 방법을 결정할 수 있다. 반복되는 실패는 메모리에 요약되어 저장될 수 있으며, 이를 통해 에이전트가 이미 효과가 없다고 확인된 전략을 무한히 반복하는 것을 방지한다.

안전 정보(Safety Information)는 인지 루프 내에서 지속적으로 사용할 수 있어야 하며 동시에 인지 시스템과 독립적으로 강제되어야 한다. 에이전트는 제한 구역(Restricted Region), 주변 사람, 탑재하중 제한(Payload Limitation), 불안정한 자세(Unstable Posture), 금지된 행동에 대해 추론할 수 있지만, 강제적인 안전 메커니즘(Hard Safety Mechanism)은 실행에 대한 최종 권한을 유지해야 한다. 인지 추론이 안전하지 않은 행동을 요청하는 경우 외부 안전 계층(External Safety Layer)이 이를 거부하거나 수정할 수 있다. 이를 통해 문맥 인지형 안전 추론(Context-Aware Safety Reasoning)과 결정론적 물리 보호(Deterministic Physical Protection)를 결합할 수 있다.

인지 시스템의 각 부분은 서로 다른 시간 척도(Time Scale)에서 동작한다. 인식과 상태 추정은 지속적으로 갱신될 수 있고, 작업 추론은 의미 있는 사건을 중심으로 수행될 수 있으며, 메모리 검색은 문맥상 필요할 때 실행될 수 있다. 반면 실시간 제어기(Real-Time Controller)는 초당 수백 번에서 수천 번의 주기로 동작할 수 있다. 이벤트 기반 인터페이스(Event-Driven Interface)를 사용하면 인지 계층이 모든 센서 프레임을 불필요하게 추론하는 것을 방지하면서 중요한 상태 변화, 실패, 사람의 명령, 안전 사건이 발생할 경우 에이전트의 의사결정 문맥을 즉시 갱신할 수 있다.

결과적으로 인식-메모리-행동 아키텍처(Perception-Memory-Action Architecture)는 자율 휴머노이드 행동에 필요한 내부적 연속성(Internal Continuity)을 제공한다. 인식은 인지 시스템을 현재의 물리적 세계에 그라운딩(Grounding)하고, 메모리는 현재와 관련된 과거 정보를 연결하며, 추론은 목표와 문맥을 의사결정으로 변환하고, 행동은 물리적 실행을 통해 이러한 결정을 검증한다. 휴머노이드는 관찰(Observe), 기억(Remember), 결정(Decide), 행동(Act), 검증(Verify)을 반복함으로써 장기 작업(Long-Horizon Task), 변화하는 환경, 인간과의 상호작용, 실패 인지형 자율 운용(Failure-Aware Autonomous Operation)을 지원할 수 있는 일관된 인지 프로세스(Cognitive Process)를 형성한다.

## 10.04. Multi Step Task Planning with Tool Use [w/Code]

![](images/image5.png){width="7.268055555555556in" height="7.268055555555556in"}

도구 사용을 포함한 다단계 작업 계획(Multi-Step Task Planning with Tool Use)은 휴머노이드 에이전트(Humanoid Agent)가 단일 스킬(Skill)이나 한 번의 추론 과정만으로 완료할 수 없는 목표를 해결할 수 있도록 한다. 복잡한 명령은 인식(Perception), 내비게이션(Navigation), 조작(Manipulation), 메모리 접근(Memory Access), 의사소통(Communication), 외부 정보 서비스(External Information Service)를 조정된 순서로 사용해야 할 수 있다. 따라서 계획기(Planner)는 필요한 물리적 행동뿐만 아니라 정보를 획득하고, 가정을 검증하고, 하위 작업을 실행하기 위해 전문화된 계산 또는 로봇 도구를 언제 호출해야 하는지도 결정해야 한다.

도구(Tool)는 정의된 인터페이스를 통해 인지 에이전트(Cognitive Agent)에 제공되는 제한된 기능(Bounded Capability)을 의미한다. 물리적 도구(Physical Tool)에는 내비게이션, 도달(Reach), 파지(Grasp), 양손 조작(Bimanual Manipulation), 검사(Inspection), 전달(Handover), 보행(Locomotion) 서비스가 포함될 수 있으며, 정보 도구(Informational Tool)는 객체 감지(Object Detection), 의미 지도 질의(Semantic Map Query), 메모리 검색(Memory Retrieval), 동작 실행 가능성 검사(Motion Feasibility Check), 진단(Diagnostics), 데이터베이스 접근(Database Access)을 제공할 수 있다. 에이전트는 내부 구현을 직접 제어하는 대신 각 기능의 설명과 매개변수를 기반으로 이러한 도구를 추론한다.

복잡한 목표는 먼저 행동 사이의 의존성을 설정하는 중간 상태(Intermediate State)로 분해된다. 예를 들어 "창고에서 요청한 부품을 찾아 작업대에 설치하라"는 명령을 수행하기 위해 에이전트는 부품 식별, 예상 위치 결정, 창고로 이동, 관련 영역 탐색, 객체 검증, 파지, 작업대로 복귀, 설치 위치 검사, 필요한 조작 수행 등의 과정을 거칠 수 있다. 각각의 완료된 단계는 이후 단계에 필요한 조건을 형성한다.

도구 선택(Tool Selection)은 누락된 정보 또는 필요한 물리적 기능에 따라 결정되어야 한다. 객체의 위치를 알 수 없다면 계획기는 비용이 높은 물리적 탐색을 시작하기 전에 의미론적 장면 메모리(Semantic Scene Memory)를 질의할 수 있다. 시각적으로 유사한 여러 객체가 존재한다면 전문 객체 인식 서비스(Object Recognition Service)를 호출할 수 있다. 무겁거나 다루기 어려운 부품을 조작하기 전에는 도달 가능성(Reachability) 또는 탑재하중 실행 가능성(Payload Feasibility)을 검사할 수 있다. 따라서 도구 사용은 추론 시스템이 근거 없는 가정에 의존하는 대신 능동적으로 증거를 획득할 수 있도록 한다.

각 도구는 목적, 필요한 인수(Argument), 사전조건(Precondition), 예상 결과(Expected Result), 실패 모드(Failure Mode), 관련 제약조건을 포함하는 구조화된 계약(Structured Contract)을 제공해야 한다. 예를 들어 파지 도구(Grasp Tool)는 객체 식별자(Object Identifier), 추정 자세(Estimated Pose), 선택된 손, 파지 전략(Grasp Strategy)을 요구할 수 있으며 성공 상태, 파지 신뢰도(Grasp Confidence), 갱신된 손 점유 상태(Hand Occupancy)를 반환할 수 있다. 명시적인 계약은 언어 수준 계획과 실행 가능한 소프트웨어 사이의 모호성을 줄이고 추론 모델이 로봇에 존재하지 않는 매개변수나 기능을 임의로 생성하는 것을 방지한다.

도구 호출(Tool Call)은 현재 인지 상태(Current Cognitive State)에 그라운딩(Grounding)되어야 한다. "작업자 옆의 컨테이너를 검사하라"는 명령은 에이전트가 검사 도구에 식별자를 전달하기 전에 언어적 참조를 인식된 물리적 개체와 연결할 것을 요구한다. 마찬가지로 내비게이션에는 유효한 목적지가 필요하고, 조작에는 도달 가능한 대상이 필요하며, 전달에는 의도된 수신자의 식별이 필요하다. 따라서 다중모달 그라운딩(Multimodal Grounding)은 언어 추론, 인식, 메모리, 실행 가능한 도구 인터페이스를 연결한다.

계획기는 다단계 절차(Multi-Step Procedure)를 고정된 시퀀스가 아니라 작업 그래프(Task Graph)로 표현할 수 있다. 노드(Node)는 행동 또는 도구 호출을 나타내며, 의존 관계(Dependency)는 후속 작업이 시작되기 전에 어떤 결과가 존재해야 하는지를 나타낸다. 일부 분기는 조건부(Conditional)로 실행될 수 있으며 독립적인 인식 또는 모니터링 프로세스는 병렬로 실행될 수 있다. 이러한 표현은 이전 도구가 환경에 관한 정보를 반환하기 전까지 올바른 다음 행동을 결정할 수 없는 작업을 지원한다.

관측(Observation) 자체도 중요한 도구 사용 형태이다. 휴머노이드는 지각적 불확실성(Perceptual Uncertainty)을 줄이기 위해 의도적으로 머리를 움직이거나, 신체 위치를 변경하거나, 손목 카메라(Wrist Camera)를 이용해 객체를 검사하거나, 특정 영역에 접근할 수 있다. 대상이 부분적으로 가려져 있는 경우 계획기는 즉시 작업이 불가능하다고 판단하거나 보이지 않는 상태를 추측해서는 안 된다. 대신 보다 유용한 관측을 생성하는 능동 인식 행동(Active Perception Action)을 계획에 삽입하고 갱신된 세계 상태(World State)를 기반으로 계획을 계속할 수 있다.

메모리 검색(Memory Retrieval) 또한 장기 작업(Long-Horizon Task)에서 인지 도구(Cognitive Tool)로 기능한다. 에이전트는 알려진 객체 위치를 찾기 위해 의미 메모리(Semantic Memory)를 질의하거나, 이전에 성공한 절차를 검색하거나, 최근 작업 이력을 조사하여 이미 시도한 행동을 확인할 수 있다. 검색된 정보는 현재의 진실이 보장된 정보가 아니라 문맥적 증거(Contextual Evidence)로 취급해야 한다. 물리적 조건이 변경되었을 가능성이 있는 경우 중요한 행동을 결정하기 전에 현재 인식을 통해 메모리 결과를 검증해야 한다.

다단계 계획기(Multi-Step Planner)는 여러 도구 호출에 걸쳐 실행 상태(Execution State)를 유지해야 한다. 이 상태에는 원래 목표, 현재 하위 목표(Subgoal), 완료된 행동, 해결되지 않은 의존성, 획득한 정보, 현재 들고 있는 객체, 선택된 자원, 해결되지 않은 실패 정보가 기록된다. 지속적인 상태(Persistent State)가 없으면 에이전트는 이미 완료한 작업을 반복하거나 이전 관측과 이후 행동 사이의 관계를 잃을 수 있다. 따라서 실행 상태는 활성 계획(Active Plan)의 운용 메모리(Operational Memory) 역할을 한다.

도구 출력(Tool Output)은 독립적인 텍스트 응답으로 남아 있는 것이 아니라 구조화된 관측(Structured Observation)을 통해 세계 모델(World Model)을 갱신해야 한다. 성공적인 내비게이션 호출은 로봇의 위치를 변경하고, 파지는 객체의 소유 관계와 손 점유 상태를 변경하며, 검사는 객체의 정체성이나 상태를 갱신할 수 있고, 문을 여는 행동은 접근 가능성 관계(Accessibility Relationship)를 변경한다. 도구 결과를 상태 변화(State Change)로 변환함으로써 이후의 추론은 단순한 명령 이력이 아니라 실제 실행 결과를 기반으로 수행될 수 있다.

검증 게이트(Verification Gate)는 서로 의존하는 물리적 행동 사이에서 특히 중요하다. 도구가 명령 완료를 보고했다고 해서 의도한 물리적 결과가 실제로 달성되었다는 것을 보장하지는 않는다. 파지 이후에는 운반을 시작하기 전에 인식 또는 촉각 센싱(Tactile Sensing)을 통해 객체가 유지되고 있는지 검증해야 한다. 컨테이너를 연 후에는 내부를 탐색하기 전에 실제 접근이 가능한지 확인해야 한다. 이러한 게이트는 하나의 감지되지 않은 실행 오류가 장기 작업의 나머지 단계 전체로 전파되는 것을 방지한다.

실패(Failure)는 국소 복구(Local Recovery)를 지원할 수 있도록 구조화된 진단 정보(Structured Diagnostic Information)를 반환해야 한다. 경로가 차단되어 내비게이션이 실패한 경우 계획기는 임무의 조작 부분을 변경하지 않고 대체 경로를 탐색할 수 있다. 대상 자세의 불확실성 때문에 파지가 실패한 경우 전체 작업을 처음부터 다시 시작하는 대신 인식을 다시 수행할 수 있다. 국소 재계획(Local Replanning)은 이미 성공적으로 완료된 진행 상태를 보존하고 여러 독립적인 하위 작업을 포함하는 긴 시퀀스에서 복구 비용을 줄인다.

도구 사용 계획(Tool-Use Planning)은 복구 가능한 실패(Recoverable Failure)와 상위 수준 대응(Escalation)이 필요한 조건도 구분해야 한다. 반복적인 파지 실패가 발생하면 다른 손이나 시점을 선택할 수 있지만, 객체 자체를 찾을 수 없다면 사람에게 추가 설명을 요청해야 할 수 있다. 안전 위반(Safety Violation), 하드웨어 고장(Hardware Fault), 접근 불가능한 작업공간은 작업 중단을 요구할 수 있다. 따라서 계획기는 동일한 실패 도구를 반복 호출하는 대신 재시도 제한(Retry Limit), 대체 기능, 지원 요청, 종료 조건(Termination Condition)을 고려해야 한다.

안전 검증(Safety Validation)은 물리적 도구 호출과 정보 도구 호출 모두를 둘러싸는 구조로 존재해야 한다. 동작과 관련된 도구를 실행하기 전에 독립적인 메커니즘이 사람과의 거리, 충돌 위험, 균형 실행 가능성(Balance Feasibility), 관절 한계(Joint Limit), 탑재하중 제약(Payload Constraint), 제한 구역을 검사할 수 있다. 도구의 사용 가능 여부 역시 운용 모드(Operational Mode)와 권한(Authorization)에 따라 달라질 수 있다. 인지 계획기는 행동을 제안하지만 해당 행동의 물리적 실행 가능 여부에 대한 최종 권한은 안전 및 실행 계층이 유지한다.

효율적인 계획(Efficient Planning)은 불필요한 도구 호출을 방지한다. 반복적인 인식 질의, 중복된 지도 검색, 과도한 대규모 언어 모델 추론은 작업 성공률을 높이지 않으면서 지연시간(Latency)을 증가시킬 수 있다. 에이전트는 충분히 최근의 결과를 재사용하고, 안정적인 환경 정보를 캐시(Cache)하며, 불확실성이 다음 의사결정에 영향을 주는 경우에만 비용이 높은 도구를 호출할 수 있다. 동시에 행동이나 외부 사건으로 관련 상태가 변경되었을 가능성이 있는 경우 오래된 정보(Stale Information)를 무효화해야 한다.

아키텍처는 느린 숙고 과정(Slow Deliberation)과 빠른 실행(Fast Execution)을 분리해야 한다. 대규모 언어 모델(LLM)은 의미 있는 작업 전환 시점에서 하위 목표를 결정하고 도구를 선택할 수 있으며, 보행, 전신 제어(Whole-Body Control), 조작, 보호 반응(Protective Response)은 보다 빠른 전용 시스템을 통해 실행된다. 물리적 스킬이 시작된 이후 인지 에이전트는 모든 제어 갱신을 직접 생성하는 대신 진행 상태를 감독한다. 이러한 분리는 실시간 안정성(Real-Time Stability)을 유지하면서 작업 수준에서 의미론적 유연성(Semantic Flexibility)을 확보한다.

다단계 도구 사용의 평가(Evaluation)는 최종적인 작업 완료 여부만을 고려해서는 안 된다. 유용한 평가 지표에는 유효한 도구 선택(Valid Tool Selection), 인수 정확성(Argument Correctness), 불필요한 호출 횟수, 계획 길이(Plan Length), 실행 시간, 복구 효율성(Recovery Efficiency), 상태 일관성(State Consistency), 그라운딩 정확도(Grounding Accuracy), 안전 준수(Safety Compliance)가 포함될 수 있다. 장기 작업 평가는 많은 행동이 수행된 이후에도 에이전트가 문맥을 유지하는지, 초기 단계의 오류가 후속 단계로 연쇄적으로 전파되기 전에 감지되는지도 추가로 검증해야 한다.

궁극적으로 도구 사용을 포함한 다단계 작업 계획(Multi-Step Task Planning with Tool Use)은 휴머노이드 인공지능 에이전트를 전문화된 지능과 물리적 기능을 조정하는 오케스트레이터(Orchestrator)로 전환한다. 계획기는 목표를 분해하고, 누락된 정보를 식별하며, 적절한 도구를 선택하고, 결과를 해석하며, 인지 상태를 갱신하고, 물리적 결과를 검증하며, 필요한 경우 재계획한다. 이러한 반복적 과정을 통해 복잡한 목표는 피드백 없이 성공하기를 기대하는 하나의 거대한 명령이 아니라 물리적 현실에 그라운딩된 의사결정과 신체화된 행동(Embodied Action)의 관리 가능한 시퀀스로 변환된다.

## 10.05. Semantic Scene Memory and Object Tracking [w/Code]

![](images/image6.png){width="7.268055555555556in" height="7.268055555555556in"}

의미론적 장면 메모리(Semantic Scene Memory)는 휴머노이드 에이전트(Humanoid Agent)가 현재 센서 프레임에서 보이는 범위를 넘어 환경에 대한 지속적인 표현(Persistent Representation)을 유지할 수 있도록 한다. 장기 작업(Long-Horizon Task)을 수행하는 로봇은 객체가 어디에서 관측되었는지, 주변 구조물과 어떤 관계를 가지는지, 누가 해당 객체와 상호작용했는지, 그리고 객체의 상태가 변화했는지를 기억해야 한다. 객체 추적(Object Tracking)과 결합된 장면 메모리는 인식 사건 사이의 연속성을 제공하고 이동, 가림, 조작, 시간 변화에 걸쳐 물리적 개체에 대한 추론을 가능하게 한다.

기존 인식 파이프라인(Perception Pipeline)은 주로 현재 관측 가능한 정보를 설명하지만, 의미론적 장면 메모리는 이전 관측을 통해 에이전트가 환경에 대해 학습한 내용을 표현한다. 메모리에는 객체, 사람, 가구, 컨테이너, 도구, 작업대, 문, 이동 가능 영역(Navigable Region)과 기타 작업 관련 개체(Task-Relevant Entity)가 포함될 수 있다. 각 개체에는 의미 레이블(Semantic Label), 공간 위치, 속성(Attribute), 관계(Relationship), 신뢰도(Confidence), 관측 이력(Observation History), 정보가 획득되거나 갱신된 시점을 나타내는 타임스탬프(Timestamp)를 연결할 수 있다.

지속적인 객체 정체성(Persistent Object Identity)은 동일한 물리적 객체에 대한 반복적인 감지가 서로 독립된 개체를 생성하지 않도록 하기 위해 필수적이다. 객체 추적은 시각적 외형(Visual Appearance), 추정 자세(Estimated Pose), 움직임(Motion), 기하 구조(Geometry), 의미 클래스(Semantic Class), 시간적 일관성(Temporal Consistency)을 고려하여 프레임과 시점 사이의 관측을 연결한다. 지속적인 식별자(Persistent Identifier)를 사용하면 객체의 위치와 시각적 외형이 크게 달라졌더라도 현재 로봇이 들고 있는 병이 이전에 테이블에서 관측된 동일한 병이라는 사실을 인지 시스템이 추론할 수 있다.

휴머노이드의 이동성(Humanoid Mobility)은 관측자 자체가 환경 안에서 이동하기 때문에 객체 추적을 특히 어렵게 만든다. 머리 회전, 보행, 신체 움직임, 조작, 카메라 시점 변화는 객체가 정지해 있는 경우에도 큰 겉보기 운동(Apparent Motion)을 발생시킬 수 있다. 따라서 추적 시스템은 로봇 위치 추정(Robot Localization), 카메라 자세(Camera Pose), 깊이 정보(Depth Information), 기하학적 변환(Geometric Transformation)을 사용하여 자기 운동(Ego-Motion)과 객체 운동(Object Motion)을 구분해야 한다. 이후 장면 메모리는 안정적인 세계, 지도, 공간 또는 작업 기준 좌표계에서 객체 위치를 표현할 수 있다.

가림(Occlusion)이 발생할 경우 아키텍처는 현재 객체가 보이지 않는 상태와 기억된 위치에 객체가 더 이상 존재하지 않는 상태를 구분해야 한다. 사람이 도구 앞을 지나가거나 휴머노이드가 테이블에서 시선을 돌린 경우 해당 객체를 즉시 삭제하지 않고 신뢰도가 점차 감소하는 상태로 메모리에 유지해야 한다. 이후 로봇이 해당 영역을 다시 관측했는데 객체가 존재하지 않는다면 시스템은 기존 믿음(Belief)을 수정하고 탐색(Search), 재연결(Reassociation), 상태 변화 추론(State-Change Reasoning)을 수행할 수 있다.

의미론적 관계(Semantic Relationship)는 장면 메모리를 서로 독립적인 객체 기록의 집합보다 더욱 유용하게 만든다. 시스템은 위에 있음(On), 내부에 있음(Inside), 옆에 있음(Beside), 부착됨(Attached-To), 들고 있음(Held-By), 도달 가능함(Reachable-From), 소유 관계(Belongs-To), 특정 공간에 위치함(Located-In)과 같은 관계를 표현할 수 있다. 컵은 작업대 위에 있는 것으로, 부품은 컨테이너 내부에 있는 것으로, 도구는 작업자가 들고 있는 것으로 표현할 수 있다. 이러한 관계는 많은 작업 명령이 절대 좌표보다 관계적 구조(Relational Structure)를 참조하기 때문에 작업 계획(Task Planning)을 지원한다.

조작(Manipulation)은 의미론적 장면 그래프(Semantic Scene Graph)를 직접 변화시키므로 명시적인 메모리 갱신이 필요하다. 휴머노이드가 객체를 파지하면 객체의 관계는 테이블 위(On-Table)에서 로봇이 들고 있음(Held-By-Robot)으로 변경될 수 있으며, 객체 자세는 기존 표면이 아니라 손을 기준으로 연결된다. 객체를 배치한 이후에는 새로운 공간 관계와 세계 좌표계 자세(World Pose)가 할당된다. 이러한 상태 전이(Transition)를 갱신함으로써 이후의 계획은 조작 이전의 오래된 관측이 아니라 행동으로 발생한 물리적 결과를 기반으로 추론할 수 있다.

객체 상태(Object State)는 객체 정체성(Object Identity)과 별도로 표현되어야 한다. 동일한 문은 열려 있거나 닫혀 있을 수 있고, 컨테이너는 비어 있거나 객체를 포함할 수 있으며, 도구는 사용 가능하거나 사용 중일 수 있고, 부품은 조립되었거나 조립되지 않은 상태일 수 있다. 상태 추정(State Estimation)은 인식, 조작 피드백(Manipulation Feedback), 사람의 지시 또는 작업 실행 결과로부터 생성될 수 있다. 상태 이력(State History)을 유지하면 에이전트는 어떤 개체가 존재하는지만이 아니라 작업과 관련된 상태가 시간에 따라 어떻게 변화하는지도 이해할 수 있다.

저장된 정보는 다시 관측되지 않는 시간이 길어질수록 신뢰성이 감소하므로 메모리 항목에는 불확실성(Uncertainty)과 최신성(Freshness)이 포함되어야 한다. 신뢰도는 감지 품질(Detection Quality), 추적 일관성(Tracking Consistency), 경과 시간, 환경의 동적 특성, 중간에 수행된 행동이 장면을 변화시켰을 가능성에 따라 달라질 수 있다. 최근 확인된 고정 캐비닛의 정보는 오랫동안 신뢰할 수 있지만, 이동 가능한 도구나 사람의 기억된 위치는 빠르게 불확실해질 수 있다. 계획 시스템은 이러한 차이를 활용하여 언제 재관측(Reobservation)이 필요한지를 결정할 수 있다.

재식별(Re-Identification)은 객체가 시야에서 사라졌다가 이후 다시 나타날 때 중요해진다. 추적 시스템은 새로운 감지가 기존 메모리 개체에 해당하는지 아니면 동일한 클래스의 다른 객체를 나타내는지를 판단해야 한다. 외형 임베딩(Appearance Embedding), 기하 구조, 크기, 의미 속성(Semantic Attribute), 마지막으로 알려진 자세, 시간적 제약조건(Temporal Constraint), 문맥적 관계(Contextual Relationship)를 이러한 연결 과정에 활용할 수 있다. 모호한 경우 잘못된 정체성을 강제로 할당하기보다 여러 가설(Multiple Hypotheses)을 유지해야 한다.

장면 메모리(Scene Memory)는 효율적인 추론을 지원하기 위해 계층적으로 구성할 수 있다. 건물은 여러 공간을 포함하고, 공간은 작업 영역을 포함하며, 작업 영역은 가구나 장비를 포함하고, 이러한 구조물은 다시 조작 가능한 객체를 포함할 수 있다. 이러한 계층 구조(Hierarchy)를 사용하면 에이전트가 적절한 추상화 수준에서 탐색할 수 있다. 요청된 도구가 즉시 보이지 않는 경우 로봇은 상세한 국소 인식(Local Perception)을 수행하기 전에 해당 도구가 있을 가능성이 높은 공간이나 작업대를 먼저 추론할 수 있다.

의미론적 장면 메모리는 언어(Language)와 연결되는 중요한 인터페이스도 제공한다. "프린터 옆의 상자", "내가 전에 사용했던 도구", "위쪽 선반에 있는 부품"과 같은 표현은 에이전트가 언어적 참조(Linguistic Reference)를 기억된 개체 및 관계와 연결하여 해석해야 한다. 언어 그라운딩(Language Grounding)은 장면 메모리에서 후보를 질의하고 의미적·공간적 속성을 비교하며, 여러 개체가 여전히 가능한 후보로 남는 경우 추가적인 인식을 요청할 수 있다.

장면 메모리가 신뢰할 수 있는 의사결정을 수행하기에 충분하지 않은 경우 능동 인식(Active Perception)을 실행할 수 있다. 객체가 캐비닛 내부에 있다고 기억하지만 현재 상태가 불확실하다면 에이전트는 캐비닛으로 이동하고, 허용되는 경우 문을 열어 내부를 검사할 수 있다. 두 개의 유사한 객체를 구분할 수 없다면 휴머노이드는 머리 또는 손목 카메라의 위치를 변경하여 더 나은 시야를 확보할 수 있다. 따라서 메모리는 전체 환경을 지속적으로 완전하게 관측하도록 요구하는 대신 정보가 부족한 영역으로 인식을 유도한다.

장면 메모리는 작업 메모리(Task Memory)와 상호작용해야 하지만 두 메모리가 동일한 시스템으로 취급되어서는 안 된다. 장면 메모리는 지속적인 개체와 환경 관계를 표현하고, 작업 메모리는 목표, 완료된 행동, 실패, 실행 진행 상태를 기록한다. 작업 행동이 환경을 변화시키는 경우 두 시스템은 서로 연결된다. 성공적인 객체 배치는 해당 단계가 완료되었다는 작업 상태를 갱신하는 동시에 객체의 새로운 위치와 관계를 나타내는 장면 상태(Scene State)도 갱신한다.

여러 센서는 동일한 지속적 객체(Persistent Object)에 대한 증거를 제공할 수 있다. 머리 카메라는 넓은 환경 관측을 제공하고, 손목 카메라는 근거리 조작 시점을 제공하며, 촉각 센싱(Tactile Sensing)은 접촉을 확인하고, 힘 정보(Force Information)는 객체가 계속 손에 유지되고 있는지를 나타낼 수 있다. 센서 융합(Sensor Fusion)은 각 모달리티마다 서로 분리된 객체 정체성을 생성하는 대신 공통 개체 표현(Common Entity Representation)을 갱신해야 한다. 이는 시각적 가시성이 감소하는 동시에 촉각 정보의 중요성이 증가하는 파지 과정에서 특히 중요하다.

지속적으로 운용되는 휴머노이드는 막대한 수의 관측과 일시적인 개체를 축적할 수 있으므로 메모리 관리(Memory Management)가 필요하다. 시스템은 작업과 관련된 지속적 객체와 관계를 유지하면서 중복되는 관측을 압축하거나 제거해야 한다. 자주 변화하지만 중요도가 낮은 세부 정보는 단기 저장소(Short-Term Storage)에 유지할 수 있으며, 안정적인 의미 지식은 보다 장기적인 메모리(Long-Term Memory)로 통합할 수 있다. 디버깅(Debugging), 학습(Learning), 에피소드 추론(Episodic Reasoning)에 유용한 경우 과거 기록을 별도로 보존할 수도 있다.

일관성 검사(Consistency Checking)는 서로 모순되는 메모리가 작업 계획에 조용히 영향을 미치는 것을 방지한다. 불확실성이나 여러 가설이 이러한 충돌을 명시적으로 설명하지 않는 한 하나의 객체가 동시에 로봇의 손에 들려 있으면서 멀리 떨어진 선반 위에 존재하는 것으로 표현되어서는 안 된다. 행동 결과, 새로운 관측, 추적 결과는 서로 호환되지 않는 상태의 조정(Reconciliation)을 유발해야 한다. 모순을 자동으로 해결할 수 없는 경우 에이전트는 신뢰도를 낮추고 중요한 행동을 수행하기 전에 추가적인 증거를 획득할 수 있다.

결과적으로 의미론적 장면 메모리(Semantic Scene Memory)와 객체 추적(Object Tracking)은 신체화된 인지 에이전트(Embodied Cognitive Agent)에 필요한 공간적·시간적 연속성(Spatial and Temporal Continuity)을 제공한다. 추적은 여러 관측에 걸쳐 객체의 정체성을 유지하고, 의미 메모리는 관계와 상태를 보존하며, 불확실성은 저장된 지식을 언제 다시 검토해야 하는지를 나타내고, 능동 인식은 필요한 경우 정보를 갱신한다. 이러한 메커니즘을 결합함으로써 휴머노이드는 현재 보이는 객체뿐만 아니라 일시적으로 가려진 객체, 행동으로 이동된 객체, 이전에 관측한 객체, 복잡한 다단계 작업(Multi-Step Task)의 후속 단계에서 다시 참조되는 객체에 대해서도 일관되게 추론할 수 있다.

## 10.06. Failure Aware Task Replanning Agent [w/Code]

![](images/image7.png){width="7.268055555555556in" height="7.268055555555556in"}

실패 인지형 작업 재계획(Failure-Aware Task Replanning)은 휴머노이드 에이전트(Humanoid Agent)가 행동이 예상한 결과를 생성하지 못하는 상황에서도 작업을 계속 수행할 수 있도록 한다. 실제 물리적 환경에는 인식 오류, 움직이는 객체, 사람의 개입, 접촉 동역학(Contact Dynamics), 위치 추정 오차(Localization Drift), 하드웨어 한계로 인한 불확실성이 존재한다. 따라서 강건한 에이전트(Robust Agent)는 처음 생성된 계획이 계속 유효할 것이라고 가정해서는 안 된다. 예상과 실제 결과 사이의 차이를 감지하고, 그 영향을 파악하며, 성공적으로 완료된 진행 상태를 보존하면서 영향을 받은 작업 부분만 수정해야 한다.

실패 감지(Failure Detection)는 예상 사후조건(Expected Postcondition)과 실제로 관측된 결과를 비교하는 것에서 시작한다. 내비게이션 명령이 완료되었다고 보고되더라도 로봇이 필요한 상호작용 영역 밖에 있을 수 있으며, 파지 명령이 종료되었더라도 객체를 안정적으로 들고 있지 못할 수 있다. 따라서 에이전트는 소프트웨어 수준의 완료 신호에만 의존하지 않고 인식(Perception), 고유수용감각(Proprioception), 촉각 센싱(Tactile Sensing), 힘 측정(Force Measurement), 위치 추정(Localization), 실행 피드백(Execution Feedback)을 이용하여 중요한 상태 전이를 검증해야 한다.

실패는 단순한 성공 또는 실패 플래그가 아니라 구조화된 사건(Structured Event)으로 표현되어야 한다. 유용한 정보에는 실패한 스킬(Skill), 대상 개체(Target Entity), 실행 단계(Execution Phase), 관측 상태(Observed State), 예상 상태(Expected State), 신뢰도(Confidence), 진단 코드(Diagnostic Code), 경과 시간, 관련 환경 조건이 포함될 수 있다. 객체 자세의 불확실성으로 발생한 파지 실패는 도달 거리 부족이나 과도한 탑재하중으로 발생한 실패와 서로 다른 대응을 요구한다. 구조화된 실패 정보는 의미 있는 재계획에 필요한 문맥을 제공한다.

실패 분류(Failure Classification)는 적절한 복구 범위(Recovery Scope)를 결정하는 데 도움을 준다. 인식 실패(Perceptual Failure)는 필요한 개체를 감지하거나 신뢰성 있게 식별할 수 없을 때 발생한다. 계획 실패(Planning Failure)는 사전조건이나 의존성이 유효하지 않을 때 발생한다. 실행 실패(Execution Failure)에는 차단된 이동 경로, 파지 실패, 불안정한 접촉, 동작 실행 불가능성이 포함될 수 있다. 시스템 실패(System Failure)는 센서 사용 불가, 액추에이터 고장, 통신 손실, 자원 고갈 등을 포함하며, 안전 실패(Safety Failure)는 작업을 계속 수행할 경우 운용 제약조건을 위반하게 되는 상황을 의미한다.

재계획 에이전트(Replanning Agent)는 먼저 실패를 국소적으로 해결할 수 있는지를 판단해야 한다. 대상 자세가 부정확하여 파지가 실패했다면 인식을 다시 수행하고 새로운 파지 방법을 생성하는 것으로 충분할 수 있다. 일시적인 장애물이 경로를 막아 내비게이션이 실패했다면 국소 경로 갱신(Local Path Update)을 통해 작업을 복구할 수 있다. 국소 복구(Local Recovery)는 전체 장기 계획(Long-Horizon Plan)을 불필요하게 다시 생성하는 것을 방지하며 이미 완료된 행동, 획득한 객체, 발견된 환경 정보, 유효한 작업 의존성을 보존한다.

국소 복구만으로 충분하지 않은 경우 에이전트는 고수준 목표(Higher-Level Objective)를 유지하면서 현재 하위 목표(Subgoal)를 수정할 수 있다. 현재 자세에서 객체에 도달할 수 없는 휴머노이드는 신체 위치를 변경하거나, 다른 방향에서 접근하거나, 반대쪽 손을 사용하거나, 다른 조작 전략(Manipulation Strategy)을 선택할 수 있다. 이러한 형태의 하위 목표 재계획(Subgoal Replanning)은 목표 자체를 변경하지 않고 목표를 달성하는 방법을 변경함으로써 동일한 의미론적 작업에 대해 여러 신체화 전략(Embodied Strategy)을 활용할 수 있도록 한다.

보다 심각한 실패는 계획의 다른 부분에 존재하는 의존성까지 무효화할 수 있다. 요청된 부품이 예상된 보관 위치에 존재하지 않는다면 이후의 파지, 운반, 설치 행동은 더 이상 실행할 수 없다. 에이전트는 다른 위치를 탐색하거나, 의미 메모리(Semantic Memory)를 질의하거나, 다른 컨테이너를 검사하거나, 사람에게 정보를 요청해야 할 수 있다. 따라서 재계획은 변경된 조건을 작업 그래프(Task Graph) 전체에 전파하고 향후 행동 가운데 어떤 것이 여전히 유효하며 어떤 것을 교체해야 하는지 식별해야 한다.

인지 상태(Cognitive State)는 재계획 과정에서 필요한 연속성을 제공한다. 여기에는 원래 목표, 완료된 단계, 현재 로봇 구성(Robot Configuration), 들고 있는 객체, 발견된 장면 정보(Scene Information), 이미 시도한 복구 전략, 해결되지 않은 제약조건이 보존되어야 한다. 이러한 상태가 없다면 계획기는 이미 성공한 행동을 반복하거나 이미 확보한 물리적 자원을 잃어버린 것으로 판단할 수 있다. 실패 복구는 임무의 최초 시작 조건을 다시 구성하는 것이 아니라 실제 현재 세계 상태(Current World State)를 기준으로 시작해야 한다.

의미론적 장면 메모리(Semantic Scene Memory)는 객체와 위치에 대한 대체 가설(Alternative Hypothesis)을 제공하여 복구를 지원한다. 작업대에서 도구를 찾을 수 없다면 메모리는 해당 도구가 이전에 관측되었던 다른 캐비닛을 알려줄 수 있다. 객체 추적(Object Tracking)은 도구가 인식 시스템에서 단순히 사라진 것이 아니라 사람에 의해 이동되었다는 사실을 나타낼 수도 있다. 그러나 기억된 정보는 오래되었을 수 있으므로 재계획 에이전트는 중요한 복구 행동을 결정하기 전에 메모리 검색 결과와 현재 관측을 결합해야 한다.

능동 인식(Active Perception)은 실패 원인이 모호한 경우 가장 안전한 초기 대응이 될 수 있다. 시스템이 객체가 이동했는지, 파지가 풀렸는지, 또는 감지기가 단순히 객체를 놓쳤는지를 판단할 수 없다면 추가 관측을 통해 작업 계획 자체를 변경하지 않고 불확실성을 해소할 수 있다. 휴머노이드는 머리를 움직이고, 신체 위치를 변경하고, 손목 카메라(Wrist Camera)를 사용하고, 접촉 상태를 검사하거나, 다른 센서 추론을 요청할 수 있다. 재계획 시스템은 정보 부족(Insufficient Information)과 실제 물리적 실행 불가능성(Physical Impossibility)을 구분해야 한다.

복구 정책(Recovery Policy)은 제어되지 않은 반복을 방지해야 한다. 실패한 행동은 조건이 의미 있게 변화한 경우 다시 시도할 수 있지만, 동일한 매개변수로 동일한 명령을 반복하면 무한 실패 루프(Infinite Failure Loop)가 발생할 수 있다. 에이전트는 재시도 횟수(Retry Counter), 이미 시도한 매개변수 조합, 이전에 선택한 시점(Viewpoint), 실패한 대안 전략을 유지할 수 있다. 각각의 복구 시도는 다시 실행되기 전에 새로운 정보를 획득하거나, 실행 조건을 변경하거나, 다른 전략을 선택해야 한다.

에이전트는 복구 대안(Recovery Alternative)의 비용과 위험도 함께 평가해야 한다. 가까운 위치에서 파지를 다시 시도하는 것은 비용이 낮을 수 있지만 다른 방으로 이동하거나, 여러 컨테이너를 열거나, 환경을 재배치하는 작업은 상당한 시간과 에너지를 소비할 수 있다. 복구 방법을 선택할 때 예상 성공 확률(Expected Success Probability), 실행 시간, 에너지 소비(Energy Consumption), 작업 우선순위(Task Priority), 물리적 위험(Physical Risk), 사람에 대한 방해 정도를 고려할 수 있다. 따라서 최적의 복구 방법이 항상 가장 짧은 행동 시퀀스인 것은 아니다.

사람의 지원(Human Assistance)은 자율 복구가 신뢰하기 어렵거나 비효율적일 때 중요한 상위 대응 경로(Escalation Path)를 제공한다. 에이전트는 여러 객체 가운데 어떤 것이 목표인지 질문하거나, 접근할 수 없는 물체를 이동해 달라고 요청하거나, 작업공간이 차단되었다고 보고하거나, 필요한 부품을 찾을 수 없음을 설명할 수 있다. 지원 요청에는 무엇을 시도했는지와 어떤 정보 또는 개입이 필요한지를 설명하는 간결한 문맥 정보가 포함되어야 하며, 이를 통해 기존 작업 진행 상태를 폐기하지 않고 사람과 협력할 수 있다.

안전 메커니즘(Safety Mechanism)은 실패 복구 과정 전체에서 최종 권한을 유지해야 한다. 재계획 모델은 기존 행동이 실패했다는 이유로 충돌 제약조건(Collision Constraint), 토크 한계(Torque Limit), 균형 요구조건(Balance Requirement), 제한 구역(Restricted Zone), 사람과의 거리 규칙(Human-Proximity Rule)을 완화해서는 안 된다. 반복적인 실패는 요청된 목표가 물리적으로 위험하거나 현재 실행 불가능하다는 신호일 수 있다. 이러한 경우 점점 더 공격적인 대안을 탐색하는 대신 작업을 중단하고, 안전 자세(Safe Posture)로 전환하고, 사람의 지원을 요청하거나 실행을 종료하는 것이 올바른 대응일 수 있다.

하드웨어 및 소프트웨어 성능 저하(Degradation)가 발생한 경우에는 기능 인지형 재계획(Capability-Aware Replanning)이 필요하다. 손목 카메라를 사용할 수 없게 되면 에이전트는 머리 카메라 인식으로 남은 작업을 수행할 수 있는지를 판단할 수 있다. 한쪽 팔이 고장 난 경우 일부 작업은 반대쪽 팔에 재할당할 수 있지만 양손 작업(Bimanual Task)은 실행 불가능해질 수 있다. 계획기는 시스템 상태(System Health)가 변화할 때 사용 가능한 스킬 집합과 사전조건을 갱신하여 새로 생성된 계획이 더 이상 사용할 수 없는 기능에 의존하지 않도록 해야 한다.

재계획은 안정화(Stabilization) 및 보호 제어(Protective Control)보다 느린 인지적 시간 척도(Cognitive Time Scale)에서 동작해야 한다. 균형 상실, 과도한 접촉력, 임박한 충돌은 대규모 언어 모델(LLM)이나 작업 계획기가 상황을 평가하기 전에 즉각적인 결정론적 대응(Deterministic Response)을 요구한다. 로봇이 안전한 상태에 도달한 이후 인지 에이전트가 사건을 분석하고 작업 문맥을 갱신하여 실행을 재개할지, 그리고 어떤 방법으로 재개할지를 결정할 수 있다. 이러한 분리는 숙고 과정의 지연시간(Deliberative Latency)이 물리적 안전을 저해하는 것을 방지한다.

실패 이력(Failure History)은 에피소드 메모리(Episodic Memory)에 요약하여 저장함으로써 이후의 의사결정을 향상시킬 수 있다. 시스템은 특정 파지 방향이 반복적으로 실패했다는 사실, 특정 시간대에 특정 경로가 자주 차단된다는 사실, 특정 객체를 양손으로 다루어야 한다는 사실을 기록할 수 있다. 미래의 계획에서는 이러한 경험을 검색하여 이미 효과가 없다고 알려진 전략을 피할 수 있다. 다만 로봇 구성, 환경, 운용 조건이 변화할 수 있으므로 이러한 메모리는 절대적인 규칙이 아니라 문맥적 정보(Contextual Information)로 유지되어야 한다.

실패 인지형 에이전트(Failure-Aware Agent)의 평가는 단순한 작업 성공 여부뿐만 아니라 복구 행동 자체를 측정해야 한다. 관련 평가 항목에는 실패 감지 정확도(Failure Detection Accuracy), 진단 품질(Diagnosis Quality), 복구 시간(Time to Recovery), 반복 시도 횟수, 완료된 진행 상태의 보존, 재계획 유효성(Replanning Validity), 사람 개입 빈도(Human Intervention Frequency), 안전 준수(Safety Compliance), 최종 작업 완료 여부가 포함된다. 테스트에서는 인식 오류, 이동된 객체, 차단된 경로, 조작 실패, 사용 불가능한 자원, 하위 시스템 성능 저하를 의도적으로 발생시켜 복구 과정이 일관성을 유지하는지를 검증해야 한다.

결과적으로 실패 인지형 작업 재계획 에이전트(Failure-Aware Task Replanning Agent)는 실패를 예외적인 종료 조건(Exceptional Termination Condition)이 아니라 지속적인 의사결정을 위한 정보로 전환한다. 에이전트는 예상 결과와의 차이를 감지하고, 실패 문맥을 진단하며, 인식과 메모리를 갱신하고, 적절한 복구 범위를 선택하고, 안전성을 검증한 뒤 변화된 물리적 상태에서 실행을 재개한다. 이러한 폐루프 과정(Closed-Loop Process)을 통해 휴머노이드 자율성(Humanoid Autonomy)은 실제 환경에 내재된 불확실성과 변동성에 강건하게 대응하면서도 통제 가능하고, 설명 가능하며, 목표 지향적인 행동(Goal-Directed Behavior)을 유지할 수 있다.

## 10.07. Multi Modal Grounding for Agent Commands [w/Code]

![](images/image8.png){width="7.268055555555556in" height="7.268055555555556in"}

다중모달 그라운딩(Multimodal Grounding)은 휴머노이드 에이전트(Humanoid Agent)가 사람의 명령을 해당 명령이 참조하는 물리적 개체, 위치, 행동, 사건과 연결할 수 있도록 한다. 언어만으로는 충분하지 않은 경우가 많다. 명령의 의미가 로봇이 현재 보고, 듣고, 기억하고, 접촉하거나 상황으로부터 추론할 수 있는 정보에 의존하기 때문이다. 따라서 그라운딩(Grounding)은 에이전트가 명령을 실행 가능한 행동으로 변환하기 전에 언어적 표현을 시각 인식, 공간 정보, 제스처, 시선, 오디오, 객체 상태, 로봇 상태, 의미 메모리(Semantic Memory)와 연결한다.

"저 상자를 집어서 저쪽에 놓아라"와 같은 명령에는 명시적인 물리 좌표가 거의 포함되어 있지 않다. "저 상자"와 "저쪽"이라는 표현은 화자의 가리키는 제스처(Pointing Gesture), 시선 방향(Gaze Direction), 보이는 객체, 공간 관계, 최근 대화와 같은 문맥적 증거를 이용하여 해석해야 한다. 다중모달 그라운딩은 이러한 모호한 표현을 지속적인 장면 개체(Persistent Scene Entity)와 목표 영역(Target Region)으로 변환하여 작업 계획(Task Planning), 내비게이션(Navigation), 조작(Manipulation) 시스템이 신뢰성 있게 참조할 수 있도록 한다.

시각 그라운딩(Visual Grounding)은 언어를 카메라 또는 다른 환경 센서가 감지한 객체 및 영역과 연결한다. 객체 범주(Object Category), 외형(Appearance), 색상, 형태, 크기, 자세(Pose), 텍스트 레이블(Text Label), 주변 문맥을 모두 참조 개체 식별에 활용할 수 있다. 개방형 어휘 인식(Open-Vocabulary Perception)은 사용자가 고정된 객체 분류 체계에 포함되지 않은 표현을 사용할 때 특히 유용하다. 여러 개의 보이는 개체가 동일한 언어적 설명을 만족하는 경우 그라운딩 시스템은 후보 객체와 각각의 신뢰도(Confidence)를 유지해야 한다.

공간 그라운딩(Spatial Grounding)은 왼쪽, 오른쪽, 뒤, 옆, 위, 내부, 가까운 곳, 가장 가까운 곳과 같은 관계적 표현(Relational Expression)을 해석한다. 이러한 표현은 적절한 기준 좌표계(Reference Frame)가 정의되어야 의미를 갖는다. "왼쪽에 있는 상자"는 화자의 시점, 로봇의 시점 또는 작업대 중심의 좌표계를 의미할 수 있다. 잘못된 공간 해석은 의미적으로는 타당하지만 물리적으로 잘못된 행동을 발생시킬 수 있으므로 에이전트는 행동하기 전에 의도된 기준 좌표계를 추론하거나 명확히 확인해야 한다.

제스처(Gesture)와 시선(Gaze)은 "이것", "저것", "여기", "저기"와 같은 지시 표현(Deictic Expression)이 언어에 포함될 때 추가적인 증거를 제공한다. 사람 자세 추정(Human Pose Estimation)을 이용하여 가리키는 방향을 식별할 수 있으며, 머리 방향 또는 눈의 시선은 사람이 주의를 기울이고 있는 영역을 나타낼 수 있다. 이러한 신호는 정확한 좌표가 아니라 확률적인 정보이므로 객체 위치 및 장면 기하(Scene Geometry)와 융합해야 한다. 하나의 가리킴 방향이 여러 객체와 교차하는 경우 근거 없이 하나를 선택하는 대신 순위가 지정된 후보(Ranked Candidate)를 생성해야 한다.

오디오(Audio)는 단순한 음성 전사(Speech Transcription) 이상의 정보를 제공한다. 화자 위치 추정(Speaker Localization)은 여러 사람이 작업공간에 존재할 때 음성 명령을 특정 사람과 연결하는 데 중요하다. 음성 활동(Voice Activity), 도달 방향(Direction of Arrival), 대화 이력(Dialogue History), 화자 정체성(Speaker Identity)은 누가 명령을 내렸으며 어떤 공간적 관점이 관련되는지를 결정하는 데 도움을 줄 수 있다. 잘못 전사된 객체 또는 위치 이름은 이후의 그라운딩 결정을 손상시킬 수 있으므로 자동 음성 인식(Automatic Speech Recognition)의 불확실성도 함께 전달되어야 한다.

의미론적 장면 메모리(Semantic Scene Memory)는 현재 보이지 않는 개체까지 그라운딩 범위를 확장한다. 사용자는 도구가 더 이상 카메라 시야에 존재하지 않더라도 "아까 사용했던 도구"를 요청할 수 있다. 에이전트는 이전에 관측한 도구, 작업 이력(Task History), 객체 정체성(Object Identity), 마지막으로 알려진 위치를 메모리에서 질의할 수 있다. 기억된 정보는 오래되어 현재 상태와 다를 수 있으므로 메모리 기반 그라운딩에는 타임스탬프(Timestamp)와 신뢰도를 포함해야 하며, 물리적 검증이 중요한 경우 재관측(Reobservation)을 수행해야 한다.

객체 추적(Object Tracking)은 개체가 이동하거나 일시적으로 가려지는 상황에서도 그라운딩을 유지한다. 특정 표현이 지속적인 객체 식별자(Persistent Object Identifier)와 연결된 이후에는 후속 행동에서 원래의 언어적 설명을 반복적으로 다시 해석하는 대신 해당 정체성을 참조해야 한다. 명령이 주어진 이후 사람이 참조된 부품을 이동시키더라도 객체 추적을 통해 정체성을 유지하면서 위치를 갱신할 수 있다. 이러한 연속성은 동적 환경에서 장기 작업(Long-Horizon Task)을 수행하는 데 필수적이다.

로봇의 신체화(Embodiment) 역시 그라운딩에 제약을 제공한다. 올바른 객체를 식별했다고 해서 현재 로봇 구성에서 해당 객체에 도달하고, 파지하고, 운반하거나 조작할 수 있다는 의미는 아니다. 따라서 그라운딩은 의미론적 참조 해석(Semantic Reference Resolution)을 도달 가능성(Reachability), 손 사용 가능 여부(Hand Availability), 탑재하중 한계(Payload Limit), 보행 접근성(Locomotion Access), 조작 실행 가능성(Manipulation Feasibility)과 연결해야 한다. 물리적 실행 가능성은 후보를 구분하는 데 도움을 줄 수 있지만 하나의 후보가 로봇에게 더 쉽게 조작된다는 이유만으로 사용자의 의도를 임의로 변경해서는 안 된다.

행동 그라운딩(Action Grounding)은 언어적 동사와 작업 표현을 휴머노이드 스킬 라이브러리(Humanoid Skill Library)에서 사용할 수 있는 기능에 연결한다. 가져오기(Bring), 건네기(Hand), 놓기(Place), 검사하기(Inspect), 열기(Open), 잡고 있기(Hold), 따라가기(Follow), 준비하기(Prepare)와 같은 표현은 서로 다른 내비게이션, 인식, 조작, 상호작용 스킬의 시퀀스에 대응할 수 있다. 시스템은 의도된 의미론적 행동과 이를 실행할 수 있는 구체적인 구현을 모두 결정해야 한다. 지원되지 않는 행동은 존재하지 않는 로봇 기능으로 임의 변환하지 않고 명시적으로 식별해야 한다.

그라운딩은 명령 요소와 세계 개체(World Entity) 사이의 구조화된 바인딩(Structured Binding) 집합으로 표현할 수 있다. 하나의 명령 표현(Command Representation)은 행동을 대상 객체, 출발 위치(Source Location), 목적지(Destination), 수신자(Recipient), 제약조건, 신뢰도 값과 연결할 수 있다. 이러한 바인딩은 언어 해석(Language Interpretation)과 작업 계획 사이에 안정적인 인터페이스를 제공한다. 계획기는 모든 실행 단계에서 제약 없는 자연어 표현을 반복적으로 해석하는 대신 객체 식별자와 공간 영역을 기반으로 추론할 수 있다.

모호성(Ambiguity)은 추론 모델이 숨겨야 하는 문제가 아니라 측정 가능한 조건으로 처리되어야 한다. 두 개의 유사한 컨테이너가 동일한 수준으로 참조 후보가 될 경우 에이전트는 신뢰도를 비교하고, 추가적인 시각 증거를 획득하고, 제스처 방향을 검사하고, 대화 문맥을 참조하거나 사용자에게 명확한 설명을 요청할 수 있다. 명확화(Clarification)에 필요한 비용은 잘못된 행동으로 발생할 결과와 비교해야 한다. 위험도가 높은 조작 작업은 일반적인 대화 응답보다 높은 수준의 그라운딩 신뢰도를 요구하는 것이 적절하다.

능동 인식(Active Perception)은 현재 관측만으로 충분하지 않을 때 로봇이 그라운딩의 품질을 향상시킬 수 있도록 한다. 휴머노이드는 머리를 회전하거나, 객체에 가까이 이동하거나, 시점을 변경하거나, 손목 카메라(Wrist Camera)로 객체를 검사하거나, 장애물 주변으로 이동할 수 있다. 이러한 행동은 명령 해석에 존재하는 불확실성을 줄이기 위한 목적으로 수행된다. 따라서 그라운딩은 인식 정보를 수동적으로 소비하는 과정이 아니라 추론에 필요한 정보에 따라 인식을 의도적으로 제어하는 상호작용 과정(Interactive Process)이 된다.

시간적 그라운딩(Temporal Grounding)은 언어를 사건 및 변화하는 상태와 연결한다. "방금 네가 옮긴 부품", "조금 전에 들어온 사람", "원래 있던 곳에 다시 놓아라"와 같은 표현은 관측 및 행동 이력에 접근해야 해석할 수 있다. 에이전트는 언어적 참조를 시간 정보가 포함된 장면 상태(Time-Indexed Scene State)와 이전 행동에 연결해야 한다. 이러한 기능은 다중모달 그라운딩을 에피소드 메모리(Episodic Memory)와 연결하며, 명령이 현재의 객체뿐만 아니라 과거의 물리적 사건까지 참조할 수 있도록 한다.

그라운딩은 대화 문맥(Dialogue Context)도 고려해야 한다. 사람의 명령은 이전 대화에서 이미 확립된 정보를 자주 생략한다. 사용자가 특정 부품을 식별한 이후 "이제 그것을 여기로 가져와"라고 명령한다면 "그것"은 이전에 그라운딩된 참조 개체에 의존한다. 대화 메모리(Dialogue Memory)는 관련 바인딩을 유지하면서 대화 주제가 변경될 경우 이를 수정할 수 있어야 한다. 이를 통해 불필요한 반복 질문을 줄이는 동시에 오래된 참조가 무기한 유지되는 위험을 감소시킬 수 있다.

서로 다른 모달리티(Modality)의 불확실성은 각각 독립적으로 폐기하는 것이 아니라 서로 융합해야 한다. 시각 정보는 하나의 객체를 강하게 지지하고, 가리키는 방향은 다른 객체를 약하게 지지하며, 메모리는 또 다른 후보를 제시할 수 있다. 그라운딩 시스템은 신뢰도 기반 점수화(Confidence-Aware Scoring), 확률적 추론(Probabilistic Inference), 학습 기반 다중모달 표현(Learned Multimodal Representation), 하이브리드 추론(Hybrid Reasoning)을 사용하여 이러한 정보를 결합할 수 있다. 목표는 단순히 하나의 후보를 선택하는 것이 아니라 사용 가능한 증거가 안전한 행동을 수행하기에 충분한지를 판단하는 것이다.

안전 경계(Safety Boundary)는 그라운딩이 성공적으로 이루어진 경우에도 독립적으로 유지되어야 한다. 명령을 정확하게 이해했다는 사실이 요청된 행동이 안전하거나 허가되었다는 의미는 아니다. 사용자는 제한 구역(Restricted Boundary) 너머에 있는 객체를 정확하게 가리키거나 탑재하중 또는 균형 한계를 초과하는 조작을 요청할 수 있다. 그라운딩은 명령이 무엇을 참조하는지를 결정하고, 안전 검증(Safety Validation)은 해당 행동의 실행 가능 여부를 결정한다. 두 책임을 분리함으로써 의미론적 신뢰도(Semantic Confidence)가 실행 권한(Execution Permission)으로 잘못 해석되는 것을 방지할 수 있다.

그라운딩 성능(Grounding Performance)은 언어, 인식, 메모리, 물리적 실행 전반에서 평가해야 한다. 유용한 평가 지표에는 참조 개체 식별 정확도(Referent Identification Accuracy), 공간 관계 정확도(Spatial Relation Accuracy), 제스처 해석, 화자 연결(Speaker Association), 그라운딩 신뢰도 보정(Grounding Confidence Calibration), 명확화 요청 빈도(Clarification Frequency), 시간적 참조 해석(Temporal Reference Resolution), 후속 작업 성공률(Downstream Task Success)이 포함된다. 실제 환경에서의 신뢰성을 검증하기 위해 혼잡한 장면, 가림, 유사 객체, 이동하는 사람, 모호한 명령, 변화하는 시점, 지연된 참조 등을 평가에 포함해야 한다.

궁극적으로 다중모달 그라운딩(Multimodal Grounding)은 기호적인 인간 의도(Symbolic Human Intent)와 지속적으로 변화하는 휴머노이드의 물리적 세계를 연결하는 역할을 한다. 언어는 목표를 불완전하게 표현하고, 인식은 현재의 증거를 제공하며, 제스처와 시선은 문맥적 단서(Contextual Cue)를 제공하고, 메모리는 시간에 걸쳐 참조 범위를 확장하며, 추적은 객체의 정체성을 유지하고, 신체화는 실행 가능한 해석에 제약을 제공한다. 계획과 행동 이전에 이러한 정보들을 통합함으로써 휴머노이드 에이전트는 모호한 사람의 명령을 물리적 현실에 그라운딩되고, 검증 가능하며, 실제 실행에 의미 있는 작업 표현(Task Representation)으로 변환할 수 있다.

## 10.08. Agent Safety Boundary and Override System [w/Code]

![](images/image9.png){width="7.268055555555556in" height="7.268055555555556in"}

에이전트 안전 경계(Agent Safety Boundary)는 휴머노이드 인공지능 에이전트(Humanoid AI Agent)가 인식하고, 추론하고, 계획하고, 행동할 수 있는 운용 한계(Operational Limit)를 정의한다. 고수준 지능(High-Level Intelligence)은 복잡한 작업에 대해 유연한 해결책을 생성할 수 있지만, 그 의사결정은 결정론적 안전 메커니즘(Deterministic Safety Mechanism)에 종속되어야 한다. 따라서 안전 아키텍처(Safety Architecture)는 의미론적 작업 추론(Semantic Task Reasoning)과 물리적 실행 권한(Physical Execution Authority)을 분리하여, 겉보기에 합리적인 에이전트의 결정이라도 사람, 장비, 환경 또는 로봇 자체를 보호하기 위해 필요한 제약조건을 직접 우회할 수 없도록 한다.

안전 경계(Safety Boundary)는 하나의 최종 검사로 구현되기보다 여러 계층에 걸쳐 존재해야 한다. 사용자 명령은 먼저 운용 권한(Operational Permission)에 대해 평가할 수 있으며, 생성된 작업 계획은 금지된 행동(Prohibited Action)이 포함되어 있는지 검사할 수 있다. 개별 스킬(Skill)은 실행 전에 검증할 수 있으며, 실시간 제어기(Real-Time Controller)는 물리적 한계를 지속적으로 강제할 수 있다. 이러한 계층 구조는 심층 방어(Defense in Depth)를 제공하여 한 계층에서 놓친 위험 조건이라도 위험한 물리적 행동으로 이어지기 전에 다른 계층에서 감지할 수 있도록 한다.

인지 에이전트(Cognitive Agent)는 안전 제약조건(Safety Constraint)을 계획 문맥(Planning Context)의 명시적인 요소로 취급해야 한다. 제한 구역(Restricted Region), 탑재하중 한계(Payload Limit), 사람과의 거리 요구조건(Human Proximity Requirement), 허용된 도구(Permitted Tool), 환경 위험(Environmental Hazard), 로봇 상태(Robot Health), 운용 모드(Operational Mode)는 어떤 행동이 유효한지를 결정하는 데 영향을 줄 수 있다. 계획 과정에 이러한 제약조건을 포함하면 이후 실행 단계에서 불필요하게 행동이 거부되는 경우를 줄일 수 있다. 그러나 확률적 추론(Probabilistic Reasoning)은 제약조건을 잘못 이해하거나 누락하거나 잘못된 우선순위를 부여할 수 있으므로 인지적 안전 인식이 독립적인 안전 강제를 대체해서는 안 된다.

독립적인 안전 감독기(Independent Safety Supervisor)는 에이전트가 생성한 의도와 실행 가능한 로봇 명령 사이에 경계를 제공한다. 요청된 스킬이 동작 계획(Motion Planning) 또는 제어 계층에 전달되기 전에 안전 감독기는 대상, 운용 영역, 로봇 상태, 필요한 기능, 현재의 안전 조건을 검증할 수 있다. 안전 감독기는 인공지능 계획기(AI Planner)가 부여한 신뢰도와 관계없이 행동을 승인(Approve), 수정(Modify), 지연(Delay), 거부(Reject), 종료(Terminate)할 수 있는 권한을 가져야 한다. 이를 통해 추론 시스템이 물리적 행동에 대한 유일한 권한을 가지는 것을 방지한다.

사람과의 근접성(Human Proximity)은 휴머노이드가 사람과 공유된 공간에서 자주 동작하기 때문에 지속적으로 고려해야 한다. 인식 시스템은 사람의 위치, 움직임, 신체 구성, 상호작용 상태를 추정할 수 있으며, 안전 로직(Safety Logic)은 허용 가능한 속도, 분리 거리(Separation), 힘, 동작 행동을 결정한다. 사람이 로봇에 접근하면 시스템은 속도를 낮추거나, 조작기 움직임을 제한하거나, 작업을 일시정지하거나, 보호 상태(Protective State)로 전환할 수 있다. 이러한 대응은 현재 작업 계획기가 해당 사람을 자신의 목표와 관련된 대상으로 판단하는지 여부와 관계없이 수행되어야 한다.

작업공간 경계(Workspace Boundary)는 로봇, 로봇의 팔다리 또는 운반 중인 객체가 이동할 수 있는 영역을 제한할 수 있다. 휴머노이드가 특정 영역을 통과하는 것은 허용되지만 해당 영역에서 조작하는 것은 금지될 수 있으며, 특정 도구는 지정된 작업대에서만 사용하도록 제한될 수 있다. 기하학적 영역(Geometric Zone), 의미 지도 레이블(Semantic Map Label), 접근 권한(Access Permission), 임시 제외 영역(Temporary Exclusion Region)을 함께 사용하여 이러한 한계를 정의할 수 있다. 계획된 궤적은 실행 전에 경계조건과 비교하여 검증해야 하며, 동작 중에도 경계 위반 여부를 지속적으로 모니터링해야 한다.

물리적 한계(Physical Limit)는 또 다른 기본적인 안전 경계를 형성한다. 관절 위치(Joint Position), 속도(Velocity), 가속도(Acceleration), 토크(Torque), 접촉력(Contact Force), 탑재하중(Payload), 열 상태(Thermal Condition), 액추에이터 전류(Actuator Current), 균형 안정성(Balance Stability)은 모두 허용 가능한 범위 안에 유지되어야 한다. 이러한 제약조건은 고수준 추론에 의존하기보다 실시간 제어 계층에 가까운 위치에서 강제할 때 가장 효과적이다. 에이전트가 객체가 무겁다는 사실을 이해하고 있더라도 하드웨어 및 제어기 수준의 메커니즘이 안전한 기계적 성능 한계를 초과하는 명령을 독립적으로 방지해야 한다.

균형 안전(Balance Safety)은 상체 행동이 로봇 전체를 불안정하게 만들 수 있기 때문에 휴머노이드에서 특히 중요하다. 도달, 들어 올리기, 운반, 밀기, 사람과의 상호작용은 질량 중심(Center of Mass)과 접촉력을 변화시킨다. 이러한 행동을 실행하기 전에 시스템은 지지 구성(Support Configuration), 도달 가능 작업공간(Reachable Workspace), 예상 하중(Expected Load), 안정성 여유(Stability Margin)를 평가할 수 있다. 실행 중에는 전신 제어(Whole-Body Control)와 보호 메커니즘(Protective Mechanism)이 인지적 재계획을 기다리지 않고 예상하지 못한 외란(Disturbance)에 즉각 대응해야 한다.

충돌 회피(Collision Avoidance)는 예측 기반 계획(Predictive Planning)과 반응형 보호(Reactive Protection)를 결합해야 한다. 동작 계획기는 움직임을 시작하기 전에 정적 기하 구조와 추적 중인 동적 객체에 대한 예상 충돌을 평가할 수 있다. 실행 중에는 갱신된 인식 정보와 근접 센싱(Proximity Sensing)을 이용하여 기존 궤적을 무효화하는 변화를 감지할 수 있다. 사람이 이동 경로에 들어오거나 객체가 예상하지 못하게 움직이는 경우 작업 에이전트가 기존 계획을 논리적으로 유효하다고 판단하더라도 안전 계층은 움직임을 감속하거나, 정지하거나, 경로를 변경할 수 있다.

오버라이드 시스템(Override System)은 안전 조건이 변화할 때 정상적인 에이전트 제어를 어떤 방식으로 중단할지를 결정한다. 오버라이드(Override)는 속도 제한이나 궤적 수정부터 스킬 일시정지, 하위 작업 취소, 안전 자세(Safe Posture) 진입, 비상 정지(Emergency Stop)에 이르기까지 여러 수준으로 구성할 수 있다. 모든 비정상 조건이 완전한 시스템 정지를 요구하는 것은 아니므로 단계적 대응(Graded Response)이 유용하다. 이를 통해 가능한 경우 생산성을 유지하면서도 위험도가 증가할수록 더욱 강력한 개입을 수행할 수 있다.

비상 정지 동작(Emergency Stop Behavior)은 고수준 인공지능 추론과 독립적으로 유지되어야 한다. 비상 정지가 활성화되면 위험한 움직임은 대규모 언어 모델(LLM), 작업 계획기(Task Planner), 네트워크 서비스(Network Service), 의미론적 해석(Semantic Interpretation)에 의존하지 않는 메커니즘을 통해 억제되어야 한다. 비상 정지 이후의 복구 역시 통제된 절차(Controlled Procedure)를 따라야 한다. 시스템 상태가 확인되고 적절한 승인 또는 복구 조건이 충족되기 전까지 인지 에이전트가 중단된 작업을 자동으로 재개해서는 안 된다.

안전 오버라이드(Safety Override)는 작업 계획기에서 보이지 않는 상태로 남아 있어서는 안 되며 인지 상태(Cognitive State)를 갱신해야 한다. 사람이 작업공간에 진입하여 동작이 정지된 경우 에이전트는 예상했던 사후조건(Expected Postcondition)이 달성되지 않았다는 사실을 알아야 한다. 작업 상태에는 중단된 스킬, 개입 이유, 현재 로봇 구성(Robot Configuration), 관련 환경 변화가 기록되어야 한다. 이를 통해 이후의 재계획은 중단된 행동이 성공적으로 완료되었다고 잘못 가정하지 않고 실제 물리적 상태에서 시작할 수 있다.

모든 오버라이드가 즉각적인 재계획(Replanning)을 유발해야 하는 것은 아니다. 짧은 보호 일시정지(Protective Pause)는 사람이 통제 영역을 벗어난 이후 기존 실행을 계속할 수 있도록 하지만, 지속적인 장애물은 대체 경로나 다른 조작 전략을 요구할 수 있다. 시스템은 일시적인 안전 중단(Temporary Safety Interruption)과 현재 계획 자체를 무효화하는 조건을 구분해야 한다. 이를 통해 과도한 인지적 재계획을 방지하면서도 중요한 변화가 발생한 경우 작업 수준 추론에 해당 변화가 반영되도록 할 수 있다.

에이전트가 생성한 복구 행동(Recovery Action)은 일반적인 행동과 동일한 안전 경계를 통과해야 한다. 파지가 실패했다고 해서 힘의 한계를 초과하거나, 충돌 제약조건을 우회하거나, 금지된 영역으로 손을 뻗는 행동이 허용되는 것은 아니다. 재계획은 시점(Viewpoint), 로봇 위치, 파지 전략(Grasp Strategy), 손 선택, 작업 순서를 변경할 수 있지만 강제적인 안전 제약조건(Hard Safety Constraint)을 다시 정의할 수는 없다. 이를 통해 반복적인 실패가 점차 더 공격적이고 위험한 행동으로 이어지는 것을 방지한다.

기능 저하(Capability Degradation)가 발생하면 안전 영역(Safety Envelope)도 동적으로 변경되어야 한다. 센서 범위 감소, 액추에이터 고장, 통신 문제, 열적 한계(Thermal Limit), 낮은 배터리 상태, 위치 추정 불확실성(Localization Uncertainty)은 이전에는 허용되었던 행동을 위험하게 만들 수 있다. 시스템은 저하된 기능에 따라 속도를 줄이고, 작업공간을 제한하고, 영향을 받은 스킬을 비활성화하거나, 자율 운용을 중단할 수 있다. 계획 시스템은 갱신된 제한조건을 전달받아 더 이상 사용할 수 없거나 신뢰할 수 없는 기능을 기반으로 행동을 계속 제안하지 않도록 해야 한다.

권한 경계(Authorization Boundary) 역시 에이전트 안전에서 중요하다. 로봇은 문을 열거나, 장비를 이동하거나, 특정 공간에 접근하거나, 기계를 작동시킬 수 있는 물리적 능력을 가지고 있더라도 그러한 행동을 수행할 권한이 없을 수 있다. 따라서 안전 아키텍처는 물리적 실행 가능성(Physical Feasibility)과 운용 권한(Operational Authorization)을 구분해야 한다. 사용자 정체성(Identity), 작업 역할(Task Role), 환경 상태, 운용 모드, 명시적 권한(Explicit Permission)을 기반으로 특정 기능의 사용 가능 여부를 결정함으로써 계획기가 기계적으로 가능하다는 사실을 행동 허가로 잘못 해석하는 것을 방지할 수 있다.

안전 모니터링(Safety Monitoring)은 진단과 검증에 충분한 사건 정보를 보존해야 한다. 기록에는 요청된 행동, 안전 조건, 오버라이드 유형(Override Type), 로봇 상태, 관련 센서 증거(Sensor Evidence), 대응 지연시간(Response Latency), 최종 결과가 포함될 수 있다. 이러한 기록은 디버깅(Debugging), 시스템 개선, 사고 분석(Incident Analysis), 안전 동작 검증을 지원한다. 또한 개발자가 안전 개입이 잘못된 계획, 변화한 환경 조건, 인식 불확실성 또는 실제 물리적 위험 가운데 어떤 원인에서 발생했는지를 판단할 수 있도록 한다.

테스트(Testing)는 정상적인 작업 실행만을 평가하는 것이 아니라 의도적으로 안전 경계를 시험해야 한다. 검증 시나리오(Validation Scenario)에는 예상하지 못한 사람의 진입, 이동 장애물, 과도한 탑재하중, 도달 불가능한 대상, 불안정한 자세, 센서 성능 저하, 금지 구역, 통신 중단, 잘못된 에이전트 명령 등을 포함할 수 있다. 목적은 안전하지 않은 요청이 거부되는지, 새롭게 발생하는 위험이 적절한 오버라이드를 유발하는지, 그리고 고수준 추론의 성공 여부에 의존하지 않고 로봇이 통제된 상태(Controlled State)로 전환되는지를 검증하는 것이다.

대응 지연시간(Response Latency)은 서로 다른 위험이 서로 다른 속도로 진행되기 때문에 중요한 설계 요소이다. 의미론적 권한 검사(Semantic Permission Check)와 작업 계획 검증(Task-Plan Validation)은 인지적 시간 척도에서 동작할 수 있지만, 임박한 충돌, 과도한 힘, 미끄러지는 접촉, 균형 상실은 훨씬 빠른 개입을 요구한다. 따라서 안전 기능은 요구되는 시간 범위 안에서 대응할 수 있는 계층에 배치해야 한다. 빠르게 발생하는 위험은 느린 숙고 추론(Deliberative Inference)에만 의존해서는 안 된다.

궁극적으로 에이전트 안전 경계 및 오버라이드 시스템(Agent Safety Boundary and Override System)은 지능형 자율성(Intelligent Autonomy)과 물리적 실행 권한 사이에 통제된 분리(Controlled Separation)를 확립한다. 인공지능 에이전트는 목표를 해석하고, 도구를 선택하고, 계획을 생성하며, 실패로부터 복구할 수 있지만, 독립적인 안전 계층은 제안된 행동의 허용 가능 여부를 결정하고 실행 과정이 계속 안전한지를 지속적으로 감시한다. 계층화된 검증(Layered Validation), 실시간 제약조건(Real-Time Constraint), 단계적 오버라이드(Graded Override), 상태 피드백(State Feedback), 통제된 복구(Controlled Recovery)를 결합함으로써 휴머노이드는 확률적 추론에 물리 시스템에 대한 무제한 제어 권한을 부여하지 않으면서도 유연한 자율 행동(Flexible Autonomous Behavior)을 유지할 수 있다.

## 10.09. Agent Evaluation Success Rate Efficiency [w/Code]

![](images/image10.png){width="7.268055555555556in" height="7.268055555555556in"}

휴머노이드 인공지능 에이전트(Humanoid AI Agent)를 평가하려면 단순히 작업이 최종적으로 성공했는지만 판단해서는 충분하지 않다. 자율 에이전트(Autonomous Agent)는 인식(Perception), 메모리(Memory), 그라운딩(Grounding), 계획(Planning), 도구 사용(Tool Use), 조작(Manipulation), 이동(Locomotion), 안전(Safety), 복구(Recovery)를 통합하므로 전체 의사결정-실행 루프(Decision-Execution Loop)에 걸쳐 성능을 측정해야 한다. 이 장의 구조에서는 성공률(Success Rate)과 효율성(Efficiency)을 휴머노이드 인공지능 에이전트 아키텍처(Humanoid AI Agent Architecture)의 핵심 평가 요소로 설정한다.

작업 성공률(Task Success Rate)은 가장 직접적인 에이전트 수준 평가 지표이다. 하나의 시험은 로봇이 단순히 일련의 명령을 완료하는 것이 아니라 요구되는 제약조건을 만족하면서 의도된 목표 상태(Goal State)에 도달했을 때 성공한 것으로 판단한다. 예를 들어 객체를 가져오는 작업에서는 올바른 객체를 찾았더라도 전달하기 전에 떨어뜨렸다면 성공으로 볼 수 없다. 따라서 성공 기준(Success Criteria)은 인식, 로봇 상태 또는 작업별 검증 로직(Task-Specific Validation Logic)을 통해 독립적으로 확인할 수 있는 관측 가능한 최종 조건으로 정의해야 한다.

이진 성공(Binary Success)만으로는 에이전트 사이의 중요한 차이를 파악하기 어렵다. 부분적으로 완료된 작업은 최종 배치에 실패했더라도 성공적인 내비게이션, 객체 위치 확인, 파지 과정을 포함할 수 있다. 따라서 전체 작업 성공 여부와 함께 하위 목표 완료(Subgoal Completion)를 기록할 수 있다. 이러한 분해를 통해 어떤 단계가 실패의 주요 원인인지를 식별할 수 있으며, 장기 작업(Long-Horizon Task)의 후반부에서 발생한 관련 없는 실패 때문에 특정 기능의 개선 효과가 가려지는 것을 방지할 수 있다.

성공률은 단일 시연(Isolated Demonstration)이 아니라 반복 시험(Repeated Trial)을 통해 계산해야 한다. 물리적 환경에는 객체 자세(Object Pose), 사람의 행동, 센서 관측, 로봇 구성(Robot Configuration), 실행 동역학(Execution Dynamics)의 변화가 존재한다. 통제된 변화를 적용하면서 동일한 작업을 반복하면 성공이 강건한지 아니면 유리한 초기 조건에 의존하는지를 확인할 수 있다. 시험 횟수와 실패 분포(Failure Distribution)를 함께 기록하여 적거나 제한적으로 구성된 테스트 집합에서 얻은 높은 성공률이 신뢰할 수 있는 자율성으로 잘못 해석되지 않도록 해야 한다.

효율성(Efficiency)은 에이전트가 성공적인 결과에 얼마나 경제적으로 도달하는지를 측정한다. 실행 시간(Execution Time)은 중요한 요소이지만 효율성에는 이동 거리, 행동 횟수, 불필요한 도구 호출(Tool Call), 반복적인 인식 질의(Perception Query), 재계획 빈도(Replanning Frequency), 에너지 사용량, 계산 비용(Computational Cost)도 포함된다. 두 에이전트가 동일한 성공률을 달성하더라도 하나의 에이전트가 관련 없는 위치를 반복적으로 탐색하거나 불필요한 검증을 수행할 수 있다. 따라서 에이전트 평가에서는 최종적인 성공과 효율적인 목표 지향 행동(Goal-Directed Behavior)을 구분해야 한다.

유용한 효율성 기준선(Efficiency Baseline)은 동일한 조건에서 사용할 수 있는 최단 또는 참조 작업 절차(Reference Task Procedure)이다. 실행된 궤적, 조작 시도 횟수 또는 전체 작업 단계 수를 전문가 시연(Expert Demonstration), 사전에 정의된 참조 계획(Reference Plan) 또는 실행 가능한 하한(Feasible Lower Bound)과 비교할 수 있다. 이러한 비교는 단순한 완료 시간만으로는 발견하기 어려운 계획 비효율성(Planning Inefficiency)을 보여준다. 추가적인 검증이나 보다 안전한 동작이 필요한 경우 긴 실행 시간이 정당화될 수 있으므로 효율성은 항상 안전성과 신뢰성과 함께 해석해야 한다.

계획 품질(Planning Quality)은 물리적 실행 이전과 실행 과정 모두에서 평가할 수 있다. 생성된 계획에는 유효한 스킬(Valid Skill), 올바른 인자(Correct Argument), 충족된 의존성(Satisfied Dependency), 도달 가능한 목표(Reachable Target), 적절한 행동 순서가 포함되어야 한다. 평가 지표에는 유효하지 않은 행동 비율(Invalid Action Rate), 불필요한 단계 수, 계획 수정 빈도(Plan Revision Frequency), 수정 없이 실행 가능한 생성 단계의 비율 등이 포함될 수 있다. 이러한 측정은 추론 실패(Reasoning Failure)를 하위 수준의 이동 또는 조작 시스템에서 발생한 실패와 구분할 수 있도록 한다.

그라운딩 정확도(Grounding Accuracy)는 에이전트 명령이 올바른 물리적 개체와 위치에 연결되는지를 측정한다. 평가에서는 객체 참조 해석(Object Reference Resolution), 공간 관계(Spatial Relation), 제스처 해석(Gesture Interpretation), 시간적 참조(Temporal Reference), 대화 의존적 표현(Dialogue-Dependent Expression)을 시험할 수 있다. 작업 실패는 계획기가 잘못된 전략을 선택해서 발생할 수도 있지만 "테이블 옆의 빨간 상자"가 잘못된 객체와 연결되어 발생할 수도 있다. 그라운딩 오류를 별도로 기록하면 에이전트 행동의 원인을 보다 의미 있게 진단할 수 있다.

도구 사용 성능(Tool-Use Performance)은 정확성과 경제성을 모두 측정해야 한다. 에이전트는 적절한 인식, 메모리, 내비게이션, 조작 또는 진단 도구를 선택하고 유효한 매개변수(Parameter)를 제공해야 한다. 평가에서는 성공적인 도구 호출, 유효하지 않은 호출, 중복 호출(Redundant Call), 도구 선택 오류(Tool-Selection Error), 반환된 정보가 후속 추론에 반영되었는지를 기록할 수 있다. 효과적인 도구 사용은 의사결정 상태를 변화시키지 않는 서비스를 반복적으로 호출하지 않으면서 필요한 정보나 기능을 획득하고 실행하는 것을 의미한다.

장기 작업은 메모리와 상태 일관성(State Consistency)에 대한 평가가 필요하다. 에이전트는 여러 행동에 걸쳐 완료된 하위 작업, 이전에 식별된 객체, 실패한 전략, 현재 들고 있는 물체, 해결되지 않은 조건을 기억해야 한다. 테스트에서는 관련 정보가 필요할 때 유지되고 있는지와 오래된 정보(Stale Information)가 적절하게 수정되는지를 측정할 수 있다. 이미 완료된 행동을 반복하거나 로봇이 이미 들고 있는 객체를 다시 탐색하는 것은 개별 인식 및 제어 모듈이 정상적으로 작동하더라도 인지 상태 실패(Cognitive-State Failure)를 의미한다.

실패 복구(Failure Recovery)는 실제 환경에서 휴머노이드 운용이 완벽한 실행에 의존할 수 없기 때문에 에이전트 평가의 중요한 구성 요소이다. 테스트에서는 차단된 경로, 이동된 객체, 파지 실패, 인식 모호성(Perception Ambiguity), 사용할 수 없는 도구, 일시적인 사람의 개입 등을 의도적으로 발생시켜야 한다. 복구 성공률(Recovery Success Rate), 복구 시간(Time to Recovery), 재시도 횟수, 보존된 작업 진행 상태, 상위 지원 요청 빈도(Escalation Frequency)를 통해 에이전트가 예기치 않은 사건을 작업 전체의 포기나 재시작이 아니라 통제된 재계획(Controlled Replanning)으로 전환할 수 있는지를 평가할 수 있다.

반복적인 실패는 복구 성공 여부뿐만 아니라 복구 효율성(Recovery Efficiency)의 관점에서도 분석해야 한다. 동일한 파지를 열 번 반복한 이후 최종적으로 성공하는 에이전트는 실패 원인을 감지하고, 시점을 변경하고, 객체 자세를 다시 획득한 뒤 다음 시도에서 성공하는 에이전트보다 능력이 낮다고 평가할 수 있다. 따라서 연속된 복구 시도가 정보 또는 실행 조건을 의미 있게 변화시키는지를 기록해야 한다. 이를 통해 적응형 재계획(Adaptive Replanning)과 단순한 반복을 지속성(Persistence)으로 가장한 행동을 구분할 수 있다.

안전(Safety)은 효율성과 교환 가능한 평가 지표가 아니라 반드시 충족해야 하는 평가 차원으로 취급해야 한다. 작업 실행이 빨라졌더라도 사람과의 위험한 근접, 과도한 힘, 불안정한 자세, 충돌 위험, 안전 오버라이드(Safety Override)가 증가한다면 성능 향상으로 볼 수 없다. 측정 항목에는 안전 규칙 위반, 보호 정지(Protective Stop), 비상 개입(Emergency Intervention), 거부된 에이전트 명령, 최소 분리 여유(Minimum Separation Margin), 정의된 물리적 제약조건 내에서의 실행 여부가 포함될 수 있다. 안전하지 않은 행동을 통해 달성한 성공은 유효한 작업 성공으로 분류해서는 안 된다.

사람의 개입(Human Intervention)은 자율성(Autonomy)을 평가하는 또 다른 지표를 제공한다. 에이전트가 대부분의 시험을 완료하더라도 빈번하게 명확화 요청(Clarification), 수동 위치 조정, 객체 조작 지원 또는 운영자 복구(Operator Recovery)를 필요로 할 수 있다. 따라서 개입 빈도(Intervention Rate), 개입 유형, 지원이 필요해지는 작업 단계를 성공률과 함께 평가할 수 있다. 그러나 적절한 명확화 요청을 자동으로 실패로 간주해서는 안 된다. 그라운딩 신뢰도(Grounding Confidence)가 충분하지 않을 때 정보를 요청하는 것은 근거가 부족한 가정으로 행동하는 것보다 더 안전하고 지능적인 행동일 수 있기 때문이다.

지연시간(Latency)은 의미 있는 의사결정 경계(Decision Boundary)에서 평가해야 한다. 인식 갱신, 그라운딩, 대규모 언어 모델(LLM) 추론, 도구 선택, 재계획, 스킬 초기화(Skill Initialization)는 각각 물리적 행동이 시작되거나 재개되기 전에 지연을 발생시킬 수 있다. 종단 간 작업 시간(End-to-End Task Time)만으로는 이러한 병목현상을 식별할 수 없다. 구성 요소별 및 전환 지연시간(Transition Latency)을 측정하면 느린 성능이 인지 추론, 외부 도구 호출, 인식 처리, 동작 계획(Motion Planning), 물리적 실행 중 어디에서 발생하는지를 판단할 수 있다.

에이전트 평가는 고정된 시연을 암기하는 능력이 아니라 일반화(Generalization)를 측정할 수 있도록 환경 변화를 포함해야 한다. 객체 위치, 혼잡도(Clutter), 조명, 시점(Viewpoint), 작업 명령 표현, 사람 위치, 경로 가용성(Route Availability), 초기 로봇 상태를 체계적으로 변화시킬 수 있다. 더욱 어려운 테스트에서는 동일한 작업 의미(Task Semantics)를 유지하면서 이전에 경험하지 않은 조합을 도입할 수 있다. 이러한 조건에서 나타나는 성능 저하는 에이전트가 계획 및 그라운딩 기능을 얼마나 신뢰성 있게 새로운 상황으로 전이(Transfer)할 수 있는지를 보여준다.

평가 시나리오는 작업 길이(Task Horizon)와 의존성 깊이(Dependency Depth)도 증가시켜야 한다. 짧은 작업은 적은 수의 의사결정만 필요하기 때문에 메모리, 계획, 복구의 약점을 숨길 수 있다. 긴 절차에서는 초기의 관측이나 행동이 훨씬 이후의 행동에 영향을 주는 의존성이 발생한다. 작업 길이가 증가할 때 성공률을 측정하면 문맥(Context)이 유지되는지, 오류가 누적되는지, 도구 사용이 계속 효율적인지, 계획기가 장시간 실행에서도 일관된 진행 상태(Coherent Progress)를 유지할 수 있는지를 확인할 수 있다.

벤치마크 결과(Benchmark Result)는 단순한 평균값만이 아니라 분포(Distribution)를 함께 보고해야 한다. 작업 시간의 중앙값(Median), 시험 간 변동성(Variation), 최악 조건에서의 복구 행동(Worst-Case Recovery Behavior), 실패 범주(Failure Category), 백분위 지연시간(Percentile Latency)은 하나의 평균값에 가려진 불안정성을 보여줄 수 있다. 또한 시나리오 유형별 결과를 비교함으로써 쉬운 작업이 전체 종합 점수를 지배하는 것을 방지할 수 있다. 균형 잡힌 평가는 에이전트가 어떤 조건에서 지속적으로 신뢰할 수 있는지, 어디에서 성능 변동이 발생하는지, 어떤 운용 조건이 아직 신뢰 가능한 기능 범위(Capability Envelope) 밖에 있는지를 보여주어야 한다.

에이전트 아키텍처가 발전함에 따라 회귀 평가(Regression Evaluation)가 필요하다. 대규모 언어 모델, 인식 모델, 메모리 시스템, 프롬프트(Prompt), 스킬 인터페이스(Skill Interface), 계획기의 변경은 하나의 작업 성능을 향상시키면서 다른 작업의 성능을 저하시킬 수 있다. 안정적인 벤치마크 집합(Benchmark Suite)을 사용하면 동일한 시나리오와 승인 기준(Acceptance Criteria)을 기반으로 각 소프트웨어 버전을 이전 버전과 비교할 수 있다. 시뮬레이션의 자동 재생(Automated Replay)은 광범위한 테스트 범위를 제공하고, 하드웨어 시험은 개선된 성능이 실제 센싱, 동역학, 접촉, 지연시간 조건에서도 유지되는지를 확인할 수 있다.

따라서 종합적인 에이전트 점수(Comprehensive Agent Score)는 하나의 보편적인 숫자가 아니라 서로 보완적인 여러 측정값의 집합으로 해석해야 한다. 작업 성공은 목표 달성 여부를 나타내고, 효율성은 필요한 자원의 양을 나타내며, 그라운딩과 계획 지표는 의사결정 품질(Decision Quality)을 설명한다. 복구 지표는 회복탄력성(Resilience)을 측정하고, 안전은 허용 가능한 운용 조건을 설정하며, 사람의 개입은 실질적인 자율성을 나타낸다. 이러한 평가 차원을 각각 명확하게 유지하면 하나의 지표를 최적화하는 과정에서 다른 영역의 성능 저하가 감춰지는 것을 방지할 수 있다.

궁극적으로 에이전트 평가(Agent Evaluation)는 휴머노이드 인공지능 아키텍처가 인식과 추론을 신뢰할 수 있는 물리적 성능(Physical Performance)으로 변환할 수 있는지를 판단한다. 우수한 시스템은 작업을 반복적으로 완료하고, 유효하면서 경제적인 행동을 선택하며, 장기 작업 동안 상태를 유지하고, 실패로부터 지능적으로 복구하며, 안전 경계를 준수하고, 불필요한 사람의 개입을 최소화해야 한다. 따라서 성공률(Success Rate)과 효율성(Efficiency)을 기반으로 한 평가는 전체 인식-메모리-계획-행동 루프(Perception-Memory-Planning-Action Loop)가 신뢰 가능하고, 확장 가능하며, 실용적인 휴머노이드 자율성(Humanoid Autonomy)을 만들어내는지를 검증하는 시스템 수준의 시험이 된다.

## 10.10. Humanoid AI Agent Industrial Pilot Case

![](images/image11.png){width="7.268055555555556in" height="7.268055555555556in"}

휴머노이드 인공지능 에이전트(Humanoid AI Agent)의 산업 파일럿(Industrial Pilot)은 통합된 인지 및 물리 아키텍처(Cognitive and Physical Architecture)가 실제 생산 환경에서 유용한 작업을 수행할 수 있는지를 평가한다. 하나의 독립된 기능에 초점을 맞추는 실험실 시연과 달리 파일럿은 인식(Perception), 의미 메모리(Semantic Memory), 언어 이해(Language Understanding), 작업 계획(Task Planning), 도구 사용(Tool Use), 이동(Locomotion), 조작(Manipulation), 안전 감독(Safety Supervision), 실패 복구(Failure Recovery)를 통합한다. 목표는 이러한 구성 요소들이 반복적인 산업 작업 주기에서 하나의 신뢰할 수 있는 자율 시스템(Autonomous System)으로 동작할 수 있는지를 판단하는 것이다.

적절한 파일럿은 명확하게 제한된 운용 영역(Operational Domain)에서 시작한다. 휴머노이드는 통제된 생산 영역에서 자재 취급(Material Handling), 부품 회수(Component Retrieval), 기계 작업 지원(Machine Tending), 작업대 보충(Workstation Replenishment), 검사(Inspection), 키팅(Kitting), 도구 전달(Tool Delivery)을 수행할 수 있다. 작업은 추론과 적응이 필요할 만큼 충분히 복잡하면서도 측정 가능한 검증이 가능하도록 제한되어야 한다. 정의된 작업 구역, 객체 클래스(Object Class), 장비 인터페이스(Equipment Interface), 작업자 역할, 허용된 행동을 통해 자율 행동을 평가할 운용 범위(Operational Envelope)를 설정한다.

파일럿 작업 흐름(Pilot Workflow)은 생산 시스템이나 작업자가 특정 부품을 가져와 조립 작업대에 전달하라는 작업 요청을 제공하면서 시작할 수 있다. 에이전트는 명령을 해석하고, 요청된 객체와 목적지를 식별하며, 사용 가능한 장면 및 작업 메모리(Scene and Task Memory)를 확인하고, 추가 정보가 필요한지를 판단한다. 이러한 초기 그라운딩 단계(Grounding Stage)는 기호적 생산 요청(Symbolic Production Request)을 물리적 개체, 위치, 제약조건, 실행 가능한 로봇 기능에 대한 참조로 변환한다.

의미론적 장면 메모리(Semantic Scene Memory)는 관련 객체가 즉시 보이지 않는 경우에도 연속성을 제공한다. 에이전트는 특정 유형의 부품이 일반적으로 특정 랙(Rack)에 보관된다는 사실이나 필요한 도구가 최근 다른 작업대에서 관측되었다는 사실을 기억할 수 있다. 메모리 항목에는 객체 정체성(Object Identity), 위치, 관계, 상태, 신뢰도(Confidence), 관측 시간이 포함된다. 산업 환경은 지속적으로 변화하기 때문에 기억된 정보는 현재의 진실로 보장되는 것이 아니라 물리적 검증이 필요할 수 있는 가설(Hypothesis)로 취급한다.

작업 계획기(Task Planner)는 요청된 작업을 실행 가능한 하위 목표(Subgoal)로 분해한다. 회수 작업은 보관 장소로 이동하고, 올바른 보관함을 식별하고, 내부 내용을 검사하고, 목표 부품을 선택하고, 파지(Grasp)를 생성하고, 객체를 운반하고, 수신 작업대를 찾은 다음 배치 또는 전달을 수행하는 과정을 포함할 수 있다. 이러한 단계 사이의 의존성(Dependency)을 유지하여 필요한 물리적 조건이 검증된 이후에만 후속 행동이 실행되도록 한다.

도구 사용(Tool Use)을 통해 에이전트는 모든 문제를 일반적인 추론으로 직접 해결하려 하지 않고 전문화된 기능을 호출할 수 있다. 계획기는 객체 감지(Object Detection), 바코드 또는 텍스트 인식(Barcode or Text Recognition), 의미 지도 질의(Semantic Map Query), 내비게이션(Navigation), 파지 계획(Grasp Planning), 조작, 진단(Diagnostics), 메모리 검색(Memory Retrieval) 서비스를 호출할 수 있다. 각 도구는 정의된 입력, 출력, 제약조건, 실패 상태를 제공한다. 이러한 구조화된 인터페이스(Structured Interface)는 고수준 인공지능 추론과 산업용 로봇 플랫폼에서 사용하는 전문 인식 및 제어 구현을 분리한다.

다중모달 그라운딩(Multimodal Grounding)은 휴머노이드가 작업자와 협력할 때 중요해진다. 작업자는 보관 영역을 가리키면서 "저 랙에 있는 부품을 가져와"라고 말하거나 "이전 조립에서 사용했던 부품"을 참조할 수 있다. 에이전트는 음성, 시각 인식, 제스처, 공간 관계, 대화 이력(Dialogue History), 의미 메모리를 결합하여 의도된 참조를 해석한다. 신뢰도가 충분하지 않은 경우 근거가 부족한 해석에 따라 행동하는 것보다 명확화(Clarification)를 요청하는 것이 바람직하다.

물리적 실행(Physical Execution)은 계층적 구조를 유지한다. 인지 에이전트(Cognitive Agent)는 목표, 하위 목표, 스킬 선택(Skill Selection)을 결정하고, 전용 내비게이션, 동작 계획(Motion Planning), 전신 제어(Whole-Body Control), 파지, 균형 시스템은 보다 빠른 시간 척도에서 물리적 행동을 실행한다. 에이전트가 모든 관절 명령을 직접 생성하는 것은 아니다. 이러한 분리는 의미론적 계획의 유연성을 유지하면서 결정론적 또는 학습 기반 하위 제어기(Low-Level Controller)가 안정성, 접촉 행동, 궤적 실행, 실시간 대응성을 유지하도록 한다.

중요한 단계 사이에는 검증(Verification)이 삽입된다. 보관 랙에 도달했다고 해서 올바른 랙에 도달했다는 것이 보장되는 것은 아니며, 그리퍼(Gripper)를 닫았다고 해서 의도한 부품을 실제로 획득했다는 의미도 아니다. 카메라, 촉각 센싱(Tactile Sensing), 힘 피드백(Force Feedback), 객체 추적(Object Tracking), 로봇 상태를 이용하여 중요한 사후조건(Postcondition)을 검증할 수 있다. 예상된 물리적 상태가 달성되었다는 충분한 증거가 확보된 경우에만 작업을 다음 단계로 진행하여 장기 산업 작업에서 감지되지 않은 오류가 연쇄적으로 전파되는 것을 줄인다.

산업 파일럿에서는 통제된 시연에서 자주 나타나지 않는 다양한 실패가 필연적으로 발생한다. 부품이 없거나, 다른 객체가 접근을 방해하거나, 작업자가 일시적으로 이동 경로를 차단하거나, 파지가 미끄러지거나, 위치 추정 신뢰도(Localization Confidence)가 감소할 수 있다. 실패 인지형 에이전트(Failure-Aware Agent)는 예상 상태와 관측 상태의 차이를 기록하고, 영향을 받은 작업 의존성을 식별하며, 국소 복구(Local Recovery), 하위 목표 수정, 보다 광범위한 재계획(Replanning), 사람의 지원 가운데 적절한 대응을 결정한다.

국소 복구는 가능한 경우 이미 완료된 작업 진행 상태를 보존한다. 부품 자세 추정(Component Pose Estimation)이 부정확하여 파지가 실패했다면 휴머노이드는 내비게이션을 처음부터 다시 시작하지 않고 새로운 시점을 확보하여 파지를 다시 생성할 수 있다. 이동 경로가 차단되었다면 내비게이션 시스템은 나머지 작업 계획을 유지하면서 대체 경로를 선택할 수 있다. 모든 실행 오류가 발생할 때마다 전체 임무를 다시 시작하면 자율 운용이 느리고 비실용적이 되므로 이러한 동작은 산업 효율성(Industrial Efficiency)에 필수적이다.

반복적인 실패는 무제한 재시도가 아니라 통제된 상위 대응(Controlled Escalation)을 요구한다. 에이전트는 이전의 시도, 시점(Viewpoint), 파지 전략(Grasp Strategy), 진단 결과를 추적하여 각각의 복구 행동이 실행 조건을 의미 있게 변화시키도록 할 수 있다. 사용 가능한 대안이 모두 소진되면 로봇은 사람의 지원을 요청하고 접근할 수 없는 부품이나 모호한 보관 위치와 같은 관련 문맥을 설명할 수 있다. 따라서 사람의 개입(Human Intervention)은 자율성을 비구조적으로 대체하는 것이 아니라 관리되는 복구 메커니즘(Managed Recovery Mechanism)으로 기능한다.

안전 감독(Safety Supervision)은 파일럿 전체에서 인공지능 계획기와 독립적으로 유지된다. 사람과의 근접성(Human Proximity), 제한 구역(Restricted Zone), 관절 한계(Joint Limit), 탑재하중 제약(Payload Constraint), 충돌 위험(Collision Risk), 균형 안정성(Balance Stability), 접촉력(Contact Force), 로봇 상태를 적절한 안전 및 제어 계층에서 감시한다. 에이전트는 행동을 제안할 수 있지만 독립적인 메커니즘이 실제 실행 허용 여부를 결정한다. 필요한 경우 보호 감속(Protective Slowdown), 일시정지, 궤적 수정, 작업 중단, 비상 정지(Emergency Stop)가 인지적 결정을 오버라이드(Override)할 수 있다.

파일럿은 로봇이 격리된 작업공간에서 동작한다고 가정하기보다 동적인 사람과의 상호작용(Dynamic Human Interaction)을 포함해야 한다. 작업자는 내비게이션 경로를 가로지르거나, 조작 중인 휴머노이드에 접근하거나, 작업 변경을 요청하거나, 객체를 직접 전달할 수 있다. 에이전트는 일시적인 중단과 작업 자체를 무효화하는 변화를 구분해야 한다. 짧은 보호 일시정지(Protective Pause) 이후에는 자동으로 실행을 재개할 수 있지만, 목적지 변경이나 부품 사용 불가 상태는 작업을 계속하기 전에 작업 계획과 인지 상태를 갱신해야 할 수 있다.

시스템 상태(System Health) 역시 자율 기능에 영향을 준다. 손목 카메라(Wrist Camera)의 성능 저하, 위치 추정 신뢰도 감소, 액추에이터 온도 경고(Actuator Temperature Warning), 통신 문제, 낮은 에너지 상태는 사용 가능한 스킬을 제한할 수 있다. 에이전트 아키텍처는 갱신된 기능 정보(Capability Information)를 전달받아 사용할 수 없는 자원을 기반으로 계획하지 않아야 한다. 문제의 심각도에 따라 시스템은 속도를 낮추고, 대체 센싱(Alternative Sensing)을 사용하고, 작업을 수정하고, 서비스 위치로 복귀하거나, 작업자에게 책임을 전환할 수 있다.

파일럿 평가는 개별적인 성공 시연을 보여주는 것이 아니라 전체 작업 성능(Complete Task Performance)을 측정해야 한다. 반복 시험을 통해 작업 성공률(Task Success Rate), 하위 목표 완료율, 실행 시간, 이동 거리, 도구 호출 횟수, 조작 시도 횟수, 재계획 발생 횟수, 복구 시간, 사람의 개입, 안전 오버라이드를 기록할 수 있다. 실패 범주(Failure Category) 역시 보존하여 성능 개선이 인식, 그라운딩, 계획, 조작, 메모리, 복구 가운데 어떤 영역에서 발생했는지를 하나의 종합 성공률에 가리지 않고 분석할 수 있도록 해야 한다.

효율성(Efficiency)은 신뢰성(Reliability)과 함께 평가해야 한다. 작업을 빠르게 완료하더라도 사람의 복구 지원을 빈번하게 필요로 하는 에이전트는 약간 느리지만 일관된 자율성을 유지하는 에이전트보다 실제 활용 가치가 낮을 수 있다. 마찬가지로 과도한 인식 질의, 반복적인 대규모 언어 모델(LLM) 추론, 중복된 내비게이션, 불필요한 검증은 높은 성공률을 유지하더라도 생산 가치를 감소시킬 수 있다. 따라서 산업 평가는 각각의 성공적인 작업 주기에 필요한 시간, 계산량, 에너지, 움직임, 사람의 개입 정도를 함께 고려한다.

파일럿 진행(Pilot Progression)은 복잡도를 점진적으로 증가시키는 방식으로 구성해야 한다. 초기 시험에서는 객체 위치와 이동 경로를 고정한 상태에서 시작하고 이후 가변적인 객체 배치, 혼잡 환경(Clutter), 이동하는 작업자, 모호한 명령, 사용할 수 없는 자원, 의도적으로 발생시킨 실행 실패를 추가할 수 있다. 이후 더 긴 작업 시퀀스를 통해 메모리 지속성(Memory Persistence)과 의존성 관리(Dependency Management)를 시험할 수 있다. 이러한 단계적 접근은 실험실 시험에서 곧바로 제한 없는 배포로 이동하는 대신 성능이 저하되기 시작하는 기능 경계(Capability Boundary)를 식별할 수 있도록 한다.

운용 로그(Operational Log)는 시스템 개선과 회귀 시험(Regression Testing)에 필요한 근거를 제공한다. 각각의 시험에서는 최초 명령, 그라운딩된 개체, 생성된 계획, 도구 호출, 중요한 관측, 상태 전이(State Transition), 실패, 복구 결정, 안전 개입, 최종 결과를 보존할 수 있다. 이러한 기록을 통해 개발자는 에이전트가 특정 방식으로 행동한 이유를 재구성할 수 있으며, 실제 현장에서 관측된 실패를 기반으로 반복 가능한 시뮬레이션 또는 하드웨어 테스트를 생성할 수 있다.

성공적인 산업 파일럿은 휴머노이드가 가능한 모든 생산 작업을 완전 자율적으로 해결해야 한다는 것을 의미하지 않는다. 대신 명시적인 운용 범위 안에서 정의된 작업 집합을 반복적으로 수행하고, 불확실성을 예측 가능한 방식으로 처리하며, 자율 기능의 한계에 도달했을 때 안전하게 상위 대응을 수행할 수 있음을 입증해야 한다. 중요한 전환점은 인상적인 단일 시연(Single Demonstration)에서 실제 산업 작업 흐름에 통합할 수 있는 측정 가능하고, 복구 가능하며, 반복 가능한 운용(Repeatable Operation)으로 발전하는 것이다.

따라서 휴머노이드 인공지능 에이전트 산업 파일럿(Humanoid AI Agent Industrial Pilot)은 실제 물리적 운용 조건에서 전체 인식-메모리-추론-행동 아키텍처(Perception-Memory-Reasoning-Action Architecture)를 검증한다. 언어 및 다중모달 그라운딩은 의도를 확립하고, 의미 메모리는 환경 문맥을 유지하며, 계획은 전문 도구와 스킬을 조정하고, 검증은 실행 루프(Execution Loop)를 폐루프로 구성하며, 실패 인지형 재계획은 회복탄력성(Resilience)을 제공하고, 독립적인 안전 메커니즘은 물리적 실행 권한을 제한한다. 이러한 기능의 통합을 통해 휴머노이드 지능이 실험적 자율성(Experimental Autonomy)에서 신뢰할 수 있는 산업 운용(Dependable Industrial Operation)으로 발전할 수 있는지를 판단할 수 있다.
