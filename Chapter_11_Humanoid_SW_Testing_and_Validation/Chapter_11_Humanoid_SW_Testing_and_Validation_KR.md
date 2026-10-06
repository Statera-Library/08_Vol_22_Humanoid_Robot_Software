**Volume 22. Humanoid Robot Software**

# Chapter 11. Humanoid SW Testing and Validation

## 11.01. Humanoid SW Test Strategy SIL HIL Field

![](images/image1.png){width="7.268055555555556in" height="7.268055555555556in"}

휴머노이드 소프트웨어 시험 전략(Humanoid Software Test Strategy)은 개별 알고리즘뿐만 아니라 인식(Perception), 상태 추정(State Estimation), 계획(Planning), 전신 제어(Whole-Body Control), 구동(Actuation), 안전 기능(Safety Function)이 결합된 전체 폐루프 동작(Closed-Loop Behavior)을 검증해야 한다. 휴머노이드는 부유 기반 동역학(Floating-Base Dynamics), 간헐적 접촉(Intermittent Contact), 고차원 구동(High-Dimensional Actuation), 조작(Manipulation), 인간 상호작용(Human Interaction)을 결합하기 때문에 작은 오류도 빠르게 물리적 위험으로 발전할 수 있다. 따라서 검증은 소프트웨어 인 더 루프(SIL, Software-in-the-Loop), 하드웨어 인 더 루프(HIL, Hardware-in-the-Loop), 통제된 현장 시험(Field Testing)의 순서로 진행하며, 각 단계에서 동역학, 하드웨어, 환경 및 운용 불확실성의 현실성을 점진적으로 높여야 한다.

소프트웨어 인 더 루프(SIL)는 실제 하드웨어를 위험한 명령에 노출시키지 않고 양산 대상 소프트웨어(Production Software)를 실행할 수 있는 최초의 시스템 수준 시험 환경을 제공한다. 로봇 모델, 센서, 액추에이터(Actuator), 접촉(Contact), 환경, 외란(Disturbance)을 시뮬레이션으로 표현하고, 보행(Locomotion), 균형(Balance), 전신 제어(Whole-Body Control), 조작, 인식 또는 인공지능 정책(AI Policy)을 실제 플랫폼과 유사한 인터페이스를 통해 실행한다. 이를 통해 빠른 반복 시험, 결정론적 디버깅(Deterministic Debugging), 자동 회귀 시험(Automated Regression Testing), 실제 로봇으로 재현하기에는 비용이 높거나 위험한 조건의 탐색이 가능하다.

효과적인 SIL 검증은 시뮬레이션된 휴머노이드가 정상적인 작업을 수행하는지를 관찰하는 수준을 넘어야 한다. 시험에는 추종 오차(Tracking Error), 질량 중심 안정성(Center-of-Mass Stability), 발 위치 정확도(Foot-Placement Accuracy), 접촉 일관성(Contact Consistency), 관절 한계 여유(Joint-Limit Margin), 토크 사용률(Torque Utilization), 충돌 발생 여부(Collision Occurrence), 작업 완료율(Task Completion Rate), 연산 지연시간(Computation Latency)과 같은 정량적인 요구조건을 정의해야 한다. 매개변수 스윕(Parameter Sweep)을 통해 마찰, 탑재 하중, 지형 형상, 센서 잡음, 액추에이터 응답, 통신 지연 및 외란을 체계적으로 변화시키면 시뮬레이션을 단순한 시연 환경에서 정량적 검증 시스템으로 전환할 수 있다.

시뮬레이션 충실도(Simulation Fidelity)는 시험 대상 기능에 따라 단계적으로 증가시켜야 한다. 제어기 개발 초기에는 단순화된 강체 모델(Rigid-Body Model)과 이상적인 상태 피드백(Ideal State Feedback)을 사용할 수 있지만, 이후 시험에서는 실제적인 액추에이터 동역학, 센서 특성, 접촉 불확실성, 타이밍 지터(Timing Jitter), 통신 영향을 도입해야 한다. 목적은 모든 시뮬레이션을 최대한 복잡하게 만드는 것이 아니라 소프트웨어 내부에 포함된 가정(Assumption)을 드러내는 것이다. 이상적인 조건과 열화된 조건(Degraded Condition)의 차이를 분석하면 동일한 취약점이 실제 하드웨어에서 불안정, 낙상, 충돌 또는 작업 실패로 나타나기 전에 민감도를 확인할 수 있다.

하드웨어 인 더 루프(HIL) 검증은 실제 연산 장치, 통신 장치, 센싱 장치, 액추에이터 전자장치 또는 선택된 기계 부품을 도입하면서 주변의 일부 시스템은 통제된 시뮬레이션으로 유지하는 방식이다. 양산용 프로세서(Production Processor)에서 실제 실시간 제어 스택(Real-Time Control Stack)을 실행하고 시뮬레이션된 로봇 동역학과 연결하면 스케줄링 동작, 종단간 지연시간(End-to-End Latency), 이더캣(EtherCAT) 또는 캔(CAN) 통신 타이밍, 패킷 손실(Packet Loss), 워치독(Watchdog) 동작, 명령 동기화(Command Synchronization)를 측정할 수 있다. 특히 약 1 kHz에서 동작하는 고속 제어 루프와 상대적으로 느린 인식 및 인공지능 프로세스가 동시에 존재할 수 있는 휴머노이드 시스템에서 이러한 검증은 중요하다.

SIL에서 HIL로 전환할 때에는 가능한 범위에서 동일한 시험 정의(Test Definition)와 합격 기준(Acceptance Metric)을 유지해야 한다. 시뮬레이션에서 검증된 보행 시나리오를 실제 양산용 컴퓨터와 인터페이스를 통해 다시 실행하면 타이밍, 수치 연산, 미들웨어(Middleware), 드라이버(Driver), 하드웨어 통신으로 인해 발생하는 변화를 정량적으로 측정할 수 있다. 기록된 기준 궤적(Reference Trajectory)은 관절 상태, 추정된 베이스 운동(Base Motion), 접촉력(Contact Force), 명령 토크(Commanded Torque), 제어기 상태(Controller State)의 기준선(Baseline)으로 활용된다. 사전에 정의된 허용 오차를 초과하는 편차는 주관적인 시각적 판단 대신 자동으로 회귀 시험 실패로 판정할 수 있다.

결함 주입(Fault Injection)은 휴머노이드의 신뢰성이 정상 조건을 벗어난 상황에서의 동작에 크게 좌우되기 때문에 SIL과 HIL 전 과정에서 필수적이다. 시험에서는 지연된 센서 패킷, 정지된 측정값(Frozen Measurement), 손상된 좌표 변환(Corrupted Transform), 통신 단절, 액추에이터 포화(Actuator Saturation), 예상하지 못한 접촉, 상태 추정 드리프트(State-Estimation Drift), 추론 시간 초과(Inference Timeout), 제어기 데드라인 미스(Controller Deadline Miss) 등을 인위적으로 발생시킬 수 있다. 시스템은 이러한 이상 상태를 탐지하고 국부적인 결함이 제어되지 않는 전신 운동으로 확산되지 않도록 성능 저하 운전(Degraded Operation), 동작 억제(Motion Inhibition), 안전 자세(Safe Posture), 보호 정지(Protective Stop) 또는 비상 정지(Emergency Shutdown)와 같은 제한된 상태로 전환해야 한다.

현장 시험(Field Testing)은 중요한 요구조건이 낮은 위험도의 검증 단계에서 충분히 통과한 이후에 시작해야 한다. 초기 실제 로봇 시험에서는 제한된 작업 공간, 보수적인 속도 및 토크 제한, 필요한 경우 안전 하네스(Safety Harness), 독립적인 비상 정지(Emergency Stop) 장치, 명확하게 정의된 운영자 책임을 적용해야 한다. 이후 정지 자세와 단순 스텝부터 연속 보행, 외란 복구(Disturbance Recovery), 계단 이동, 객체 조작, 보행-조작 결합(Locomotion-Manipulation), 인간 상호작용까지 복잡성을 점진적으로 높인다. 이러한 과정은 보행, 균형, 전신 제어, 인식, 조작, 비전-언어-행동(VLA, Vision-Language-Action), 인간-로봇 상호작용(HRI, Human-Robot Interaction), 에이전트(Agent) 기능을 개별적으로 개발한 후 통합 검증으로 진행하는 휴머노이드 소프트웨어 스택의 전체 구조와 연결된다.

현장 시험 프로토콜(Field-Test Protocol)은 단순한 작업 성공과 안전하고 강건한 작업 수행을 구분해야 한다. 로봇이 특정 작업을 한 번 성공한 것만으로는 양산 준비도(Production Readiness)에 대한 충분한 근거가 되지 않는다. 반복 시험을 통해 작업 완료 확률, 복구 발생 빈도, 운영자 개입 횟수, 낙상 발생, 최소 안정성 여유(Minimum Stability Margin), 추종 오차, 열 상태(Thermal State), 전력 소비, 연산 부하, 통신 상태, 실행 시간을 측정해야 한다. 또한 환경 조건을 함께 기록하여 실패를 바닥 표면, 조명, 장애물, 탑재 하중, 접촉 구성 또는 인간 행동과 연관하여 분석할 수 있어야 한다.

추적성(Traceability)은 SIL, HIL, 현장 시험의 세 가지 검증 단계를 연결한다. 각각의 소프트웨어 요구사항은 하나 이상의 시험과 연결되어야 하며, 모든 시험은 소프트웨어 버전, 로봇 구성, 모델 버전, 매개변수 집합(Parameter Set), 데이터셋 또는 시나리오, 예상 결과, 측정 결과, 합격·불합격 기준(Pass/Fail Criterion)을 식별할 수 있어야 한다. 시뮬레이션과 실제 실험의 로그(Log)는 가능한 범위에서 호환 가능한 스키마(Schema)와 동기화된 타임스탬프(Timestamp)를 사용해야 한다. 이를 통해 현장에서 발생한 실패를 HIL 또는 SIL에서 재현하고 그 원인이 알고리즘, 타이밍, 모델링 가정, 하드웨어 인터페이스 또는 환경 조건 중 어디에 있는지 분석할 수 있다.

휴머노이드 소프트웨어에 학습 기반 구성요소(Learned Component)가 증가할수록 회귀 시험(Regression Testing)의 중요성도 커진다. 새로운 보행 정책(Locomotion Policy), 인식 모델, 비전-언어-행동 모델(VLA Model) 또는 조작 정책(Manipulation Policy)이 특정 벤치마크에서는 성능을 향상시키면서 다른 영역의 동작을 저하시킬 수 있기 때문이다. 따라서 공통 시나리오 라이브러리(Common Scenario Library)는 정상 작업, 과거 실패 사례, 경계 조건(Boundary Condition), 외란, 안전 중요 사례(Safety-Critical Case)를 포함해야 한다. 중요한 소프트웨어 또는 모델 업데이트가 발생할 때마다 먼저 확장 가능한 SIL에서 이 라이브러리를 재실행하고, 이후 선별된 시험을 HIL에서 수행한 다음 비용이 높은 실제 로봇 시험을 승인하는 방식이 적절하다.

SIL, HIL, 현장 시험의 관계는 서로 독립된 세 가지 활동이 아니라 연속적인 검증 파이프라인(Continuous Validation Pipeline)으로 이해해야 한다. SIL은 시험 범위(Test Coverage)와 반복성(Repeatability)을 극대화하고, HIL은 실제 구현과 하드웨어 인터페이스에서 발생하는 영향을 드러내며, 현장 시험은 실제 물리적 불확실성 아래에서 검증 근거를 확보한다. 후반 단계에서 발견된 실패는 초기 단계에서 재현 가능한 새로운 시험 사례로 다시 생성되어야 한다. 이를 통해 검증 환경은 실제 운용 과정에서 축적된 지식을 지속적으로 흡수하고 로봇의 전체 개발 수명주기(Development Lifecycle)에 걸쳐 현실성을 높이는 피드백 루프(Feedback Loop)를 형성한다.

성숙한 휴머노이드 개발 프로그램은 궁극적으로 위험 기반 시험 게이트(Risk-Based Test Gate)를 사용하여 각 검증 환경 사이의 진행 여부를 통제해야 한다. 안전 중요 기능(Safety-Critical Function)은 편의 기능보다 강한 검증 근거와 넓은 결함 시험 범위를 요구하며, 높은 에너지를 사용하는 동작은 저속 동작보다 엄격한 시험 진입 조건을 적용해야 한다. 따라서 릴리스 결정(Release Decision)은 하나의 벤치마크 점수에 의존하는 것이 아니라 기능 성능, 강건성(Robustness), 타이밍 무결성(Timing Integrity), 결함 대응(Fault Response), 안전 동작, 회귀 시험 상태를 종합하여 판단해야 한다. 이러한 시험 전략은 이후의 보행 SIL, 전신 제어 HIL, 정책 회귀 시험, HRI 시험, 현장 시험, 안전 시험, 환경 스트레스 시험(Environmental Stress Test), 최종 소프트웨어 릴리스 및 인증(Release and Certification) 검증을 위한 기반을 제공한다.

## 11.02. Locomotion Control SIL Simulation Validation [w/Code]

![](images/image2.png){width="7.268055555555556in" height="7.268055555555556in"}

보행 제어 소프트웨어 인 더 루프(SIL, Software-in-the-Loop) 검증은 실제 하드웨어에서 제어 명령을 실행하기 전에 휴머노이드 보행 소프트웨어를 평가하기 위한 통제된 환경을 제공한다. 목적은 상태 추정(State Estimation), 보행 생성(Gait Generation), 발걸음 계획(Footstep Planning), 균형 조절(Balance Regulation), 궤적 생성(Trajectory Generation), 저수준 제어(Low-Level Control)가 폐루프 시스템(Closed-Loop System)으로 올바르게 상호작용하는지를 검증하는 것이다. 이족 보행 로봇(Biped Robot)은 접촉 시점이나 신체 운동의 작은 오차도 빠르게 불안정과 낙상으로 이어질 수 있으므로 시뮬레이션 검증이 특히 중요하다.

효과적인 SIL 아키텍처(SIL Architecture)는 실제 휴머노이드에 적용할 보행 소프트웨어를 그대로 실행하면서 물리적 로봇을 시뮬레이션 동역학 모델(Simulated Dynamic Model)로 대체한다. 시뮬레이터(Simulator)는 부유 기반 운동(Floating-Base Motion), 관절 동역학(Joint Dynamics), 중력(Gravity), 접촉력(Contact Force), 마찰(Friction), 충돌(Collision)을 계산하며, 가상 센서(Virtual Sensor)는 관절, 관성측정장치(IMU), 힘 및 기타 피드백 신호를 생성한다. 제어 명령은 가능한 범위에서 실제 양산 소프트웨어 스택(Production Software Stack)의 구조를 재현한 인터페이스를 통해 시뮬레이션 액추에이터(Simulated Actuator)로 전달된다.

검증은 동적 보행(Dynamic Walking)으로 진행하기 전에 기본적인 동작부터 시작해야 한다. 기립 시험(Standing Test)을 통해 제어기가 진동 증가 없이 목표 질량 중심(Center of Mass), 베이스 자세(Base Orientation), 관절 자세(Joint Posture), 지면 접촉(Ground Contact)을 유지하는지 확인한다. 이후 체중 이동 시험(Weight-Shifting Test)을 통해 질량 중심을 지지 영역 사이에서 이동시키면서 균형 조절과 접촉 전환(Contact Transition)의 오류를 확인한다. 이러한 기본 조건에서 안정성이 확보된 이후에 스텝, 연속 보행, 회전 및 고속 보행을 시험해야 한다.

기준값 추종(Reference Tracking)은 보행 SIL의 주요 측정 항목 중 하나이다. 목표 관절 궤적, 질량 중심 운동, 베이스 자세, 발 궤적(Foot Trajectory), 영 모멘트 점(ZMP, Zero Moment Point), 발산 운동 성분(DCM, Divergent Component of Motion) 또는 기타 제어기 기준값을 시뮬레이션 응답과 비교할 수 있다. 각 시험에서 위치, 속도, 자세, 힘 및 타이밍 오차를 기록해야 한다. 이러한 신호를 분석하면 성능 저하의 원인이 궤적 생성, 피드백 제어(Feedback Control), 동역학 모델링 또는 접촉 실행 중 어디에 있는지 파악할 수 있다.

발 접촉(Foot Contact)은 휴머노이드 보행이 단일 지지(Single Support)와 이중 지지(Double Support) 상태를 반복적으로 전환하기 때문에 특별한 검증이 필요하다. 착지 위치(Touchdown Position), 착지 속도(Touchdown Velocity), 접촉 시점(Contact Timing), 발 자세(Foot Orientation), 수직력(Normal Force), 접선력(Tangential Force), 의도하지 않은 미끄러짐(Slip)을 평가해야 한다. 또한 시뮬레이션에서 발생한 실제 접촉 상태와 제어기가 가정한 접촉 상태를 비교해야 한다. 두 상태의 불일치는 관절 추종이 정상적으로 보이는 경우에도 상태 추정을 손상시키거나 균형 제어를 불안정하게 만들 수 있다.

