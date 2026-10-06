**Volume 22. Humanoid Robot Software**

# Chapter 04. Balance and Posture Control

## 04.01. Humanoid Balance Theory CoM CoP ZMP

![](images/image1.png){width="7.268055555555556in" height="7.268055555555556in"}

휴머노이드 균형(Humanoid Balance)은 근본적으로 로봇의 질량 분포(Mass Distribution), 지면 접촉력(Ground Contact Force), 그리고 허용 가능한 지지 영역(Support Region) 사이의 관계를 조절하는 문제이다. 고정 베이스 매니퓰레이터(Fixed-Base Manipulator)와 달리 휴머노이드는 부유 몸체(Floating Body)가 현재 이용 가능한 접촉(Contact)을 통해 제어할 수 없는 운동 상태로 발전하지 않도록 지속적으로 제어해야 한다. 따라서 질량중심(Center of Mass, CoM), 압력중심(Center of Pressure, CoP), 영모멘트점(Zero Moment Point, ZMP)은 각각 기하학적(Geometric), 힘 상호작용(Force Interaction), 동역학적(Dynamic) 관점에서 균형을 상호보완적으로 설명한다.

질량중심(Center of Mass, CoM)은 로봇을 구성하는 모든 링크(Link)의 질량 가중 평균 위치(Mass-Weighted Average Position)를 나타내며, 휴머노이드의 전체 형상을 압축하여 표현하는 중요한 상태 변수이다. 각 링크의 질량을 \\(m_i\\), 개별 질량중심 위치를 \\(p_i\\)라고 하면 전체 질량중심은 \\(p_{CoM}=\\sum_i m_i p_i/\\sum_i m_i\\)로 계산할 수 있다. 관절 운동(Joint Motion), 몸통 기울기(Torso Inclination), 팔 운동(Arm Motion), 물체 운반(Payload Manipulation), 다리 자세(Leg Configuration)는 모두 이 위치를 변화시키므로 CoM 제어는 다리만의 문제가 아니라 전신 제어(Whole-Body Control)의 문제이다.

정적 균형(Static Balance)을 판단하는 첫 번째 근사 방법은 질량중심(CoM)을 지면으로 투영한 위치를 이용하는 것이다. 수직 투영점(Vertical Projection)이 활성 접촉(Active Contact)에 의해 형성되는 지지 다각형(Support Polygon) 내부에 존재하면 일반적으로 지속적인 가속 없이 중력 하중(Gravity Load)의 균형을 유지할 수 있다. 양발 지지(Double Support)에서는 지지 다각형이 두 발에 걸쳐 형성되지만, 한발 지지(Single Support)에서는 허용 영역이 대략 지지하고 있는 한쪽 발의 영역으로 축소된다. 이러한 기하학적 기준은 직관적이지만 큰 가속도, 각운동량(Angular Momentum), 외란(External Disturbance)이 발생하면 충분하지 않다.

압력중심(Center of Pressure, CoP)은 접촉면(Contact Surface)에 작용하는 합력(Resultant Ground Reaction Force)이 해당 표면에 대한 접선 방향 모멘트(Tangential Moment)를 발생시키지 않는 유효 작용점을 나타낸다. 발의 힘/토크 센서(Force/Torque Sensor)를 사용하면 측정된 접촉 렌치(Contact Wrench) 성분으로부터 CoP를 추정할 수 있으며, 이를 통해 제어기는 로봇이 발바닥에 하중을 어떻게 분배하고 있는지 관찰할 수 있다. CoP가 발의 경계에 가까워진다는 것은 접촉 여유(Contact Margin)가 감소한다는 의미이며, 요구되는 압력 위치가 실제 지지 영역을 벗어나면 가정된 평발 접촉(Flat-Foot Contact)으로는 필요한 균형 렌치(Balancing Wrench)를 더 이상 생성할 수 없다.

CoP는 전신 운동(Whole-Body Motion)을 실제 측정 가능한 지면 상호작용(Ground Interaction)과 연결한다는 점에서 특히 유용하다. 서기 제어기(Standing Controller)는 CoM을 가속하기 위해 의도적으로 CoP를 발가락이나 뒤꿈치 방향으로 이동시킬 수 있으며, 좌우 방향의 CoP 운동을 이용하여 측면 흔들림(Lateral Sway)을 조절할 수도 있다. 그러나 CoP는 접촉 형상(Contact Geometry)과 마찰(Friction)에 의해 제한된다. 제어기는 지지면 밖으로 CoP를 임의로 명령할 수 없으며, 큰 수평력이 발생하면 원하는 압력점이 기하학적으로 발 내부에 존재하더라도 마찰 원뿔(Friction Cone) 조건을 위반할 수 있다.

영모멘트점(Zero Moment Point, ZMP)은 균형에 대한 설명을 동적 운동(Dynamic Motion)으로 확장한다. ZMP는 일반적으로 중력(Gravity)과 관성력(Inertial Force)에 의해 생성되는 합성 모멘트(Resultant Moment)의 수평 성분이 0이 되는 지지 평면(Support Plane)상의 점으로 정의된다. 계산되거나 명령된 ZMP가 실행 가능한 지지 영역(Feasible Support Region) 내부에 유지된다면 모델의 가정하에서 지면 접촉은 계획된 운동에 필요한 렌치(Wrench)를 생성할 수 있다. 따라서 ZMP는 전통적인 휴머노이드 보행(Humanoid Walking)과 자세 안정화(Posture Stabilization)의 핵심 개념으로 발전하였다.

선형 역진자 모델(Linear Inverted Pendulum Model, LIPM)을 적용하면 수평 방향 CoM 위치와 ZMP 사이의 관계가 특히 명확해진다. CoM 높이 \\(z_c\\)가 거의 일정하고, 각운동량 변화가 무시할 수 있을 정도로 작으며, 지지면이 평평하다고 가정하면 전후 방향 관계는 \\(x_{ZMP}=x_{CoM}-(z_c/g)\\ddot{x}_{CoM}\\)로 표현할 수 있으며 좌우 방향에도 동일한 형태의 식을 적용할 수 있다. 따라서 ZMP는 단순한 CoM의 지면 투영점이 아니며, 가속도에 의해 CoM과 ZMP 사이에 동적인 위치 차이가 발생한다.

이러한 차이는 순간적인 CoM 투영점이 단순한 정적 경계에 접근하거나 일시적으로 이를 벗어나더라도 휴머노이드가 동적 균형(Dynamic Balance)을 유지할 수 있는 이유를 설명한다. 몸체를 적절하게 가속하고 미래 접촉(Future Contact)의 위치를 변경함으로써 제어기는 단순히 정적 기하학 조건을 만족시키는 대신 시간에 따라 변화하는 동역학을 관리할 수 있다. 반대로 CoM 투영점이 지지 다각형 내부에 있더라도 운동량(Momentum)이 로봇을 현재 접촉력으로 회복할 수 없는 상태로 빠르게 이동시키고 있다면 강인한 동적 안정성(Dynamic Stability)이 보장되지 않는다.

따라서 CoM, CoP, ZMP는 서로 관련되어 있지만 동일하지 않은 물리량으로 해석해야 한다. CoM은 로봇의 질량 분포를 나타내고, CoP는 측정되거나 명령된 합성 접촉 압력(Resultant Contact Pressure)의 작용 위치를 나타내며, ZMP는 동적인 모멘트 균형 조건(Dynamic Moment-Balance Condition)을 표현한다. 이상적인 평면 평발 접촉(Planar Flat-Foot Contact)에서는 측정된 CoP와 ZMP가 거의 일치할 수 있지만 개념적인 역할은 서로 다르다. 실제 소프트웨어에서는 센서 추정(Sensor Estimation), 모델 예측(Model Prediction), 기준 궤적 생성(Reference Generation)이 이 값들을 서로 다른 방식으로 사용하기 때문에 이러한 구분을 유지해야 한다.

지지 다각형(Support Polygon)은 일반적으로 CoP와 ZMP에 관련된 기하학적 실행 가능 영역(Geometric Feasibility Region)을 제공한다. 여러 개의 동일 평면 접촉(Coplanar Contact)이 존재하는 경우 활성 접촉 영역들의 볼록 껍질(Convex Hull)을 이용하여 지지 다각형을 구성할 수 있다. 실제 제어기는 정확한 경계까지 모두 사용하는 대신 안전 여유(Safety Margin)를 유지하는 경우가 일반적이다. 모델 불확실성(Model Uncertainty), 발바닥 순응성(Sole Compliance), 힘 센서 잡음(Force-Sensor Noise), 접촉 변형(Contact Deformation), 타이밍 오차(Timing Error), 마찰 변화(Friction Variation) 등이 실제 사용 가능한 안정성 여유(Stability Margin)를 감소시키기 때문이다. 따라서 경계에 가까운 ZMP 궤적은 수학적으로 가능하더라도 실제 운용에서는 취약할 수 있다.

휴머노이드 균형은 단순한 ZMP와 LIPM 공식에서 의도적으로 단순화하는 각운동량(Angular Momentum)의 영향도 받는다. 빠른 팔 스윙(Arm Swing), 몸통 회전(Torso Rotation), 조작력(Manipulation Force), 전신 회복 운동(Whole-Body Recovery Motion)은 상당한 질량중심 각운동량(Centroidal Angular Momentum)을 발생시킬 수 있다. 보다 완전한 모델은 질량중심 동역학(Centroidal Dynamics)을 사용하여 CoM 가속도, 접촉력(Contact Force), 접촉 모멘트(Contact Moment), 각운동량 변화율을 서로 연결한다. 이를 통해 로봇은 상체 운동을 단순한 외란으로 취급하지 않고 균형 조절의 일부로 적극적으로 활용할 수 있다.

외부 외란(External Disturbance)은 균형 유지 메커니즘의 계층 구조를 명확하게 보여준다. 작은 외란은 발목 토크(Ankle Torque)를 변경하고 CoP를 발 내부에서 이동시키는 방법으로 제거할 수 있다. 더 큰 외란에는 전신 운동량(Whole-Body Momentum)을 변화시키기 위한 엉덩이와 몸통 운동(Hip and Torso Motion)이 필요할 수 있다. 기존 지지 영역 내에서 필요한 지면 반력(Ground Reaction Force)을 더 이상 생성할 수 없다면 로봇은 회복 스텝(Recovery Step)을 통해 접촉 구성(Contact Configuration)을 변경해야 한다. 따라서 균형은 단순히 CoM을 발 위에 유지하는 문제가 아니라 실행 가능한 운동량(Feasible Momentum)과 접촉 전환(Contact Transition)을 관리하는 문제로 이해하는 것이 적절하다.

전후 방향(Sagittal Direction)과 좌우 방향(Lateral Direction)은 실제 제어에서 서로 다른 제약 조건을 가진다. 사람과 유사한 형태의 발은 일반적으로 좌우 폭보다 전후 방향으로 더 긴 지지 영역을 제공하며, 다리 구성(Leg Configuration)과 자체 충돌(Self-Collision) 또한 실행 가능한 운동 범위에 영향을 미친다. 따라서 제어기는 방향에 따라 서로 다른 안정성 여유를 고려해야 한다. 보행 중에는 지지 영역이 양발 지지(Double Support)와 한발 지지(Single Support) 사이에서 불연속적으로 변화하므로 빠르게 변화하는 접촉 제약(Contact Constraint)에도 불구하고 부드러운 기준 궤적 생성(Smooth Reference Generation)이 필요하다.

신뢰할 수 있는 균형 제어 소프트웨어(Balance Control Software)는 상태 추정(State Estimation)에 크게 의존한다. 관절 인코더(Joint Encoder)와 로봇 모델(Robot Model)을 이용하여 링크 구성과 CoM을 추정하며, 관성측정장치(Inertial Measurement Unit, IMU)는 베이스 자세(Base Orientation)와 각속도(Angular Velocity)를 제공한다. 발의 힘/토크 센싱(Force/Torque Sensing)은 접촉 렌치와 CoP 정보를 제공하며, 접촉 추정(Contact Estimation)은 어떤 발이나 다른 신체 표면이 지지 모델(Support Model)에 포함되어야 하는지를 결정한다. 부유 베이스 자세(Floating-Base Pose)나 접촉 상태에 오차가 발생하면 기본 제어기의 수학적 설계가 정확하더라도 추정된 안정성 상태가 잘못될 수 있다.

실제 제어 아키텍처(Control Architecture)에서는 목표 자세와 운동이 CoM 및 운동량 목표(Momentum Objective)를 생성하고, 계획기(Planner)는 실행 가능한 접촉과 ZMP 또는 CoP 기준 궤적을 생성한다. 이후 하위 수준의 전신 제어기(Whole-Body Controller)는 마찰, 액추에이터(Actuator), 운동학(Kinematic), 접촉 제약을 만족시키면서 필요한 힘과 관절 토크(Joint Torque)를 사용 가능한 접촉점에 분배한다. 이러한 구조는 균형 이론(Balance Theory)을 이후의 서기 제어(Standing Control), 밀림 회복(Push Recovery), 자세 조절(Posture Regulation), 운동량 제어(Momentum Control), 부유 베이스 상태 추정(Floating-Base State Estimation) 기능과 자연스럽게 연결한다.

따라서 균형 성능(Balance Quality)은 단순한 안정 또는 불안정의 이진 조건(Binary Condition)이 아니라 다양한 여유도(Margin)를 이용하여 평가해야 한다. 유용한 평가 변수에는 CoP 또는 ZMP와 지지 경계 사이의 최소 거리, CoM 추종 오차(CoM Tracking Error), 질량중심 운동량(Centroidal Momentum), 접촉력 분배(Contact-Force Distribution), 마찰 여유(Friction Margin), 몸통 자세(Torso Orientation), 외란 이후의 회복 능력(Recovery Capability) 등이 포함된다. 이러한 변수들을 시간에 따라 지속적으로 관찰하면 특정 순간의 한 점이 지지 다각형 내부에 존재하는지만 검사하는 것보다 강인성(Robustness)을 훨씬 의미 있게 평가할 수 있다.

궁극적으로 CoM, CoP, ZMP는 휴머노이드 균형을 이해하기 위한 기초적인 개념 체계(Foundational Vocabulary)를 구성하지만, 어느 하나도 그 자체만으로 완전한 안정성 이론(Complete Stability Theory)이 될 수는 없다. CoM은 전체 질량의 운동을 나타내고, CoP는 지지면과의 실제 물리적 상호작용(Physical Interaction)을 나타내며, ZMP는 목표 동역학(Desired Dynamics)을 실행 가능한 접촉 모멘트(Feasible Contact Moment)와 연결한다. 이 세 개념을 통합하여 해석하면 정적 자세(Static Posture)에서 동적 균형, 외란 회복, 전신 운동량 조절, 그리고 접촉과 질량 분포가 지속적으로 변화하는 이동-조작(Locomotion-Manipulation) 행동으로 확장되는 이론적 기반을 구축할 수 있다.

## 04.02. Standing Balance Controller Ankle Hip Strategy [w/Code]

![](images/image2.png){width="7.268055555555556in" height="7.268055555555556in"}

서기 균형 제어(Standing Balance Control)는 휴머노이드 로봇(Humanoid Robot)이 모델링 오차(Modeling Error), 센서 잡음(Sensor Noise), 작은 지면 변화(Surface Variation), 내부 신체 운동(Internal Body Motion), 외부 외란(External Disturbance)을 보상하면서 직립 자세(Upright Posture)를 유지하도록 한다. 목표 자세가 명목상 정적 상태라 하더라도 로봇은 동적으로 결합된 부유 베이스 시스템(Dynamically Coupled Floating-Base System)으로 동작한다. 따라서 실제 제어기는 고정된 관절 각도만 유지하는 것이 아니라 몸체 자세(Body Orientation), 질량중심 운동(Center of Mass Motion), 접촉력(Contact Force), 관절 토크(Joint Torque)를 지속적으로 조절해야 한다.

서기 제어기(Standing Controller)는 신뢰할 수 있는 상태 추정(State Estimation)과 접촉 추정(Contact Estimation)을 기반으로 동작한다. 관절 인코더(Joint Encoder)는 관절 구조의 상태를 제공하고, 관성측정장치(Inertial Measurement Unit, IMU)는 몸통 자세(Torso Orientation)와 각속도(Angular Velocity)를 추정한다. 발의 힘/토크 센서(Force/Torque Sensor)는 지면 반력(Ground Reaction Force)과 압력중심(Center of Pressure, CoP) 정보를 제공한다. 이러한 측정값은 로봇 모델과 융합되어 CoM 위치와 속도, 부유 베이스 운동(Floating-Base Motion), 접촉 상태(Contact State), 균형 피드백 루프(Balance Feedback Loop)에 필요한 안정성 여유(Stability Margin)를 추정하는 데 사용된다.

기준 서기 자세(Nominal Standing Posture)는 안정화 제어가 수행되는 평형 상태(Equilibrium)를 정의한다. 목표 구성은 일반적으로 CoM을 두 발 사이에 위치시키고, 몸통을 거의 수직으로 유지하며, 무릎과 엉덩이에 적절한 굽힘을 유지하고, 수직 하중(Vertical Load)을 양발에 분배하도록 설정한다. 또한 기준 자세는 관절 한계(Joint Limit)를 피하고 충분한 토크 여유(Torque Reserve)를 확보해야 한다. 기하학적으로 안정된 자세라 하더라도 액추에이터(Actuator)가 포화 상태라면 외란을 제거할 수 있는 실제 제어 능력은 매우 제한될 수 있기 때문이다.

발목 전략(Ankle Strategy)은 비교적 작고 느린 외란에 대응하는 기본적인 균형 회복 방법이다. 휴머노이드는 양발이 지면에 안정적으로 접촉한 상태에서 발목 영역을 중심으로 회전하는 역진자(Inverted Pendulum)와 유사하게 동작한다. 보정 발목 토크(Corrective Ankle Torque)는 사용 가능한 발 영역 내에서 CoP를 이동시키고, 이를 통해 수평 지면 반력(Horizontal Ground Reaction Force)을 생성하여 상체를 크게 움직이지 않고도 CoM을 목표 상태로 다시 가속시킨다.

시상 방향(Sagittal Direction)에서는 발목 피치 토크(Ankle Pitch Torque)가 주로 전후 방향의 흔들림을 조절한다. 로봇이 앞으로 넘어지기 시작하면 제어기는 발목 토크와 이에 따른 압력 분포(Pressure Distribution)를 변경하여 복원 효과(Restoring Effect)를 발생시킨다. 발목 롤(Ankle Roll)은 좌우 방향 흔들림에 대해 유사한 기능을 수행한다. 이러한 동작은 전체 자세를 크게 변화시키지 않고 비교적 작은 관절 운동만 필요로 하기 때문에 요구되는 CoP가 지지 다각형(Support Polygon) 내부에 충분한 여유를 두고 존재하는 경우 발목 조절(Ankle Regulation)이 우선적으로 사용된다.