발걸음 계획 검증(Footstep-Planning Validation)은 명령된 보행 속도와 방향이 물리적으로 실행 가능한 지지 위치(Support Location)로 변환되는지를 평가한다. 시험에는 전진 및 후진 보행, 측면 이동, 회전, 가속, 감속, 정지, 보폭(Step Length) 또는 보행 주기(Cadence)의 변화가 포함되어야 한다. 생성된 발걸음은 운동학적 도달 가능성(Kinematic Reachability), 자기 충돌 제약(Self-Collision Constraint), 지지 형상(Support Geometry), 안정성 요구조건을 만족하면서 이후 단계에서 생성되는 스윙 풋 궤적(Swing-Foot Trajectory) 및 전신 운동과 호환되어야 한다.

외란 시험(Disturbance Testing)은 정상적인 시뮬레이션을 강건성 검증(Robustness Validation)으로 확장한다. 보행 주기(Gait Cycle)의 서로 다른 단계에서 몸통에 여러 방향의 외력을 가할 수 있으며, 동시에 지면 마찰, 지면 높이, 탑재 하중 또는 모델 매개변수를 변화시킬 수 있다. 제어기는 설계 방식에 따라 발목(Ankle), 엉덩이(Hip), 운동량(Momentum) 또는 스텝핑(Stepping) 대응을 이용하여 제한된 범위 안에서 복구할 수 있어야 한다. 복구 시간(Recovery Time), 최대 신체 편차, 추가 스텝 수, 낙상 여부는 외란 허용 능력을 정량적으로 평가하는 지표가 된다.

보행 제어가 이상적인 로봇 모델에 얼마나 의존하는지를 파악하기 위해 매개변수 변화(Parameter Variation) 시험도 필요하다. 링크 질량(Link Mass), 질량 중심 위치, 관절 감쇠(Joint Damping), 액추에이터 응답, 마찰 계수(Friction Coefficient), 센서 잡음, 제어 지연시간(Control Latency)을 정상값 주변에서 변화시킬 수 있다. 정확한 시뮬레이션 매개변수에서만 성공하는 제어기는 실제 하드웨어에서 즉시 실패할 가능성이 있다. 따라서 몬테카를로 시험(Monte Carlo Test)이나 체계적인 매개변수 스윕(Parameter Sweep)은 하나의 결정론적 보행 시나리오를 반복하는 것보다 강건성에 대한 더 강한 검증 근거를 제공한다.

보행 제어기는 완벽하게 알려진 물리 상태가 아니라 추정된 상태에 의존하므로 센서 성능 저하(Sensor Degradation)도 SIL에 포함해야 한다. 관성측정장치 편향(IMU Bias)과 잡음, 엔코더 잡음(Encoder Noise), 힘 센서 오차, 측정 지연, 샘플 손실(Dropped Sample), 일시적인 접촉 오분류(Contact Misclassification)를 가상 센서 스트림(Virtual Sensor Stream)에 주입할 수 있다. 그 결과 나타나는 베이스 자세, 속도, 지지 상태 및 제어기 응답을 분석하면 측정값이 이상적인 시뮬레이션 조건에서 벗어나더라도 상태 추정 및 제어 파이프라인이 안정성을 유지하는지 확인할 수 있다.

타이밍 동작(Timing Behavior) 역시 중요한 검증 요소이다. 휴머노이드에서는 고주기 관절 제어 및 전신 제어가 실행되는 동시에 계획기(Planner), 인식 모듈(Perception Module), 학습 기반 정책(Learned Policy)이 훨씬 낮은 주기로 동작할 수 있다. SIL 시험에서는 이러한 서로 다른 업데이트 주기를 재현하고 현실적인 연산 지연, 통신 지연(Communication Latency), 지터(Jitter)를 도입해야 한다. 수학적으로 안정적인 제어기라도 샘플링 시간(Sampling Time)에 대한 가정이 위반되면 불안정해질 수 있으므로 데드라인 위반(Deadline Violation)과 오래된 명령(Stale Command)을 탐지할 수 있어야 한다.

지형 시나리오(Terrain Scenario)는 평탄한 실험실 바닥을 넘어 검증 범위를 확장한다. 동일한 제어기를 경사면(Slope), 계단(Stairs), 디딤돌(Stepping Stone), 작은 높이 단차, 불규칙한 표면, 서로 다른 마찰 계수를 가진 영역에서 평가할 수 있다. 이러한 환경에서는 계획된 접촉이 실제로 도달 가능한지, 그리고 균형 제어가 기하학적 불확실성(Geometric Uncertainty)을 허용할 수 있는지를 시험한다. 시험 난이도는 점진적으로 높여야 하며, 이를 통해 통제되지 않은 여러 난제의 조합이 아니라 특정 지형 특성과 실패 원인 사이의 관계를 분석할 수 있다.

보행 모드 전환(Locomotion Mode Transition)은 안정적인 개별 동작 사이에서도 실패가 빈번하게 발생할 수 있기 때문에 별도의 시험이 필요하다. 기립에서 보행으로의 전환, 보행에서 정지로의 전환, 전진에서 측면 이동으로의 전환, 속도 변화, 회전, 계단 진입, 외란 이후의 복구 과정에서는 기준값, 접촉 상태 또는 제어기 내부 상태가 변경된다. SIL은 이러한 전환 과정에서 위치, 속도, 토크 및 내부 상태의 연속성(Continuity)을 검증하고 충격성 또는 물리적으로 비현실적인 명령을 발생시킬 수 있는 불연속성(Discontinuity)을 탐지해야 한다.

SIL에서는 실제 로봇이 손상되지 않더라도 안전 제약조건(Safety Constraint)을 항상 활성화해야 한다. 관절 위치 및 속도 제한, 토크 제한, 충돌 제약(Collision Constraint), 발 작업공간 제한(Foot Workspace Restriction), 안정성 경계(Stability Boundary), 제어기 워치독(Controller Watchdog) 조건을 실제 하드웨어 운용과 동일한 방식으로 평가해야 한다. 안전하지 않은 명령이 발생하면 식별 가능한 결함(Fault) 또는 통제된 폴백 동작(Fallback Behavior)이 실행되어야 한다. 시뮬레이션을 아무런 제한이 없는 실험 환경으로 사용하면 이후 HIL 또는 현장 시험에서 중요해질 수 있는 안전 로직의 결함을 발견하지 못할 수 있다.

자동화된 회귀 시험(Automated Regression Testing)은 개별 SIL 실험을 지속 가능한 검증 시스템으로 전환한다. 대표적인 보행 시나리오와 과거 실패 사례는 보행 소프트웨어, 로봇 모델, 제어기 매개변수 또는 의존성(Dependency)이 변경될 때마다 자동으로 실행할 수 있다. 각 시험에서는 보행 성공 여부, 낙상 발생, 추종 오차, 발 위치 오차, 미끄러짐, 안정성 여유(Stability Margin), 제어 포화(Control Saturation), 실행 시간 등의 지표를 자동으로 계산하고 저장된 합격 기준(Acceptance Threshold) 및 기준 결과(Reference Result)와 비교해야 한다.

재현성(Reproducibility)을 확보하려면 모든 시뮬레이션 결과를 정확한 시험 구성과 연결해야 한다. 소프트웨어 커밋(Software Commit), 로봇 모델 버전, 제어기 매개변수, 시뮬레이터 설정, 지형 정의, 난수 시드(Random Seed), 초기 상태, 외란 프로파일(Disturbance Profile), 합격 기준을 시험 결과와 함께 기록해야 한다. 시간 동기화된 로그(Time-Synchronized Log)는 기준 상태와 측정 상태, 접촉 정보, 액추에이터 명령, 추정기 출력(Estimator Output), 제어기 모드(Controller Mode)를 보존해야 하며, 이를 통해 시각적인 관찰에만 의존하지 않고 실패한 시험을 다시 재생하고 분석할 수 있다.

보행 SIL의 최종 목적은 시뮬레이션과 현실이 동일하다는 것을 증명하는 것이 아니라 실제 로봇 시험 이전에 예방 가능한 소프트웨어 결함을 제거하는 것이다. 안정성, 추종 성능, 접촉, 타이밍, 강건성, 안전 요구조건을 지속적으로 만족하는 시나리오는 하드웨어 인 더 루프(HIL) 및 통제된 실제 로봇 시험 단계로 진행할 수 있다. 이후 실제 휴머노이드에서 발견된 실패는 다시 새로운 SIL 시나리오로 재구성되어야 하며, 이를 통해 시뮬레이션 검증 시험군(Simulation Validation Suite)이 축적되는 현장 경험과 함께 지속적으로 발전하도록 해야 한다.

## 11.03. WBC HIL Test with Physical Robot [w/Code]

![](images/image3.png){width="7.268055555555556in" height="7.268055555555556in"}

전신 제어(WBC, Whole-Body Control) 하드웨어 인 더 루프(HIL, Hardware-in-the-Loop) 시험은 시뮬레이션 검증과 제약이 없는 실제 휴머노이드 운용 사이의 중요한 전환 단계를 제공한다. 목적은 실제 연산 하드웨어, 통신 인터페이스, 센서, 액추에이터(Actuator), 선택된 로봇 기구를 사용하여 양산용 WBC 소프트웨어를 실행하면서 통제된 안전 조건을 유지하는 것이다. 이를 통해 이상적인 시뮬레이션으로 완전히 표현하기 어려운 타이밍 변화, 액추에이터 동역학, 구조적 유연성(Structural Compliance), 마찰, 통신 지연, 센서 불완전성과 같은 실제 구현의 영향을 확인할 수 있다.

실용적인 WBC HIL 환경은 최종 로봇에 적용될 소프트웨어 아키텍처(Software Architecture)를 그대로 유지해야 한다. 실시간 제어기(Real-Time Controller)는 측정된 관절 상태, 관성 정보, 힘 또는 토크 측정값, 접촉 추정값(Contact Estimate)을 입력받아 목표 관절 토크, 위치 또는 임피던스 명령(Impedance Command)을 계산한다. 시험 성숙도에 따라 이러한 명령은 시뮬레이션 동역학, 구속된 액추에이터, 선택된 팔다리 또는 전체 실제 휴머노이드를 구동할 수 있다. 양산용 인터페이스를 유지하면 별도의 시험 전용 소프트웨어가 시스템 통합 결함을 가리는 위험을 줄일 수 있다.

초기 HIL 시험에서는 로봇을 기계적으로 고정하거나 제어기 오류가 낙상으로 이어지지 않는 조건에서 운용해야 한다. WBC 명령을 활성화하기 전에 관절 토크 제한, 속도 제한, 작업공간 제약(Workspace Constraint), 비상 정지 회로(Emergency-Stop Circuit), 워치독(Watchdog), 통신 시간 초과 보호(Communication Timeout Protection)가 활성화되어 있어야 한다. 보수적인 제어 게인(Control Gain)과 제한된 운동 범위를 사용하면 동적인 전신 운동을 도입하기 전에 명령 방향, 관절 매핑(Joint Mapping), 센서 극성(Sensor Polarity), 좌표계(Coordinate Frame), 액추에이터 응답을 검증할 수 있다.

타이밍 검증(Timing Validation)은 WBC가 일반적으로 고주기의 결정론적 실행(Deterministic Execution)에 의존하기 때문에 기본적으로 중요하다. 명목상 1 kHz로 동작하는 제어기는 상태 획득, 모델 갱신, 최적화, 명령 생성, 출력 전송을 수행하는 데 약 1밀리초의 시간을 사용할 수 있다. 따라서 HIL 시험에서는 평균 및 최악 조건의 연산 시간, 데드라인 미스(Deadline Miss), 스케줄링 지터(Scheduling Jitter), 통신 지연시간, 센서 샘플링과 액추에이터 명령 사이의 동기화를 측정해야 한다. 드물게 발생하는 타이밍 위반은 예측할 수 없는 제어 동작을 발생시키므로 중간 수준의 평균 지연보다 더 위험할 수 있다.

WBC에서 사용하는 로봇 동역학 모델(Robot Dynamics Model) 역시 실제 측정값을 기준으로 검증해야 한다. 관절 위치와 속도는 로봇 구성(Configuration)을 정의하며, 관성측정장치(IMU)와 접촉 센서는 부유 기반 운동(Floating-Base Motion)과 지지 상태에 관한 정보를 제공한다. 질량 중심(Center of Mass), 자코비안(Jacobian), 질량 행렬(Mass Matrix), 중력 보상(Gravity Compensation), 운동량(Momentum), 접촉 좌표 변환(Contact Transformation)과 같이 모델에서 계산되는 값은 실제 측정 동작과 일관성을 유지해야 한다. 잘못된 링크 매개변수 또는 좌표계 정의는 최적화 알고리즘 자체가 수학적으로 정확하더라도 체계적인 토크 오차를 발생시킬 수 있다.

정적 자세 조절(Static Posture Regulation)은 첫 번째 실제 WBC 시험으로 적합하다. 제어기는 사전에 정의된 기립 자세를 유지하면서 질량 중심 위치, 몸통 자세(Torso Orientation), 관절 자세, 접촉력을 조절한다. 엔지니어는 목표값과 측정값을 비교하면서 추가 관절에 대한 토크 제어 또는 임피던스 제어(Impedance Control)를 점진적으로 활성화할 수 있다. 지속적인 진동, 비대칭 하중(Asymmetric Loading), 예상하지 못한 관절 운동 또는 액추에이터 포화(Actuator Saturation)는 체중 이동이나 동적 접촉 전환 시험으로 진행하기 전에 해결해야 한다.

접촉력 검증(Contact-Force Validation)은 휴머노이드 WBC가 마찰 및 단방향 접촉 제약(Unilateral-Contact Constraint)을 만족하면서 여러 접촉점에 힘을 분배하기 때문에 특히 중요하다. 이중 지지(Double Support) 기립 상태에서 측정된 왼발과 오른발의 힘을 최적화된 접촉 렌치(Contact Wrench) 및 예상 하중 분배와 비교할 수 있다. 이후 통제된 질량 중심 이동을 통해 최적화기가 지지 영역 사이에서 하중을 부드럽게 전달하는지 확인할 수 있다. 과도한 접선력(Tangential Force), 음의 수직력 명령(Negative Normal-Force Command), 급격한 렌치 변화는 스텝 시험 전에 반드시 수정해야 하는 문제를 의미한다.

계층적 작업 동작(Hierarchical Task Behavior)은 여러 목표 사이에 통제된 충돌을 발생시키는 방식으로 시험해야 한다. 균형과 접촉 유지는 일반적으로 손 추종(Hand Tracking), 자세 선호(Posture Preference), 보조 조작 목표보다 높은 우선순위를 가진다. 실제 로봇에 안정적인 기립 상태를 유지하면서 한쪽 팔로 특정 위치에 도달하도록 명령하여 낮은 우선순위 작업이 지지 제약을 위반하지 않는지 검증할 수 있다. 목표 말단장치(End-Effector) 위치에 도달할 수 없는 경우에도 관절 한계, 자기 충돌 회피(Self-Collision Avoidance), 토크 제한, 접촉 조건은 계속 만족되어야 한다.

토크 제어 검증(Torque-Control Validation)은 명령된 토크와 실제 액추에이터에서 구현되는 동작을 비교해야 한다. 모터 전류, 추정 관절 토크, 측정된 힘, 관절 가속도, 온도, 추종 응답을 분석하면 명목 모델에 존재하지 않는 마찰, 백래시(Backlash), 유연성, 포화, 대역폭 제한(Bandwidth Limitation)을 확인할 수 있다. 이러한 차이를 단순히 제어 게인을 증가시키는 방식으로 보상해서는 안 된다. 대신 특성을 정량화하여 WBC에서 사용하는 액추에이터 모델, 제한 조건, 피드포워드 항(Feedforward Term) 또는 강건 제어 여유(Robust Control Margin)에 반영해야 한다.

외란 시험(Disturbance Experiment)은 작은 수동 외란부터 반복 가능한 기계적 외란까지 점진적으로 확대해야 한다. 몸통이나 팔다리에 힘을 가하면 질량 중심 조절, 운동량 제어(Momentum Control), 접촉력 재분배(Contact-Force Redistribution), 자세 복구(Posture Recovery)를 평가할 수 있다. 가능한 경우 각 외란의 크기와 방향을 측정해야 한다. 복구 시간, 최대 자세 오차, 질량 중심 변위, 토크 사용률, 안정성 여유(Stability Margin)를 사용하면 단순히 로봇이 서 있는지를 관찰하는 것보다 정량적인 검증 근거를 확보할 수 있다.

통신 및 센서 결함도 안전한 조건에서 의도적으로 발생시켜야 한다. HIL 시험에서는 관절 상태 패킷을 지연시키거나 힘 측정을 중단하고, 관성측정장치 편향(IMU Bias)을 주입하거나 센서 값을 고정하며, 의도적으로 제어기 데드라인을 초과하도록 만들 수 있다. WBC는 유효하지 않은 상태 정보를 사용하면서 무기한 동작해서는 안 된다. 결함 탐지(Fault Detection)는 이상 상태의 심각도와 지속 시간에 따라 안전 자세 유지, 토크 감소, 제어 모드 전환, 동작 비활성화 또는 보호 정지(Protective Stop) 요청과 같은 사전에 정의된 대응을 실행해야 한다.

시스템에 대한 신뢰도가 높아지면 시험을 고정된 이중 지지 상태에서 체중 이동, 뒤꿈치 또는 발끝 하중 제거, 단일 지지(Single Support) 준비, 통제된 스텝으로 확장할 수 있다. 각 전환에서는 활성 접촉 집합(Active Contact Set)이 변경되며 이에 따라 WBC가 해결해야 하는 제약조건도 달라진다. 접촉 활성화와 비활성화는 명목상의 타이밍에만 의존하지 않고 실제로 측정된 물리적 접촉과 동기화되어야 한다. 잘못된 접촉 전환은 불연속적인 힘이나 토크를 발생시킬 수 있으며 실제 전신 제어에서 가장 중요한 실패 메커니즘 중 하나이다.

이후 조작(Manipulation)을 균형 조절과 결합하여 실제적인 전신 협응(Whole-Body Coordination)을 평가할 수 있다. 팔 뻗기, 밀기, 물체 운반 또는 양손 작업(Bimanual Task)은 로봇의 질량 분포를 변화시키고 반력을 발생시키므로 다리와 몸통을 통해 이를 보상해야 한다. 서로 다른 탑재 하중(Payload)과 말단장치 힘(End-Effector Force)을 적용하면서 지지 안정성을 모니터링하여 성능을 비교해야 한다. 이 단계에서는 WBC가 보행과 관련된 제약조건을 유지하면서 사용 가능한 운동과 토크를 조작 목표에 적절하게 할당할 수 있는지를 평가한다.

자동화된 데이터 수집(Automated Data Collection)은 모든 실제 HIL 실험과 함께 수행되어야 한다. 시간 동기화된 기록에는 목표 및 측정 관절 상태, 토크 명령, 액추에이터 피드백, 베이스 자세, IMU 측정값, 접촉 상태, 발 렌치(Foot Wrench), 작업 오차(Task Error), 최적화 상태, 제약조건 여유(Constraint Margin), 연산 시간, 안전 이벤트(Safety Event)가 포함되어야 한다. 이러한 기록을 사용하면 SIL 결과와 직접 비교할 수 있으며, 영상이나 운영자의 주관적인 판단에만 의존하지 않고 예상하지 못한 실제 동작을 시뮬레이션에서 재현할 수 있다.

합격 기준(Acceptance Criteria)은 로봇의 동작을 관찰한 이후에 조정하는 것이 아니라 각 시험을 시작하기 전에 정의해야 한다. 관련 기준에는 작업 공간 추종 오차(Task-Space Tracking Error), 자세 오차, 접촉력 오차, 토크 한계 사용률, 제어 루프 데드라인 준수 여부, 최적화 수렴(Optimization Convergence), 안정성 여유, 안전 위반 부재 등이 포함될 수 있다. 한 번의 성공적인 실행만으로 강건성을 입증할 수 없기 때문에 반복 시험이 필요하다. 시험 간 변동성(Variability) 자체도 물리적 불확실성과 초기화 조건에 대한 시스템의 민감도를 보여주는 중요한 검증 근거가 된다.

WBC HIL 시험의 최종 목표는 제어기가 실제 휴머노이드 하드웨어와 연결된 상태에서도 안정성, 연산 신뢰성(Computational Reliability), 제약조건 준수(Constraint Compliance), 복구 가능성(Recoverability)을 유지한다는 것을 입증하는 것이다. 성공적인 HIL 결과는 제약을 점진적으로 줄인 현장 시험(Field Experiment)으로 진행할 수 있는 근거가 되며, 발견된 모든 실패는 재현 가능한 SIL 또는 HIL 회귀 시험 사례(Regression Case)로 전환되어야 한다. 이러한 폐루프 검증 과정(Closed Validation Loop)을 통해 실제 로봇에서 얻은 경험이 지속적으로 시험군을 강화하고, 이미 확인된 전신 제어의 취약점이 이후 소프트웨어 릴리스에서 다시 발생할 가능성을 줄일 수 있다.

## 11.04. Manipulation Policy SIL RLBench Gazebo [w/Code]

![](images/image4.png){width="7.268055555555556in" height="7.268055555555556in"}

조작 정책 소프트웨어 인 더 루프(SIL, Software-in-the-Loop) 검증은 정책을 실제 팔, 손, 전신 제어기(Whole-Body Controller)에 연결하기 전에 반복 가능한 가상 환경에서 휴머노이드 조작 소프트웨어를 평가한다. 이 장의 전체 검증 구조에서 조작 시험은 학습 기반 또는 계획 기반 객체 상호작용에 초점을 맞추어 보행 SIL과 전신 제어 하드웨어 인 더 루프(WBC HIL)를 보완한다. 이를 위해 알엘벤치(RLBench)와 가제보(Gazebo)는 각각 작업 중심 및 로봇 시스템 중심의 시뮬레이션 환경을 제공할 수 있다.

조작 SIL 환경은 목표 정책(Target Policy)에서 사용하는 관측-행동 루프(Observation-Action Loop)를 재현해야 한다. 시뮬레이션 카메라, 관절 엔코더(Joint Encoder), 힘 관련 신호, 객체 상태, 로봇 상태가 관측값을 제공하고 정책은 팔, 손목, 손, 그리퍼(Gripper) 또는 작업 공간 명령(Task-Space Command)을 생성한다. 이후 시뮬레이터는 로봇 및 객체 동역학을 갱신하고 다음 관측값을 반환한다. 이 루프를 실제 양산 아키텍처(Production Architecture)에 가깝게 유지하면 하드웨어 배포 전에 인터페이스, 좌표계, 타이밍, 명령 해석과 관련된 오류를 발견할 수 있다.

알엘벤치(RLBench)는 작업을 시연 데이터(Demonstration), 객체 구성(Object Configuration), 작업 성공 조건(Task Success Condition), 반복 가능한 변형으로 표현할 수 있기 때문에 구조화된 조작 평가에 특히 유용하다. 객체 자세와 초기 로봇 조건을 변화시키면서 여러 에피소드(Episode)에 걸쳐 정책을 평가할 수 있다. 시각적으로 성공한 단일 궤적만 평가하는 대신 통제된 변화 조건에서 정책이 의도한 조작 작업을 반복적으로 완료하는지, 그리고 특정 작업 상태에서 실패가 집중되는지를 측정할 수 있다.

가제보(Gazebo)는 조작 소프트웨어를 로봇 모델, 센서, 제어기, 로봇 운영체제 2(ROS 2) 인터페이스와 통합함으로써 작업 수준 평가(Task-Level Evaluation)를 보완할 수 있다. 따라서 학습 모델을 독립적으로 평가하는 것보다 더 큰 휴머노이드 소프트웨어 스택에 조작 정책이 포함되었을 때 어떻게 동작하는지를 평가하는 데 유용하다. 관절 제어기, 시뮬레이션 카메라, 충돌 형상(Collision Geometry), 좌표 변환(Transform), 통신 토픽(Communication Topic) 및 기타 소프트웨어 인터페이스를 함께 실행하여 정책 단독 평가에서는 나타나지 않는 통합 결함을 확인할 수 있다.

검증은 접촉이 많은 조작(Contact-Rich Manipulation)으로 진행하기 전에 단순한 도달 동작(Reaching)과 파지 전 위치 결정(Pre-Grasp Positioning)부터 시작해야 한다. 초기 시험에서는 관절 한계를 준수하고 자기 충돌(Self-Collision)을 회피하면서 말단장치(End Effector)가 허용 가능한 위치 및 자세 오차 범위에서 목표 자세에 도달하는지를 측정할 수 있다. 이후 시험 대상 정책에 따라 파지, 들어 올리기, 배치, 밀기, 따르기(Pouring), 도구 사용 또는 양손 동작(Bimanual Behavior)과 같은 객체 상호작용을 추가할 수 있다. 복잡성을 점진적으로 높이면 개별 실패 메커니즘을 더욱 쉽게 분리할 수 있다.

실제 휴머노이드에서 조작 정책은 완벽한 객체 정보를 제공받는 경우가 거의 없으므로 인식 오차(Perception Error)를 반드시 고려해야 한다. 객체 자세, 조명, 시점(Viewpoint), 부분 가림(Partial Occlusion), 배경 구성, 센서 잡음을 변경하여 가상 카메라 관측값을 변화시킬 수 있다. 정책이 추정된 객체 자세 또는 시각 특징(Visual Feature)에 의존하는 경우 관측 불확실성이 도달 및 파지 동작으로 어떻게 전파되는지를 측정해야 한다. 시뮬레이터의 정답 상태(Ground-Truth State)를 사용할 때만 성공하는 정책은 실제 로봇 배포를 위한 충분한 검증 근거가 될 수 없다.

파지 검증(Grasp Validation)은 단순히 객체가 최종적으로 들어 올려졌는지만 평가해서는 안 된다. 접근 방향(Approach Direction), 파지 전 자세, 손가락 또는 그리퍼 폐쇄, 접촉 형성(Contact Formation), 객체 변위, 파지 안정성(Grasp Stability), 파지 이후 궤적(Post-Grasp Trajectory)은 모두 신뢰성에 영향을 미친다. 실패한 시도는 인식 오류, 도달 불가능한 목표, 충돌, 조기 접촉(Premature Contact), 부적절한 파지 형상, 객체 미끄러짐(Object Slip), 불안정한 들어 올리기와 같이 동작 과정의 어느 단계에서 문제가 발생했는지에 따라 분류해야 한다. 이러한 분해는 정책 개선을 더욱 체계적으로 수행할 수 있게 한다.

객체 및 환경 변화(Object and Environment Variation)는 정책의 일반화 성능(Policy Generalization)을 측정하는 실용적인 방법을 제공한다. 위치, 자세, 크기, 질량, 마찰, 초기 배치를 정의된 범위 안에서 변화시키고 목표 객체 주변에 방해 객체(Distractor Object)나 장애물을 추가할 수 있다. 각 조건에서는 동일한 작업 정의와 성공 기준을 유지해야 한다. 이러한 변화에 따른 성능 저하는 단순한 명목 성공률(Nominal Success Rate)이 아니라 조작 정책을 신뢰할 수 있는 실제 운용 범위(Operational Envelope)를 보여준다.

휴머노이드에서는 조작을 항상 전신 동작과 분리할 수 있는 것은 아니다. 팔의 편안한 작업공간을 넘어선 위치에 도달하려면 몸통 운동, 자세 조정 또는 스텝이 필요할 수 있으며, 객체를 운반하면 질량 분포와 균형 요구조건도 달라진다. 따라서 SIL 시험에는 조작 명령이 자세 또는 전신 제어(WBC) 제약조건과 상호작용하는 사례가 포함되어야 한다. 조작 목표로 인해 관절 한계 위반, 자기 충돌, 불안정한 지지 또는 물리적으로 실행 불가능한 신체 자세가 발생해서는 안 된다.

양손 조작(Bimanual Manipulation)은 추가적인 협응 요구조건(Coordination Requirement)을 가진다. 두 손은 상대 자세(Relative Pose)를 유지하거나 객체 하중을 분담하고, 접촉 시점을 동기화하거나 서로 다른 보완적 역할을 수행해야 할 수 있다. SIL 검증에서는 한쪽 팔이 다른 팔의 동작을 방해하는지, 명령된 궤적이 자기 충돌을 발생시키는지, 접촉력 또는 파지 위치가 변할 때에도 객체가 안정적으로 유지되는지를 평가할 수 있다. 운반, 열기, 한 손으로 고정하면서 다른 손으로 조작하기, 협응 배치(Coordinated Placement)와 같은 작업은 동기화 취약점을 발견하는 데 유용하다.

정책 강건성(Policy Robustness)은 외란 및 실행 불확실성(Execution Uncertainty)을 통해서도 시험해야 한다. 지연된 관측값, 행동 지연(Action Latency), 잡음이 포함된 관절 상태, 부정확한 객체 위치 추정, 변화된 마찰, 예상하지 못한 객체 이동 또는 파지 폐쇄 실패를 의도적으로 발생시킬 수 있다. 중요한 것은 실제 환경이 정책의 예상 상태에서 벗어난 이후에도 정책이 맹목적으로 동작을 계속하는지, 아니면 불일치를 감지하여 복구, 재시도, 재계획(Replanning) 또는 안전 종료를 수행하는지를 확인하는 것이다. 복구 동작(Recovery Behavior)은 실용적인 조작 신뢰성의 핵심 요소이다.

학습 기반 조작 정책(Learned Manipulation Policy)은 동일한 작업 범주에서도 에피소드마다 서로 다른 결과가 나타날 수 있기 때문에 통계적 평가(Statistical Evaluation)가 필요하다. 따라서 성공률은 시험 횟수와 시험 다양성을 함께 고려하여 보고해야 한다. 추가적인 지표에는 완료 시간, 궤적 길이, 파지 시도 횟수, 충돌 횟수, 최종 객체 자세 오차, 제어 포화(Control Saturation), 개입 필요 여부, 복구 빈도가 포함될 수 있다. 이러한 지표는 비슷한 성공률을 보이더라도 불안정하거나 불필요하게 많은 행동을 사용하는 정책과 일관되고 효율적인 정책을 구분할 수 있게 한다.

시뮬레이션-현실 민감도(Simulation-to-Real Sensitivity)는 실제 로봇에서 불확실한 매개변수를 변화시키는 방식으로 조사해야 한다. 관절 감쇠(Joint Damping), 액추에이터 응답, 접촉 마찰, 객체 질량, 카메라 보정(Camera Calibration), 제어 지연, 파지 유연성(Grasp Compliance)을 명목 설정 주변에서 변화시킬 수 있다. 작은 매개변수 변화가 큰 성능 저하를 발생시킨다면 정책이 시뮬레이터에 특화된 동작을 이용하고 있을 가능성이 있다. 도메인 무작위화(Domain Randomization) 또는 목표 지향적 매개변수 스윕(Targeted Parameter Sweep)을 통해 이러한 의존성이 실제 로봇 배포 실패로 이어지기 전에 발견할 수 있다.

실제 하드웨어가 손상될 가능성이 없는 조작 SIL에서도 안전 로직(Safety Logic)은 항상 활성화되어 있어야 한다. 관절 위치, 속도 및 토크 제한, 자기 충돌 제약, 작업공간 경계(Workspace Boundary), 금지된 접촉 영역(Prohibited Contact Region), 작업 수준 정지 조건(Task-Level Stop Condition)을 정책 실행과 함께 평가해야 한다. 안전하지 않은 행동은 거부되거나 통제된 폴백 동작(Fallback Behavior)으로 변환되어야 한다. 이를 통해 안전성이 학습 정책이 올바르게 동작할 것이라는 암묵적 가정에 의존하는 것이 아니라 시스템 아키텍처 자체에 의해 강제되는지를 검증할 수 있다.

자동화된 회귀 시험(Automated Regression Testing)을 적용하면 RLBench와 Gazebo 시나리오를 소프트웨어 릴리스 파이프라인(Software Release Pipeline)의 일부로 활용할 수 있다. 대표적인 작업, 경계 사례(Boundary Case), 과거에 관찰된 실패 사례를 고정된 시험 집합으로 구성하고 정책, 인식 모델, 제어기, 로봇 기술 모델(Robot Description) 또는 미들웨어 구성요소가 변경될 때마다 반복 실행할 수 있다. 결과는 저장된 기준선(Baseline)과 비교하여 특정 작업의 성능 향상이 다른 조작 기능의 성능 저하를 가리지 않도록 해야 한다.

재현성(Reproducibility)을 확보하려면 각 에피소드에서 정책 버전, 시뮬레이터 구성, 로봇 모델, 작업 정의, 객체 매개변수, 난수 시드(Random Seed), 관측 구성, 제어기 설정, 성공 기준을 기록해야 한다. 시간 동기화된 로그(Time-Synchronized Log)는 관측값, 정책 출력, 관절 상태, 말단장치 운동, 접촉, 충돌, 작업 이벤트(Task Event)를 보존해야 한다. 이를 통해 실패한 에피소드를 정확하게 재실행하고 오프라인에서 분석하며, 일회성 시뮬레이션 이상 현상으로 처리하는 대신 영구적인 회귀 시험 사례로 전환할 수 있다.