간단한 발목 제어기(Ankle Controller)는 몸체 각도(Body Angle), 각속도, CoM 오차(CoM Error), 또는 CoP 오차(CoP Error)의 피드백을 이용하여 구성할 수 있다. 비례 및 미분 항(Proportional and Derivative Terms)은 목표 평형 상태로부터의 편차에 따라 보정 토크를 생성한다. 보다 발전된 구현에서는 상태공간 제어(State-Space Control), 선형 이차 조절기(Linear Quadratic Regulation, LQR), 모델 예측 제어(Model Predictive Control, MPC), 전신 최적화(Whole-Body Optimization)를 사용할 수 있지만, 기본적인 목적은 동일하다. 즉, 지지 여유가 소진되기 전에 불안정한 운동을 반전시킬 수 있는 실행 가능한 접촉 렌치(Feasible Contact Wrench)를 생성하는 것이다.

발목 제어의 효과는 근본적으로 발의 형상(Foot Geometry)과 접촉 조건(Contact Condition)에 의해 제한된다. 명령된 CoP는 실제 발바닥 영역을 벗어날 수 없으며, 수평 지면 반력은 사용 가능한 마찰(Friction) 조건을 만족해야 한다. 액추에이터의 토크 및 속도 한계(Torque and Velocity Limit) 역시 보정 동작을 제한한다. 외란의 크기가 증가하면 발목 제어기는 물리적으로 생성할 수 없는 압력 위치 또는 접촉 렌치를 요구할 수 있으며, 이는 다른 균형 메커니즘(Balance Mechanism)이 주도적으로 작동해야 함을 의미한다.

엉덩이 전략(Hip Strategy)은 발목 조절만으로 대응하기에는 지나치게 빠르거나 큰 외란을 처리한다. 전체 몸체를 거의 강체 역진자(Rigid Inverted Pendulum)로 취급하는 대신 제어기는 상체와 하체 사이에 의도적인 상대 운동(Relative Motion)을 발생시킨다. 빠른 엉덩이 및 몸통 운동은 전신 각운동량(Whole-Body Angular Momentum)을 변화시키고 CoM 동역학을 수정함으로써 로봇이 지지 접촉 구성(Support Contact Configuration)을 즉시 변경하지 않고도 외란에 대응할 수 있도록 한다.

예를 들어 휴머노이드가 앞으로 밀렸을 경우, 조정된 엉덩이 신전(Hip Extension)과 몸통 운동(Torso Motion)을 통해 넘어지는 방향의 운동에 반대되는 각운동량을 생성할 수 있다. 이 과정에는 다리, 골반(Pelvis), 몸통, 경우에 따라 팔까지 참여한다. 상체 세그먼트(Upper-Body Segment)는 상당한 질량을 가지므로 비교적 작은 관절 운동만으로도 질량중심 운동량(Centroidal Momentum)에 큰 영향을 줄 수 있다. 따라서 엉덩이 전략은 CoP 조절만으로 회복할 수 있는 외란의 범위를 넘어 더 큰 외란에 대응할 수 있도록 한다.

발목 전략과 엉덩이 전략은 일반적으로 완전히 독립된 동작 모드(Operating Mode)로 구현해서는 안 된다. 사람과 유사한 강인한 균형(Robust Balance)은 두 메커니즘을 협조적으로 사용함으로써 구현된다. 작은 외란은 주로 발목 토크를 통해 처리할 수 있지만 외란의 크기가 증가하면 엉덩이와 몸통 운동을 점진적으로 활성화해야 한다. 연속적인 혼합 메커니즘(Continuous Blending Mechanism)을 사용하면 제어기의 갑작스러운 전환을 방지하면서 사용 가능한 지지 여유와 운동량 제어 능력(Momentum Authority)을 동시에 활용할 수 있다.

실용적인 혼합 변수(Blending Variable) 중 하나는 남아 있는 CoP 여유(CoP Margin)이다. 측정되거나 예측된 CoP가 발 중심 부근에 존재하는 동안에는 제어기가 발목 조절에 더 큰 제어 권한(Control Authority)을 부여할 수 있다. 그러나 CoP가 지지 경계(Support Boundary)에 접근하면 접촉 렌치가 실행 가능 한계(Feasibility Limit)에 가까워지므로 추가적인 발목 토크의 효과가 감소한다. 이때 제어기는 균형 상태가 회복 불가능한 수준에 도달하기 전에 엉덩이, 몸통, 전신 운동량 목표(Whole-Body Momentum Objective)의 비중을 증가시킬 수 있다.

주파수 특성(Frequency Characteristics)은 전략 선택을 이해하는 또 다른 방법을 제공한다. 느린 몸체 흔들림(Body Sway)은 CoP를 이동시키고 CoM을 점진적으로 가속할 충분한 시간이 있기 때문에 발목 운동으로 효율적으로 보정할 수 있다. 반면 빠른 외란은 더욱 즉각적인 반응을 요구하므로 상체를 이용한 빠른 각운동량 생성이 유용하다. 따라서 제어기는 고정된 임계값(Fixed Threshold)에만 의존하지 않고 외란의 크기와 주파수를 함께 고려하여 발목과 엉덩이 관절 사이에 보정 동작을 분배할 수 있다.

서기 균형은 시상 방향과 좌우 방향에 대한 협조 제어(Coordinated Control)도 필요로 한다. 휴머노이드의 발은 일반적으로 전후 방향 길이에 비해 좌우 방향 폭이 좁기 때문에 측면 안정성(Lateral Stability)이 더욱 제한적인 경우가 많다. 엉덩이 외전 및 내전(Hip Abduction and Adduction), 발목 롤, 골반 이동(Pelvis Translation), 몸통 기울임(Torso Lean)을 협조적으로 제어하여 측면 균형을 유지할 수 있다. 좁은 스탠스(Narrow Stance)에서는 사용 가능한 측면 CoP 범위가 특히 작아지므로 전신 운동량 조절과 정확한 상태 추정의 중요성이 더욱 증가한다.

두 발 사이의 힘 분배(Force Distribution) 역시 서기 제어의 중요한 계층을 구성한다. 양발 지지(Double Support) 상태에서 제어기는 각 발 내부의 CoP뿐만 아니라 왼쪽과 오른쪽 다리가 담당하는 전체 하중의 비율도 변경할 수 있다. 적절한 하중 이동(Load Transfer)은 측면 안정화를 위한 실질적인 제어 능력을 확대하고 이후의 보행 동작을 준비할 수 있도록 한다. 그러나 한쪽 발의 하중을 지나치게 감소시키면 마찰력을 사용할 수 있는 능력이 저하되거나 의도하지 않은 접촉 전환(Contact Transition)이 발생할 수 있다.

현대적인 구현에서는 서기 안정화(Standing Stabilization)를 전신 제어 목표(Whole-Body Objective)의 계층 구조로 표현하는 경우가 많다. 높은 우선순위의 제약 조건은 유효한 발 접촉, 마찰 한계(Friction Limit), 액추에이터 한계, 안전한 관절 구성을 유지한다. 균형 목표는 CoM 가속도, 몸통 자세, 질량중심 운동량, 접촉 렌치를 조절한다. 낮은 우선순위의 자세 목표(Posture Objective)는 안정성을 유지하는 데 필요한 힘을 방해하지 않으면서 팔, 골반, 무릎 및 기타 관절을 편안한 구성으로 유지한다.

제어기는 또한 순응 구조(Compliant Structure)와 불완전한 지면 접촉(Imperfect Ground Contact)을 고려해야 한다. 실제 휴머노이드의 발, 신발, 힘 센서, 변속 장치(Transmission), 관절 메커니즘에는 이상적인 강체 접촉(Rigid Contact) 모델과 다른 탄성(Elasticity)이 존재한다. 고르지 않은 지면은 유효 접촉 형상을 추가로 변화시킬 수 있다. 지나치게 공격적인 피드백 게인(Feedback Gain)은 구조적 진동(Structural Oscillation)을 유발할 수 있으며, 게인이 지나치게 낮으면 몸체 흔들림이 커질 수 있다. 따라서 필터링(Filtering), 감쇠(Damping), 접촉 추정, 실험적으로 검증된 게인 스케줄링(Gain Scheduling)이 중요하다.

외란 감시(Disturbance Monitoring)는 일반적인 서기 안정화에서 능동적인 회복 동작(Active Recovery Behavior)으로 전환하기 위한 기반을 제공한다. CoM 속도, 몸체 각속도, CoP 여유, ZMP 여유(ZMP Margin), 질량중심 운동량, 예측된 미래 균형 상태(Predicted Future Balance State)를 이용하여 현재의 발목 및 엉덩이 대응만으로 충분한지를 판단할 수 있다. 측정된 CoP가 발의 경계에 도달할 때까지 기다리는 대신 예측 로직(Predictive Logic)을 이용하면 현재 접촉으로 운동을 정지시킬 수 없는 상태가 가까워지고 있음을 사전에 판단할 수 있다.

발목 전략과 엉덩이 전략 모두로 균형을 회복할 수 없다면 제어기는 기존 발 위치를 유지하는 것이 더 이상 올바른 목표가 아니라는 것을 인식해야 한다. 이 시점에서는 외란 방향으로 새로운 지지 영역을 형성하는 스테핑 전략(Stepping Strategy)이 필요하다. 이를 통해 자연스러운 회복 동작의 계층 구조가 형성된다. 작은 외란은 발목 조절로 처리하고, 더 큰 외란은 엉덩이 및 전신 운동량 조절로 처리하며, 고정된 발 접촉으로 회복할 수 있는 범위를 넘어서는 외란은 접촉 위치 변경(Contact Relocation)을 통해 처리한다.

서기 제어기의 검증(Validation)은 로봇이 단순히 넘어지지 않았는지만 평가해서는 안 된다. 주요 측정 항목에는 최대 CoM 변위(Peak CoM Displacement), 정착 시간(Settling Time), 몸통 각도 편차(Torso-Angle Deviation), CoP 이동량(CoP Excursion), 접촉력 분배, 최대 관절 토크(Maximum Joint Torque), 잔류 진동(Residual Oscillation), 그리고 스텝 없이 제거할 수 있는 외란의 최대 크기가 포함된다. 서로 다른 방향과 크기의 반복적인 외란 시험을 수행하면 실제 운용 영역(Operating Envelope) 전체에서 발목-엉덩이 협조 제어(Ankle-Hip Coordination)가 일관되게 유지되는지를 평가할 수 있다.

따라서 강인한 서기 균형 제어기(Robust Standing Balance Controller)는 하나의 안정화 메커니즘에만 의존하지 않고 국부적인 발목 토크 조절(Local Ankle Torque Regulation)과 협조된 엉덩이 및 전신 운동(Coordinated Hip and Whole-Body Motion)을 결합한다. 발목 전략은 사용 가능한 접촉 압력 영역(Contact-Pressure Region)을 효율적으로 활용하고, 엉덩이 전략은 그 영역만으로 충분하지 않을 때 내부 신체 운동과 각운동량을 활용한다. 이러한 두 전략의 협조 동작은 밀림 회복(Push Recovery), 자세 조절(Posture Regulation), 운동량 기반 균형 제어(Momentum-Based Balance Control), 그리고 정지 상태에서 동적 휴머노이드 운동(Dynamic Humanoid Motion)으로 안전하게 전환하기 위한 기반을 제공한다.

## 04.03. Push Recovery from External Disturbance [w/Code]

![](images/image3.png){width="7.268055555555556in" height="7.268055555555556in"}

밀림 회복(Push Recovery)은 예상하지 못한 외부 외란(External Disturbance)이 휴머노이드 로봇(Humanoid Robot)의 자세, 속도 또는 운동량(Momentum)을 변화시킨 이후 다시 안정적인 상태를 회복하는 능력이다. 외란은 사람과의 접촉, 물체와의 충돌, 조작 작업에서 발생하는 반력(Reaction Force), 불규칙한 지형, 계획된 운동의 오차 등에서 발생할 수 있다. 효과적인 회복을 위해서는 불안정한 운동이 되돌릴 수 없는 상태에 도달하기 전에 외란을 신속하게 추정하고 접촉력(Contact Force), 신체 운동량(Body Momentum), 접촉 위치 변경(Contact Relocation)을 협조적으로 사용해야 한다.

일반적인 서기 안정화(Standing Stabilization)와 달리 밀림 회복은 시간에 따라 변화하는 로봇의 동적 상태(Dynamic State)를 명시적으로 고려해야 한다. 충격(Impulse)이 발생한 직후 질량중심(Center of Mass, CoM)은 여전히 지지 다각형(Support Polygon) 내부에 존재할 수 있지만, 이미 CoM 속도에 의해 로봇이 넘어지는 방향으로 이동하고 있을 수 있다. 따라서 위치 기반 안정성 지표(Position-Based Stability Measure)만으로는 충분하지 않다. 회복 로직은 CoM 속도, 몸체 각속도(Body Angular Velocity), 접촉 렌치(Contact Wrench), 압력중심(Center of Pressure, CoP), 질량중심 운동량(Centroidal Momentum), 그리고 현재 접촉을 이용하여 운동을 감속시킬 수 있는 잔여 능력을 함께 평가해야 한다.

회복 가능성(Recoverability)을 이해하는 데 유용한 개념이 캡처 포인트(Capture Point)이다. CoM 높이가 일정한 단순화된 선형 역진자 모델(Linear Inverted Pendulum Model, LIPM)에서 캡처 포인트는 로봇이 지지점을 배치했을 때 추가적인 연속 스텝 없이 최종적으로 정지할 수 있는 지면상의 위치를 의미한다. 하나의 수평 방향에서는 \\(x_{CP}=x_{CoM}+\\dot{x}_{CoM}/\\omega_0\\)로 근사할 수 있으며, 여기서 \\(\\omega_0=\\sqrt{g/z_c}\\)이다. 이 식에 CoM 속도 항이 포함되므로 캡처 포인트는 본질적으로 동적인 안정성 기준이다.

캡처 포인트가 사용 가능한 지지 영역(Available Support Region) 내부에 유지되는 경우 발 위치를 변경하지 않고도 외란을 제거할 수 있는 경우가 많다. 제어기는 CoP를 이동시키고 적절한 지면 반력(Ground Reaction Force)을 생성하여 CoM을 감속할 수 있다. 반대로 캡처 포인트가 실행 가능한 지지 영역(Feasible Support Region)을 벗어나면 단순화된 모델에서 고정된 발을 이용한 회복(Fixed-Foot Recovery)은 점점 어려워지거나 불가능해진다. 따라서 이 기준은 발목 조절(Ankle Regulation)에서 엉덩이 운동(Hip Motion) 또는 스테핑(Stepping)으로 회복 전략을 전환하는 물리적으로 의미 있는 판단 기준을 제공한다.

첫 번째 회복 메커니즘은 일반적으로 발목 전략(Ankle Strategy)이다. 보정 발목 토크(Corrective Ankle Torque)는 CoP를 발의 경계 방향으로 이동시키고 균형을 회복하기 위한 수평력을 생성한다. 이 방법은 전체 자세를 크게 변화시키지 않고 기존 접촉 구성(Contact Configuration)을 유지할 수 있기 때문에 효율적이다. 그러나 그 효과는 발바닥 크기, 마찰(Friction), 발목 토크 능력, 그리고 현재 CoP와 지지 경계(Support Boundary) 사이에 남아 있는 거리에 의해 제한된다.

더 큰 외란에 대해서는 로봇이 엉덩이 전략(Hip Strategy)과 상체 운동(Upper-Body Motion)을 활용할 수 있다. 몸통(Torso), 골반(Pelvis), 팔을 빠르게 회전시키면 질량중심 각운동량(Centroidal Angular Momentum)이 변화하고 CoM 운동과 지면 반력 사이의 관계도 변경된다. 이러한 메커니즘은 발목 토크만으로 가능한 범위를 넘어 회복 능력을 일시적으로 확장할 수 있다. 그러나 과도한 상체 회전은 이후 다시 제거해야 하는 이차적인 운동량(Secondary Momentum)을 생성할 수 있으므로 제어기는 이러한 운동을 세심하게 협조해야 한다.

팔 운동(Arm Motion) 역시 외란 제거에 크게 기여할 수 있다. 휴머노이드의 팔은 분산된 질량(Distributed Mass)과 비교적 넓은 운동 범위를 가지고 있기 때문에 발이 고정된 상태에서도 빠른 팔 스윙(Arm Swing)을 통해 유용한 각운동량을 생성할 수 있다. 이러한 동작은 사람이 균형을 잃었을 때 나타나는 반응과 유사하다. 그러나 균형 회복 과정에서 관절 한계(Joint Limit)를 위반하거나 자체 충돌(Self-Collision)이 발생하거나 조작 중인 물체에 영향을 주지 않도록 팔 운동은 전신 최적화(Whole-Body Optimization) 구조에 통합되어야 한다.

실제 회복 제어기(Recovery Controller)는 현재의 접촉 구성으로 불안정한 운동을 계속 제어할 수 있는지를 지속적으로 추정해야 한다. 이러한 추정에는 캡처 포인트 또는 발산 운동 성분(Divergent Component of Motion, DCM)의 거동과 함께 CoP 여유(CoP Margin), 마찰 제약(Friction Constraint), 관절 토크 여유(Joint Torque Reserve), 예측된 운동량 변화(Predicted Momentum Evolution)를 사용할 수 있다. 따라서 회복 결정은 로봇이 이미 기하학적 경계에 도달하여 보정 가능성이 크게 감소한 이후가 아니라 미래의 실행 가능성(Future Feasibility)을 기반으로 이루어져야 한다.

고정 접촉 회복(Fixed-Contact Recovery)이 더 이상 가능하지 않으면 로봇은 스테핑을 통해 새로운 지지 영역을 생성해야 한다. 회복 스텝(Recovery Step)은 새로운 접촉이 발산하는 운동(Divergent Motion)을 포착할 수 있으면서 동시에 운동학적으로 도달 가능(Kinematically Reachable)하고 충돌이 발생하지 않는 위치에 배치되어야 한다. 스텝 위치는 CoM 상태, 외란 방향, 스윙 다리(Swing Leg)의 운동 능력, 지형 형상(Terrain Geometry), 사용 가능한 시간에 따라 결정된다. 이론적으로 이상적인 발 디딤 위치(Foothold)라 하더라도 로봇이 균형을 잃기 전에 다리가 해당 위치에 도달할 수 없다면 실제로는 사용할 수 없다.