조작 정책 SIL의 최종 목적은 하드웨어 인 더 루프(HIL) 및 통제된 실제 로봇 조작 시험으로 진행하기 위한 충분한 검증 근거를 확보하는 것이다. RLBench는 반복 가능한 작업 중심 평가를 제공할 수 있으며, Gazebo는 더 넓은 휴머노이드 소프트웨어 아키텍처와의 통합을 시험할 수 있다. 통제된 변화 조건에서 일관된 작업 완료, 강건성, 제약조건 준수, 복구 동작 및 허용 가능한 성능을 입증한 정책은 실제 로봇 시험을 위한 더욱 신뢰할 수 있는 후보가 되며, 이후 실제 환경에서 시뮬레이션의 가정을 점진적으로 검증할 수 있다.

## 11.05. VLA Policy Regression Test Automation [w/Code]

![](images/image5.png){width="7.268055555555556in" height="7.268055555555556in"}

비전-언어-행동(VLA, Vision-Language-Action) 정책 회귀 시험(Policy Regression Testing)은 모델, 데이터셋, 프롬프트(Prompt), 행동 표현(Action Representation), 인식 모듈, 추론 엔진(Inference Engine) 또는 제어 인터페이스가 변경된 이후에도 휴머노이드 범용 정책(Generalist Policy)이 이전에 검증된 기능을 지속적으로 수행하는지를 확인한다. 기존의 결정론적 소프트웨어(Deterministic Software)와 달리 VLA 정책은 한 작업에서 성능이 향상되는 동시에 다른 작업에서는 성능이 조용히 저하될 수 있다. 따라서 자동화된 회귀 시험(Automated Regression Testing)은 새로운 정책을 실제 휴머노이드에 배포하기 전에 이러한 행동 변화를 탐지하기 위한 반복 가능한 검증 수단을 제공한다.

회귀 시험 프레임워크(Regression Framework)는 VLA 시스템에서 요구되는 운용 기능을 대표하는 통제된 작업 라이브러리(Task Library)를 유지해야 한다. 시나리오에는 언어 조건 기반 도달(Language-Conditioned Reaching), 객체 선택, 픽앤플레이스(Pick-and-Place), 도구 상호작용, 양손 조작(Bimanual Manipulation), 내비게이션 보조 조작(Navigation-Assisted Manipulation), 다단계 작업 실행(Multi-Step Task Execution) 등이 포함될 수 있다. 각 시나리오는 초기 조건, 관측값, 언어 명령, 성공 조건, 안전 제약조건, 평가 지표를 정의하여 서로 다른 정책 버전을 동일한 조건에서 비교할 수 있도록 해야 한다.

시험 범위(Test Coverage)는 정상적인 명령만을 포함해서는 안 된다. 언어 명령은 의도한 작업의 의미를 유지하면서 다양한 표현으로 변경하여 정책이 언어적 변화(Linguistic Variation)에 대해 강건성을 유지하는지를 평가해야 한다. 객체 위치, 카메라 시점, 조명, 방해 객체(Distractor Object), 로봇 자세, 환경 배치 역시 통제된 범위에서 변경할 수 있다. 이러한 변화는 실제 작업 이해(Task Understanding)와 특정 시각 관측 및 명령 표현의 좁은 조합에 의존하는 동작을 구분하는 데 도움이 된다.

기준 정책(Baseline Policy)은 후보 정책 버전을 평가하기 위한 참조 기준을 제공한다. 기준 정책은 정확한 모델 체크포인트(Model Checkpoint), 소프트웨어 커밋(Software Commit), 추론 구성, 전처리 파이프라인(Preprocessing Pipeline), 행동 디코더(Action Decoder), 로봇 모델, 시나리오 집합과 연결되어야 한다. 후보 정책이 도입되면 가능한 경우 두 버전에서 동일한 회귀 시험군(Regression Suite)을 실행해야 한다. 이를 통해 성공률, 안전성, 효율성 및 행동의 차이를 평가 대상 정책이나 소프트웨어 변경과 보다 신뢰성 있게 연관시킬 수 있다.

작업 성공률(Task Success Rate)은 중요한 지표이지만 VLA 정책의 전체 품질을 설명할 수는 없다. 회귀 평가에서는 완료 시간, 행동 횟수, 궤적 효율성(Trajectory Efficiency), 파지 시도 횟수, 충돌, 제약조건 위반, 복구 이벤트(Recovery Event), 불필요한 움직임, 운영자 개입도 함께 측정해야 한다. 언어 조건 기반 작업에서는 의도하지 않은 행동을 통해 우연히 허용 가능한 최종 물리 상태에 도달했는지만 평가하는 것이 아니라 실행된 행동이 언어 명령의 의미와 일치하는지도 추가로 확인해야 한다.

행동 공간 회귀(Action-Space Regression)는 휴머노이드 VLA 정책이 여러 신체 구성요소를 동시에 제어할 수 있기 때문에 특별한 주의가 필요하다. 정책은 관절 공간(Joint Space), 작업 공간(Task Space) 또는 토큰화된 표현(Tokenized Representation)을 통해 팔, 손, 몸통, 다리 또는 전신 행동을 생성할 수 있다. 행동 정규화(Action Normalization), 스케일링(Scaling), 토큰 디코딩(Token Decoding), 제어 주기 또는 좌표계 규칙의 변경은 작은 소프트웨어 수정에도 큰 물리적 차이를 발생시킬 수 있다. 따라서 자동화 시험에서는 작업 결과뿐만 아니라 생성된 행동의 수치적 유효성(Numerical Validity)도 검증해야 한다.

안전 회귀 시험(Safety Regression)은 작업 성공 평가와 독립적으로 수행되어야 한다. 더 많은 작업을 완료하더라도 중간 과정에서 안전하지 않은 동작을 생성하는 정책을 자동으로 개선된 정책이라고 판단해서는 안 된다. 시험에서는 관절 한계, 속도 및 토크 제약, 자기 충돌(Self-Collision), 금지 작업공간 진입(Prohibited Workspace Entry), 불안정한 자세, 과도한 접촉력, 안전 오버라이드(Safety Override) 작동을 모니터링해야 한다. 안전 이벤트는 명시적으로 기록되어야 하며, 요청된 작업이 최종적으로 완료되더라도 심각한 안전 이벤트는 즉각적인 시험 실패로 설정할 수 있다.

확률적 정책(Stochastic Policy)은 한 번의 실행만으로 행동 신뢰성을 평가할 수 없으므로 반복 시험이 필요하다. 각 시나리오는 필요에 따라 여러 난수 시드(Random Seed), 환경 변화, 초기 구성에서 반복 실행되어야 한다. 회귀 판정은 개별적인 값이 아니라 성공률, 작업 지속 시간, 행동 횟수, 안전 이벤트의 분포를 이용할 수 있다. 신뢰 구간(Confidence Interval)이나 사전에 정의된 통계적 허용 오차(Statistical Tolerance)를 적용하면 확률적 실행에서 발생하는 정상적인 변동과 의미 있는 성능 저하를 구분하는 데 도움이 된다.

실패 분류(Failure Classification)는 자동화된 회귀 시험 결과를 엔지니어링에 더욱 유용하게 만든다. 작업 실패는 시각적 그라운딩(Visual Grounding), 언어 해석(Language Interpretation), 객체 위치 추정, 계획, 행동 생성, 파지 실행, 전신 협응(Whole-Body Coordination), 복구 로직 또는 제어 통합에서 발생할 수 있다. 따라서 로그(Log)는 최종 성공 또는 실패만 기록하는 것이 아니라 중간 관측값과 정책 출력도 보존해야 한다. 실패를 단계별로 분류하면 회귀의 원인이 VLA 모델 자체에 있는지 또는 휴머노이드 소프트웨어 스택의 다른 구성요소에 있는지를 판단할 수 있다.

과거 실패(Historical Failure)는 신뢰성 있게 재현할 수 있다면 회귀 시험군의 영구적인 구성요소로 포함해야 한다. 이전 정책이 잘못된 객체를 선택하거나 도달 과정에서 충돌하거나 가림(Occlusion) 이후 실패하거나 파지가 실패했음에도 동작을 계속한 경우, 결함을 수정한 이후에도 해당 시나리오를 유지해야 한다. 이러한 방식은 실제 운용 경험을 누적되는 검증 지식(Cumulative Validation Knowledge)으로 전환하고 이후 모델 업데이트에서 이미 허용할 수 없는 것으로 확인된 행동이 다시 발생하는 것을 방지한다.

자동화된 회귀 시험은 모델 및 소프트웨어 개발 파이프라인(Development Pipeline)에 통합할 수 있다. 정책 코드, 전처리, 행동 디코딩 또는 구성 파일이 변경될 때마다 경량 시험(Lightweight Test)을 실행할 수 있으며, 후보 모델 체크포인트나 릴리스 빌드(Release Build)에 대해서는 더 큰 규모의 시뮬레이션 시험군을 실행할 수 있다. 고충실도 물리 시뮬레이션(High-Fidelity Physics)이나 긴 다단계 작업을 포함하는 비용이 높은 시나리오는 상대적으로 낮은 빈도로 실행할 수 있다. 이러한 계층형 전략(Layered Strategy)은 개발 과정에서 빠른 피드백을 제공하면서 배포 결정 전에는 폭넓은 검증 범위를 유지할 수 있게 한다.

시뮬레이션 환경(Simulation Environment)은 실제 로봇의 시험 시간을 소비하지 않고 많은 에피소드를 실행할 수 있기 때문에 대규모 VLA 회귀 시험을 가능하게 한다. 작업을 여러 연산 자원에서 병렬화하여 언어 명령, 객체 배치, 외란, 난수 시드의 조합을 체계적으로 평가할 수 있다. 또한 시뮬레이션에서는 위험한 경계 사례(Edge Case)를 의도적으로 시험할 수 있다. 그러나 시뮬레이션에서의 성공은 실제 하드웨어에서도 동일한 성능이 발생한다는 증거가 아니라 실제 로봇 검증 단계로 진행하기 위한 릴리스 게이트(Release Gate)로 간주해야 한다.

데이터셋 및 모델 변경은 특히 세심한 회귀 분석이 필요하다. 새로운 시연 데이터(Demonstration)를 이용한 미세조정(Fine-Tuning)은 최근 추가된 작업의 성능을 향상시키는 동시에 치명적 망각(Catastrophic Forgetting)을 발생시키거나 기존에 안정적이었던 행동을 변화시킬 수 있다. 비전 인코더(Vision Encoder), 언어 모델, 정책 헤드(Policy Head), 행동 데이터셋의 변경 역시 여러 기능에서 공유되는 표현을 변화시킬 수 있다. 따라서 회귀 시험군에는 새롭게 목표로 하는 작업과 안정적으로 유지되어야 하는 기존 작업을 모두 포함하여 기능 획득(Capability Acquisition)과 기능 유지(Capability Retention)를 함께 측정해야 한다.

성능 회귀(Performance Regression)는 VLA 정책이 실시간 로봇 시스템 내부에서 실행되므로 추론 특성(Inference Characteristics)도 포함해야 한다. 모델 업데이트는 작업 성공률을 향상시키면서도 그래픽 처리 장치(GPU) 메모리 사용량, 추론 지연시간, 전처리 시간 또는 행동 생성 지터(Action-Generation Jitter)를 증가시킬 수 있다. 시험 시스템은 종단간 관측-행동 지연시간(End-to-End Observation-to-Action Latency), 추론 처리량(Inference Throughput), 메모리 사용량, 데드라인 위반, 오래된 행동(Stale-Action) 발생을 기록해야 한다. 휴머노이드 제어 아키텍처의 타이밍 예산(Timing Budget)을 초과하는 정책은 벤치마크 성능이 높더라도 실제 배포에는 적합하지 않을 수 있다.

재현성(Reproducibility)을 확보하려면 모든 자동화 시험 실행에서 전체 실험 구성을 기록해야 한다. 모델 체크포인트 식별자, 소프트웨어 커밋, 데이터셋, 시뮬레이터 버전, 로봇 기술 모델(Robot Description), 프롬프트, 난수 시드, 작업 매개변수, 추론 설정, 제어기 구성, 평가 임계값(Evaluation Threshold)을 결과와 함께 저장해야 한다. 영상, 관측값, 예측 행동, 이벤트 로그, 지표 요약과 같은 산출물(Artifact)은 실패하거나 성능 변화가 크게 발생한 사례에 대해 보존하여 엔지니어가 회귀 문제를 재현하고 진단할 수 있도록 해야 한다.

회귀 임계값(Regression Threshold)은 릴리스 평가를 시작하기 전에 설정해야 한다. 심각한 안전 위반과 같은 일부 지표에는 무관용(Zero Tolerance)을 적용할 수 있으며, 통계적 성능 지표에는 작은 변동을 허용할 수 있다. 전체 성능이 향상되더라도 중요한 기능이 최소 임계값 아래로 떨어지면 후보 정책을 거부할 수 있어야 한다. 이를 통해 하나의 종합 점수(Aggregate Score)가 목표 휴머노이드 응용에 필요한 특정 조작, 언어 그라운딩(Language Grounding), 전신 동작 또는 안전 기능의 심각한 성능 저하를 가리는 것을 방지할 수 있다.

VLA 정책 회귀 자동화의 최종 목적은 모델 평가를 일회성 시연에서 지속적인 엔지니어링 검증 근거(Continuous Engineering Evidence)로 전환하는 것이다. 모든 정책 업데이트를 지속적으로 증가하는 검증된 행동, 알려진 실패 사례, 안전 제약조건, 성능 요구사항과 비교할 수 있다. 자동화된 시뮬레이션 회귀 시험을 통과한 정책은 통제된 하드웨어 인 더 루프(HIL) 및 실제 로봇 시험으로 진행할 수 있으며, 실패 사례는 재현 가능한 시험으로 개발 과정에 다시 전달된다. 이러한 검증 루프(Validation Loop)를 통해 휴머노이드 VLA 시스템은 이전에 확보한 기능을 인지하지 못한 채 상실하는 것을 방지하면서 지속적으로 발전할 수 있다.

## 11.06. HRI Function Test Voice Gesture Safety [w/Code]

![](images/image6.png){width="7.268055555555556in" height="7.268055555555556in"}

인간-로봇 상호작용(HRI, Human-Robot Interaction) 기능 시험은 휴머노이드가 안전한 물리적 동작을 유지하면서 인간의 의사소통을 인식하고 해석하며 적절하게 반응할 수 있는지를 검증한다. 음성 명령, 제스처(Gesture), 시선(Gaze), 근접성(Proximity), 직접적인 물리적 상호작용이 동시에 로봇 행동에 영향을 줄 수 있으므로 시험에서는 개별 인식 정확도만이 아니라 전체 인식-행동 루프(Perception-to-Action Loop)를 평가해야 한다. 목적은 실제 운용 조건에서 의사소통의 이해 가능성, 예측 가능성, 응답성, 안전성을 확보하는 것이다.

음성 인터페이스(Voice Interface) 시험은 오디오 획득(Audio Acquisition) 및 음성 처리 파이프라인(Speech-Processing Pipeline)에서 시작한다. 마이크는 예상되는 거리, 방향, 발화 음량, 억양, 환경 소음 조건에서 명령을 수집할 수 있어야 한다. 자동 음성 인식(ASR, Automatic Speech Recognition)은 대표적인 어휘와 문장 구조를 사용하여 평가해야 하며, 명령 해석 과정에서는 인식된 텍스트가 유효한 로봇 의도(Robot Intention)에 대응하는지를 판단해야 한다. 인식 신뢰도, 명령 지연시간, 거부율(Rejection Rate), 잘못된 활성화(Incorrect Activation)는 유용한 정량적 평가 지표가 된다.

자연어 이해(NLU, Natural Language Understanding) 시험에서는 정확하게 변환된 음성과 정확하게 해석된 의도를 구분해야 한다. 문장이 정확하게 인식되더라도 잘못된 작업 표현(Task Representation)으로 변환될 수 있기 때문이다. 따라서 동의 표현(Synonymous Expression), 불완전한 명령, 모호한 지시 대상, 정정, 부정(Negation), 문맥 의존적 명령을 시험에 포함해야 한다. 의도에 대한 신뢰도가 충분하지 않은 경우 휴머노이드는 위험한 상황을 발생시킬 수 있는 불확실한 물리적 행동을 선택하는 대신 사용자에게 명확화를 요청하거나 명령 실행을 거부해야 한다.

음성 출력(Speech Output)은 주변 사람에게 로봇의 상태와 의도를 전달하므로 함께 검증해야 한다. 텍스트 음성 변환(TTS, Text-to-Speech) 응답은 예상되는 음향 환경에서 명확하게 이해할 수 있어야 하며 허용 가능한 지연시간 내에 제공되어야 한다. 더욱 중요한 것은 음성 응답이 실제 시스템 상태와 일치해야 한다는 점이다. 제어기가 아직 움직이고 있거나 안전 상태 전환이 완료되지 않았다면 로봇은 동작이 정지되었거나 완료되었거나 안전한 상태가 되었다고 말해서는 안 된다.