스텝 위치만큼 스텝 타이밍(Step Timing)도 중요하다. 강한 외란이 발생하면 불안정한 운동 성분이 회복 가능한 범위를 넘어 증가하기 전까지 사용할 수 있는 시간이 짧아진다. 따라서 제어기는 기존의 서기 또는 보행 계획을 중단하고 선택된 스윙 발의 하중을 빠르게 제거하며, 단축된 스윙 궤적(Swing Trajectory)을 생성하여 새로운 접촉을 형성해야 할 수 있다. 이러한 비상 동작(Emergency Behavior)은 회복을 최우선으로 하기 때문에 편안함, 대칭성(Symmetry), 또는 정상 보행 목표보다 우선될 수 있다는 점에서 일반적인 보행 생성(Gait Generation)과 다르다.

어느 다리를 스테핑에 사용할지는 외란 방향과 현재의 하중 분포(Load Distribution)에 따라 달라진다. 전방 외란은 전방 스텝을 요구할 수 있으며, 측면 외란은 지지 기반(Support Base)을 빠르게 넓히는 스텝이 필요한 경우가 많다. 대각선 방향의 외란은 서로 독립적으로 처리할 수 없는 시상 방향(Sagittal Direction)과 측면 방향(Lateral Direction)의 결합된 요구 조건을 생성한다. 따라서 제어기는 도달 가능성, 지형, 자체 충돌, 착지 이후 예상되는 지지 다각형을 고려하면서 2차원 또는 3차원 공간에서 후보 발 디딤 위치(Candidate Foothold)를 평가해야 한다.

한 번의 접촉 위치 변경만으로 외란을 제거할 수 없는 경우 여러 번의 스텝(Multiple Steps)이 필요할 수 있다. 첫 번째 회복 스텝 이후 로봇은 안정성이 회복되었다고 가정해서는 안 되며 즉시 동적 상태를 다시 평가해야 한다. 잔여 CoM 속도(Residual CoM Velocity) 또는 각운동량으로 인해 두 번째 또는 세 번째 스텝이 필요할 수 있다. 이러한 과정은 새로운 상태 추정값이 제공될 때마다 발 위치와 운동량 조절을 반복적으로 갱신하는 이동 지평선 회복 전략(Receding-Horizon Recovery Strategy)으로 자연스럽게 확장된다.

밀림 회복은 보행 중에 발생하는 외란도 처리해야 한다. 이 경우 로봇은 이미 계획된 스윙 발, 변화하는 지지 단계(Support Phase), 0이 아닌 운동량을 가지고 있다. 제어기는 현재 계획된 발 디딤 위치를 수정하거나, 착지(Touchdown)를 앞당기거나 지연시키고, 스텝 폭이나 길이를 조절하거나, 계획된 접촉 순서(Contact Sequence)를 완전히 교체할 수 있다. 따라서 회복 기능은 서기 상태에서만 동작하는 독립적인 안전 기능이 아니라 이동 제어(Locomotion Control)와 긴밀하게 결합되어야 한다.

전신 제어(Whole-Body Control, WBC)는 이러한 회복 결정을 실제 로봇 동작으로 실행하는 계층을 제공한다. 목표 CoM 가속도, 몸통 자세, 질량중심 운동량, 발 운동(Foot Motion), 접촉 렌치를 서로 협조된 목표로 정의하면서 마찰 원뿔(Friction Cone), 단방향 접촉(Unilateral Contact), 토크 한계(Torque Limit), 관절 한계, 자체 충돌을 명시적인 제약 조건으로 설정할 수 있다. 우선순위 또는 최적화 기반 제어(Priority or Optimization-Based Control)를 사용하면 외란이 발생했을 때 균형에 중요한 작업이 일반적인 자세 및 조작 목표보다 일시적으로 높은 우선순위를 갖도록 할 수 있다.

밀림 회복은 매우 제한된 시간 안에 수행되므로 정확하고 지연이 작은 상태 추정(Low-Latency State Estimation)이 필수적이다. IMU 측정값은 갑작스러운 각운동을 감지하고, 관절 인코더는 형상 변화를 제공하며, 발의 힘/토크 센서는 접촉 렌치와 압력 분포의 변화를 감지한다. 특히 추정된 부유 베이스 속도(Floating-Base Velocity)와 CoM 속도는 중요하다. 작은 속도 추정 오차도 예측된 캡처 포인트 또는 미래 균형 상태(Future Balance State)에 상당한 오차를 발생시킬 수 있기 때문이다.

외란 감지(Disturbance Detection)는 실제 외부 외란과 정상적으로 예상되는 동적 힘을 구별해야 한다. 의도적인 팔 운동, 물체 운반(Payload Handling), 보행 전환(Gait Transition)으로 발생하는 빠른 가속도가 불필요한 비상 회복을 유발해서는 안 된다. 모델 기반 잔차(Model-Based Residual), 운동량 관측기(Momentum Observer), 힘 센싱(Force Sensing), 접촉 정보를 활용하면 예상하지 못한 외부 충격을 식별하는 데 도움이 된다. 특히 사람과 물리적으로 가까운 환경에서 동작하는 로봇에서는 감지 민감도와 오작동(False Activation) 사이의 균형을 고려하여 임계값을 설정해야 한다.

회복 동작은 넘어짐을 방지하는 것이 최우선 목표인 상황에서도 안전(Safety)을 준수해야 한다. 큰 비상 스텝(Emergency Step)은 주변 사람과의 충돌 위험을 발생시킬 수 있으며, 공격적인 팔 운동은 주변 물체와 충돌할 수 있다. 따라서 제어기는 동적 회복 요구 조건과 환경 인식(Environmental Awareness), 충돌 제약(Collision Constraint)을 함께 고려해야 한다. 어떤 상황에서는 실행 가능하지 않은 회복 동작을 무리하게 시도하여 사람이나 하드웨어에 대한 위험을 증가시키는 것보다 제어된 보호 낙상(Controlled Protective Falling)을 수행하는 것이 더 안전할 수 있다.

검증(Validation)은 외란의 방향, 크기, 지속 시간(Duration), 작용 위치(Application Point)를 반복 가능하게 제어할 수 있는 외란 시험(Perturbation Experiment)을 통해 수행해야 한다. 시험은 전방, 후방, 측면, 대각선 방향의 외란을 점진적으로 확대하면서 CoM 운동, 캡처 포인트, CoP, 접촉력, 관절 토크, 스텝 타이밍, 회복 시간을 기록해야 한다. 이를 통해 생성되는 회복 영역(Recovery Envelope)은 고정된 발, 단일 스텝, 다중 스텝으로 회복할 수 있는 외란 범위와 안전하게 회복할 수 없는 범위를 정량적으로 나타낸다.

성숙한 밀림 회복 시스템(Push-Recovery System)은 하나의 단순한 반사 동작(Reflex)이 아니라 동적으로 협조되는 계층적 반응(Hierarchical Response)으로 구성된다. 작은 외란은 발목과 CoP 조절을 통해 제거하고, 더 큰 외란에는 엉덩이 및 전신 운동량을 활용하며, 고정 접촉 능력을 넘어서는 외란에는 하나 이상의 회복 스텝을 실행한다. 회복 가능성을 지속적으로 예측하고 현재 상황에서 실행 가능한 가장 작은 수준의 대응을 선택함으로써 휴머노이드의 균형 제어는 수동적인 자세 유지(Passive Posture Maintenance)를 넘어 예상하지 못한 물리적 상호작용을 능동적으로 관리하는 기능으로 확장될 수 있다.

## 04.04. Whole Body Posture Regulation [w/Code]

![](images/image4.png){width="7.268055555555556in" height="7.268055555555556in"}

전신 자세 조절(Whole-Body Posture Regulation)은 균형(Balance), 접촉 일관성(Contact Consistency), 물리적 실행 가능성(Physical Feasibility)을 유지하면서 휴머노이드 로봇(Humanoid Robot)을 원하는 신체 구성(Body Configuration) 부근에 유지하는 기능이다. 단순한 관절 위치 제어(Joint-Position Control)와 달리 자세 조절에서는 부유 베이스(Floating Base)와 다리, 골반(Pelvis), 몸통(Torso), 팔, 머리 사이의 강한 동적 결합(Dynamic Coupling)을 고려해야 한다. 하나의 관절 운동도 다른 부위의 질량 분포와 운동량(Momentum)을 변화시키므로 자세는 협조된 전신 제어(Whole-Body Control) 문제로 다루어야 한다.

목표 자세(Desired Posture)는 일반적으로 하나의 관절 각도 벡터만으로 표현하지 않고 관절 구성(Joint Configuration)과 작업 공간 물리량(Task-Space Quantity)을 결합하여 표현한다. 주요 기준값에는 골반 위치와 방향, 몸통 자세(Torso Attitude), 머리 방향, 팔 구성, 무릎 굽힘(Knee Flexion), 발 자세(Foot Pose), 질량중심(Center of Mass, CoM) 위치 등이 포함될 수 있다. 이러한 기준값은 명목 신체 형상(Nominal Body Shape)을 정의하면서 균형이나 접촉 제약(Contact Constraint)에 보정 운동이 필요한 경우 개별 관절을 수정할 수 있도록 한다.

자세 조절은 활성 접촉(Active Contact)이 부과하는 제약 조건 내에서 동작한다. 양발 지지(Double Support) 상태에서는 두 발 모두 지면과 일관된 접촉을 유지하면서 나머지 관절이 목표 구성을 조절해야 한다. 한발 지지(Single Support) 또는 다중 접촉(Multi-Contact) 상태에서는 지지 접촉이 부유 베이스를 구속하기 때문에 사용 가능한 운동 범위가 달라진다. 따라서 제어기는 자유롭게 조절할 수 있는 자세 목표와 지지 모드(Support Mode)를 변경하지 않고는 위반할 수 없는 접촉 조건을 구분해야 한다.

기본적인 관절 공간 자세 목표(Joint-Space Posture Objective)는 목표 관절 위치 및 속도와 측정된 관절 위치 및 속도의 차이를 이용하여 구성할 수 있다. 비례 및 미분 피드백(Proportional and Derivative Feedback)은 관절을 기준 상태로 이동시키면서 진동 운동(Oscillatory Motion)을 감쇠시키는 복원 명령(Restoring Command)을 생성한다. 그러나 전신 동역학(Whole-Body Dynamics)을 고려하지 않고 독립적인 관절 제어기를 적용하면 서로 충돌하는 토크가 발생하거나 접촉력이 교란되거나 CoM이 안전하지 않은 영역으로 이동할 수 있으며, 이러한 문제는 동적 결합이 강한 휴머노이드 구성에서 특히 중요하다.

작업 공간 조절(Task-Space Regulation)은 중요한 신체 세그먼트(Body Segment)를 보다 물리적으로 의미 있게 제어할 수 있는 방법을 제공한다. 모든 관절을 직접 지정하는 대신 제어기는 골반 높이, 몸통 방향, 손 자세(Hand Pose), 머리 방향 또는 기타 작업 공간 물리량을 조절할 수 있다. 이후 역기구학(Inverse Kinematics) 또는 전신 최적화(Whole-Body Optimization)를 이용하여 이러한 목표를 달성하는 데 필요한 협조된 관절 운동을 결정한다. 이를 통해 여유 자유도(Redundant Degree of Freedom)를 부가적인 자세 및 균형 요구 조건에 활용할 수 있다.

골반(Pelvis)은 상체와 양쪽 다리를 연결하고 부유 베이스 구성에 큰 영향을 주기 때문에 핵심적인 역할을 한다. 골반 방향(Pelvis Orientation)은 다리 운동학(Leg Kinematics), 몸통 정렬(Torso Alignment), 하중 분배(Load Distribution)에 영향을 주며, 골반의 병진 운동(Pelvis Translation)은 CoM과 지지 영역(Support Region)의 관계를 변화시킨다. 따라서 자세 제어기는 일반적으로 골반 높이와 자세를 조절하면서 균형 보정을 위해 몸체를 발 위로 이동시켜야 할 경우 제한적인 수평 운동을 허용한다.

몸통 조절(Torso Regulation) 역시 유용하고 안정적인 휴머노이드 구성을 유지하는 데 중요하다. 몸통을 대체로 수직으로 유지하면 인지(Perception), 조작(Manipulation), 인간-로봇 상호작용(Human-Robot Interaction)을 지원하면서 불필요한 각운동량(Angular Momentum)을 제한할 수 있다. 그러나 몸통 방향을 지나치게 고정하면 회복 능력(Recovery Capability)이 감소할 수 있다. 따라서 발목 제어 능력(Ankle Authority)이 부족하거나 외란 제거를 위해 전신 운동량을 생성해야 하는 경우 제어기는 제어된 몸통 운동을 허용해야 한다.

팔과 머리는 조작 또는 인지 작업을 수행하지 않을 때 일반적으로 낮은 우선순위의 자세 목표(Posture Objective)를 할당받는다. 이들의 관절은 자체 충돌(Self-Collision)과 관절 한계(Joint Limit)를 피하면서 편안한 기준 구성(Nominal Configuration) 부근에 유지될 수 있다. 팔 운동은 CoM과 각운동량을 변화시키기 때문에 부가적인 자세 조절이라 하더라도 균형과 협조되어야 한다. 외란이 발생하면 팔이 회복 동작에 기여할 수 있도록 팔 자세 목표를 일시적으로 완화할 수 있다.

여유성(Redundancy)은 전신 자세 조절의 주요 장점 중 하나이다. 휴머노이드는 일반적으로 가장 높은 우선순위의 균형 및 접촉 작업을 만족하는 데 필요한 것보다 더 많은 자유도(Degree of Freedom)를 가진다. 남아 있는 영공간 운동(Null-Space Motion)은 선호하는 관절 구성을 유지하고, 조작성(Manipulability)을 개선하며, 특이점(Singularity)을 회피하고, 에너지를 최소화하거나 관절 한계로부터의 거리를 증가시키는 데 사용할 수 있다. 이를 통해 주요 안정성 목표를 크게 방해하지 않으면서 자세 품질(Posture Quality)을 향상시킬 수 있다.

계층적 제어(Hierarchical Control)는 자세 목표와 안전 필수 작업(Safety-Critical Task) 사이의 충돌을 체계적으로 해결하는 방법을 제공한다. 접촉 제약, 마찰 실행 가능성(Friction Feasibility), 균형 목표는 일반적으로 명목 자세 추종(Nominal Posture Tracking)보다 높은 우선순위를 가진다. 이후 몸통, 골반, 팔, 관절 구성 목표를 더 낮은 계층에 배치할 수 있다. 두 목표가 충돌하면 접촉 조건을 위반하거나 로봇을 불안정하게 만드는 대신 낮은 우선순위의 자세 작업을 수정한다.

최적화 기반 접근법(Optimization-Based Approach)은 전신 동역학과 자세 조절을 이차 계획법(Quadratic Programming, QP) 구조 안에서 함께 표현할 수 있다. 결정 변수(Decision Variable)에는 일반화 가속도(Generalized Acceleration), 관절 토크, 접촉력 등이 포함될 수 있다. 목표 자세 가속도는 추종 목표(Tracking Objective)로 표현되고, 강체 접촉 방정식(Rigid-Contact Equation), 마찰 원뿔(Friction Cone), 토크 한계, 가속도 한계, 관절 한계는 제약 조건으로 표현된다. 이러한 구성은 서로 경쟁하는 목표 사이의 절충 관계를 명시적으로 처리하면서 동역학적으로 일관된 명령을 생성한다.

관절 한계 회피(Joint-Limit Avoidance)는 로봇이 기계적 경계에 도달하기 전에 제어에 통합되어야 한다. 제어기는 관절이 허용 범위에 가까워질수록 증가하는 구성 의존적 페널티(Configuration-Dependent Penalty) 또는 반발 목표(Repulsive Objective)를 적용할 수 있다. 유사한 방법으로 작은 작업 공간 운동에 과도한 관절 속도가 필요한 운동학적 특이점(Kinematic Singularity)으로부터 로봇을 멀리 유지할 수 있다. 명령을 갑작스럽게 제한하는 방식은 균형을 교란하고 바람직하지 않은 접촉력을 발생시킬 수 있으므로 예방적인 조절(Preventive Regulation)이 더 적합하다.

자체 충돌 회피(Self-Collision Avoidance)는 높은 자유도를 가진 휴머노이드의 또 다른 필수 자세 제약이다. 큰 보정 운동 중에는 팔이 몸통과 충돌하거나, 손이 다리 영역으로 들어가거나, 무릎이나 발이 바람직하지 않은 구성에 진입할 수 있다. 충돌 거리(Collision Distance)를 지속적으로 감시하고 이를 부등식 제약(Inequality Constraint) 또는 반발 목표로 변환할 수 있다. 이는 균형 회복 과정에서 명목 자세와 크게 다른 운동이 일시적으로 발생할 때 특히 중요하다.

자세 전환(Posture Transition)은 한 구성에서 다른 구성으로 순간적으로 변경하도록 명령하는 대신 부드럽게 생성해야 한다. 기준값 보간(Reference Interpolation)을 사용하면 실행 가능한 CoM 및 접촉 거동을 유지하면서 관절 속도, 가속도, 저크(Jerk)를 제한할 수 있다. 몸통 기울기, 골반 높이, 스탠스 폭(Stance Width), 무릎 굽힘을 변경할 때는 질량과 접촉력의 분포가 변화하므로 부드러운 전환이 특히 중요하다. 따라서 자세 궤적(Posture Trajectory)은 기하학적 측면뿐만 아니라 동역학적으로도 평가해야 한다.

순응성(Compliance)은 로봇이 불확실한 환경이나 사람과 상호작용할 때 자세 조절 성능을 향상시킨다. 임피던스 제어(Impedance Control)는 완전히 강체적인 추종을 강제하는 대신 구성 오차(Configuration Error)와 보정력(Corrective Force) 사이의 목표 관계를 정의할 수 있다. 물리적 상호작용 중에는 팔이나 몸통에 낮은 강성(Stiffness)을 적용할 수 있으며, 다리는 지지 상태를 유지할 수 있는 충분한 제어 능력이 필요하다. 가변 강성 및 감쇠(Variable Stiffness and Damping)를 사용하면 작업 및 접촉 조건에 따라 자세 거동을 조정할 수 있다.