제스처 시험(Gesture Testing)은 시각 인식 시스템이 인간의 신체 움직임을 신뢰성 있게 탐지하고 해석할 수 있는지를 평가한다. 대표적인 명령에는 가리키기(Pointing), 손 흔들기(Waving), 정지 제스처(Stop Gesture), 방향 지시, 물체 전달 신호(Handover Cue), 작업별 신호 등이 포함될 수 있다. 사용자의 위치, 거리, 신체 방향, 의복, 배경, 조명, 부분 가림(Partial Occlusion), 움직임 속도를 변화시키면서 시험해야 한다. 인식 성능은 이후의 명령 실행과 별도로 측정하여 인식 실패와 계획 또는 제어 실패를 구분할 수 있도록 해야 한다.

제스처의 의미는 공간적 문맥(Spatial Context)에 따라 달라지는 경우가 많다. 가리키기 동작은 객체, 위치, 방향 또는 사람을 지정할 수 있으므로 로봇은 인간 자세 추정(Human Pose Estimation)과 장면 이해(Scene Understanding)를 결합해야 한다. 따라서 기능 시험에서는 제스처의 기하학적 정보가 로봇 또는 세계 좌표계(World Coordinate Frame)로 정확하게 변환되는지를 평가해야 한다. 그렇지 않으면 자세 추정이나 보정(Calibration)의 작은 오차만으로도 제스처 자체는 올바르게 탐지했음에도 휴머노이드가 잘못된 객체를 선택하거나 의도하지 않은 영역으로 이동할 수 있다.

멀티모달 상호작용(Multimodal Interaction)에서는 음성과 제스처가 상호 보완적인 정보를 제공하는 상황이 발생한다. 사용자가 특정 객체를 가리키면서 "저 상자를 가져와"라고 말하거나 손으로 목적지를 표시하면서 음성으로 이동 방향을 지시할 수 있다. 시험에서는 적절한 시간 구간(Temporal Window) 안에서 발생한 신호를 시스템이 정확하게 연결하고 의미적 관계(Semantic Relationship)를 해석하는지 확인해야 한다. 음성과 제스처가 서로 다른 행동을 나타내는 충돌 상황도 시험하여 로봇이 임의로 하나의 해석을 선택하여 실행하지 않도록 해야 한다.

상호작용 타이밍(Interaction Timing)은 사용성과 안전성에 큰 영향을 준다. 인간이 명령을 시작하거나 완료한 시점부터 인식, 해석, 계획을 거쳐 실제로 관찰 가능한 로봇 응답이 나타날 때까지의 종단간 지연시간(End-to-End Latency)을 측정해야 한다. 지연이 지나치게 길면 사용자는 명령이 무시되었다고 판단하여 같은 명령을 반복하거나 로봇에 접근할 수 있다. 처리가 오래 걸리는 경우 시스템은 적절한 확인 응답(Acknowledgement)을 제공해야 하며, 중복 요청으로 인해 동일한 물리적 행동이 의도하지 않게 반복되는 것을 방지해야 한다.

안전 시험(Safety Testing)은 대화 또는 제스처 해석 파이프라인과 독립적으로 유지되어야 한다. 비상 정지(Emergency Stop), 보호 정지(Protective Stop), 충돌 회피(Collision Avoidance), 속도 제한, 작업공간 제한 및 기타 안전 메커니즘은 언어 모델이나 인식 시스템의 출력과 관계없이 작업 명령보다 높은 우선순위를 가져야 한다. 신뢰도가 높은 음성 명령이라도 물리적 안전 제약조건을 우회해서는 안 된다. 마찬가지로 인식 결과가 불확실하면 로봇이 가장 편리한 해석을 임의로 선택하는 대신 움직임을 줄이거나 억제해야 한다.

정지 및 취소 동작(Stop and Cancellation Behavior)은 휴머노이드가 이미 움직이고 있는 동안 명령이 입력될 수 있으므로 별도의 시험이 필요하다. 보행, 팔 뻗기, 운반, 조작 중에 음성, 제스처, 인터페이스 또는 물리적 안전 신호를 이용한 정지 명령을 시험해야 한다. 평가에서는 탐지 지연시간(Detection Latency), 제어 응답 지연시간, 정지 거리(Stopping Distance), 잔류 운동(Residual Motion), 최종 안정성을 측정해야 한다. 로봇은 단순히 명령을 제거하여 자세나 하중이 제어되지 않는 상태로 만드는 것이 아니라 적절한 안전 상태로 전환해야 한다.

인간 근접성 시험(Human Proximity Testing)은 사람이 로봇 주변의 서로 다른 영역으로 진입할 때 로봇의 행동이 어떻게 변화하는지를 평가한다. 휴머노이드는 자신을 고정된 장애물로 간주하는 것이 아니라 팔, 손, 몸통, 다리의 움직임까지 고려해야 한다. 로봇이 대표적인 작업을 수행하는 동안 정지한 사람과 움직이는 사람이 여러 방향에서 접근하도록 시험할 수 있다. 반복 가능한 조건에서 최소 분리 거리(Minimum Separation), 속도 조정, 궤적 변경, 보호 정지 및 동작 재개(Resumption Behavior)를 기록해야 한다.

물리적 협업(Physical Collaboration)은 사람과의 접촉이 의도적으로 발생할 수 있기 때문에 추가적인 요구조건을 가진다. 객체 전달(Object Handover), 협력 운반(Cooperative Carrying), 유도 동작(Guided Motion), 보조 조작(Assisted Manipulation)에서는 시스템이 의도된 상호작용 힘과 충돌 또는 외란을 구분해야 한다. 접촉 시점, 파지력(Grip Force), 객체 무게, 사람의 움직임, 조기 해제(Premature Release)를 변화시키면서 시험해야 한다. 휴머노이드는 과도한 힘을 발생시키지 않으면서 균형을 유지하고, 사람의 행동이 예상된 순서와 달라지는 경우에도 공동으로 취급하는 객체에 대한 제어를 유지해야 한다.

실패 및 불확실성 시나리오(Failure and Uncertainty Scenario)는 HRI 검증에서 필수적이다. 마이크 입력 손실, 손상된 오디오, 가려진 손, 인간 추적 손실(Lost Human Tracking), 여러 화자의 충돌, 동시 명령, 지연된 인식, 빠르게 변화하는 사용자 의도를 의도적으로 발생시켜야 한다. 이러한 상황에서 반드시 작업을 성공적으로 완료해야 하는 것은 아니다. 많은 경우 올바른 행동은 일시 정지, 명확화 요청, 안전 자세 복귀 또는 신뢰할 수 있는 상호작용 문맥이 복구될 때까지 명령을 거부하는 것이다.

시험에서는 의도하지 않은 활성화(Unintended Activation)도 평가해야 한다. 로봇을 대상으로 하지 않은 대화, 배경의 텔레비전 소리, 기계 소음, 우연한 제스처 또는 관련 없는 사람의 움직임이 유효한 명령과 유사할 수 있다. 특히 휴머노이드에서는 인식 오류가 실제 물리적 움직임으로 이어질 수 있기 때문에 오활성화(False Activation)가 중요하다. 따라서 웨이크 워드(Wake Word) 로직, 화자 연계(Speaker Association), 상호작용 상태, 신뢰도 임계값(Confidence Threshold), 확인 메커니즘을 원시 ASR 또는 제스처 분류 정확도에만 의존하지 않고 함께 평가해야 한다.

정량적 평가(Quantitative Evaluation)는 인식, 상호작용, 작업, 안전 지표를 함께 고려해야 한다. 유용한 지표에는 음성 인식 정확도, 의도 해석 정확도, 제스처 인식률, 오활성화율(False Activation Rate), 멀티모달 그라운딩 성공률(Multimodal Grounding Success), 응답 지연시간, 작업 완료율, 명확화 요청 빈도, 운영자 개입, 최소 인간-로봇 분리 거리, 안전 정지 활성화 횟수, 안전하지 않은 행동 횟수가 포함된다. 개별적인 성공 시연만 보고하는 것이 아니라 변동성을 평가하기 위해 여러 사용자와 환경 조건에서 반복 시험을 수행해야 한다.

자동화된 회귀 시험(Automated Regression Testing)은 음성, 인식, 언어 모델, 제스처 분류기, 작업 계획기 또는 안전 소프트웨어가 변경될 때마다 대표적인 HRI 시나리오를 보존하여 재검증할 수 있도록 해야 한다. 기록되거나 합성된 오디오, 시각 시퀀스(Visual Sequence), 스크립트 기반 멀티모달 명령, 시뮬레이션 시나리오를 이용하면 모든 소프트웨어 빌드마다 실제 사람이 참여하지 않아도 많은 조건을 재현할 수 있다. 이후 낮은 위험도의 회귀 시험을 통과한 경우에 선별된 물리적 상호작용 사례를 실제 로봇 시험으로 진행할 수 있다.

모든 HRI 시험에서는 재현 및 실패 분석에 충분한 정보를 보존해야 한다. 소프트웨어 및 모델 버전, 마이크와 카메라 구성, 환경 조건, 사용자 위치, 명령 전사(Command Transcript), 인식 결과, 해석된 의도, 계획기 응답, 로봇 행동, 타이밍 정보, 안전 이벤트를 시험 기록에서 시간적으로 동기화해야 한다. 이러한 추적성(Traceability)을 확보하면 현장에서 발생한 상호작용 실패를 재구성하고 영구적인 회귀 시험 사례(Regression Case)로 전환할 수 있다.

HRI 기능 검증의 최종 목적은 단순히 인식 정확도를 극대화하는 것이 아니라 인간과 물리적 능력을 가진 휴머노이드 사이에 신뢰할 수 있는 상호작용(Trustworthy Interaction)을 확립하는 것이다. 음성 및 제스처 명령은 예측 가능한 행동으로 연결되어야 하고, 불확실성은 보수적인 동작(Conservative Behavior)으로 이어져야 하며, 독립적인 안전 메커니즘은 전체 상호작용 과정에서 항상 최상위 권한을 유지해야 한다. 이러한 특성이 반복적으로 검증된 이후에만 HRI 기능을 제약이 적은 사용자, 환경 및 협업 작업을 포함하는 보다 광범위한 현장 시험(Field Trial)으로 확장해야 한다.

## 11.07. Field Test Protocol Walking Climbing Manip

![](images/image7.png){width="7.268055555555556in" height="7.268055555555556in"}

현장 시험(Field Testing)은 소프트웨어 인 더 루프(SIL, Software-in-the-Loop)와 하드웨어 인 더 루프(HIL, Hardware-in-the-Loop)를 통해 검증된 휴머노이드 소프트웨어가 실제 물리적 조건에서도 신뢰성 있게 동작할 수 있는지를 평가하는 최종 환경을 제공한다. 보행, 등반, 조작은 접촉 불확실성(Contact Uncertainty), 구조적 유연성(Structural Compliance), 센서 오차, 환경 변화, 예상하지 못한 외란을 동시에 로봇에 발생시키므로 체계적인 시험 프로토콜(Test Protocol)이 필수적이다. 시험은 측정 가능한 합격 기준(Acceptance Criteria)과 사전에 정의된 안전 경계(Safety Boundary)를 유지하면서 통제된 개별 기능에서 통합 작업으로 점진적으로 진행해야 한다.

각 현장 시험 세션(Field Session)을 시작하기 전에 승인된 시험 기준선(Test Baseline)을 기준으로 로봇 구성을 검증해야 한다. 소프트웨어 버전, 제어기 매개변수, 로봇 모델, 보정 데이터(Calibration Data), 인식 모델, 배터리 상태, 액추에이터 상태, 통신 연결, 안전 장치, 로깅 시스템(Logging System)을 확인해야 한다. 동적 운동을 시작하기 전에 비상 정지(Emergency Stop) 기능과 보호 제한(Protective Limit)을 시험해야 한다. 또한 시험 구역의 위험 요소, 대피 경로, 바닥 상태, 장애물 및 시험에 참여하지 않는 인원의 접근 가능성을 점검해야 한다.

보행 검증(Walking Validation)은 보수적인 속도 및 가속도 제한을 적용한 평탄하고 마찰력이 높은 표면에서 시작해야 한다. 초기 시험에는 기립, 체중 이동, 단일 스텝, 짧은 직선 보행, 정지, 통제된 회전을 포함할 수 있다. 이러한 동작의 반복성이 확보되면 더 긴 궤적, 측면 이동, 후진 보행, 속도 변화, 좁은 통로, 반복적인 출발-정지 주기(Start-Stop Cycle)를 도입할 수 있다. 각 단계는 이전 조건에서 정의된 안정성 및 안전 요구사항을 충족한 이후에만 진행해야 한다.

보행 성능은 외관상 성공 여부만으로 판단하지 않고 정량적으로 측정해야 한다. 관련 측정 항목에는 명령 속도와 실제 속도, 발 위치 오차(Foot-Placement Error), 베이스 자세(Base Orientation), 질량 중심(Center of Mass) 동작, 스텝 타이밍(Step Timing), 접촉력 분포(Contact-Force Distribution), 관절 추종 오차, 토크 사용률, 미끄러짐 이벤트(Slip Event), 복구 동작(Recovery Action)이 포함된다. 완료 거리와 낙상 없는 운용 시간(Fall-Free Operating Time)은 유용한 시스템 수준 지표이며, 상세 로그를 이용하면 성능 저하의 원인이 상태 추정, 계획, 제어, 접촉 실행 또는 하드웨어 동작 중 어디에서 발생하는지 분석할 수 있다.

외란 시험(Disturbance Testing)은 현장 조건이 명목상의 가정에서 벗어났을 때에도 보행 안정성이 유지되는지를 평가한다. 통제된 밀기, 작은 바닥 불규칙성, 탑재 하중 변화, 마찰 변화, 예상하지 못한 정지 또는 움직이는 장애물을 점진적으로 도입할 수 있다. 로봇은 보행을 유지하거나, 복구 스텝(Recovery Step)을 실행하거나, 속도를 줄이거나, 안전하게 정지하거나, 사전에 정의된 다른 복구 상태로 전환해야 한다. 따라서 성공적인 시험은 모든 외란에서 중단 없이 계속 걷는 것만을 의미하지 않으며 적절한 실패 대응(Failure Handling)도 포함한다.

등반 검증(Climbing Validation)은 계단과 높은 표면에서 발 위치 및 균형 오차가 더 큰 결과를 초래할 수 있기 때문에 별도로 진행해야 한다. 시험은 하나의 낮은 단차에서 시작하여 반복적인 계단 또는 더욱 복잡한 높이 변화로 확장할 수 있다. 단차 높이(Step Height), 디딤면 깊이(Tread Depth), 접근 거리, 발 자세, 지지 전환(Support Transition), 난간 사용 가능 여부를 통제하고 기록해야 한다. 충분한 반복성이 확보될 때까지 초기 시험에서는 기계적 지지 장치 또는 낙상 방지 장비(Fall-Arrest Equipment)를 사용할 수 있다.

계단 상승(Stair Ascent) 과정에서는 단차 형상 인식, 선행 발(Leading Foot)의 위치, 체중 이동, 후행 발(Trailing Foot)의 간격 확보, 각 전환 이후의 안정화를 평가해야 한다. 계단 하강(Stair Descent)은 가시성, 충격 조건, 균형 요구사항이 상승과 다르기 때문에 독립적으로 시험해야 한다. 양방향 시험 과정에서 발과 계단 모서리 사이의 거리, 착지 속도(Touchdown Velocity), 수직 질량 중심 운동, 접촉력 피크(Contact-Force Peak), 몸통 자세(Torso Orientation), 보정 동작(Corrective Action)을 모니터링해야 한다.

정상적인 계단 동작이 안정된 이후에는 등반 시험에 기하학적 불확실성(Geometric Uncertainty)을 포함해야 한다. 단차 높이, 디딤면 깊이, 접근 각도, 표면 마찰 또는 계단 위치 추정에 작은 변화를 적용하면 이상적인 환경 모델에 대한 과도한 의존성을 확인할 수 있다. 목적은 로봇이 위험한 형상을 무리하게 통과하도록 만드는 것이 아니라 성능이 저하되기 시작하는 경계를 확인하는 것이다. 불확실성이 검증된 운용 범위(Validated Operating Envelope)를 초과하면 작업 거부, 재계획(Replanning), 지원 요청 또는 통제된 후퇴(Controlled Retreat)가 올바른 대응이 될 수 있다.