전신 자세 조절은 정확한 상태 추정(State Estimation)과 일관된 로봇 모델(Robot Model)에 의존한다. 관절 인코더(Joint Encoder)는 구성 정보를 제공하고, 관성측정장치(Inertial Measurement Unit, IMU)는 부유 베이스 방향과 운동을 추정한다. 접촉 센싱(Contact Sensing)은 어떤 운동학적 제약(Kinematic Constraint)이 활성화되어야 하는지를 결정하며, 힘 측정(Force Measurement)은 의도된 하중 분배가 실제로 구현되고 있는지를 보여준다. 이러한 정보에 오차가 있으면 최적화기가 실제로 존재하지 않는 자세 편차를 보상하거나 일관되지 않은 접촉 명령을 생성할 수 있다.

페이로드(Payload)와 외력(External Force)은 유효 질량 분포와 필요한 관절 토크를 변화시키기 때문에 자세 제어 문제 자체를 변화시킨다. 몸통 앞쪽에서 무거운 물체를 들고 있으면 로봇과 물체를 결합한 CoM이 이동하고 허리, 엉덩이, 무릎, 발목 관절의 하중이 증가한다. 제어기는 지면 반력을 재분배하면서 골반, 몸통, 다리 구성을 조정하여 이를 보상해야 한다. 하중이 없는 상태에서 사용하던 원래 자세를 그대로 유지하려는 것은 비효율적이거나 동역학적으로 실행 불가능할 수 있다.

자세 품질은 추종 성능(Tracking Performance)과 실행 가능성(Feasibility)을 모두 나타내는 지표를 사용하여 감시해야 한다. 관절 오차, 몸통 방향 오차, 골반 편차, CoM 위치, 접촉력 균형(Contact-Force Balance), 토크 사용률(Torque Utilization), 관절 한계까지의 거리, 자체 충돌 여유(Self-Collision Margin)는 서로 보완적인 정보를 제공한다. 액추에이터가 포화 상태에 가깝거나 안정성 여유가 부족하다면 관절 공간 추종 오차가 작더라도 좋은 자세라고 판단할 수 없다. 따라서 평가는 전체 물리적 상태를 고려해야 한다.

외란 회복(Disturbance Recovery) 중에는 명목 자세 조절이 균형 제어와 경쟁하는 대신 점진적이고 안정적으로 완화되어야 한다. 강한 외란이 발생하면 제어기는 일시적인 몸통 기울임(Torso Lean), 팔 스윙(Arm Swing), 더 깊은 무릎 굽힘, 골반 변위를 허용할 수 있다. 외란이 제거된 이후에는 자세 목표를 이용하여 선호하는 구성을 점진적으로 복원할 수 있다. 일시적인 회복 운동과 장기적인 자세 복원(Posture Restoration)을 분리하면 안전 필수 동작 중 불필요한 제어 충돌을 방지할 수 있다.

따라서 전신 자세 조절은 균형 목표와 작업 실행(Task Execution) 사이에서 지속적으로 신체 구성을 관리하는 계층(Configuration-Management Layer)의 역할을 한다. 접촉 조건을 준수하고 여유성을 활용하면서 안정성을 위해 보정 운동이 필요할 때는 명목 자세를 유연하게 변경함으로써 유용한 신체 형상을 유지한다. 관절 공간 조절, 작업 공간 목표, 계층적 최적화(Hierarchical Optimization), 한계 회피(Limit Avoidance), 동적 실행 가능성(Dynamic Feasibility)을 결합하면 휴머노이드는 서기, 조작, 회복, 이동(Locomotion) 전반에서 안정적이고 적응 가능한 자세를 유지할 수 있다.

## 04.05. Sitting Crouching Getting Up Posture Control [w/Code]

![](images/image5.png){width="7.268055555555556in" height="7.268055555555556in"}

앉기(Sitting), 쪼그려 앉기(Crouching), 일어서기(Getting Up) 동작에서는 휴머노이드 로봇(Humanoid Robot)이 균형(Balance)과 접촉 실행 가능성(Contact Feasibility)을 지속적으로 유지하면서 큰 범위의 전신 자세 전환(Whole-Body Posture Transition)을 수행해야 한다. 작은 서기 자세 보정과 달리 이러한 동작에서는 질량중심(Center of Mass, CoM) 높이, 관절 구성(Joint Configuration), 지지 형상(Support Geometry), 액추에이터 하중(Actuator Loading)이 크게 변화한다. 따라서 성공적인 제어를 위해서는 독립적인 관절 위치 명령이 아니라 골반(Pelvis), 몸통(Torso), 다리, 팔을 위한 협조된 궤적 생성(Coordinated Trajectory Generation)이 필요하다.

쪼그려 앉기(Crouching)는 일반적으로 골반과 CoM을 낮추면서 엉덩이, 무릎, 발목 관절을 협조적으로 굽혀 생성한다. 제어기는 신체가 하강하는 동안 수평 방향 CoM 위치를 동적으로 실행 가능한 영역(Dynamically Feasible Region) 안에 유지해야 한다. 무릎 굽힘(Knee Flexion)은 다리의 기계적 지렛대 효과(Mechanical Leverage)와 필요한 관절 토크를 변화시키며, 몸통 기울기(Torso Inclination)는 변화하는 질량 분포를 보상할 수 있다. 결과적으로 생성되는 운동은 충분한 발 접촉을 유지하고 관절 한계(Joint Limit) 부근에서 과도한 하중이 발생하지 않도록 해야 한다.

목표 쪼그려 앉기 깊이(Desired Crouching Depth)는 기계적 운동 범위(Mechanical Range), 액추에이터 성능, 균형 여유(Balance Margin), 작업 요구 조건에 따라 선택해야 한다. 얕은 쪼그려 앉기(Shallow Crouch)는 순응성(Compliance)을 높이고 물체를 들어 올리거나 외란 회복(Disturbance Recovery)을 준비하는 데 사용할 수 있으며, 깊은 쪼그려 앉기(Deep Crouch)는 낮은 위치의 물체에 접근하거나 앉기 동작으로 전환할 때 필요할 수 있다. 깊이가 증가하면 무릎과 엉덩이에 요구되는 토크가 크게 증가할 수 있으므로 토크 한계(Torque Limit)와 열적 제약(Thermal Constraint)을 궤적 실행 가능성의 중요한 요소로 고려해야 한다.

쪼그려 앉는 동안에는 부드러운 수직 질량중심 조절(Vertical Center of Mass Regulation)이 필수적이다. 지나치게 빠른 하향 가속은 발의 하중을 감소시킬 수 있으며, 목표 자세 부근에서 급격하게 감속하면 큰 지면 반력(Ground Reaction Force)이 발생할 수 있다. 따라서 제어기는 골반 높이에 대해 위치, 속도, 가속도, 가능하면 저크(Jerk)까지 제한된 프로파일을 생성해야 한다. 전신 최적화(Whole-Body Optimization)를 사용하면 원하는 접촉력을 유지하면서 필요한 운동을 발목, 무릎, 엉덩이, 몸통에 적절히 분배할 수 있다.

앉기(Sitting)는 지지 상태가 발만 사용하는 상태에서 발과 좌석(Seat)을 함께 사용하는 상태로 점진적으로 변화하므로 추가적인 접촉 전환(Contact Transition)을 포함한다. 좌석과 접촉하기 전에 로봇은 서기 균형(Standing Balance)을 유지하면서 의자에 대한 골반 위치를 조절해야 한다. 하강 궤적은 CoM에 과도한 후방 속도가 발생하지 않도록 하면서 골반을 예상 좌석 위치(Expected Seat Location)로 이동시켜야 한다. 좌석의 자세(Seat Pose)를 사전에 정확하게 알 수 없는 경우에는 인지(Perception)와 기하학적 추정(Geometric Estimation)이 필요하다.

좌석에 접근하는 과정에서는 너무 이르거나 잘못된 위치에서의 접촉이 로봇을 불안정하게 만들 수 있으므로 동역학적으로 보수적인 접근(Dynamically Conservative Approach)이 필요하다. 제어기는 예상 접촉점에 가까워질수록 하강 속도를 줄이고 힘, 토크 또는 촉각 정보(Tactile Information)를 감시하여 실제 좌석 접촉을 감지할 수 있다. 접촉이 감지되면 골반을 계속 자유로운 상태로 취급하지 않고 활성 접촉 집합(Active Contact Set)을 갱신해야 한다. 이러한 전환은 서로 충돌하는 운동 명령과 힘 명령이 생성되는 것을 방지한다.

좌석 접촉 이후에는 수직 하중을 다리에서 좌석으로 점진적으로 전달해야 한다. 갑작스러운 하중 전달은 충격력(Impact Force)을 발생시키거나 발의 미끄러짐을 유발할 수 있으며, 다리의 하중을 너무 일찍 제거하면 골반이 의자 위로 떨어질 수 있다. 접촉력 조절(Contact-Force Regulation)을 이용하면 발의 하중을 제어된 방식으로 감소시키면서 좌석의 지지력을 증가시킬 수 있다. 이러한 하중 전달 과정 전체에서 결합된 지지 구성(Combined Support Configuration)이 안정적으로 유지되도록 몸통과 골반을 협조적으로 제어해야 한다.

최종 착석 자세(Final Seated Posture)는 단순한 기하학적 편안함 이상의 조건을 만족해야 한다. 발은 이후의 일어서기 전환을 지원할 수 있는 위치에 유지되어야 하며, 무릎과 엉덩이는 안전한 운동 범위 안에 있어야 하고, 몸통은 조작 또는 상호작용 작업에 필요한 충분한 운동성을 유지해야 한다. 또한 불필요하게 높은 관절 토크나 접촉 마찰(Contact Friction)이 요구되는 자세를 피해야 한다. 향후 수행할 일어서기 동작을 고려하여 착석 자세를 계획하면 전체적인 강인성(Robustness)을 향상시킬 수 있다.

의자에서 일어서기(Getting Up)는 지지 전환의 방향을 반대로 수행하지만 단순히 앉기 궤적을 시간적으로 역전시키는 동작은 아니다. 로봇은 먼저 다리가 충분한 수직 및 전방 힘을 생성할 수 있는 구성을 만들어야 한다. 일반적인 전략은 좌석의 하중을 크게 감소시키기 전에 몸통과 CoM을 발 위쪽으로 전진시키는 것이다. 이러한 전방 하중 이동(Forward Transfer)은 좌석 지지가 사라질 때 신체가 뒤쪽으로 회전하는 것을 방지하면서 골반을 들어 올리기에 기계적으로 유리한 조건을 형성한다.

일어서기의 초기 단계에서는 발과 좌석이 다중 접촉 시스템(Multi-Contact System)을 형성한다. 제어기는 상체 위치를 변경하고 다리가 하중을 받을 준비를 하는 동안 좌석 반력(Seat Reaction Force)을 활용할 수 있다. CoM이 전방으로 이동함에 따라 수직력을 점진적으로 발 쪽으로 전달한다. 다리에 충분한 토크 여유(Torque Reserve)가 확보되고 예측된 동적 상태가 발 접촉 영역(Foot Contact Region)에 의해 지지될 수 있을 때에만 좌석 접촉을 해제해야 한다.

들어 올리기 단계(Lift Phase)에서는 무릎 및 엉덩이 관절의 신전(Extension)과 발목 및 몸통 조절을 협조적으로 수행해야 한다. 골반이 낮은 위치에 있을 때는 큰 무릎 신전 토크(Knee-Extension Torque)가 필요한 경우가 많으며, 엉덩이 신전(Hip Extension)은 상체를 들어 올리고 안정화한다. 발목은 CoM 운동과 지지 영역 사이의 관계를 조절한다. 모든 상황에서 동일한 관절 궤적을 강제하는 대신 좌석 높이, 신체 구성, 페이로드(Payload), 액추에이터 성능에 따라 동작을 적응적으로 조절해야 한다.

몸통 운동(Torso Motion)은 일어서기 과정에서 특히 중요하다. 몸통을 전방으로 기울이면 CoM이 발 방향으로 이동하여 좌석 하중이 제거될 때 뒤로 넘어지려는 경향을 감소시킬 수 있다. 골반이 상승함에 따라 몸통은 점진적으로 직립 방향으로 복귀할 수 있다. 그러나 과도한 전방 기울기는 동작 후반부에서 큰 균형 회복 요구를 발생시킬 수 있으므로 몸통과 골반 궤적을 서로 독립적인 자세 변수로 제어하지 않고 함께 계획해야 한다.

팔 지지(Arm Support)를 활용하면 앉기와 일어서기 동작의 실행 가능 범위를 확장할 수 있다. 난간(Handrail), 팔걸이(Armrest), 기타 안정적인 표면을 사용할 수 있다면 손을 추가 접촉점으로 활용하여 다리에 필요한 토크를 감소시키고 지지 영역을 확대할 수 있다. 이러한 접촉은 단방향 힘 제약(Unilateral Force Constraint)과 마찰 제약(Friction Constraint)을 포함하여 전신 제어기(Whole-Body Controller)에 명시적으로 표현해야 한다. 환경의 지지물과 신뢰할 수 있는 접촉이 형성되기 전에는 해당 구조물이 하중을 지지할 수 있다고 가정해서는 안 된다.

궤적 생성(Trajectory Generation)은 각 자세 전환을 구성하는 서로 다른 동적 단계(Dynamic Phase)를 고려해야 한다. 쪼그려 앉기는 고정된 발 접촉 상태의 운동으로 유지될 수 있지만, 앉기는 접근(Approach), 좌석 접촉, 하중 전달(Load Transfer), 최종 안정화(Final Stabilization) 단계로 구성된다. 일어서기는 준비, 전방 운동량 생성(Forward Momentum Generation), 좌석 하중 제거(Seat Unloading), 수직 신전(Vertical Extension), 서기 안정화 단계를 포함한다. 이러한 단계를 명시적으로 표현하면 제어 목표와 접촉 제약을 예측 가능하고 물리적으로 일관된 방식으로 변경할 수 있다.

접촉 전환은 새로운 지지점이 생성되거나 제거될 때 로봇 동역학이 변화하므로 특별한 주의가 필요하다. 제약 조건을 순간적으로 전환하면 불연속적인 토크 명령(Discontinuous Torque Command)이나 비현실적인 충격(Impulse)이 발생할 수 있다. 실제 제어기에서는 접촉 감지(Contact Detection), 힘 램프(Force Ramp), 순응 제어(Compliant Control), 또는 전환 단계(Transition Phase)를 사용하여 제약 조건을 부드럽게 추가하거나 제거한다. 상태 추정기(State Estimator) 역시 실제 물리적 지지 상태와 부유 베이스 운동이 일관되도록 접촉 가정을 신속하게 갱신해야 한다.

전신 최적화는 이러한 운동을 협조적으로 제어하기 위한 자연스러운 프레임워크를 제공한다. 골반 궤적, CoM 운동, 몸통 방향, 발 접촉, 손 접촉, 관절 자세를 서로 다른 우선순위의 작업(Task)으로 표현할 수 있다. 동역학 방정식(Dynamic Equation), 마찰 원뿔(Friction Cone), 토크 한계, 관절 한계, 충돌 제약(Collision Constraint)은 실행 가능성을 정의한다. 이후 최적화기는 명목 자세를 유지하기 어려워지거나 실제 환경 접촉이 예상과 달라질 때 여유 관절(Redundant Joint)에 운동을 재분배할 수 있다.

자체 충돌(Self-Collision)과 환경 충돌(Environmental Collision) 제약은 깊은 쪼그려 앉기와 착석 과정에서 특히 중요하다. 큰 엉덩이 및 무릎 굽힘으로 인해 몸통, 허벅지, 팔, 다리가 서로 가까워질 수 있으며, 의자는 로봇 주변에 추가적인 기하학적 구조를 형성한다. 충돌 인식 계획(Collision-Aware Planning)은 부자연스러운 운동을 강제하지 않으면서 충분한 간격을 유지해야 한다. 팔 역시 좌석과의 충돌을 피하거나 보호 지지(Protective Support)를 준비하기 위해 명목 자세에서 벗어나 움직여야 할 수 있다.

모든 자세 전환에는 실패 감지(Failure Detection)가 함께 수행되어야 한다. 예상하지 못한 발 접촉 상실, 과도한 관절 토크, 불충분한 좌석 반력, 예상하지 못한 충돌, 급격하게 증가하는 몸체 속도는 계획된 운동이 더 이상 실행 가능하지 않음을 나타낼 수 있다. 제어기는 유효하지 않은 궤적을 계속 실행하는 대신 동작을 중지하거나, 더 안전한 자세로 복귀하거나, 환경 지지를 증가시키거나, 균형 회복 동작(Balance-Recovery Behavior)을 활성화할 수 있어야 한다.

검증(Validation)은 서로 다른 좌석 높이, 쪼그려 앉기 깊이, 동작 속도, 페이로드 조건, 발 배치(Foot Placement), 접촉 불확실성(Contact Uncertainty)을 포함해야 한다. 주요 측정 항목에는 CoM 궤적, 골반 높이, 몸통 각도, 접촉력 전달(Contact-Force Transfer), 관절 토크 사용률(Joint Torque Utilization), 발의 CoP, 전환 시간(Transition Duration), 최종 자세 오차(Final Posture Error)가 포함된다. 특히 낮은 좌석이나 깊은 쪼그려 앉기 조건에서는 기계적 지렛대 효과와 액추에이터 요구량이 가장 까다로워지므로 반복적인 시험이 중요하다.

따라서 앉기, 쪼그려 앉기, 일어서기 제어는 미리 정의된 관절 운동의 집합이 아니라 협조된 다단계 전신 자세 문제(Coordinated Multi-Phase Whole-Body Posture Problem)로 이해해야 한다. 신뢰할 수 있는 실행을 위해서는 동적 균형(Dynamic Balance), 부드러운 CoM 및 골반 궤적, 명시적인 접촉 전환, 힘 재분배(Force Redistribution), 토크 실행 가능성(Torque Feasibility), 적응형 자세 조절(Adaptive Posture Regulation)이 필요하다. 이러한 기능을 통해 휴머노이드는 서기 상태와 낮은 높이의 자세 사이를 안전하게 전환하면서 이후의 조작, 이동(Locomotion), 균형 회복에 대응할 수 있는 상태를 유지할 수 있다.

## 04.06. Fall Detection and Protective Fall Strategy [w/Code]

![](images/image6.png){width="7.268055555555556in" height="7.268055555555556in"}

낙상 감지(Fall Detection)와 보호 낙상(Protective Falling)은 일반적인 균형 제어(Balance Control)와 밀림 회복(Push Recovery) 메커니즘으로 더 이상 휴머노이드 로봇(Humanoid Robot)을 회복 가능한 상태(Recoverable State)에 유지할 수 없을 때 작동하는 최종 안전 계층(Final Safety Layer)을 구성한다. 낙상을 단순히 제어기의 실패로 취급해서는 안 된다. 일단 회복이 물리적으로 불가능해지면 공격적인 안정화 제어를 계속하는 것이 오히려 충돌 에너지(Impact Energy)나 손상을 증가시킬 수 있다. 따라서 제어 목표는 직립 균형 유지에서 사람의 부상, 하드웨어 손상, 위험한 환경 상호작용을 최소화하는 방향으로 전환되어야 한다.

낙상 감지는 실제 충돌이 발생할 때까지 기다리는 것이 아니라 로봇의 동적 상태(Dynamic State)를 지속적으로 관찰하는 것에서 시작한다. 유용한 신호에는 몸통 자세(Torso Orientation), 베이스 각속도(Base Angular Velocity), 질량중심(Center of Mass, CoM)의 위치와 속도, 압력중심(Center of Pressure, CoP) 여유, 접촉 상태(Contact State), 질량중심 운동량(Centroidal Momentum), 관절 운동 등이 포함된다. 큰 몸체 기울기만으로는 충분하지 않으며, 의도적인 쪼그려 앉기나 조작에서도 유사한 자세가 나타날 수 있다. 신뢰할 수 있는 감지를 위해서는 자세와 운동 상태 및 지지 실행 가능성(Support Feasibility)을 함께 고려해야 한다.

실제 낙상 감지기(Fall Detector)는 불안정 상태를 여러 단계로 구분할 수 있다. 초기에는 균형 여유(Balance Margin)가 감소하지만 일반적인 보정이 여전히 가능한 경고 영역(Warning Region)에 진입할 수 있다. 이후 회복 임계 상태(Recovery-Critical State)에서는 발목, 엉덩이 또는 스테핑 전략(Stepping Strategy)이 즉시 작동해야 한다. 최종적인 회복 불가능 상태(Unrecoverable State)는 도달 가능한 접촉(Reachable Contact)과 사용 가능한 액추에이터 제어 능력(Actuator Authority)을 이용하더라도 예측된 운동을 정지시킬 수 없는 상태이다. 보호 낙상은 일반적으로 이러한 전환이 충분한 신뢰도로 확인된 이후 시작해야 한다.

예측 기준(Predictive Criterion)은 휴머노이드가 기하학적으로 위험한 자세에 도달하기 전에 이미 동역학적으로 불안정해질 수 있기 때문에 특히 중요하다. 캡처 포인트(Capture Point), 발산 운동 성분(Divergent Component of Motion, DCM), 예측된 CoM 궤적(Predicted CoM Trajectory), 질량중심 운동량 등을 이용하면 미래의 지지 상태가 현재 운동을 정지시킬 수 있는지를 추정할 수 있다. 이러한 지표는 도달 가능한 발 디딤 위치(Reachable Foothold), 마찰 한계(Friction Limit), 토크 여유(Torque Reserve), 남아 있는 반응 시간과 함께 평가하여 단일 임계값이 아니라 물리적 회복 가능성을 기반으로 낙상 여부를 판단해야 한다.

센서 융합(Sensor Fusion)은 낙상 감지의 강인성(Robustness)을 향상시킨다. 관성측정장치(Inertial Measurement Unit, IMU)는 몸체 자세, 각속도, 가속도를 제공하며, 관절 인코더(Joint Encoder)는 급격한 구성 변화를 감지한다. 발의 힘/토크 센서(Force/Torque Sensor)는 하중 감소, 미끄러짐(Slipping), 예상하지 못한 접촉 손실을 식별하며, 촉각 센서(Tactile Sensor) 또는 분산 접촉 센서(Distributed Contact Sensor)는 새로운 신체-환경 접촉을 감지할 수 있다. 이러한 관측값을 결합하면 잡음이 있는 측정으로 인한 오작동을 줄이고 실제 낙상의 방향과 진행 상태를 판단하는 데 도움이 된다.

보호 동작은 전방, 후방, 측면, 회전 낙상에 따라 달라지므로 낙상 방향(Fall Direction)을 판단하는 것이 중요하다. 전방 낙상에서는 팔, 무릎 또는 다른 적절한 구조를 이용하여 접촉을 관리해야 할 수 있으며, 후방 낙상에서는 머리, 척추, 몸체 후면을 보호해야 한다. 측면 낙상은 어깨, 엉덩이, 팔에 대한 위험을 발생시킨다. 제어기는 현재의 기울기만 사용하는 것이 아니라 몸체 자세, 속도, 각운동량(Angular Momentum), 예측된 충돌 형상(Predicted Impact Geometry)을 이용하여 지배적인 낙상 방향을 추정해야 한다.

낙상이 회복 불가능한 것으로 판단되면 제어기는 기존의 서기, 이동(Locomotion), 조작(Manipulation) 목표를 더 이상 추종해서는 안 된다. 그렇지 않으면 극단적인 토크를 요구할 수 있는 균형 작업(Balance Task)을 완화하고 전용 보호 자세(Protective Posture)에 더 높은 우선순위를 부여할 수 있다. 이러한 모드 전환(Mode Transition)은 신속하게 이루어져야 하지만 불연속적인 액추에이터 명령이 발생하지 않을 정도로 부드러워야 한다. 관절, 토크, 충돌, 접촉 제약을 포함한 안전 필수 한계(Safety-Critical Limit)는 전환 과정 전체에서 계속 활성 상태로 유지해야 한다.

보호 낙상의 핵심 목표 중 하나는 충돌 에너지를 감소시키는 것이다. 큰 충돌이 발생하기 전에 충분한 시간이 남아 있다면 제어기는 CoM을 낮춰 중력 위치 에너지(Gravitational Potential Energy)를 감소시킬 수 있다. 무릎, 엉덩이, 몸통 운동을 이용하여 신체 궤적을 변경할 수 있으며, 순응적인 관절 거동(Compliant Joint Behavior)을 통해 충돌 에너지의 일부를 흡수할 수 있다. 이미 낙상을 정지시키는 것이 불가능할 수 있으므로 목표는 반드시 낙상 자체를 멈추는 것이 아니라 운동 에너지(Kinetic Energy)가 어디에서 어떤 방식으로 소산되는지를 제어하는 것이다.

충격 분산(Impact Distribution) 역시 중요한 원칙이다. 전체 충돌 에너지가 중간 수준이더라도 취약한 관절, 센서, 손 또는 머리에 에너지가 집중되면 심각한 손상이 발생할 수 있다. 보호 동작은 기계적으로 적합한 구조(Mechanically Appropriate Structure)를 통해 접촉을 유도하고 가능한 경우 여러 접촉 영역에 하중을 분산해야 한다. 적절한 전략은 로봇의 형태(Morphology), 구조적 보강(Structural Reinforcement), 관절 운동 범위, 액추에이터 역구동성(Actuator Backdrivability), 민감한 탑재 부품의 위치에 따라 크게 달라진다.

휴머노이드의 인지(Perception) 및 컴퓨팅 하드웨어가 머리 또는 상부 몸통에 집중될 수 있으므로 머리 보호(Head Protection)는 명시적으로 높은 우선순위를 가져야 한다. 후방 또는 회전 낙상에서는 기계 설계가 허용하는 범위에서 목과 몸통 운동을 협조적으로 제어하여 머리의 직접적인 충돌을 줄일 수 있다. 경우에 따라 팔을 이용하여 머리를 보호하거나 운동 방향을 변경할 수도 있지만, 손목, 팔꿈치 또는 어깨에 과도한 하중을 발생시키는 자세를 명령해서는 안 된다.

따라서 보호 낙상 과정에서 팔은 신중하게 사용해야 한다. 팔을 지면 방향으로 뻗으면 운동량이 소산되는 시간을 증가시킬 수 있지만, 팔을 강체처럼 고정하면 상대적으로 취약한 관절을 통해 큰 충격력이 전달될 수 있다. 기계 설계가 이를 지원하는 경우 순응 접촉(Compliant Contact), 제어된 팔꿈치 굽힘(Controlled Elbow Flexion), 적절한 어깨 운동을 통해 에너지를 흡수할 수 있다. 보호 전략은 사람의 반사 동작을 단순히 모방하는 것이 아니라 실제 액추에이터 및 구조적 한계를 기반으로 설계해야 한다.

하체 구성(Lower-Body Configuration) 역시 충돌의 심각도에 영향을 준다. 제어된 무릎 및 엉덩이 굽힘은 실질적인 낙상 높이를 낮추고 보다 강한 구조를 통해 접촉할 수 있도록 로봇을 준비시킬 수 있다. 그러나 지나치게 몸을 접으면 자체 충돌(Self-Collision)이 발생하거나 민감한 부품이 노출될 수 있다. 측면 또는 회전 낙상에서는 비대칭적인 다리 운동(Asymmetric Leg Motion)을 이용하여 신체를 위험한 접촉으로부터 다른 방향으로 유도할 수도 있다. 이러한 동작은 독립적인 관절 반사 동작이 아니라 전신 형상(Whole-Body Geometry)을 기반으로 생성해야 한다.

환경 인식(Environmental Awareness)을 활용하면 보호 낙상 결정을 크게 향상시킬 수 있다. 열린 평평한 바닥으로 넘어지는 상황은 벽, 기계, 계단, 사람 또는 날카로운 장애물 방향으로 넘어지는 상황과 다르다. 충분한 인지 및 계산 시간이 확보된다면 예측된 충돌 형상을 이용하여 몸체 회전과 접촉 위치 선택(Contact Selection)을 조정할 수 있다. 그러나 빠른 운동, 진동, 센서 가림(Sensor Occlusion)이 발생하는 낙상 상황에서는 인지 성능 자체가 저하될 수 있으므로 보호 제어에는 항상 신뢰할 수 있는 기본 대체 전략(Fallback Strategy)이 존재해야 한다.

조작 중인 물체(Manipulated Object)는 추가적인 안전 문제를 발생시킨다. 로봇이 페이로드(Payload)를 운반하는 경우 제어기는 물체를 계속 잡고 있는 것과 놓는 것 중 어느 쪽이 더 안전한지를 판단해야 한다. 무거운 물체를 계속 잡고 있으면 각운동량이 증가하거나 위험한 충격력이 발생할 수 있지만, 제어되지 않은 상태로 물체를 놓으면 주변 사람에게 위험을 줄 수 있다. 따라서 낙상 중 물체 처리는 모든 상황에서 동일한 해제 명령을 사용하는 것이 아니라 작업별 안전 규칙(Task-Specific Safety Rule)과 환경 상황에 따라 결정되어야 한다.

전신 제어(Whole-Body Control)는 남아 있는 물리적 제약을 준수하면서 보호 운동을 협조적으로 실행할 수 있다. 목표 몸통 회전, 사지 구성(Limb Configuration), CoM 낮추기, 예상 접촉 거동(Prospective Contact Behavior)을 높은 우선순위의 목표로 표현할 수 있다. 동시에 관절 토크 한계, 속도 한계, 자체 충돌, 액추에이터 제약은 계속 활성화한다. 충돌이 임박하면 강체적인 추종(Rigid Tracking) 대신 임피던스 제어(Impedance Control) 또는 감쇠 제어(Damping Control)를 적용하여 관절이 과도한 강성으로 충돌에 저항하는 대신 에너지를 흡수하도록 할 수 있다.

충격 감지(Impact Detection)는 또 다른 중요한 상태 전환을 나타낸다. 갑작스러운 가속도, 접촉력 임펄스(Contact-Force Impulse), 새로운 촉각 접촉, 급격한 속도 변화는 신체가 환경에 도달했음을 나타낼 수 있다. 충돌 이후 제어기는 자동으로 이전 자세로 복귀하려 해서는 안 된다. 먼저 현재의 접촉을 안정화하고 잔여 운동(Residual Motion)을 억제하며 로봇이 기계적으로 안전한 정지 구성(Safe Resting Configuration)에 도달했는지를 판단한 후에 회복 동작을 시작해야 한다.

다시 일어서기를 시도하기 전에 낙상 후 평가(Post-Fall Assessment)가 필요하다. 로봇은 관절 상태, 액추에이터 고장, 센서 건전성(Sensor Health), 접촉 형상, 자세, 통신 상태를 평가해야 한다. 예상하지 못한 토크 오프셋(Torque Offset), 인코더 불일치(Encoder Inconsistency), 과도한 온도, 구조물 접촉, 사용 불가능한 센서는 손상이 발생했음을 나타낼 수 있다. 시스템이 계속해서 액추에이터를 구동하는 것이 안전하며 주변 환경에 충분한 공간이 존재한다고 판단한 경우에만 일어서기 시퀀스(Get-Up Sequence)를 시작해야 한다.

보호 낙상은 비상 정지(Emergency Stop) 및 고장 관리(Fault Management) 로직과도 통합되어야 한다. 일부 고장에서는 충돌을 줄이기 위해 제어된 액추에이터 동작이 필요하지만, 다른 고장에서는 계속해서 토크를 생성하는 것이 위험할 수 있다. 통신 손실, 전원 고장, 액추에이터 폭주(Actuator Runaway), 손상된 상태 추정(Corrupted State Estimation)은 서로 다른 대응을 요구할 수 있다. 따라서 안전 아키텍처(Safety Architecture)는 각 고장 등급(Fault Class)에서 어떤 보호 기능을 계속 사용할 수 있는지와 어떤 조건에서 즉각적인 액추에이터 정지 또는 수동적 거동(Passive Behavior)이 필요한지를 정의해야 한다.

검증(Validation)은 다양한 낙상 방향, 초기 속도, 지지 조건, 외란 크기를 대상으로 반복 가능한 시험을 수행해야 한다. 위험한 상황은 실제 하드웨어 시험 전에 시뮬레이션(Simulation)을 통해 탐색할 수 있으며, 계측된 실제 시험(Instrumented Physical Test)을 이용하여 충돌 가속도, 최대 접촉력(Peak Contact Force), 관절 토크, 흡수 에너지(Absorbed Energy), 부품 하중(Component Loading)을 측정할 수 있다. 보호 전략은 단순히 손상 발생 여부만 평가하는 것이 아니라 제어되지 않은 낙상(Uncontrolled Falling)과 비교하여 충돌의 심각도를 얼마나 일관되게 감소시키는지를 평가해야 한다.

따라서 낙상 감지와 보호 낙상은 휴머노이드 균형 안전(Humanoid Balance Safety)의 계층 구조를 완성한다. 일반적인 균형 조절(Normal Balance Regulation)은 현재의 지지 상태를 유지하려 하고, 밀림 회복은 운동량 제어(Momentum Control)와 스테핑을 통해 회복 가능한 영역을 확장하며, 이러한 메커니즘으로 더 이상 회복할 수 없을 때 보호 낙상이 활성화된다. 회복 불가능한 운동을 조기에 감지하고 자세, 접촉, 순응성(Compliance), 충돌 에너지를 의도적으로 관리함으로써 로봇은 제어되지 않은 붕괴(Uncontrolled Collapse)를 구조화된 안전 동작(Structured Safety Behavior)으로 전환할 수 있다.

## 04.07. Balance Under Load Carrying Heavy Objects [w/Code]

![](images/image7.png){width="7.268055555555556in" height="7.268055555555556in"}

무거운 물체 운반(Carrying a Heavy Object)은 휴머노이드 균형(Humanoid Balance)을 로봇 자체의 안정화 문제에서 로봇-페이로드 결합 동역학(Coupled Robot-Payload Dynamics) 문제로 변화시킨다. 페이로드(Payload)는 전체 질량, 질량중심(Center of Mass, CoM) 위치, 관성(Inertia), 필요한 관절 토크(Joint Torque), 지면 반력(Ground Reaction Force)을 변화시킨다. 발이 정지해 있더라도 물체를 이동하거나 회전시키면 상당한 외란이 발생할 수 있다. 따라서 균형 제어에서는 조작력을 작은 외부 외란으로 취급하는 대신 페이로드를 전체 물리 시스템의 일부로 모델링해야 한다.

결합 질량중심(Combined Center of Mass)은 로봇과 운반되는 물체를 모두 고려하여 결정된다. 페이로드를 몸통에서 멀리 전방으로 들고 있으면 질량이 중간 정도이더라도 모멘트 암(Moment Arm)으로 인해 결합 CoM이 전방으로 크게 이동할 수 있다. 로봇은 발목, 무릎, 엉덩이, 골반(Pelvis), 몸통(Torso)의 구성을 조정하여 이를 보상해야 한다. 따라서 페이로드가 없는 상태에서는 안정적인 자세라도 물체를 파지한 이후에는 비효율적이거나 토크 한계에 도달하거나 동역학적으로 실행 불가능한 자세가 될 수 있다.

페이로드의 위치(Payload Location)는 페이로드 질량만큼 중요할 수 있다. 물체를 몸통 가까이에서 들면 일반적으로 어깨, 허리, 엉덩이, 발목에 작용하는 중력 모멘트(Gravitational Moment)가 감소하지만, 팔을 뻗으면 지렛대 효과가 증가하여 액추에이터 요구량이 커진다. 따라서 균형 제어기는 로봇이 특정 질량을 들어 올릴 수 있는지만 판단하는 것이 아니라 해당 질량을 어느 위치에서 안전하게 운반할 수 있는지도 고려해야 한다. 도달 가능성(Reachability)과 하중 용량(Load Capacity)은 본질적으로 신체 구성에 따라 달라지는 물리량이다.

정확한 페이로드 추정(Payload Estimation)은 균형 조절 성능을 향상시킨다. 물체의 질량은 작업 정보로부터 사전에 알 수 있고, 손목 힘/토크 센싱(Wrist Force/Torque Sensing)을 통해 추정하거나 예상 관절력 또는 접촉력과 실제 측정값의 차이를 이용하여 추론할 수도 있다. 또한 제어기는 페이로드의 질량중심과 관성을 추정해야 할 수 있다. 이러한 물리적 특성의 오차는 특히 큰 물체를 가속, 감속 또는 회전시킬 때 잘못된 전신 보상(Whole-Body Compensation)을 발생시킬 수 있다.

물체가 강체적으로 파지되면 로봇과 페이로드를 확장된 다물체 시스템(Augmented Multibody System)으로 근사할 수 있다. 추가된 질량과 관성은 중력 보상(Gravity Compensation), 질량중심 동역학(Centroidal Dynamics), 역동역학(Inverse Dynamics), 접촉력 최적화(Contact-Force Optimization)에 반영되어야 한다. 이를 통해 팔 제어기는 국부적으로 물체를 지지하지만 하체 제어기는 실제 물리 시스템을 더 이상 정확히 표현하지 못하는 무부하 로봇 모델(Unloaded Robot Model)을 계속 사용하는 상황을 방지할 수 있다.