조작 현장 시험(Manipulation Field Test)은 안정적인 기립 상태에서 수행하는 정적인 작업부터 시작해야 한다. 도달, 파지, 들어 올리기, 배치, 밀기, 당기기, 객체 전달(Object Handover)은 더 큰 변화를 도입하기 전에 형상과 질량을 알고 있는 객체를 사용하여 평가할 수 있다. 시험에서는 말단장치 정확도(End-Effector Accuracy), 파지 성공률, 객체 미끄러짐, 접촉력, 관절 한계 여유(Joint-Limit Margin), 자기 충돌(Self-Collision) 이벤트, 실행 시간, 복구 시도를 기록해야 한다. 실패는 인식, 계획, 파지, 제어 또는 물리적 상호작용에 따라 분류해야 한다.

객체 변화(Object Variation)는 자세, 크기, 질량, 표면 마찰, 유연성(Compliance), 시각적 외형을 변화시키는 방식으로 점진적으로 도입해야 한다. 또한 현실적인 조명, 복잡한 배경(Background Clutter), 부분 가림(Partial Occlusion), 불완전한 객체 위치 추정 조건에서도 조작 성능을 평가해야 한다. 이러한 조건을 통해 시뮬레이션에서 성공적으로 동작했던 정책이 관측값과 접촉 특성이 학습 또는 검증 분포(Validation Distribution)에서 벗어나는 실제 환경에서도 신뢰성을 유지하는지 판단할 수 있다.

전신 조작(Whole-Body Manipulation)은 조작과 자세 및 균형 제어를 결합한다. 일반적인 팔 작업공간을 넘어선 위치에 도달하거나, 무거운 객체를 들어 올리거나, 환경을 밀거나, 탑재물을 운반하면 로봇의 질량 중심과 반력이 변화한다. 현장 시험에서는 전신 제어기(Whole-Body Controller)가 관절, 토크, 충돌 또는 안정성 제약을 위반하지 않으면서 운동과 접촉력을 재분배하는지를 검증해야 한다. 휴머노이드의 안정성을 희생하는 방식으로 조작 작업을 성공해서는 안 된다.

통합 보행-조작 시험(Integrated Locomotion-Manipulation Testing)은 독립적인 보행 및 조작 검증이 성공한 이후에 진행해야 한다. 대표적인 작업에서는 로봇이 객체까지 걸어가고, 적절한 자세에서 정지하고, 객체를 파지하고, 운반한 후 다른 위치에 배치하도록 할 수 있다. 내비게이션(Navigation), 자세 안정화, 도달, 파지, 운반, 해제 사이의 각 전환을 모니터링해야 한다. 이러한 전환 과정에서는 개별 서브시스템(Subsystem)을 독립적으로 시험할 때 발견되지 않았던 통합 결함이 자주 나타날 수 있다.

등반과 조작의 결합은 더 높은 위험도의 검증 단계이며 두 기능이 독립적으로 충분히 성숙한 이후에만 도입해야 한다. 객체를 운반하면서 높은 표면으로 이동하면 가시성, 팔의 사용 가능성, 질량 분포, 균형 여유(Balance Margin)가 변화한다. 시험은 가벼운 탑재물과 단순한 형상에서 시작하면서 속도를 제한해야 한다. 로봇이 충분한 인식, 지지 안정성 또는 조작 제어를 유지할 수 없다면 점점 위험해지는 작업을 계속하는 대신 정지해야 한다.

현장 시험에서 사람이 참여하는 경우 명확한 운용 규칙(Operational Rule)이 필요하다. 시험 인원은 로봇 운용, 안전 감독, 데이터 수집, 비상 개입(Emergency Intervention)에 대한 역할을 명확하게 정의해야 한다. 특정 시험에서 상호작용이 필요한 경우를 제외하면 사람은 위험한 운동 영역 밖에 위치해야 한다. 특히 액추에이터를 활성화하거나 정지된 로봇을 재설정하기 전에는 시험 인원 사이의 의사소통이 명확해야 한다. 안전 이벤트 이후의 재시작은 원인이 파악되었으며 시험 조건이 여전히 유효하다는 사실을 확인한 후에 수행해야 한다.

현장 실패(Field Failure)는 개별적인 사고가 아니라 중요한 검증 데이터로 취급해야 한다. 낙상, 스텝 실패, 파지 실패, 과도한 접촉력, 위치 추정 오류(Localization Error), 제어기 시간 초과(Controller Timeout), 예상하지 못한 안전 정지가 발생하면 동기화된 로그와 구성 정보를 보존해야 한다. 가능한 경우 해당 이벤트를 SIL 또는 HIL에서 재구성해야 한다. 원인을 수정한 이후에는 동일한 실패 메커니즘을 향후 소프트웨어 릴리스에서 탐지할 수 있도록 해당 시나리오를 회귀 시험군(Regression Suite)에 포함해야 한다.

환경 조건(Environmental Condition)은 현장 성능이 온도, 바닥 재질, 조명, 진동, 먼지, 음향 소음, 네트워크 품질 및 기타 외부 요인에 따라 달라질 수 있으므로 기록해야 한다. 이러한 변수는 인식, 액추에이터 성능, 통신, 열적 동작(Thermal Behavior), 접촉 추정에 영향을 줄 수 있다. 통제된 환경 범위에서 반복 시험을 수행하면 무작위 실패와 체계적인 민감도(Systematic Sensitivity)를 구분할 수 있으며, 로봇이 지원하는 운용 범위를 정의하기 위한 검증 근거를 확보할 수 있다.

현장 시험 보고서(Field-Test Report)는 각 시나리오를 명시적인 진입 조건(Entry Condition), 시험 절차, 측정 항목, 합격 기준, 관찰된 이상 현상(Observed Anomaly), 최종 판정과 연결해야 한다. 성공적인 완료는 단순히 작업 목표에 도달하는 것만을 의미하지 않으며 실행 전 과정에서 로봇이 안전, 안정성, 타이밍 및 하드웨어 한계를 준수해야 한다. 일관성을 입증하기 위해 반복 시험이 필요하며, 실패한 시험도 성능 통계에서 제외하지 않고 데이터셋에 그대로 유지해야 한다.

보행, 등반 및 조작 현장 프로토콜의 최종 목적은 통제된 시뮬레이션과 실험실 검증을 넘어 통합 휴머노이드 시스템이 실제로 유용한 물리적 작업을 수행할 수 있다는 검증 근거를 확보하는 것이다. 기능은 통제되지 않은 시연을 통해 한 번에 확대하는 것이 아니라 점진적으로 확장해야 한다. 현장 관찰 결과를 SIL, HIL, 회귀 시험, 안전 요구사항과 다시 연결함으로써 모든 실제 로봇 실험은 지속적으로 개선되는 검증 프로세스(Validation Process)에 기여하며 소프트웨어 릴리스 준비도(Software Release Readiness)에 대해 더욱 신뢰할 수 있는 판단 근거를 제공한다.

## 11.08. Safety Function Test E Stop Fall Recovery [w/Code]

![](images/image8.png){width="7.268055555555556in" height="7.268055555555556in"}

휴머노이드 안전 기능 시험(Safety-Function Testing)은 로봇이 위험 상태를 감지하고, 안전하지 않은 동작을 중단하며, 주변 사람과 하드웨어를 보호하고, 정상적인 운용을 더 이상 유지할 수 없을 때 통제된 상태로 전환할 수 있는지를 검증한다. 비상 정지(Emergency Stop), 보호 정지(Protective Stop), 낙상 감지(Fall Detection), 보호 낙상(Protective Falling), 복구(Recovery)는 정상 제어의 부수적인 결과로 간주하지 않고 독립적인 안전 메커니즘(Safety Mechanism)으로 평가해야 한다. 인식, 계획, 통신 또는 제어 기능이 실패하더라도 이러한 기능의 동작은 예측 가능하게 유지되어야 한다.

비상 정지 시험(Emergency-Stop Testing)은 물리적인 비상 정지 장치(E-Stop Device)에서 액추에이터 억제(Actuator Inhibition)에 이르는 전체 경로를 검증하는 것에서 시작한다. 시험에서는 비상 정지 활성화가 신뢰성 있게 감지되고 안전 아키텍처(Safety Architecture)를 통해 전달되어 의도된 하드웨어 및 소프트웨어 응답으로 변환되는지를 확인해야 한다. 측정 항목에는 감지 지연시간(Detection Latency), 제어 종료 지연시간(Control Shutdown Latency), 잔류 액추에이터 명령(Residual Actuator Command), 정지 시간, 최종 로봇 상태가 포함되어야 한다. 정지 동작은 로봇의 현재 운동과 접촉 구성에 크게 영향을 받을 수 있으므로 서로 다른 운용 모드에서 반복적으로 활성화하여 시험해야 한다.

비상 정지는 기립, 보행, 회전, 팔 뻗기, 조작, 객체 운반 및 기타 대표적인 동작 중에 시험해야 한다. 동적으로 균형을 유지하는 휴머노이드에서는 단순히 액추에이터 명령을 제거하는 방식이 적절하지 않을 수 있는데, 제어되지 않은 토크 손실 자체가 낙상을 발생시킬 수 있기 때문이다. 따라서 안전 아키텍처는 각 조건에서 즉각적인 전원 차단, 통제된 감속(Controlled Deceleration), 토크 감소, 자세 안정화(Posture Stabilization), 브레이크 활성화 또는 에너지를 완전히 제거하기 전의 다른 보호 전환(Protective Transition) 중 어떤 방식이 필요한지를 정의해야 한다.

보호 정지 시험(Protective-Stop Testing)은 반드시 가장 강력한 비상 대응이 필요하지는 않은 위험 조건을 대상으로 한다. 인간의 근접, 충돌 위험, 과도한 힘, 통신 결함, 제어기 오류, 작업공간 위반 또는 센싱 성능 저하(Degraded Sensing)는 통제된 운동 감소 또는 정지를 발생시킬 수 있다. 시험에서는 보호 기능이 작업 목표와 독립적으로 유지되는지 검증해야 하며, 현재 작업의 완료에 대한 신뢰도나 우선순위가 높다는 이유만으로 계획기(Planner) 또는 학습 기반 정책(Learned Policy)이 이러한 보호 기능을 무시할 수 없어야 한다.

정지 성능(Stopping Performance)은 정량적으로 특성화해야 한다. 관련 측정 항목에는 활성화 이전의 명령 속도, 감지 시간, 정지 거리(Stopping Distance), 잔류 관절 속도(Residual Joint Velocity), 최대 접촉력, 토크 감소(Torque Decay), 베이스 변위(Base Displacement), 최종 안정성이 포함된다. 시험은 서로 다른 보행 속도, 팔의 운동 속도, 탑재 하중(Payload), 신체 구성에서 반복해야 한다. 이를 통해 측정 가능한 정지 운용 범위(Stopping Envelope)를 정의하고 저에너지 및 고에너지 운용 상태에 서로 다른 안전 임계값이 필요한지를 확인할 수 있다.

결함 주입(Fault Injection)을 통해 주요 소프트웨어 스택(Main Software Stack)의 성능이 저하된 상황에서도 안전 기능이 유지되는지를 검증할 수 있다. 통제된 조건에서 통신 패킷을 지연시키거나 제어 프로세스를 의도적으로 정지하고, 센서 데이터를 고정하거나 워치독 데드라인(Watchdog Deadline)을 초과하도록 만들 수 있다. 안전 시스템은 유효한 제어 권한(Control Authority)의 상실을 감지하고 사전에 정의된 안전 상태로 전환해야 한다. 하나의 응용 프로세스가 실패하더라도 독립적인 비상 정지 또는 보호 정지 메커니즘의 동작을 방해해서는 안 된다.

낙상 감지(Fall Detection)는 복구 가능한 외란과 실제 또는 임박한 낙상을 구분할 수 있는 여러 신호를 결합해야 한다. 베이스 자세(Base Orientation), 각속도(Angular Velocity), 질량 중심(Center of Mass) 동작, 발 접촉, 관절 상태, 관성측정장치(IMU) 측정값, 지지 형상(Support Geometry), 제어기 안정성 지표를 판단에 활용할 수 있다. 임계값은 정상적인 동적 운동에 불필요하게 반응하지 않으면서도 보호 동작을 수행할 충분한 시간 안에 빠르게 진행되는 불안정성을 감지할 수 있어야 한다. 따라서 실제 낙상 사례와 공격적이지만 복구 가능한 운동을 모두 시험해야 한다.

낙상 감지 검증(Fall-Detection Validation)은 의도적인 낙상 시험을 수행하기 전에 시뮬레이션과 구속된 실제 조건(Restrained Physical Condition)에서 시작해야 한다. 통제된 외란을 이용하여 로봇을 안정적인 기립 상태에서 큰 자세 편차와 지지 상실 상태까지 점진적으로 이동시킬 수 있다. 기록된 감지 시점은 실제 낙상 과정의 물리적 진행과 비교해야 한다. 거짓 음성(False Negative)은 보호 동작이 너무 늦게 시작될 수 있어 위험하며, 과도한 거짓 양성(False Positive)은 정상적인 보행과 조작을 실용적으로 수행하기 어렵게 만들 수 있다.

낙상을 더 이상 피할 수 없게 되면 보호 낙상 동작(Protective Fall Behavior)은 원래 작업을 계속 수행하려 하기보다 사람의 부상과 하드웨어 손상을 줄이는 것을 목표로 해야 한다. 로봇 설계와 구성에 따라 팔다리 자세 변경, 관절 강성(Joint Stiffness) 감소, 위험한 팔 배치 회피, 머리 또는 센서 보호, 접촉 순서(Contact Sequence) 관리, 충격 에너지 제한 등의 동작을 사용할 수 있다. 이 단계에서 안전 목표는 작업 성능을 유지하는 것에서 달성 가능한 범위 내에서 가장 피해가 적은 자세로 지면에 도달하는 것으로 전환된다.

보호 낙상 시험(Protective-Fall Testing)은 전방, 후방, 측면, 회전 낙상이 서로 다른 충격 위험을 발생시키므로 다양한 낙상 방향을 평가해야 한다. 초기 실험에서는 시뮬레이션, 안전 하네스(Harness), 패딩(Padding), 저에너지 조건 또는 기타 적절한 보호 수단을 사용해야 한다. 충격 위치, 자세, 관절 하중, 접촉력, 액추에이터 상태, 구조적 응답(Structural Response)을 기록해야 한다. 또한 운반 중인 객체나 뻗은 팔이 예상되는 보호 전략에 어떠한 영향을 미치는지도 고려해야 한다.

균형 복구(Balance Recovery)와 보호 낙상 사이의 전환은 특히 중요하다. 보호 낙상 동작이 너무 일찍 활성화되면 로봇이 발목, 엉덩이, 운동량(Momentum) 또는 스텝핑 전략(Stepping Strategy)을 통해 복구할 수 있는 외란까지 포기할 수 있다. 반대로 너무 늦게 활성화되면 충격을 줄이는 데 필요한 시간과 신체 구성이 충분하지 않을 수 있다. 따라서 시험에서는 복구 가능한 불안정성과 낙상이 확정된 상태(Committed Falling) 사이의 경계를 확인하고, 해당 경계 주변에서 안전 상태 기계(Safety State Machine)가 일관성 있게 모드를 전환하는지를 평가해야 한다.

낙상 이후 로봇은 자신의 상태를 평가하지 않은 채 즉시 일어서려고 해서는 안 된다. 낙상 후 점검(Post-Fall Check)에서는 신체 자세, 관절 위치, 액추에이터 상태, 센서 상태, 통신 상태, 예상하지 못한 접촉, 기계적 한계(Mechanical Limit), 주변 장애물을 평가할 수 있다. 시스템은 자율 복구(Autonomous Recovery)가 허용되는지, 운영자의 확인이 필요한지 또는 동작을 비활성화된 상태로 유지해야 하는지를 판단해야 한다. 이를 통해 손상되었거나 장애물에 구속된 휴머노이드가 최초 낙상 이후 추가적인 위험 동작을 발생시키는 것을 방지할 수 있다.

낙상 복구(Fall Recovery)는 하나의 일어서기 동작이 아니라 연속적인 통제 상태(Controlled State)의 과정으로 검증해야 한다. 로봇은 엎드린 자세(Prone), 바로 누운 자세(Supine), 측면 자세(Lateral), 앉은 자세 또는 주변 객체에 의해 구속된 상태인지를 식별한 후 적절한 복구 전략을 선택해야 할 수 있다. 복구 과정에서도 관절 한계, 자기 충돌(Self-Collision), 환경과의 충돌, 토크 제한, 접촉 안정성(Contact Stability)은 계속 활성화되어야 한다. 예상한 지지 접촉을 확보할 수 없거나 복구 과정이 불안정해지면 동작을 정지하거나 다른 안전 전략으로 전환해야 한다.