페이로드 질량이 증가하면 지면 반력이 증가하며, 이러한 힘은 접촉 제약(Contact Constraint)을 위반하지 않도록 분배되어야 한다. 양발 지지(Double Support) 상태에서는 각 발 내부의 압력중심(Center of Pressure, CoP)을 조절하면서 두 발 사이에 수직 및 수평력을 재분배할 수 있다. 페이로드가 비대칭인 경우 의도적으로 불균등한 하중 분배를 사용할 수 있지만, 한쪽 발에 지나치게 하중이 집중되면 사용 가능한 균형 여유(Balance Margin)가 감소하고 미끄러짐이나 액추에이터 포화(Actuator Saturation)의 위험이 증가할 수 있다.

하중을 운반하는 휴머노이드가 가속하거나 방향을 변경할 때 마찰(Friction)은 핵심적인 제약 조건이 된다. 수직력이 증가하면 사용 가능한 마찰력도 증가할 수 있지만, 무거운 페이로드는 운동 중 더 큰 수평력을 요구한다. 필요한 접촉 렌치(Contact Wrench)는 실행 가능한 마찰 영역(Feasible Friction Region) 내부에 유지되어야 하며 동시에 CoP도 지지 영역 안에 있어야 한다. 바닥의 물리적 특성이 불확실할 수 있고 페이로드 운동이 순간적인 힘을 발생시킬 수 있으므로 보수적인 마찰 여유(Friction Margin)를 유지하는 것이 바람직하다.

무거운 물체를 운반할 때는 관절 토크 한계(Joint Torque Limit)가 지배적인 제약 조건이 되는 경우가 많다. 어깨와 팔꿈치는 물체를 지지하고, 몸통, 엉덩이, 무릎, 발목은 그 결과 발생하는 중력 및 관성 모멘트를 상쇄한다. 따라서 제어기는 팔의 하중 용량만 확인하는 것이 아니라 전신의 토크 사용률(Torque Utilization)을 감시해야 한다. 어떤 파지 자세가 운동학적으로 도달 가능하더라도 하체 또는 몸통 액추에이터가 지속적으로 한계에 가까운 상태에서 동작한다면 장시간 운반에는 적합하지 않을 수 있다.

자세 적응(Posture Adaptation)을 통해 이러한 하중을 크게 감소시킬 수 있다. 로봇은 무릎을 약간 굽히고, 골반을 이동시키며, 몸통을 기울이고, 스탠스(Stance)를 넓히거나 손 위치를 조정하여 결합 CoM과 페이로드 모멘트를 유리한 영역에 유지할 수 있다. 이러한 조정은 개별적인 보상 운동이 아니라 협조된 전신 목표(Coordinated Whole-Body Objective)를 통해 생성되어야 한다. 선호 자세는 안정성 여유(Stability Margin), 액추에이터 부하, 관절 한계, 조작성(Manipulability), 작업 요구 조건을 종합적으로 고려하여 결정해야 한다.

대칭적인 양손 운반(Symmetric Two-Handed Carrying)은 조작력을 분산하고 몸통에 작용하는 회전 하중을 줄일 수 있기 때문에 일반적으로 유리하다. 그러나 물체 자체의 질량 분포가 비대칭이거나 파지점(Grasp Point)이 물체의 질량중심을 기준으로 대칭적이지 않을 수 있다. 손목 힘/토크 측정값을 통해 이러한 불균형을 감지할 수 있다. 전신 제어(Whole-Body Control)는 내부 파지력(Internal Grasp Force)이 전체 균형을 불필요하게 방해하지 않도록 팔의 힘, 몸통 자세, 발의 하중을 조정할 수 있다.

한손 운반(One-Handed Carrying)은 더 강한 비대칭 효과를 발생시킨다. 한쪽에서 들고 있는 페이로드는 결합 CoM을 측면으로 이동시키고 몸통과 골반에 모멘트를 발생시킨다. 로봇은 골반을 이동시키거나 상체를 반대 방향으로 기울이면서 양발 사이의 힘을 재분배하여 이를 보상할 수 있다. 그러나 과도한 몸통 기울기는 이동성(Mobility)을 감소시키고 관절 하중을 증가시키며 예상하지 못한 외란에 대응할 수 있는 여유를 감소시킬 수 있으므로 이러한 보상은 제한된 범위에서 이루어져야 한다.

페이로드 운동 자체도 균형 제어의 일부로 계획해야 한다. 물체를 빠르게 들어 올리거나 내리거나 흔들면 관성력이 발생하여 팔을 통해 몸통과 지지 접촉으로 전달된다. 속도, 가속도, 저크(Jerk)가 제한된 부드러운 궤적을 사용하면 이러한 외란을 감소시킬 수 있다. 빠른 조작이 불가피한 경우에는 예측된 페이로드 가속도(Predicted Payload Acceleration)를 피드포워드 동역학(Feedforward Dynamics)에 포함하여 외란이 발생하기 전에 다리와 몸통이 필요한 상쇄력을 준비하도록 할 수 있다.

무거운 물체 운반은 외란 회복 능력(Disturbance-Recovery Capability)도 감소시킨다. 이미 페이로드를 지지하기 위해 큰 토크를 생성하고 있는 액추에이터는 발목, 엉덩이 또는 스테핑(Stepping) 반응에 사용할 수 있는 토크 여유가 감소한다. 결합 시스템은 더 큰 운동량을 가질 수 있으며, 일반적으로 균형 회복에 사용할 수 있는 팔 운동도 물체 때문에 제한될 수 있다. 따라서 하중이 증가할수록 안정성 여유를 더욱 보수적으로 설정하고 남아 있는 회복 제어 능력(Recovery Authority)을 명시적으로 감시해야 한다.

무거운 페이로드를 운반하면서 스테핑을 수행하면 추가적인 동적 문제가 발생한다. 각 지지 전환(Support Transition) 과정에서는 일시적으로 한쪽 다리에 하중이 집중되며, 스윙 발(Swing Foot)의 배치는 이동된 결합 CoM을 고려해야 한다. 실행 가능한 접촉력과 관절 토크를 유지하려면 스텝 길이, 폭, 속도, 가속도를 감소시켜야 할 수 있다. 따라서 발걸음 계획(Footstep Planning)은 단순히 무부하 보행 패턴을 느리게 실행하는 것이 아니라 하중이 포함된 동역학 모델(Loaded Dynamic Model)을 기반으로 수행해야 한다.

보행 중 하중 전달(Load Transfer)은 페이로드 운동과 협조되어야 한다. 한쪽 발을 들어 올리기 전에 제어기는 물체를 동역학적으로 유리한 구성에 유지하면서 지지 상태를 입각 다리(Stance Leg) 방향으로 이동시켜야 한다. 한발 지지(Single Support) 중 갑작스러운 측면 페이로드 운동은 좁은 균형 여유를 빠르게 소진시킬 수 있다. 따라서 안정적인 하중 운반 이동(Load-Carrying Locomotion)을 위해 손 궤적(Hand Trajectory), 몸통 방향, CoM 운동, 발 배치를 협조적으로 제어하는 것이 필수적이다.

전신 최적화(Whole-Body Optimization)는 이러한 협조 제어에 적합한 프레임워크를 제공한다. 결정 변수(Decision Variable)에는 일반화 가속도(Generalized Acceleration), 관절 토크, 접촉력, 조작 렌치(Manipulation Wrench)가 포함될 수 있다. 균형, 물체 자세(Object Pose), 몸통 자세, 관절 구성은 목표로 표현하고, 마찰 원뿔(Friction Cone), 토크 한계, 접촉 제약, 파지 조건(Grasp Condition), 충돌 한계(Collision Limit)는 실행 가능성을 정의하는 제약 조건으로 표현할 수 있다. 이러한 최적화를 통해 페이로드 특성이나 지지 조건이 변화할 때 자세와 힘 분배를 자동으로 조정할 수 있다.

파지 안정성(Grasp Stability)은 신체 균형과 함께 고려해야 한다. 손의 힘을 증가시키면 물체의 미끄러짐을 방지할 수 있지만 액추에이터 용량을 소모하고 불필요한 내부 힘을 발생시킬 수 있다. 반대로 파지력이 부족하면 페이로드가 갑자기 이동하여 예상하지 못한 질량 분포 변화가 발생할 수 있다. 힘 또는 촉각 센싱(Force or Tactile Sensing)을 이용하여 물체 무게, 표면 마찰, 가속도, 불확실성에 따라 파지력을 조절하고, 전신 제어기는 그 결과 발생하는 반력을 관리할 수 있다.

안전 로직(Safety Logic)은 페이로드가 실행 가능한 운용 영역(Feasible Operating Envelope)을 초과하는 상황을 감지해야 한다. 지속적인 토크 포화, 감소하는 CoP 또는 ZMP 여유, 과도한 관절 온도, 파지 미끄러짐(Grasp Slip), 예상하지 못한 페이로드 운동, 불충분한 마찰 여유 등이 주요 지표가 될 수 있다. 균형을 잃을 때까지 작업을 계속하는 대신 로봇은 운동 속도를 줄이거나 물체를 안전한 표면에 내려놓고, 스탠스를 넓히거나, 지원을 요청하거나, 사전에 정의된 다른 하중 처리 전략(Load-Handling Strategy)으로 전환해야 한다.

검증(Validation)은 서로 다른 페이로드 질량, 질량중심 위치, 파지 위치, 운반 높이, 스탠스 폭, 운동 프로파일(Motion Profile)을 포함해야 한다. 주요 측정 항목에는 결합 CoM 거동, 발의 CoP, 지면 반력, 관절 토크 사용률, 몸통 편차(Torso Deviation), 파지력(Grasp Force), 열적 부하(Thermal Loading), 외란 회복 여유(Disturbance-Recovery Margin)가 포함된다. 정적 물체 유지뿐만 아니라 동적 운반도 시험해야 한다. 정지 상태에서 안전한 구성이 가속 또는 스테핑 과정에서는 실행 불가능해질 수 있기 때문이다.

따라서 무거운 하중에서의 균형(Balance Under Heavy Load)은 팔과 다리 제어를 서로 분리하는 대신 조작과 이동(Locomotion)을 통합적으로 고려해야 한다. 페이로드를 파지하는 순간부터 물체는 로봇의 유효 동역학(Effective Dynamics), 접촉 요구 조건, 액추에이터 여유, 회복 능력을 변화시킨다. 페이로드 특성을 전신 동역학에 포함하고 자세와 힘 분배를 적응적으로 조정하며 명시적인 안전 여유(Safety Margin)를 유지함으로써 휴머노이드는 상당한 무게의 물체를 운반하면서도 안정적이고 물리적으로 실행 가능한 동작을 유지할 수 있다.

## 04.08. Momentum Based Balance Controller [w/Code]

![](images/image8.png){width="7.268055555555556in" height="7.268055555555556in"}

운동량 기반 균형 제어(Momentum-Based Balance Control)는 휴머노이드 로봇(Humanoid Robot) 전체의 선형 운동량(Linear Momentum)과 각운동량(Angular Momentum)을 직접 고려하여 전역적인 운동을 조절한다. 각 관절을 독립적으로 안정화하는 대신 모든 신체 세그먼트(Body Segment)가 환경과 집합적으로 어떻게 상호작용하는지를 고려한다. 이러한 표현은 다리, 골반(Pelvis), 몸통(Torso), 팔의 운동이 접촉력(Contact Force)과 부유 베이스 동역학(Floating-Base Dynamics)을 통해 강하게 결합되는 휴머노이드에 특히 유용하다.

휴머노이드의 전체 운동량(Total Momentum)은 선형 운동량과 각운동량으로 구분할 수 있다. 선형 운동량은 질량중심(Center of Mass, CoM)의 속도와 직접적으로 관련되며, 각운동량은 일반적으로 CoM과 같은 특정 기준점을 중심으로 모든 신체 세그먼트가 수행하는 회전 운동을 나타낸다. 이 두 운동량을 결합한 질량중심 운동량(Centroidal Momentum)은 균형 계획(Balance Planning) 수준에서 모든 내부 관절 운동을 개별적으로 고려하지 않고도 전신 동역학(Whole-Body Dynamics)을 간결하게 표현할 수 있도록 한다.

질량중심 운동량은 질량중심 운동량 행렬(Centroidal Momentum Matrix)을 통해 일반화 로봇 속도(Generalized Robot Velocity)의 함수로 표현할 수 있다. 일반화 속도를 \\(\\dot{q}\\)라고 하면 운동량 벡터는 \\(h=A_G(q)\\dot{q}\\)로 표현할 수 있으며, 여기서 \\(A_G(q)\\)는 질량중심 운동량 행렬이다. 이러한 관계는 관절 및 부유 베이스 운동을 로봇의 균형 거동을 궁극적으로 결정하는 전역적인 선형 및 각운동량과 연결한다.

질량중심 운동량의 변화율(Rate of Change of Centroidal Momentum)은 외력(External Force)과 외부 모멘트(External Moment)에 의해 결정된다. 내부 관절력(Internal Joint Force)은 로봇 내부에서 운동을 재분배할 수 있지만 전체 시스템의 총 운동량을 직접 변화시킬 수는 없다. 지면 반력(Ground Reaction Force), 중력(Gravity), 손 접촉(Hand Contact), 환경 지지(Environmental Support), 기타 외부 상호작용이 운동량의 변화를 결정한다. 따라서 균형 제어는 원하는 운동량 변화율을 생성하는 실행 가능한 접촉 렌치(Feasible Contact Wrench)를 선택하는 문제로 표현할 수 있다.

선형 운동량의 경우 질량중심 동역학(Centroidal Dynamics)은 전체 질량, CoM 가속도, 중력, 외부 접촉력 사이의 일반적인 관계를 따른다. 따라서 선형 운동량 조절은 CoM 운동 조절과 밀접하게 관련된다. 로봇이 지지 영역(Support Region)의 경계를 향해 지나치게 빠르게 이동하기 시작하면 제어기는 마찰(Friction), 압력중심(Center of Pressure, CoP) 한계, 사용 가능한 액추에이터 제어 능력(Actuator Authority)을 만족하면서 CoM을 감속할 수 있는 접촉력을 생성한다.

각운동량은 단순한 CoM 기반 모델(CoM-Based Model)에서는 충분히 고려되지 않을 수 있는 추가적인 제어 메커니즘을 제공한다. 몸통, 골반, 팔, 다리의 운동을 이용하면 발이 지면과 접촉한 상태에서도 각운동량을 생성하거나 재분배할 수 있다. 이러한 능력은 큰 외란(Disturbance), 빠른 자세 전환(Posture Transition), 조작(Manipulation), 좁은 지지 조건(Narrow-Support Condition)처럼 CoP 조절만으로 충분한 균형 제어 능력을 확보하기 어려운 상황에서 특히 중요하다.

운동량 제어기(Momentum Controller)는 일반적으로 목표 선형 및 각운동량 궤적(Desired Linear and Angular Momentum Trajectory)을 정의한다. 정적인 서기 상태에서는 목표 선형 운동량이 거의 0이며 각운동량 역시 일반적으로 작은 값으로 조절된다. 동적 동작에서는 0이 아닌 운동량을 의도적으로 명령할 수 있다. 예를 들어 밀림 회복(Push Recovery) 과정에서는 몸통과 팔 운동을 이용하여 일시적으로 각운동량을 생성하고, 이후 안정적인 구성으로 복귀하기 위해 해당 운동량을 소산하거나 반대 방향으로 변화시켜야 한다.

운동량 피드백(Momentum Feedback)은 목표 운동량과 추정된 운동량 사이의 차이를 이용하여 구성할 수 있다. 이후 비례(Proportional), 미분(Derivative), 또는 모델 기반 피드백(Model-Based Feedback)을 사용하여 목표 운동량 변화율 명령(Desired Momentum-Rate Command)을 생성한다. 이러한 오차로부터 직접 관절 토크를 명령하는 대신 전신 제어기(Whole-Body Controller)가 자세, 접촉, 액추에이터 제약을 동시에 만족하면서 요구된 운동량 변화를 구현할 수 있는 접촉력과 관절 가속도를 결정한다.

접촉 렌치 최적화(Contact Wrench Optimization)는 운동량 기반 제어의 핵심 요소이다. 각각의 지지 발은 물리적으로 실행 가능한 한계 내에서만 힘과 모멘트를 생성할 수 있다. 수직력(Normal Force)은 압축 방향으로 유지되어야 하고, 접선력(Tangential Force)은 마찰 제약(Friction Constraint)을 만족해야 하며, 유효 CoP는 접촉면(Contact Surface) 내부에 존재해야 한다. 최적화기는 이러한 제약을 유지하면서 요구되는 전체 렌치를 사용 가능한 접촉점 사이에 분배하고 불필요하게 크거나 수치적으로 불안정한 힘이 발생하지 않도록 한다.

양발 지지(Double Support) 상태에서는 왼발과 오른발에 작용하는 다양한 힘의 조합을 통해 동일한 목표 질량중심 운동량 변화율을 생성할 수 있다. 이러한 여유성(Redundancy)은 강인성(Robustness)과 효율성을 향상시키는 데 활용할 수 있다. 제어기는 수직 하중을 균형 있게 분배하고, CoP 여유(CoP Margin)를 유지하며, 관절 토크를 감소시키거나 한쪽 다리의 하중 제거(Unloading)를 준비할 수 있다. 따라서 운동량 조절은 필요한 전역적인 효과를 결정하고, 접촉력 분배(Contact-Force Distribution)는 개별 지지점이 그 효과를 어떻게 공동으로 구현할지를 결정한다.

각운동량 조절(Angular Momentum Regulation)은 몸통 및 사지(Limb)의 거동과 강하게 연결된다. 상체가 예상하지 못하게 회전하면 제어기는 보상 지면 모멘트(Compensating Ground Moment)를 생성하거나 다른 신체 세그먼트를 협조적으로 움직여 발생한 운동량을 감소시킬 수 있다. 반대로 발을 이용한 제어가 한계에 가까워질 경우 몸통을 의도적으로 회전시키거나 팔을 흔들어 균형 회복을 지원할 수 있다. 국부적인 관절 보정이 의도하지 않게 전신 각운동량을 증가시킬 수 있으므로 이러한 동작은 전역적으로 계획해야 한다.

운동량과 영모멘트점(Zero Moment Point, ZMP) 또는 CoP 사이의 관계는 단순화된 균형 모델과 전신 균형 모델(Whole-Body Balance Model)을 비교할 때 중요하다. CoM 높이가 거의 일정하고 각운동량 변화가 작은 조건에서는 CoM과 ZMP 동역학만으로 균형을 근사할 수 있는 경우가 많다. 그러나 각운동량이 크게 변화하면 균형을 위해 필요한 접촉 렌치가 단순화된 역진자 모델(Inverted-Pendulum Model)의 예측과 달라지므로 질량중심 동역학을 사용하는 것이 더 유용하다.

운동량 기반 제어는 팔에 작용하는 힘이 전신을 교란할 수 있는 조작 작업에서 특히 유용하다. 물체를 밀고, 당기고, 들어 올리거나 운반하면 반력(Reaction Force)과 모멘트가 발생하여 몸통과 다리를 거쳐 지면으로 전달된다. 이러한 상호작용을 외부 렌치 모델(External Wrench Model)에 포함하면 조작력이 로봇을 불안정하게 만들기 전에 제어기가 발의 힘, CoM 가속도, 신체 운동량(Body Momentum)을 조정할 수 있다.

동일한 프레임워크는 다중 접촉 거동(Multi-Contact Behavior)에도 자연스럽게 적용할 수 있다. 휴머노이드는 양발, 한 손 또는 양손, 무릎, 기타 신체 표면을 동시에 사용하여 환경과 상호작용할 수 있다. 각각의 유효한 접촉은 질량중심 동역학에 외부 렌치를 추가한다. 단방향 힘(Unilateral Force), 마찰, 도달 가능성(Reachability), 접촉 안정성(Contact Stability) 제약을 만족한다면 제어기는 이러한 추가 접촉을 활용하여 실행 가능한 운동량 변화율 영역(Feasible Momentum-Rate Region)을 확대할 수 있다.

운동량 기반 균형 제어는 일반적으로 전신 역동역학(Whole-Body Inverse Dynamics) 또는 이차 계획법(Quadratic Programming, QP)과 통합된다. 결정 변수(Decision Variable)에는 일반화 가속도(Generalized Acceleration), 관절 토크, 접촉력이 포함될 수 있다. 운동량 변화율 추종(Momentum-Rate Tracking)은 높은 우선순위 목표로 표현하고, 운동 방정식(Equation of Motion)과 접촉 조건을 통해 물리적 일관성을 보장한다. 낮은 우선순위 목표에서는 몸통 방향, 팔 자세, 관절 구성, 조작성(Manipulability), 기타 작업별 물리량을 조절할 수 있다.

수학적으로 실행 가능한 외부 렌치라도 실제로는 구현할 수 없는 관절 토크를 요구할 수 있으므로 균형 제어기는 액추에이터 실행 가능성(Actuator Feasibility)을 유지해야 한다. 접촉력은 로봇의 운동학적 구조(Kinematic Structure)를 통해 전달되며, 특이점(Singularity)이나 관절 한계에 가까운 구성에서는 액추에이터 요구량이 크게 증가할 수 있다. 따라서 토크 한계(Torque Limit)는 목표 운동량 명령을 생성한 이후에 확인하는 것이 아니라 최적화 과정에 명시적으로 포함해야 한다.

정확한 상태 추정(State Estimation)은 운동량 피드백에 필수적이다. 선형 운동량은 전체 질량과 CoM 속도에 의존하고, 각운동량은 신체 구성과 각 세그먼트의 속도 정보를 필요로 한다. 관절 인코더(Joint Encoder), IMU 측정값, 부유 베이스 추정(Floating-Base Estimation), 접촉 정보를 로봇 모델과 결합하여 질량중심 운동량을 추정한다. 속도 추정에서 발생하는 잡음이나 지연은 특히 빠른 외란 제거 과정에서 피드백 성능을 직접적으로 저하시킬 수 있다.

기준값 생성(Reference Generation)에서도 사용 가능한 접촉 제어 능력을 초과하는 운동량 변화를 명령하지 않아야 한다. 지나치게 공격적인 목표 CoM 감속은 마찰 한계를 초과하는 수평력을 요구할 수 있으며, 빠른 각운동량 보정은 사용할 수 없는 접촉 모멘트나 관절 토크를 요구할 수 있다. 따라서 운동량 기준값(Momentum Reference)은 실행 가능한 렌치 영역(Feasible Wrench Region)을 고려하여 생성해야 하며, 이를 위해 예측 최적화(Predictive Optimization) 또는 포화 인식 제어(Saturation-Aware Control)를 사용할 수 있다.

모델 예측 제어(Model Predictive Control, MPC)는 유한 예측 구간(Finite Horizon)에서 미래의 CoM, 운동량, 접촉력, 지지 전환(Support Transition)을 예측함으로써 운동량 조절을 확장할 수 있다. 이를 통해 현재 접촉 구성이 부족해지는 시점을 사전에 예측하고 안정성 경계(Stability Boundary)에 도달하기 전에 운동량을 조정할 수 있다. 미래의 발 디딤(Footstep) 또는 손 접촉도 함께 고려할 수 있으므로 운동량 조절을 접촉 계획(Contact Planning) 및 동적 이동(Dynamic Locomotion)과 직접 연결할 수 있다.

검증(Validation)에서는 운동량 추종 성능과 실제 물리적 균형 성능을 함께 평가해야 한다. 주요 측정 항목에는 선형 및 각운동량 오차, CoM 궤적, CoP 또는 ZMP 여유, 접촉력 분배, 관절 토크 사용률(Joint Torque Utilization), 외란 제거 능력(Disturbance-Rejection Capability), 정착 거동(Settling Behavior)이 포함된다. 정적 서기, 자세 변화, 외부 밀림, 조작력, 접촉 전환을 포함한 시험을 통해 다양한 운용 조건에서 전역 운동량 조절이 효과적으로 유지되는지 확인해야 한다.

따라서 운동량 기반 균형 제어는 휴머노이드 안정화를 위한 통합된 동역학적 표현(Unified Dynamic Representation)을 제공한다. 선형 운동량은 전역적인 병진 운동(Translational Behavior)을 나타내고, 각운동량은 전신의 회전 효과(Rotational Whole-Body Effect)를 표현하며, 외부 접촉 렌치는 이 두 운동량이 어떻게 변화하는지를 결정한다. 접촉 및 액추에이터 제약 내에서 이러한 물리량을 제어함으로써 휴머노이드는 서기, 조작, 외란 회복, 다중 접촉 지지, 동적 이동 전반에서 전신을 협조적으로 사용할 수 있다.

## 04.09. Balance State Estimation Floating Base [w/Code]

![](images/image9.png){width="7.268055555555556in" height="7.268055555555556in"}

균형 상태 추정(Balance State Estimation)은 로봇의 베이스가 세계 좌표계(World Frame)에 직접 고정되지 않은 부유 베이스(Floating Base)를 갖는 휴머노이드 안정화(Humanoid Stabilization)에 필요한 실시간 물리 상태를 제공한다. 고정 베이스 매니퓰레이터(Fixed-Base Manipulator)와 달리 골반(Pelvis)이나 몸통(Torso)의 자세는 관절 인코더(Joint Encoder)만으로 얻을 수 없다. 따라서 추정기는 고유수용성 센싱(Proprioceptive Sensing)과 운동학적·동역학적 제약을 결합하여 베이스 위치와 방향, 선속도, 각속도, 접촉 상태(Contact State), 관련 전신 상태를 복원해야 한다.

부유 베이스 휴머노이드는 일반적으로 6개의 비구동 베이스 자유도(Unactuated Base Degree of Freedom)와 그 뒤에 연결된 구동 관절 좌표(Actuated Joint Coordinate)로 모델링된다. 따라서 일반화 구성(Generalized Configuration)은 전역 베이스 병진(Global Base Translation), 베이스 방향(Base Orientation), 내부 관절 위치를 포함한다. 부유 베이스를 직접 제어하는 액추에이터가 존재하지 않기 때문에 베이스 운동은 접촉력과 전신 동역학(Whole-Body Dynamics)에 의해 결정된다. 신뢰할 수 있는 균형 제어를 위해서는 이러한 비구동 상태를 충분히 정확하고 낮은 지연으로 추정해야 한다.

관성측정장치(Inertial Measurement Unit, IMU)는 부유 베이스 추정을 위한 핵심 센서이다. 자이로스코프(Gyroscope)는 각속도를 제공하고, 가속도계(Accelerometer)는 병진 가속도와 중력 효과가 함께 포함된 비력(Specific Force)을 측정한다. 이러한 신호를 적분하면 높은 주파수에서 방향, 속도, 위치를 전파할 수 있다. 그러나 센서 바이어스(Sensor Bias)와 잡음은 적분 과정에서 누적되어 드리프트(Drift)를 발생시키므로 관성 전파(Inertial Propagation)는 추가적인 물리 정보를 이용하여 지속적으로 보정해야 한다.

방향 추정(Orientation Estimation)은 일반적으로 위치 추정보다 신뢰성이 높다. 중력은 중간 정도의 운동 상태에서 롤(Roll)과 피치(Pitch)를 추정하기 위한 강력한 기준을 제공하기 때문이다. 자이로스코프 적분은 빠른 회전 변화를 포착하고, 동적 가속도를 적절히 처리하면 가속도계 정보를 이용하여 느린 방향 드리프트를 보정할 수 있다. 반면 요(Yaw)는 중력으로 방위를 구속할 수 없기 때문에 더 어렵다. 따라서 장기적인 요 드리프트를 방지하려면 추가 센싱이나 접촉 기반 정보가 필요할 수 있다.

관절 인코더는 부유 베이스와 발 및 기타 신체 세그먼트 사이의 관계를 계산하는 데 필요한 관절 구성을 제공한다. 특정 발이 지면에 고정되어 있다고 가정하면 순기구학(Forward Kinematics)을 이용하여 측정된 관절 구성과 베이스 자세 사이에 제약을 형성할 수 있다. 이러한 접촉 제약(Contact Constraint)은 관성 드리프트를 보정하고 베이스 위치와 속도에 관한 정보를 제공한다. 그러나 이러한 보정의 신뢰성은 고정된 것으로 가정한 접촉이 실제로 안정적인지에 직접적으로 의존한다.

따라서 접촉 추정(Contact Estimation)은 균형 상태 추정의 핵심 구성 요소이다. 발 힘/토크 센서(Foot Force/Torque Sensor), 관절 토크 정보, 촉각 센싱(Tactile Sensing), 운동학적 속도(Kinematic Velocity), 예상 보행 단계(Expected Gait Phase)를 결합하여 발이 로봇을 안정적으로 지지하고 있는지, 지면에 가볍게 접촉하고 있는지, 미끄러지고 있는지, 또는 공중에 있는지를 판단할 수 있다. 미끄러지는 발을 정지 상태로 잘못 판단하면 추정된 베이스 운동에 체계적인 오차가 발생하고 균형 피드백 성능이 빠르게 저하될 수 있다.

양발 지지(Double Support) 상태에서는 두 발 모두 부유 베이스 운동에 대한 제약을 제공한다. 이상적으로 이러한 제약은 관측 가능성(Observability)을 향상시키고 드리프트를 감소시키지만 실제 접촉은 완전한 강체가 아니다. 발의 순응성(Foot Compliance), 발바닥 변형(Sole Deformation), 구조적 유연성(Structural Flexibility), 불규칙한 지면, 작은 미끄러짐으로 인해 두 접촉 제약이 서로 일치하지 않을 수 있다. 따라서 추정기는 측정된 모든 발 자세를 정확한 강체 제약으로 강제하기보다 접촉 불확실성(Contact Uncertainty)을 처리할 수 있어야 한다.

한발 지지(Single Support) 상태에서는 입각 발(Stance Foot)이 주요 운동학적 기준이 되고 스윙 발(Swing Foot)은 자유롭게 움직인다. 추정기는 베이스 자세나 속도에 불연속성이 발생하지 않도록 지지 발 사이를 전환해야 한다. 착지(Touchdown) 시에는 새로운 접촉이 안정적인 지지를 제공한다는 충분한 증거가 확보된 이후에만 해당 제약을 추가해야 한다. 이륙(Liftoff) 시에는 의도적인 발 운동이 부유 베이스 운동으로 잘못 해석되지 않도록 해당 접촉 제약을 신속하게 제거해야 한다.

대표적인 상태 추정 프레임워크로 확장 칼만 필터(Extended Kalman Filter, EKF)를 사용할 수 있다. 필터는 IMU 측정값과 프로세스 모델(Process Model)을 이용하여 상태를 전파한 후 접촉 운동학(Contact Kinematics)과 기타 관측값을 이용하여 보정한다. 상태에는 베이스 방향, 위치, 속도, IMU 바이어스, 경우에 따라 접촉점 위치(Contact-Point Location)가 포함될 수 있다. 공분산 전파(Covariance Propagation)는 불확실성을 표현하고 예상 신뢰도에 따라 측정값에 가중치를 부여하기 위한 체계적인 방법을 제공한다.

불변 필터링(Invariant Filtering) 방법도 부유 베이스 로봇에 유용할 수 있다. 상태에 포함되는 회전과 강체 변환(Rigid-Body Transformation)은 일반적인 유클리드 벡터 공간(Euclidean Vector Space)에 자연스럽게 존재하지 않기 때문이다. 방향과 자세의 기하학적 구조를 유지함으로써 불변 추정기(Invariant Estimator) 또는 다양체 기반 추정기(Manifold-Based Estimator)는 큰 회전에서도 추정의 일관성을 향상시킬 수 있다. 어떤 수식을 사용하더라도 추정기는 고대역폭 균형 제어(High-Bandwidth Balance Control)와 전신 제어에 사용할 수 있는 안정적인 출력을 제공해야 한다.

베이스 선속도(Base Linear Velocity)는 동적 균형(Dynamic Balance)이 로봇의 현재 위치뿐만 아니라 얼마나 빠르게 움직이는지에 크게 의존하기 때문에 특히 중요하다. CoM 속도, 캡처 포인트(Capture Point), 발산 운동 성분(Divergent Component of Motion, DCM), 운동량 추정(Momentum Estimation)은 모두 신뢰할 수 있는 속도 정보를 필요로 한다. 작은 속도 바이어스조차 예측된 안정성 지표를 충분히 변화시켜 불필요한 회복 동작을 유발하거나 필요한 보정 동작의 시작을 지연시킬 수 있다.

추정된 부유 베이스 상태는 로봇 모델과 결합되어 질량중심 위치와 속도를 계산하는 데 사용된다. 각 링크(Link)는 자신의 질량과 현재 자세에 따라 전체 CoM 계산에 기여하므로 추정된 베이스와 측정된 관절 상태를 이용하여 전신 CoM을 복원할 수 있다. 이러한 물리량은 CoM 조절, ZMP 또는 CoP 감시, 운동량 제어(Momentum Control), 발걸음 계획(Footstep Planning), 외란 회복(Disturbance Recovery)을 지원한다. 따라서 베이스 상태 오차는 여러 균형 제어 계층으로 직접 전파된다.

질량중심 운동량 추정(Centroidal Momentum Estimation)은 세그먼트 속도와 관성 특성(Inertial Property)을 포함함으로써 이러한 과정을 확장한다. 선형 운동량은 전체 질량과 CoM 속도에 의존하며, 각운동량(Angular Momentum)은 CoM을 기준으로 모든 신체 세그먼트의 운동을 반영한다. 따라서 정확한 부유 베이스 속도는 운동량 기반 균형 제어(Momentum-Based Balance Control)에 필수적이다. 센서 융합을 통해 더 좋은 속도 추정값을 얻을 수 있다면 위치 신호를 단순 미분하여 발생하는 잡음이 큰 속도 추정은 피하는 것이 바람직하다.

힘 센싱(Force Sensing)은 물리적인 지지 상태에 대한 상호 보완적인 정보를 제공한다. 발 힘/토크 센서를 이용하면 지면 반력, 접촉 모멘트(Contact Moment), CoP를 추정할 수 있다. 이러한 물리량은 전역 베이스 자세를 직접 제공하지는 않지만 추정된 운동이 실제 지지 상호작용과 일치하는지를 보여준다. 예상하지 못한 힘 변화는 충격(Impact), 하중 제거(Unloading), 미끄러짐, 외부 외란 또는 접촉 모델 불일치(Contact-Model Mismatch)를 나타낼 수 있으며, 이는 추정기의 신뢰도에 반영되어야 한다.

외부 외란은 IMU가 측정한 가속도가 예상된 로봇 운동과 예상하지 못한 외력 모두에서 발생할 수 있기 때문에 중요한 문제를 만든다. 추정기는 모든 가속도를 방향 오차나 센서 바이어스로 해석해서는 안 된다. 동역학 모델(Dynamic Model), 접촉력, 운동량 관측기(Momentum Observer), 이노베이션 잔차(Innovation Residual)를 이용하면 타당한 운동과 일관되지 않은 측정값을 구분하는 데 도움이 된다. 강인한 상태 추정(Robust State Estimation)은 외부 밀림, 충격, 빠른 회복 동작에서 특히 중요하다.

발 미끄러짐(Foot Slip)은 보행 로봇 상태 추정에서 가장 유용한 가정 중 하나를 위반하므로 명시적으로 처리해야 한다. 미끄러짐은 일관되지 않은 발 속도, 예상하지 못한 접선력(Tangential Force), 마찰 여유 위반(Friction-Margin Violation), 여러 센싱 방식 사이의 불일치를 통해 감지할 수 있다. 접촉의 신뢰성이 감소하면 해당 측정값의 가중치를 낮추거나 정지 접촉 제약(Stationary Contact Constraint)을 제거해야 한다. 미끄러지는 접촉을 계속 신뢰하면 제어기에 잘못된 안정 상태를 제공할 수 있다.

균형 상태 추정이 장시간 동안 전역적으로 일관된 상태를 유지해야 한다면 추가적인 외부수용성 센서(Exteroceptive Sensor)를 이용하여 장기 드리프트를 감소시킬 수 있다. 시각-관성 오도메트리(Visual-Inertial Odometry), 라이다 오도메트리(LiDAR Odometry), 깊이 센싱(Depth Sensing), 외부 위치추정(External Localization)은 고유수용성 센싱만으로 얻기 어려운 위치 및 방위 보정 정보를 제공할 수 있다. 이러한 측정값은 일반적으로 IMU 및 인코더 피드백보다 낮은 주파수 또는 더 높은 지연을 가지므로 고주파 부유 베이스 추정기를 대체하기보다 보완해야 한다.