복구 시험(Recovery Testing)은 깨끗한 실험실 바닥에서 성공하는 일어서기 동작이 벽, 가구, 계단, 흩어진 객체 또는 마찰력이 낮은 표면 주변에서는 실패할 수 있으므로 환경 변화를 포함해야 한다. 로봇은 큰 복구 동작을 실행하기 전에 필요한 지지 영역과 팔다리 운동 공간을 사용할 수 있는지를 확인해야 한다. 환경이 안전한 자율 복구를 허용하지 않는 경우 실행 불가능한 동작을 반복적으로 시도하는 것보다 인간의 지원을 요청하는 것이 올바른 시스템 동작이 될 수 있다.

안전 상태 전환(Safety-State Transition)은 결정론적(Deterministic)이고 관찰 가능해야 한다. 운영자와 상위 수준 소프트웨어는 정상 운용, 성능 저하 운용(Degraded Operation), 보호 정지, 비상 정지, 낙상 감지, 보호 낙상, 낙상 후 평가(Post-Fall Assessment), 복구 상태를 명확하게 구분할 수 있어야 한다. 각 전환에서는 트리거(Trigger), 타임스탬프(Timestamp), 관련 센서 근거, 결과적인 제어 모드를 기록해야 한다. 명확한 상태 보고는 예상하지 못한 물리적 결과가 감지, 의사결정 로직, 제어 실행 또는 하드웨어 응답 중 어디에서 발생했는지를 진단하는 데 필수적이다.

재설정 및 재시작 동작(Reset and Restart Behavior)은 정지 동작과 동일한 수준으로 중요하게 다루어야 한다. 비상 정지를 해제했다고 해서 자동으로 움직임을 복원하거나 이전에 중단된 명령을 다시 실행해서는 안 된다. 시스템은 안전 조건을 검증하고 정의된 절차에 따라 결함을 해제하거나 확인하며, 필요한 상태 추정기(State Estimator)와 제어기를 다시 초기화하고, 활성 운용으로 복귀하기 전에 명시적인 승인을 요구해야 한다. 이를 통해 안전 이벤트 직후 오래된 명령(Stale Command)이나 유효하지 않은 내부 상태가 예상하지 못한 움직임을 발생시키는 것을 방지할 수 있다.

반복적인 안전 시험에서는 성공적인 안전 개입과 원하지 않는 활성화를 모두 측정해야 한다. 평가 지표에는 비상 정지 응답 시간, 보호 정지 거리, 낙상 감지 지연시간, 거짓 양성 및 거짓 음성 비율, 충격 관련 측정값, 복구 성공률, 운영자 개입, 안전 상태 전환의 정확성이 포함될 수 있다. 결과는 로봇 구성, 탑재 하중, 소프트웨어 버전, 운동 상태, 표면 조건, 시험 매개변수와 연결하여 소프트웨어 릴리스 간 성능 변화를 추적할 수 있도록 해야 한다.

발견된 모든 안전 실패(Safety Failure)는 재현 가능하다면 영구적인 회귀 시험 사례(Regression Case)로 전환해야 한다. 지연된 정지, 감지되지 않은 낙상, 잘못된 안전 상태 전환, 불안정한 복구 또는 예상하지 못한 재시작은 반복적인 조사가 상대적으로 안전한 소프트웨어 인 더 루프(SIL) 또는 하드웨어 인 더 루프(HIL) 환경에서 먼저 재구성할 수 있다. 문제를 수정한 이후에는 동일한 시나리오를 통제된 실제 로봇 조건에서 다시 검증해야 한다. 이러한 과정은 안전 검증 근거(Safety Evidence)를 지속적으로 축적하고 이후 소프트웨어 변경으로 이미 수정된 실패 메커니즘이 다시 발생할 가능성을 줄인다.

비상 정지, 낙상 및 복구 검증의 최종 목적은 정상적인 작업 실행이 실패하는 경우에도 휴머노이드가 독립적인 안전 아키텍처의 통제 범위 안에 유지된다는 것을 입증하는 것이다. 안전은 모든 낙상을 방지하는 것만으로 정의되지 않으며, 위험 상태를 감지하고, 에너지를 제한하며, 피해 결과를 줄이고, 제어 권한을 유지하며, 조건이 허용되는 경우에만 복구하는 것을 포함한다. 이러한 기능은 사람, 장애물, 탑재물 및 환경 불확실성으로 인해 소프트웨어 또는 하드웨어 실패의 결과가 더욱 커지는 광범위한 현장 운용(Field Operation)으로 진행하기 전에 반드시 통과해야 하는 핵심 릴리스 게이트(Release Gate)를 제공한다.

## 11.09. Environmental Stress Test Vibration Temp [w/Code]

![](images/image9.png){width="7.268055555555556in" height="7.268055555555556in"}

환경 스트레스 시험(Environmental Stress Testing)은 휴머노이드 로봇과 그 소프트웨어 스택(Software Stack)이 통제된 실험실 환경과 다른 물리적 조건에서도 신뢰성 있게 동작을 지속할 수 있는지를 평가한다. 진동, 온도 변화, 기계적 충격(Mechanical Shock), 습도, 먼지, 변화하는 표면 조건은 센서, 액추에이터(Actuator), 연산 하드웨어, 커넥터, 배터리, 구조 부품에 영향을 줄 수 있다. 목적은 현장 배포 전에 환경 의존적 고장(Environment-Dependent Failure)을 발견하고 전체 로봇 시스템에 대해 검증된 운용 범위(Validated Operating Envelope)를 정의하는 것이다.

환경 검증(Environmental Validation)은 기준 구성(Reference Configuration)과 정상 성능 기준선(Nominal Performance Baseline)을 설정하는 것에서 시작해야 한다. 로봇 하드웨어 개정 버전, 소프트웨어 버전, 제어기 매개변수, 센서 보정값, 배터리 상태, 탑재 하중(Payload), 기계적 구성은 시험 전 과정에서 추적 가능해야 한다. 정상적인 실험실 조건에서 수집한 기능 성능 측정값은 스트레스 조건에서의 성능을 비교하기 위한 기준선이 된다. 이를 통해 노출 이후 관찰된 변화를 기존 변동이나 구성 차이와 구분할 수 있다.

진동 시험(Vibration Testing)은 보행 자체가 발의 충격, 관절 운동, 구조 진동을 통해 반복적인 기계적 가진(Mechanical Excitation)을 발생시키기 때문에 휴머노이드에서 특히 중요하다. 외부 진동은 차량, 기계 장비, 산업 현장의 바닥 또는 운송 과정에서도 발생할 수 있다. 시험에서는 진동이 카메라, 관성측정장치(IMU), 힘 센서, 관절 엔코더(Joint Encoder), 커넥터, 연산 모듈, 액추에이터 피드백에 미치는 영향을 평가해야 한다. 연속 진동과 순간적인 가진(Transient Excitation)은 서로 다른 전기적 및 기계적 고장 모드를 발생시킬 수 있으므로 모두 고려해야 한다.

진동 시험에서는 안전하고 기술적으로 가능한 경우 가진이 적용되는 동안에도 로봇의 기능을 평가해야 한다. 센서 스트림(Sensor Stream)을 모니터링하여 잡음 증가, 바이어스 변화(Bias Change), 샘플 손실(Dropped Sample), 동기화 오류, 간헐적 통신 문제를 확인할 수 있다. 또한 예상하지 못한 진동, 상태 추정(State Estimation) 성능 저하, 접촉 불안정성, 액추에이터 명령 변화를 관찰하여 제어 성능을 평가해야 한다. 기계적으로 손상되지 않더라도 진동 환경에서 신뢰할 수 있는 인식이나 제어가 불가능하다면 운용 강건성(Operational Robustness)이 확보되었다고 판단할 수 없다.

관성측정장치(IMU)는 진동이 부유 기반 상태 추정(Floating-Base State Estimation)에 사용되는 가속도와 각속도 측정값을 직접 오염시킬 수 있기 때문에 특별한 주의가 필요하다. 시험에서는 진동 노출 전, 노출 중, 노출 후의 IMU 바이어스, 잡음 특성, 자세 추정값, 추정기 잔차(Estimator Residual)를 비교해야 한다. 필터링(Filtering)은 불필요한 고주파 성분을 억제하면서 과도한 지연을 발생시키거나 균형 제어에 필요한 동적 정보를 제거해서는 안 된다. 따라서 원시 센서 품질과 함께 추정기 안정성(Estimator Stability)을 평가해야 한다.

비전 시스템(Vision System) 역시 기계적 가진으로 인해 성능이 저하될 수 있다. 카메라 진동은 영상 흐림(Image Blur), 영상의 롤링 또는 전체 변위, 보정값 변화, 불일치한 깊이 측정(Depth Measurement)을 발생시킬 수 있다. 스테레오 또는 다중 카메라 시스템은 센서 사이의 상대적 정렬이 변할 경우 특히 민감할 수 있다. 따라서 환경 시험에서는 진동이 존재하는 동안과 반복적인 기계적 노출 이후에 특징 안정성(Feature Stability), 객체 감지, 깊이 추정, 시각 위치 추정(Visual Localization), 카메라-로봇 보정(Camera-to-Robot Calibration)을 모니터링해야 한다.

온도 시험(Temperature Testing)은 보관, 기동, 운용 과정에서 예상되는 열적 범위(Thermal Range) 전반의 성능을 평가한다. 저온에서는 배터리 특성, 윤활 특성, 재료 강성, 센서 응답, 액추에이터 마찰이 변화할 수 있으며, 고온에서는 연산 성능이 감소하고 전기적 저항이 증가하며 열 보호(Thermal Protection)가 빠르게 활성화되고 지속적인 액추에이터 출력을 제한할 수 있다. 로봇 서브시스템은 운용 중 상당한 자체 발열(Self-Heating)을 발생시킬 수 있으므로 시험에서는 주변 온도(Ambient Temperature)와 내부 구성요소 온도를 구분해야 한다.

저온 시동 동작(Cold-Start Behavior)은 정상 상태 운용(Steady-State Operation)과 별도로 시험해야 한다. 저온 환경에 장시간 있었던 휴머노이드는 내부적으로 예열된 상태에서 동일한 환경에 진입한 로봇과 다른 관절 마찰, 배터리 전압, 센서 바이어스 또는 초기화 특성을 나타낼 수 있다. 시동 시험에서는 동작을 활성화하기 전에 부팅 신뢰성, 통신 초기화, 보정 유효성, 상태 추정기 수렴(State-Estimator Convergence), 액추에이터 준비 상태, 안전 시스템 가용성을 검증해야 한다. 핵심 서브시스템이 유효한 운용 조건에 도달하지 못한 경우에는 동작을 활성화하지 않아야 한다.

고온 시험(High-Temperature Testing)에서는 즉각적인 기능뿐만 아니라 지속 운용(Sustained Operation)도 평가해야 한다. 보행, 조작, 인식, 온보드 추론(Onboard Inference)을 동시에 수행하면 모터, 드라이브, 배터리, 중앙처리장치(CPU), 그래픽처리장치(GPU), 전력 전자장치(Power Electronics)에서 열이 발생할 수 있다. 온도 센서, 팬 또는 펌프 동작, 열 스로틀링(Thermal Throttling), 액추에이터 디레이팅(Actuator Derating), 보호 종료 로직(Protective Shutdown Logic)을 모니터링해야 한다. 열적 조건이 하드웨어 무결성을 위협하거나 제어 가정을 무효화하기 전에 로봇은 작업 부하를 줄이거나 통제된 안전 상태로 전환해야 한다.

열 사이클링(Thermal Cycling)은 하나의 고정 온도에서는 나타나지 않는 문제를 발견할 수 있다. 반복적인 팽창과 수축은 커넥터, 케이블 배선, 실링(Seal), 기계적 인터페이스, 센서 정렬, 구조 체결부에 영향을 줄 수 있다. 따라서 보정 매개변수는 최초 노출 시점에만 확인하는 것이 아니라 여러 차례의 온도 전환 이후에도 점검해야 한다. 시스템이 실온으로 복귀한 이후에도 영구적인 드리프트(Persistent Drift), 느슨해진 연결부 또는 누적된 환경 손상을 나타내는 기타 변화가 있는지를 평가해야 한다.

환경 스트레스는 어떤 구성요소도 완전히 고장 나지 않은 상태에서도 액추에이터 동작을 변화시킬 수 있다. 모터 상수(Motor Constant), 기어박스 마찰, 브레이크 응답, 관절 감쇠(Joint Damping), 유연성(Compliance), 토크 추정은 온도 또는 기계적 가진에 따라 달라질 수 있다. 시험에서는 동일한 동작에 대해 서로 다른 환경 조건에서 명령값과 측정된 위치, 속도, 전류, 토크를 비교해야 한다. 전신 제어기(Whole-Body Controller)는 안전하지 않은 제어 게인 증가 없이 예상되는 매개변수 변화를 허용해야 하며, 운용 범위의 경계에서도 불안정성을 발생시키지 않아야 한다.

환경 온도는 사용 가능한 에너지와 최대 전력 공급 능력에 큰 영향을 미치므로 배터리 및 전력 시스템(Battery and Power System)의 동작도 시험에 포함해야 한다. 대표적인 동적 작업을 수행하면서 배터리 팩 전압, 전류, 충전 상태(State of Charge) 추정값, 셀 온도, 전압 강하(Voltage Sag), 전력 제한 활성화, 종료 동작을 모니터링할 수 있다. 사용 가능한 전력이 감소할 경우 제어 시스템은 운동 요구량을 줄이거나 안전하게 상태를 전환해야 하며, 저전압이나 전력 제한이 제어되지 않은 액추에이터 동작으로 이어져서는 안 된다.

개별 환경 요인의 특성을 파악한 이후에는 복합 스트레스 시험(Combined Stress Testing)이 중요해진다. 온도와 진동은 상호작용할 수 있으며, 높은 연산 부하는 높은 주변 온도 및 동적 보행과 결합되어 개별 시험보다 더 가혹한 조건을 만들 수 있다. 따라서 실제 운용 시나리오를 바탕으로 대표적인 조합을 선정해야 한다. 목적은 모든 극한 조건을 동시에 결합하는 것이 아니라 하드웨어, 인식, 추정, 제어 사이의 숨겨진 의존성을 드러낼 수 있는 현실적인 상호작용을 확인하는 것이다.

기능 시험(Functional Testing)은 구성요소의 생존 여부만 확인하는 것이 아니라 환경 스트레스가 적용되는 동안에도 반복 수행해야 한다. 시험 환경의 위험 수준에 따라 기립 안정성, 보행, 관절 추종, 인식, 통신, 조작, 비상 정지, 안전 모니터링 기능을 실행할 수 있다. 성능 저하는 사전에 정의된 임계값과 비교하여 측정해야 한다. 환경 시험 장비 내부에서 동적 로봇 운용이 위험한 경우에는 동등한 서브시스템 또는 하드웨어 인 더 루프(HIL) 구성을 사용하여 의미 있는 기능 검증을 유지할 수 있다.

환경적 결함(Environmental Fault)은 통제된 시스템 대응으로 이어져야 한다. 보정 범위를 벗어난 센서 온도, 과도한 액추에이터 온도, 불안정한 전원 공급, 통신 성능 저하 또는 유효하지 않은 상태 추정은 식별 가능한 진단 정보와 적절한 안전 동작을 발생시켜야 한다. 개별 구성요소가 전기적으로 계속 동작한다는 이유만으로 로봇이 정상 운용을 지속해서는 안 된다. 감지된 상태의 심각성과 복구 가능성에 따라 성능 저하 운용(Degraded Operation), 작업 제한, 통제된 정지 또는 시스템 종료를 선택해야 한다.

스트레스 시험 후 점검(Post-Stress Inspection)은 일부 환경적 손상이 운용 중에는 드러나지 않을 수 있기 때문에 필요하다. 상당한 진동 또는 열 노출 이후에는 커넥터, 케이블, 체결부, 커버, 실링, 관절, 센서 마운트, 냉각 경로, 구조 인터페이스를 점검해야 한다. 이후 정상 조건에서 보정 점검과 대표적인 기능 시험을 다시 수행해야 한다. 시험 전후의 측정값 차이를 통해 환경 노출이 영구적인 드리프트, 기계적 풀림(Mechanical Loosening) 또는 잠재적 성능 저하(Latent Degradation)를 발생시켰는지 판단할 수 있다.

자동화된 로깅(Automated Logging)은 환경 조건과 로봇의 동작을 함께 기록해야 한다. 주변 온도, 구성요소 온도, 진동 측정값, 전원 상태, 센서 진단 정보, 제어기 타이밍, 추정기 상태, 통신 오류, 액추에이터 피드백, 안전 이벤트, 작업 성능 지표에 동기화된 타임스탬프를 적용해야 한다. 이를 통해 엔지니어는 이벤트 발생 이후 기록된 대략적인 관찰에 의존하지 않고 특정 고장이 발생하기 직전의 환경 조건과 해당 고장을 직접 연관시킬 수 있다.