상태 추정은 서로 다른 주파수와 지연으로 동작하는 여러 센서를 결합하기 때문에 타이밍(Timing)과 동기화(Synchronization)가 매우 중요하다. 서로 다른 물리적 시점을 나타내는 IMU 샘플, 인코더 측정값, 힘 측정값을 결합하면 실제로 발생하지 않은 운동이 존재하는 것처럼 보일 수 있다. 하드웨어 타임스탬프(Hardware Timestamp), 동기화된 클록(Synchronized Clock), 결정론적 통신(Deterministic Communication), 신중한 버퍼링(Buffering)을 통해 일관성을 향상시킬 수 있다. 고대역폭 균형 제어에서는 정확한 상태라도 늦게 전달되면 부정확한 상태만큼 문제가 될 수 있다.

추정기 출력(Estimator Output)은 하나의 최적 상태 추정값만 제공하는 것이 아니라 불확실성(Uncertainty) 또는 건전성 정보(Health Information)도 함께 제공해야 한다. 증가하는 공분산(Covariance), 반복되는 이노베이션 거부(Innovation Rejection), 일관되지 않은 접촉, 사용 불가능한 센서는 추정 신뢰도가 저하되고 있음을 나타낼 수 있다. 균형 제어기는 완전히 신뢰할 수 있는 상태 정보가 제공되는 것처럼 계속 동작하는 대신 이동 속도를 줄이고, 안정성 여유(Stability Margin)를 증가시키며, 공격적인 한발 지지 동작을 피하거나 더 안전한 자세로 전환할 수 있다.

검증(Validation)은 정적 서기, 제어된 신체 흔들림(Controlled Body Sway), 보행, 지지 전환, 외부 밀림, 발 미끄러짐, 불규칙한 지면, 일시적인 센서 성능 저하를 포함해야 한다. 가능한 경우 추정된 베이스 자세와 속도를 모션 캡처(Motion Capture) 또는 다른 고정밀 기준 시스템과 비교할 수 있다. 주요 평가 지표에는 방향 오차, 위치 드리프트, 속도 오차, 접촉 감지 지연(Contact-Detection Delay), 추정기 지연(Estimator Latency), 접촉 전환 시 불연속성, 그리고 그 결과 나타나는 균형 제어 성능이 포함된다.

따라서 부유 베이스 균형 상태 추정(Floating-Base Balance State Estimation)은 휴머노이드 안정화를 위한 감각적 기반(Sensory Foundation)의 역할을 한다. IMU 전파(IMU Propagation)는 빠른 운동 정보를 제공하고, 관절 운동학(Joint Kinematics)은 신체를 지지 접촉과 연결하며, 힘 센싱은 실제 물리적 상호작용을 식별하고, 확률적 융합(Probabilistic Fusion)은 잡음과 불확실성을 관리한다. 베이스 운동, CoM 상태, 전신 동역학에 대한 정확하고 접촉 인식적인 추정(Contact-Aware Estimation)을 유지함으로써 제어기는 서기, 이동(Locomotion), 운동량 조절, 회복 동작에 필요한 신뢰성 높은 결정을 수행할 수 있다.

## 04.10. Balance Controller Validation Perturbation Test

![](images/image10.png){width="7.268055555555556in" height="7.268055555555556in"}

균형 제어기 검증(Balance-Controller Validation)은 휴머노이드가 실제 운용 조건을 재현하는 제어된 외란(Controlled Disturbance)에 노출되었을 때 안정성을 유지하거나 회복할 수 있는지를 평가한다. 정상적인 서기 동작만으로는 환경이 예측 가능한 상황에서 많은 제어기가 우수한 성능을 보이기 때문에 강인성(Robustness)을 충분히 입증할 수 없다. 외란 시험(Perturbation Testing)은 의도적으로 로봇을 평형 상태(Equilibrium)에서 벗어나게 하고 센싱(Sensing), 상태 추정(State Estimation), 접촉 제어(Contact Control), 운동량 조절(Momentum Regulation), 회복 로직(Recovery Logic)이 물리적 한계 내에서 통합적으로 동작하는지를 측정한다.

검증 프로그램(Validation Program)은 명확하게 정의된 안정성 및 회복 기준(Stability and Recovery Criteria)에서 시작해야 한다. 성공 조건에는 로봇이 직립 상태를 유지하고, 의도된 접촉을 보존하며, 질량중심(Center of Mass, CoM)을 허용 가능한 영역으로 복귀시키고, 과도한 진동 없이 안정화되는 것이 포함될 수 있다. 보다 어려운 시험에서는 회복 스테핑(Recovery Stepping)을 허용하면서 제어기가 적절한 전략을 선택하는지를 평가할 수 있다. 실패 기준에는 낙상, 제어되지 않은 접촉 손실, 액추에이터 포화(Actuator Saturation), 과도한 미끄러짐, 위험한 충돌, 비상 정지(Emergency Shutdown)가 포함되어야 한다.

외란(Perturbation)은 방향, 크기, 지속 시간, 적용 위치(Application Point), 시간적 프로파일(Temporal Profile)을 기준으로 특성화해야 한다. 전방, 후방, 측면, 대각선 외란은 서로 다른 발목, 엉덩이, 몸통, 스테핑 반응의 조합을 시험한다. 짧은 충격(Impulse)은 주로 운동량을 변화시키는 반면, 지속적인 힘은 변위된 평형 상태(Displaced Equilibrium)를 형성하는 제어기의 능력을 시험한다. 동일한 크기의 힘이라도 신체의 서로 다른 높이에 적용하면 병진 및 회전 효과가 크게 달라질 수 있다.

의미 있는 비교를 위해서는 반복 가능한 외란 생성(Repeatable Disturbance Generation)이 필수적이다. 계측된 밀림 장치(Instrumented Push Device), 진자 충격기(Pendulum Impactor), 선형 액추에이터(Linear Actuator), 케이블 당김 장치(Cable-Pull Mechanism), 제어된 이동 플랫폼(Moving Platform)을 이용하면 사람이 직접 미는 것보다 일정한 외란을 생성할 수 있다. 힘 센서(Force Sensor) 또는 로드 셀(Load Cell)을 사용하여 실제 로봇에 전달된 외란을 기록해야 한다. 동일한 조건을 반복하면 제어기 버전, 게인 설정(Gain Setting), 추정기, 회복 전략을 주관적 판단이 아니라 정량적으로 비교할 수 있다.

낮은 강도의 시험을 통해 강한 외란을 적용하기 전에 기본적인 고정 발 안정화 영역(Fixed-Foot Stabilization Region)을 확인해야 한다. 작은 외란은 비상 회복 동작을 활성화하지 않은 상태에서 발목 제어, 압력중심(Center of Pressure, CoP) 조절, 상태 추정, 감쇠(Damping)가 올바르게 동작하는지를 보여준다. 이후 외란의 크기를 체계적으로 증가시켜 엉덩이 운동, 팔 운동 또는 스테핑이 필요한 수준까지 시험할 수 있다. 이러한 단계적 접근은 서로 다른 균형 제어 메커니즘 사이의 경계를 확인하는 데 유용하다.

CoM 궤적(CoM Trajectory)은 주요 검증 신호 중 하나이다. 최대 변위(Peak Displacement)는 외란이 로봇을 기준 상태에서 얼마나 멀리 이동시키는지를 나타내며, CoM 속도는 외란의 동적 심각도를 보여준다. 회복 시간(Recovery Time)과 잔류 진동(Residual Oscillation)은 제어기가 외란에 의해 생성된 운동을 얼마나 효과적으로 소산하는지를 나타낸다. 동일한 변위라도 스탠스 폭(Stance Width)에 따라 의미가 달라질 수 있으므로 CoM 거동은 지지 형상(Support Geometry)과 함께 평가해야 한다.

CoP와 영모멘트점(Zero Moment Point, ZMP) 측정은 제어기가 사용 가능한 지지 영역을 어떻게 활용하는지에 대한 상호 보완적인 정보를 제공한다. 고정 발 회복(Fixed-Foot Recovery) 과정에서는 복원 지면 반력(Restoring Ground Reaction Force)을 생성하기 위해 CoP가 발의 경계 방향으로 빠르게 이동할 수 있다. 남아 있는 CoP 여유(CoP Margin)는 아직 사용할 수 있는 접촉 제어 능력(Contact Authority)을 나타낸다. 지지 영역의 경계 부근에서 반복적으로 포화가 발생하면 제어기가 물리적 안정성 한계에 지나치게 가깝게 동작하고 있음을 의미할 수 있다.

캡처 포인트(Capture Point) 또는 발산 운동 성분(Divergent Component of Motion, DCM)은 회복 가능성(Recoverability)을 평가하기 위한 동적 지표를 제공할 수 있다. 이러한 물리량은 CoM 속도를 포함하기 때문에 CoM 자체가 지지 영역을 벗어나기 전에 불안정성을 나타낼 수 있다. 시험 중 이러한 궤적을 분석하면 고정 발 반응이 불충분해지는 시점과 스테핑이 적절한 시점에 활성화되는지를 확인할 수 있다. 전환이 지연되면 회복에 실패할 수 있으며, 지나치게 이른 스테핑은 회복 로직이 과도하게 보수적임을 나타낼 수 있다.

접촉력(Contact Force)은 부드러운 하중 재분배와 짧은 충격 이벤트를 모두 포착할 수 있을 만큼 충분한 대역폭(Bandwidth)으로 기록해야 한다. 수직력은 하중 제거(Unloading)와 지지 전환(Support Transfer)을 보여주며, 접선력(Tangential Force)은 마찰 사용률과 미끄러짐 위험을 평가하는 데 도움이 된다. 접촉 모멘트(Contact Moment)와 CoP 궤적은 각 발이 안정화에 어떻게 기여하는지를 보여준다. 양발 지지(Double Support) 상태에서는 좌우 힘 분배를 통해 제어기가 사용 가능한 여유성(Redundancy)을 효율적으로 활용하는지도 평가할 수 있다.

관절 토크(Joint Torque)와 액추에이터 사용률(Actuator Utilization)은 이론적인 안정성과 물리적으로 실행 가능한 안정성을 구분하는 데 필요하다. 시험 중 로봇이 직립 상태를 유지하더라도 토크, 속도 또는 전류 한계(Current Limit) 부근에서 반복적으로 동작할 수 있다. 이러한 거동은 더 강한 외란에 대응할 수 있는 여유를 감소시키며 반복 운용 시 열적 문제(Thermal Problem)를 발생시킬 수 있다. 따라서 검증에서는 외부 외란의 크기와 함께 최대 및 지속 액추에이터 사용률을 기록해야 한다.

몸통 방향(Torso Orientation)과 각속도(Angular Velocity)는 제어기가 상체 운동에 얼마나 의존하는지를 보여준다. 작은 외란에서는 일반적으로 제한된 몸통 편차가 나타나야 하지만, 큰 외란에서는 엉덩이 및 운동량 전략(Momentum Strategy)을 의도적으로 활성화할 수 있다. 질량중심 각운동량(Centroidal Angular Momentum)을 측정하면 이러한 운동이 회복에 효과적으로 기여하는지를 확인할 수 있다. 외란이 제거된 후에도 과도한 각운동량이 남아 있다면 상체 운동과 접촉력 조절 사이의 협조가 부족하다는 것을 의미할 수 있다.

상태 추정 성능(State-Estimation Performance)은 독립적인 하위 시스템으로 평가하는 것이 아니라 균형 시험의 일부로 함께 평가해야 한다. 외부 밀림은 빠른 베이스 가속도, 접촉력 변화, 잠재적인 발 미끄러짐을 발생시켜 부유 베이스 추정(Floating-Base Estimation)을 어렵게 만든다. 추정기 지연(Estimator Latency), 속도 오차, 접촉 상태 전환, 이노베이션 거동(Innovation Behavior)을 기록해야 한다. 추정된 상태가 실제 로봇의 물리적 상태보다 크게 지연되면 제어기는 외란으로부터 신뢰성 있게 회복할 수 없다.

지지 형상은 회복 능력에 큰 영향을 주므로 외란 시험에는 다양한 스탠스 구성(Stance Configuration)을 포함해야 한다. 넓은 스탠스와 좁은 스탠스는 측면 CoP 범위를 변화시키며, 앞뒤로 엇갈린 발 배치(Staggered Foot Placement)는 방향별 안정성을 변화시킨다. 한발 지지(Single Support) 시험은 사용 가능한 접촉 영역이 훨씬 작기 때문에 특히 어렵다. 이러한 조건을 비교하면 제어기 자체의 한계와 로봇의 기하학적 구조에서 직접 발생하는 안정성 제약을 구분할 수 있다.

표면 조건(Surface Condition)도 다양하게 변경하여 시험해야 한다. 마찰력이 높은 실험실 바닥에서는 발견되지 않는 문제가 매끄럽거나, 순응적이거나, 경사지거나, 약간 불규칙한 표면에서 나타날 수 있다. 마찰이 감소하면 실행 가능한 지면 반력이 변화하고 CoP가 기하학적 경계에 도달하기 전에 미끄러짐이 발생할 수 있다. 강인한 제어기는 접촉 실행 가능성(Contact Feasibility)이 감소하는 것을 인식하고 실제 표면이 지지할 수 없는 수평력을 명령하지 않아야 한다.

물체 운반은 질량 분포, 관성, 관절 하중, 사용 가능한 회복 제어 능력을 변화시키므로 페이로드 조건(Payload Condition)도 중요하다. 검증에서는 대표적인 페이로드와 파지 구성(Grasp Configuration)을 적용하여 선택된 외란 시험을 반복해야 한다. 무부하 상태에서 강한 밀림을 견디는 제어기라도 물체 운반으로 액추에이터 여유가 소모된 상태에서는 동일한 외란에 실패할 수 있다. 따라서 결합된 로봇-페이로드 질량중심과 운동량도 평가에 포함해야 한다.

회복 스텝 시험(Recovery-Step Test)에서는 발을 디디는 위치뿐만 아니라 타이밍도 평가해야 한다. 고정 발 안정화가 더 이상 실행 가능하지 않으면 로봇은 적절한 다리의 하중을 제거하고, 도달 가능한 스윙 궤적(Swing Trajectory)을 생성하며, 발산 운동이 회복 불가능한 상태가 되기 전에 새로운 지지 접촉을 형성해야 한다. 발 배치 오차(Foot-Placement Error), 착지 시간(Touchdown Time), 스윙 여유 높이(Swing Clearance), 접촉 후 운동량(Post-Contact Momentum), 필요한 회복 스텝 수를 이용하여 스테핑 성능을 정량적으로 평가할 수 있다.

결정론적인 단일 방향 시험을 통과한 이후에는 다방향 및 예측 불가능한 외란(Multi-Directional and Unexpected Perturbation)을 시험해야 한다. 외란 발생 시점을 무작위화하면 제어기가 동기화되거나 사전에 프로그래밍된 반응에 의존하는 것을 방지할 수 있다. 안전한 범위 내에서 방향과 크기도 무작위화하여 회복 결정이 실제 측정 상태에 기반하는지를 시험할 수 있다. 이러한 시험은 충돌, 인간-로봇 상호작용(Human-Robot Interaction), 조작 외란, 불확실한 현장 조건을 더욱 현실적으로 재현한다.

반복 외란(Repeated Perturbation)은 한 번의 성공적인 회복을 넘어서는 강인성을 평가한다. 두 번째 외란은 로봇이 운동량을 완전히 소산하거나 명목 자세(Nominal Posture)를 복원하기 전에 발생할 수 있다. 연속 시험(Sequential Testing)은 정착 시간(Settling Time), 접촉 상태 초기화(Contact Reset), 추정기 수렴(Estimator Convergence), 액추에이터 여유에 관한 제어기의 가정을 검증한다. 또한 개별적인 실험에서는 나타나지 않는 열 축적(Thermal Accumulation)이나 점진적인 자세 드리프트(Posture Drift)를 발견할 수 있다.

안전 절차(Safety Procedure)는 검증 환경에 통합되어야 한다. 오버헤드 지지 시스템(Overhead Support System), 낙상 방지 장치(Fall-Arrest Mechanism), 비상 정지 인터페이스(Emergency-Stop Interface), 출입 제한 구역(Exclusion Zone), 단계적 외란 한계를 이용하면 정상적인 균형 거동에 실질적인 영향을 주지 않으면서 작업자와 하드웨어를 보호할 수 있다. 낮은 에너지 조건에서 일관된 성공이 확인된 이후에만 시험 강도를 증가시켜야 한다. 보호 장치는 유효한 측정 중 의도하지 않은 안정화 힘을 제공하지 않도록 구성해야 한다.

시뮬레이션(Simulation), 소프트웨어 인 더 루프(Software-in-the-Loop, SIL), 하드웨어 인 더 루프(Hardware-in-the-Loop, HIL) 시험을 활용하면 실제 물리적 외란 시험 전에 위험을 감소시킬 수 있다. 시뮬레이션에서는 외란 크기, 방향, 마찰, 지연, 모델 불확실성(Model Uncertainty)을 광범위하게 변경하며 시험할 수 있다. 그러나 순응성, 백래시(Backlash), 센서 잡음, 구조 진동, 접촉 충격, 액추에이터 포화는 완벽하게 재현하기 어렵기 때문에 실제 하드웨어 시험이 필수적이다. 따라서 검증은 가상 시험에서 시작하여 점차 현실적인 물리 시험으로 진행해야 한다.

회복 영역(Recovery Envelope)은 제어기의 능력을 간결하게 요약할 수 있는 방법을 제공한다. 각각의 외란 방향과 운용 조건에서 고정 발 회복, 단일 스텝 회복(Single-Step Recovery), 다중 스텝 회복(Multi-Step Recovery)이 가능한 최대 회복 충격량(Maximum Recoverable Impulse) 또는 힘-지속시간 조합(Force-Duration Combination)을 식별할 수 있다. 제어기 버전별 회복 영역을 비교하면 특정 알고리즘이 실제 안정성 범위를 확장했는지를 확인할 수 있다. 이러한 회복 영역은 항상 스탠스, 페이로드, 표면, 액추에이터 조건과 함께 정의되어야 한다.

따라서 균형 제어기 검증은 휴머노이드가 몇 차례의 수동 밀림을 견디는 것을 보여주는 것만으로는 충분하지 않다. 엄격한 외란 시험은 반복 가능한 조건에서 외란 입력, 상태 변화(State Evolution), 접촉 활용(Contact Utilization), 운동량 응답(Momentum Response), 액추에이터 요구량, 추정기 성능, 회복 결과를 측정해야 한다. 정상적인 안정화에서 스테핑을 거쳐 최종적인 실패에 이르는 경계를 단계적으로 매핑함으로써 균형 강인성을 측정 가능한 공학적 성능(Measurable Engineering Performance)으로 변환할 수 있다.