합격 기준(Acceptance Criteria)은 구조적 생존과 기능 성능을 모두 정의해야 한다. 로봇이 구조 검사에는 합격하더라도 위치 추정 오차, 센서 잡음, 제어 지연시간, 액추에이터 출력 또는 안전 응답이 허용 한계를 초과한다면 운용 요구사항을 충족하지 못할 수 있다. 따라서 임계값에는 핵심 기능에 허용되는 성능 저하 범위뿐만 아니라 즉시 시험을 종료해야 하는 조건도 포함해야 한다. 환경 민감도가 체계적인지, 간헐적인지 또는 특정 하드웨어 개체와 관련되는지를 판단하기 위해 반복 시험이 필요하다.

재현 가능한 모든 환경적 실패(Environmental Failure)는 회귀 시험 프로세스(Regression Process)에 포함해야 한다. 진동에 따른 센서 입력 손실, 열 사이클링 이후의 보정 드리프트, 추론 중 발생하는 열 스로틀링, 액추에이터 디레이팅 또는 전력 불안정성은 필요에 따라 서브시스템, 소프트웨어 인 더 루프(SIL), 하드웨어 인 더 루프(HIL) 또는 실제 시스템 수준에서 재구성할 수 있다. 수정 조치를 적용한 이후에는 동일한 스트레스 조건을 반복하여 개선 효과를 검증하고, 향후 하드웨어 또는 소프트웨어 개정에도 관련성이 있는 경우 해당 조건을 릴리스 시험(Release Test)으로 유지해야 한다.

진동 및 온도 스트레스 시험의 최종 목적은 휴머노이드 기능이 운용 대상으로 정의된 환경 조건 전반에서 제한 범위 안에 유지되고, 관찰 가능하며, 안전하게 동작한다는 것을 입증하는 것이다. 환경 적합성 검증(Environmental Qualification)은 단순히 하드웨어가 환경 노출을 견딘다는 것을 증명하는 것이 아니라 인식, 상태 추정, 제어, 연산, 전력 관리, 안전 기능이 지속적으로 올바르게 상호작용한다는 것을 보여주어야 한다. 이러한 검증 결과를 통해 휴머노이드가 정상적으로 운용될 수 있는 영역, 성능 저하 모드(Degraded Mode)가 필요한 영역, 그리고 운용을 금지해야 하는 영역을 명확하게 정의할 수 있다.

## 11.10. Humanoid SW Release Checklist and Certification

![](images/image10.png){width="7.268055555555556in" height="7.268055555555556in"}

휴머노이드 소프트웨어 릴리스(Humanoid Software Release)는 통합된 소프트웨어 기준선(Software Baseline)이 실제 로봇에 배포하기에 충분히 검증되었는지를 결정하는 최종 엔지니어링 의사결정이다. 일반적인 소프트웨어와 달리 휴머노이드 소프트웨어 릴리스는 움직임, 접촉력, 균형, 사람과의 상호작용에 직접적인 영향을 줄 수 있다. 따라서 릴리스 승인을 위해서는 인식, 상태 추정, 보행, 조작, 전신 제어, 인공지능 정책(AI Policy), 통신, 진단, 안전 기능이 정의된 한계 내에서 통합적으로 동작한다는 검증 근거가 필요하다.

릴리스 프로세스(Release Process)는 후보 기준선(Candidate Baseline)을 동결하는 것에서 시작해야 한다. 모든 실행 파일, 모델 체크포인트(Model Checkpoint), 구성 파일, 로봇 기술 모델(Robot Description), 제어기 매개변수 집합, 보정 패키지(Calibration Package), 펌웨어 의존성(Firmware Dependency), 미들웨어 버전, 외부 라이브러리는 식별 가능한 릴리스 후보(Release Candidate)와 연결되어야 한다. 소스 제어 커밋(Source-Control Commit)과 빌드 산출물(Build Artifact)은 추적 가능해야 하며, 이를 통해 시험된 로봇에 설치된 정확한 소프트웨어를 수동으로 기억한 구성 변경에 의존하지 않고 이후에도 재구성할 수 있어야 한다.

동일한 소스 코드라도 매개변수나 모델이 변경되면 서로 다른 로봇 동작이 발생할 수 있기 때문에 구성 관리(Configuration Management)는 특히 중요하다. 릴리스 체크리스트(Release Checklist)에서는 액추에이터 제한, 관절 오프셋(Joint Offset), 센서 보정, 좌표계, 제어 게인(Control Gain), 로봇 질량 특성, 인식 임계값, 네트워크 설정, 안전 매개변수를 검증해야 한다. 필요한 경우 로봇별 보정값은 공통 소프트웨어 구성과 분리해야 하며, 하드웨어 개정 버전과 소프트웨어 릴리스 사이의 호환성을 명시적으로 검증해야 한다.

소프트웨어 인 더 루프(SIL, Software-in-the-Loop) 검증은 릴리스 승인 전에 완료되어야 한다. 보행, 조작, 비전-언어-행동(VLA, Vision-Language-Action) 정책, 상태 추정, 계획, 실패 대응 시나리오는 요구되는 시뮬레이션 회귀 시험군(Simulation Regression Suite)을 통과해야 한다. 정상 조건만으로는 충분하지 않으며 경계 조건, 외란, 센서 성능 저하, 타이밍 변화, 과거 실패 사례도 포함해야 한다. 필수 회귀 시험 사례에서 실패가 발생하면 권한이 부여된 엔지니어링 절차를 통해 관련 제한사항을 명시적으로 문서화하고 승인하지 않는 한 릴리스를 차단해야 한다.

하드웨어 인 더 루프(HIL, Hardware-in-the-Loop) 검증에서는 실제 연산 장치, 통신, 센서, 액추에이터 또는 대표적인 로봇 하드웨어가 도입된 상태에서도 소프트웨어 동작이 허용 가능한 수준을 유지하는지 확인해야 한다. 제어기 타이밍, 통신 지연시간, 액추에이터 응답, 센서 동기화, 토크 동작, 워치독(Watchdog) 작동을 사전에 정의된 한계와 비교하여 측정해야 한다. HIL 결과는 결정론적 시뮬레이션 검증 근거와 완전한 실제 휴머노이드에서 발생하는 불확실성 사이를 연결하는 중요한 검증 단계가 된다.

실제 로봇 검증(Physical Robot Validation)은 목표 릴리스 범위에서 요구되는 통합 기능을 입증해야 한다. 제품 또는 연구 플랫폼의 목적에 따라 기립, 보행, 회전, 계단 이동, 팔 뻗기, 파지, 객체 운반, 전신 조작(Whole-Body Manipulation), 인간-로봇 상호작용(HRI, Human-Robot Interaction), 복구 동작이 포함될 수 있다. 릴리스에서는 문서화된 조건에서 실제로 시험된 기능만을 공식 기능으로 정의해야 한다. 통제된 시험 매트릭스(Test Matrix) 외부에서 성공한 시연만으로 인증된 운용 범위(Certified Operating Envelope)를 자동으로 확장해서는 안 된다.

안전 검증(Safety Verification)은 작업 성능 지표와 독립적인 릴리스 게이트(Release Gate)로 취급해야 한다. 비상 정지(Emergency Stop), 보호 정지(Protective Stop), 관절 및 토크 제한, 충돌 보호, 낙상 감지, 보호 낙상(Protective Falling), 낙상 후 동작, 워치독, 통신 손실 대응, 열 보호(Thermal Protection), 전력 관련 안전 기능에 대한 문서화된 시험 근거가 있어야 한다. 심각한 안전 실패를 성공적인 작업 성능과 평균화하여 평가해서는 안 된다. 해결되지 않은 고심각도 안전 결함이 존재하는 릴리스는 배포 대상에서 제외되어야 한다.

회귀 시험 결과(Regression Result)는 이전에 검증된 기능이 의도하지 않게 저하되지 않았음을 보여주어야 한다. 데이터셋, 모델 아키텍처, 미세조정(Fine-Tuning), 인식 인코더(Perception Encoder), 행동 디코딩(Action Decoding)의 변경으로 한 기능은 개선되면서 다른 기능이 손상될 수 있기 때문에 학습 기반 정책에서 특히 중요하다. 따라서 가능한 경우 반복 가능한 지표를 이용하여 후보 버전과 기준 버전(Baseline Version)을 안정적인 기존 작업, 새롭게 도입된 기능, 알려진 경계 사례(Edge Case), 과거 실패 시나리오 전반에서 비교해야 한다.

휴머노이드 제어에서는 기능적 정확성만으로 충분하지 않으므로 실시간 성능(Real-Time Performance)을 릴리스 적격성 평가에 포함해야 한다. 제어 루프 실행 시간, 추론 지연시간, 스케줄링 지터(Scheduling Jitter), 데드라인 미스(Missed Deadline), 통신 지연, 중앙처리장치(CPU) 및 그래픽처리장치(GPU) 사용률, 메모리 사용량, 오래된 데이터 이벤트(Stale-Data Event)는 정의된 예산 범위 안에 유지되어야 한다. 보행, 인식, 조작, 로깅, 인공지능 추론이 개별적으로는 통과하지만 동시에 실행할 때 실패하는 상황을 방지하기 위해 대표적인 동시 작업 부하(Concurrent Workload)에서 성능을 평가해야 한다.

릴리스가 실험실 환경을 넘어선 운용을 목적으로 하는 경우 환경 적합성 검증(Environmental Qualification) 근거를 검토해야 한다. 진동, 온도, 전력 변화, 조명, 표면 마찰, 음향 소음, 네트워크 성능 저하 및 기타 관련 환경 요인은 센서와 하드웨어를 통해 소프트웨어 동작에 영향을 줄 수 있다. 릴리스 체크리스트에서는 검증된 환경 범위와 필요한 성능 저하 모드(Degraded Mode)를 명확하게 정의해야 한다. 안전한 제어에 필요한 가정이 환경 조건에 의해 무효화된 경우에도 소프트웨어가 이를 감지하지 못한 채 정상 운용을 지속해서는 안 된다.

진단 범위(Diagnostic Coverage)는 운영자와 엔지니어가 운용 전과 운용 중에 로봇의 정상 상태를 판단할 수 있도록 해야 한다. 기동 자체 시험(Startup Self-Test), 센서 상태, 액추에이터 결함, 보정 유효성, 통신 상태, 열적 조건, 배터리 상태, 제어기 상태, 추정기 신뢰도(Estimator Confidence), 안전 상태 정보를 관찰할 수 있어야 한다. 결함 메시지는 단순히 일반적인 실패를 보고하는 것이 아니라 조치 가능한 상태를 식별해야 한다. 중요한 결함 정보는 시스템 종료 또는 재시작 이후에도 조사할 수 있도록 충분히 지속적으로 기록되어야 한다.

실제 휴머노이드에서 발생한 실패는 재현하기 어려울 수 있으므로 릴리스 전에 로깅(Logging)과 추적성(Traceability) 요구사항을 검증해야 한다. 시간 동기화된 기록에는 관련 센서 데이터, 상태 추정값, 제어 명령, 정책 출력, 안전 이벤트, 작업 상태 전환, 통신 결함, 시스템 타이밍 정보가 포함되어야 한다. 로그 형식과 타임스탬프는 분석 도구와 호환되어야 한다. 장시간 운용이나 반복 실험 중에도 중요한 검증 근거가 손실되지 않도록 저장 용량 제한과 보존 정책(Retention Policy)을 적절하게 정의해야 한다.

복구 및 재시작 동작(Recovery and Restart Behavior)은 명시적으로 적격성 평가를 받아야 한다. 소프트웨어는 제어기 실패, 통신 손실, 보호 정지, 비상 정지, 낙상, 전원 중단 또는 서브시스템 재시작 이후에 어떤 동작을 수행할 것인지 정의해야 한다. 활성 운용으로 복귀하려면 유효한 상태 추정, 정상적인 센서, 적절한 액추에이터 상태, 해제된 안전 조건, 필요한 운영자 승인이 확보되어야 한다. 이전에 중단된 운동 명령은 해당 동작이 명시적으로 설계되고 검증 및 승인된 경우를 제외하면 자동으로 재개되어서는 안 된다.

설치 및 배포 절차(Installation and Deployment Procedure) 자체도 시험해야 한다. 릴리스 패키지(Release Package)에는 호환 가능한 하드웨어, 의존성, 설치 순서, 구성 요구사항, 모델 파일, 펌웨어 가정, 검증 단계가 명시되어야 한다. 동등한 로봇에 배포할 경우 동일하게 식별 가능한 소프트웨어 기준선이 생성되어야 한다. 배포에 실패했을 때 호환되지 않는 구성이나 모델 산출물을 남기지 않고 로봇을 정상 동작이 확인된 이전 버전으로 복원할 수 있도록 업그레이드 및 롤백(Rollback) 절차도 검증해야 한다.

알려진 제한사항(Known Limitation)은 전체적인 성공 통계에 가리는 것이 아니라 릴리스 준비도의 일부로 문서화해야 한다. 지원하지 않는 지형, 탑재 하중 제한, 환경 한계, 인식 취약점, 사용할 수 없는 복구 모드, 필요한 인간 감독(Human Supervision), 제한된 HRI 기능 등을 명시적으로 기술해야 한다. 적절한 추가 안전 조치를 적용한 통제된 엔지니어링 실험이 아니라면 로봇이 의도적으로 이러한 한계 밖에서 사용되지 않도록 운용 절차를 마련해야 한다.

인증 근거(Certification Evidence)는 요구사항과 검증 결과를 연결해야 한다. 각각의 안전, 기능, 성능, 인터페이스, 환경 요구사항에는 분석, 검사, SIL, HIL 또는 실제 시험과 같은 식별 가능한 검증 방법(Verification Method)이 연결되어야 한다. 시험 보고서에는 구성, 절차, 합격 기준, 결과, 이상 현상(Anomaly), 최종 처리 결과(Disposition)를 기록해야 한다. 이러한 요구사항-검증 근거 추적성(Requirement-to-Evidence Traceability)은 내부 릴리스 승인과 필요한 경우 외부 적합성 평가(Conformity) 또는 인증 활동을 위한 체계적인 기반을 제공한다.

해결되지 않은 결함(Unresolved Defect)은 심각도, 운용 영향, 탐지 가능성, 적용 가능한 완화 조치(Mitigation)에 따라 분류해야 한다. 모든 경미한 결함이 반드시 연구용 릴리스를 차단하는 것은 아니지만, 이러한 결함의 수용은 우연이 아니라 명시적인 결정이어야 한다. 안전에 중요한 결함, 제어되지 않은 운동 위험, 손상된 상태 추정, 신뢰할 수 없는 정지 기능 또는 필수 기능을 무효화하는 실패는 일반적으로 배포를 차단해야 한다. 수용된 잔여 문제(Residual Issue)는 제한사항, 담당자, 향후 수정 조건과 함께 문서화해야 한다.

공식 릴리스 검토(Formal Release Review)에서는 소프트웨어, 제어, 인공지능, 하드웨어, 시험, 안전, 시스템 엔지니어링의 검증 근거를 통합해야 한다. 검토 과정에서는 필수 시험의 통과 여부, 예외 사항의 처리 여부, 문서의 완전성, 후보 버전이 실제 평가된 기준선과 정확하게 일치하는지를 확인해야 한다. 승인 권한(Approval Authority)은 사전에 정의해야 한다. 승인된 이후에는 릴리스 식별자(Release Identifier)를 변경할 수 없도록 유지하고, 이후의 수정 사항은 이미 승인된 소프트웨어 패키지를 조용히 변경하는 대신 새로운 릴리스 후보로 생성해야 한다.

릴리스 이후 모니터링(Post-Release Monitoring)은 검증 활동을 통제된 실제 운용 단계까지 확장한다. 현장 결함, 비정상적인 안전 이벤트, 성능 저하, 운영자 개입, 이전에 경험하지 못한 환경 조건을 수집하고 검토해야 한다. 재현 가능한 실패는 SIL, HIL 또는 실제 로봇 회귀 시험으로 다시 전달하고 필요한 경우 영구적인 시험 사례로 포함해야 한다. 따라서 릴리스 프로세스는 검증의 종료점이 아니라 실제 운용을 통한 학습과 소프트웨어 개선이 지속되는 순환 과정 안의 통제된 체크포인트(Controlled Checkpoint)이다.

휴머노이드 소프트웨어 릴리스 체크리스트 및 인증 프로세스의 최종 목적은 다양한 시험 결과를 방어 가능한 배포 의사결정(Defensible Deployment Decision)으로 전환하는 것이다. 릴리스 준비도(Release Readiness)는 성공적인 시연만으로 결정되지 않는다. 소프트웨어 기준선을 명확하게 식별할 수 있어야 하고, 요구사항을 추적할 수 있어야 하며, 회귀가 통제되고, 안전 기능이 검증되며, 운용 한계가 문서화되고, 복구 동작이 충분히 이해되어야 한다. 이러한 검증 근거가 완전하게 확보된 이후에만 통합 휴머노이드 소프트웨어를 정의된 운용 범위에 대해 승인해야 한다.
