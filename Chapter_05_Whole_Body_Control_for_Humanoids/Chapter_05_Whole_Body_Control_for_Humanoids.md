**Volume 22. Humanoid Robot Software**


# Chapter 05. Whole Body Control for Humanoids

##  

## 05.01. Humanoid WBC Architecture Task Space Hierarchy

![](images/image1.png){width="7.268055555555556in" height="7.268055555555556in"}

Whole-body control for humanoid robots provides a unified framework for coordinating the many degrees of freedom distributed across the legs, torso, arms, and head. Rather than controlling each joint or limb independently, WBC interprets robot behavior as a collection of coupled tasks that must be satisfied while maintaining physical consistency, contact stability, and actuator feasibility.

A humanoid WBC architecture normally operates between high-level motion generation and low-level joint control. Motion planners provide desired quantities such as center-of-mass motion, foot poses, hand trajectories, torso orientation, and posture references. The WBC transforms these task-space objectives into dynamically consistent joint accelerations, torques, or contact forces that can be executed by the robot.

Task-space control is important because humanoid objectives are naturally expressed in Cartesian or physically meaningful coordinates. A manipulation task may specify the desired six-dimensional pose of a hand, while locomotion specifies foot positions and orientations. Balance may instead regulate the center of mass, centroidal momentum, or contact wrench. WBC allows these heterogeneous objectives to coexist within one mathematical control problem.

The floating-base nature of a humanoid fundamentally distinguishes its control problem from that of a fixed industrial manipulator. The pelvis or torso is not rigidly attached to the environment, and its motion can only be influenced indirectly through contact forces generated at the feet, hands, knees, or other supported body regions. Consequently, feasible whole-body motion must simultaneously satisfy rigid-body dynamics and environmental contact constraints.

A typical controller receives a state estimate containing floating-base pose and velocity, joint positions and velocities, contact states, and potentially force or torque measurements. A robot model then provides forward kinematics, Jacobians, inertia matrices, nonlinear dynamics, and other quantities required by the controller. Accurate synchronization between estimation, modeling, and control is therefore essential for stable task execution.

The central organizational principle is a task-space hierarchy. Tasks are assigned priorities according to their physical importance rather than simply being combined as unrelated joint commands. Constraints associated with dynamics, contact maintenance, actuator limits, and safety generally occupy the highest level. Balance and support objectives follow, while manipulation, locomotion, gaze, and posture objectives are organized according to the current behavior.

Strict hierarchy prevents a lower-priority objective from degrading a more important one. For example, a humanoid reaching toward an object should not sacrifice foot contact or destabilize its center of mass merely to reduce hand-position error. The controller first preserves the feasible solution space required by critical objectives and then uses the remaining degrees of freedom to improve subordinate tasks.

Mathematically, a Cartesian task can be related to generalized robot motion through its Jacobian. Desired task acceleration may be constructed from feedforward trajectory acceleration together with feedback terms based on position and velocity errors. The controller then searches for generalized accelerations or torques that reproduce this desired behavior while respecting the equations of motion and other active tasks.

Redundancy is one of the major advantages of task-space WBC. A humanoid usually possesses more controllable degrees of freedom than are necessary for a single end-effector objective. When the hand pose is constrained, the remaining freedom can regulate torso orientation, maintain comfortable joint configurations, increase distance from joint limits, improve balance, or prepare the body for subsequent motion.

Null-space reasoning provides a conceptual method for exploiting this redundancy. After a high-priority task consumes the motion directions necessary for its execution, lower-priority commands are projected into directions that do not interfere with that task. This creates a hierarchical relationship in which secondary behaviors can optimize the robot configuration without modifying the achievement of dominant objectives.

In practical humanoid systems, however, purely kinematic null-space control is insufficient because motion is constrained by dynamics, contacts, friction, and finite actuator capability. Modern WBC therefore frequently formulates control as an optimization problem. Decision variables may include generalized acceleration, joint torque, contact force, or combinations of these quantities, enabling kinematic objectives and physical feasibility to be considered together.

Contact modeling is particularly important during standing and locomotion. A stance foot should remain compatible with the ground while generating forces capable of supporting and accelerating the body. Contact forces must remain within admissible friction conditions, and the resulting wrench should remain consistent with the available support region. These requirements restrict the solution space available to all other whole-body tasks.

The task hierarchy changes with the robot\'s contact mode. During double support, both feet constrain body motion and provide a comparatively large support region. During single support, one foot becomes the primary physical connection to the environment while the opposite leg performs a swing task. Manipulation can introduce additional hand contacts, transforming the control problem again and potentially increasing available support.

Whole-body momentum provides another useful coordination variable. Instead of attempting to stabilize every body segment independently, the controller can regulate the combined linear and angular momentum generated by all links. Arm, torso, and leg motions can therefore cooperate to compensate for disturbances, manipulation forces, or rapid changes in posture while preserving global balance.

Posture regulation generally occupies a lower level of the hierarchy but remains essential. Without an appropriate posture objective, redundant joints can drift toward undesirable configurations even while primary Cartesian tasks are satisfied. A nominal posture reference keeps the robot near mechanically favorable configurations and can incorporate preferences related to joint range, manipulability, energy use, visibility, or collision avoidance.

Task priorities need not remain fixed throughout an operation. Walking, reaching, carrying, pushing, and recovery impose different physical requirements. A supervisory controller can activate tasks, modify references, or change relative importance according to the behavioral state. Smooth transitions are necessary because abrupt changes in priorities or constraints can create discontinuities in commanded accelerations, forces, or torques.

A useful architecture therefore separates task generation from whole-body resolution. Locomotion, manipulation, balance, and higher-level behavioral modules define what the robot should accomplish, while WBC determines how the available body coordinates and contact forces can realize those requests simultaneously. This separation allows specialized planners to operate without independently solving the complete coupled-body control problem.

The hierarchy also provides a natural interface for integrating locomotion and manipulation. A humanoid carrying an object may simultaneously track hand poses, regulate object orientation, maintain torso posture, control center-of-mass behavior, and preserve foot contacts. Because these requirements interact mechanically, independent arm and walking controllers can produce conflicting commands, whereas WBC resolves them within a common model of the robot.

Safety constraints should be embedded directly into the feasible control region whenever possible. Joint position, velocity, acceleration, and torque limits can restrict candidate solutions before commands reach actuators. Self-collision avoidance and contact-force bounds can be represented similarly. This approach makes safety part of motion generation rather than relying exclusively on downstream clipping of already infeasible commands.

The WBC output depends on the actuation interface. An acceleration-level controller may generate desired joint accelerations for a lower-level servo, whereas inverse-dynamics WBC can compute torque commands directly. Torque-controlled humanoids benefit from explicit dynamic consistency and compliant interaction, but they also require accurate models, reliable force estimation, deterministic execution, and robust actuator-level control.

Real-time execution is consequently an architectural requirement rather than merely a performance optimization. State estimation, model updates, task construction, constraint evaluation, optimization, and command transmission must complete within a predictable control period. Computational complexity must remain bounded as contacts and tasks change, particularly when WBC is executed in the high-frequency control layer of the humanoid software stack.

The surrounding chapter structure reflects this progression from architectural principles toward increasingly concrete implementations. The WBC architecture and task-space hierarchy form the conceptual foundation for operational-space control, hierarchical quadratic programming, contact and wrench modeling, unified locomotion-manipulation, compliant torque control, bimanual execution, real-time optimization, safety constraints, and final simulation-to-real validation. Volume_22_Humanoid_Robot_Softwa...

Ultimately, humanoid WBC can be understood as a real-time physical arbitration layer. High-level modules request multiple behaviors, but the robot has only one mechanically coupled body interacting with one physical environment. The controller continuously reconciles these requests according to task priority, robot dynamics, contact feasibility, redundancy, actuator capability, and safety, producing one coherent whole-body action at every control cycle.

휴머노이드 로봇(Humanoid Robot)을 위한 전신 제어(Whole-Body Control, WBC)는 다리, 몸통, 팔, 머리에 분산된 수많은 자유도(Degrees of Freedom, DoF)를 통합적으로 조정하기 위한 프레임워크를 제공한다. 각 관절이나 팔다리를 독립적으로 제어하는 대신, WBC는 로봇의 동작을 물리적 일관성(Physical Consistency), 접촉 안정성(Contact Stability), 액추에이터 실행 가능성(Actuator Feasibility)을 유지하면서 동시에 만족해야 하는 상호 결합된 작업(Task)의 집합으로 해석한다.

휴머노이드 WBC 아키텍처(Architecture)는 일반적으로 상위 수준 모션 생성(High-Level Motion Generation)과 하위 수준 관절 제어(Low-Level Joint Control) 사이에서 동작한다. 모션 플래너(Motion Planner)는 무게중심(Center of Mass, CoM) 운동, 발 자세(Foot Pose), 손 궤적(Hand Trajectory), 몸통 방향(Torso Orientation), 자세 기준(Posture Reference) 등의 목표값을 제공한다. WBC는 이러한 작업 공간(Task Space) 목표를 로봇이 실제 실행할 수 있는 동역학적으로 일관된 관절 가속도(Joint Acceleration), 토크(Torque), 접촉력(Contact Force)으로 변환한다.

작업 공간 제어(Task-Space Control)가 중요한 이유는 휴머노이드의 제어 목표가 본질적으로 데카르트 좌표계(Cartesian Coordinates) 또는 물리적으로 의미 있는 좌표로 표현되기 때문이다. 조작 작업(Manipulation Task)은 손의 원하는 6차원 자세(6D Pose)를 지정할 수 있으며, 보행(Locomotion)은 발의 위치와 방향을 지정한다. 반면 균형 제어(Balance Control)는 무게중심(CoM), 중심 운동량(Centroidal Momentum), 접촉 렌치(Contact Wrench)를 조절할 수 있다. WBC는 이러한 서로 다른 종류의 목표가 하나의 수학적 제어 문제 안에서 공존하도록 한다.

휴머노이드가 가지는 부유 베이스(Floating Base) 특성은 고정형 산업용 매니퓰레이터(Fixed Industrial Manipulator)의 제어 문제와 근본적으로 구별된다. 골반(Pelvis)이나 몸통(Torso)은 환경에 단단히 고정되어 있지 않으며, 발, 손, 무릎 또는 기타 지지 신체 영역에서 생성되는 접촉력(Contact Force)을 통해서만 간접적으로 운동에 영향을 줄 수 있다. 따라서 실행 가능한 전신 운동(Whole-Body Motion)은 강체 동역학(Rigid-Body Dynamics)과 환경 접촉 제약(Environmental Contact Constraints)을 동시에 만족해야 한다.

일반적인 제어기(Controller)는 부유 베이스 자세 및 속도(Floating-Base Pose and Velocity), 관절 위치 및 속도(Joint Position and Velocity), 접촉 상태(Contact State), 필요에 따라 힘 또는 토크 측정값(Force or Torque Measurement)을 포함하는 상태 추정(State Estimate)을 입력으로 받는다. 로봇 모델(Robot Model)은 순기구학(Forward Kinematics), 자코비안(Jacobian), 관성 행렬(Inertia Matrix), 비선형 동역학(Nonlinear Dynamics) 등 제어에 필요한 정보를 제공한다. 따라서 상태 추정(State Estimation), 모델링(Modeling), 제어(Control) 사이의 정확한 동기화는 안정적인 작업 수행에 필수적이다.

핵심적인 구성 원리는 작업 공간 계층 구조(Task-Space Hierarchy)이다. 작업(Task)은 단순히 서로 독립적인 관절 명령으로 결합되는 것이 아니라 물리적 중요도에 따라 우선순위(Priority)가 부여된다. 동역학(Dynamics), 접촉 유지(Contact Maintenance), 액추에이터 제한(Actuator Limits), 안전(Safety)과 관련된 제약은 일반적으로 가장 높은 수준을 차지한다. 그 다음 균형 및 지지 목표(Balance and Support Objectives)가 배치되며, 조작(Manipulation), 보행(Locomotion), 시선(Gaze), 자세(Posture) 목표는 현재 수행 중인 행동에 따라 구성된다.

엄격한 계층 구조(Strict Hierarchy)는 낮은 우선순위의 목표가 더 중요한 목표를 훼손하지 못하도록 한다. 예를 들어 휴머노이드가 물체를 향해 손을 뻗을 때 손 위치 오차(Hand-Position Error)를 줄이기 위해 발 접촉(Foot Contact)을 희생하거나 무게중심을 불안정하게 만들어서는 안 된다. 제어기는 먼저 중요한 목표를 만족하는 데 필요한 실행 가능 해 공간(Feasible Solution Space)을 보존하고, 이후 남아 있는 자유도를 이용하여 하위 우선순위 작업의 성능을 향상시킨다.

수학적으로 데카르트 작업(Cartesian Task)은 자코비안(Jacobian)을 통해 일반화 로봇 운동(Generalized Robot Motion)과 연결될 수 있다. 원하는 작업 가속도(Desired Task Acceleration)는 피드포워드 궤적 가속도(Feedforward Trajectory Acceleration)와 위치 및 속도 오차(Position and Velocity Errors)에 기반한 피드백 항(Feedback Terms)을 결합하여 구성할 수 있다. 이후 제어기는 운동 방정식(Equations of Motion)과 다른 활성 작업(Active Tasks)을 만족하면서 원하는 동작을 구현하는 일반화 가속도(Generalized Acceleration) 또는 토크(Torque)를 탐색한다.

여유도(Redundancy)는 작업 공간 WBC(Task-Space WBC)의 주요 장점 중 하나이다. 휴머노이드는 일반적으로 하나의 말단장치(End-Effector) 목표를 달성하는 데 필요한 것보다 많은 제어 가능 자유도를 가지고 있다. 손 자세가 구속된 상태에서도 남은 자유도를 이용하여 몸통 방향(Torso Orientation)을 조절하고, 편안한 관절 구성(Joint Configuration)을 유지하며, 관절 한계(Joint Limits)에서 멀어지고, 균형을 개선하거나 다음 동작을 위한 신체 자세를 준비할 수 있다.

영공간 추론(Null-Space Reasoning)은 이러한 여유도를 활용하는 개념적인 방법을 제공한다. 높은 우선순위 작업이 수행에 필요한 운동 방향을 사용한 이후, 낮은 우선순위 명령은 해당 작업을 방해하지 않는 방향으로 투영(Project)된다. 이를 통해 부차적인 동작은 상위 목표의 달성 상태를 변경하지 않으면서 로봇의 구성을 최적화할 수 있는 계층적 관계를 형성한다.

그러나 실제 휴머노이드 시스템에서는 순수한 기구학적 영공간 제어(Kinematic Null-Space Control)만으로 충분하지 않다. 로봇 운동은 동역학(Dynamics), 접촉(Contact), 마찰(Friction), 제한된 액추에이터 성능(Finite Actuator Capability)에 의해 제약되기 때문이다. 따라서 현대적인 WBC는 제어 문제를 최적화 문제(Optimization Problem)로 구성하는 경우가 많다. 결정 변수(Decision Variables)에는 일반화 가속도, 관절 토크, 접촉력 또는 이들의 조합이 포함될 수 있으며, 이를 통해 기구학적 목표와 물리적 실행 가능성을 함께 고려할 수 있다.

접촉 모델링(Contact Modeling)은 정지 자세(Standing)와 보행(Locomotion)에서 특히 중요하다. 지지 발(Stance Foot)은 지면과의 접촉을 유지하면서 신체를 지지하고 가속할 수 있는 힘을 생성해야 한다. 접촉력은 허용 가능한 마찰 조건(Friction Conditions) 안에 있어야 하며, 그 결과로 발생하는 렌치(Wrench)는 사용 가능한 지지 영역(Support Region)과 일관되어야 한다. 이러한 조건은 다른 모든 전신 작업이 사용할 수 있는 해 공간을 제한한다.

작업 계층 구조(Task Hierarchy)는 로봇의 접촉 모드(Contact Mode)에 따라 변화한다. 양발 지지(Double Support)에서는 두 발 모두 신체 운동을 제한하며 비교적 넓은 지지 영역을 제공한다. 한발 지지(Single Support)에서는 한쪽 발이 환경과 연결되는 주요 물리적 접점이 되고 반대쪽 다리는 스윙 작업(Swing Task)을 수행한다. 조작 과정에서 추가적인 손 접촉(Hand Contact)이 발생하면 제어 문제는 다시 변화하며, 경우에 따라 사용 가능한 지지 능력을 증가시킬 수도 있다.

전신 운동량(Whole-Body Momentum)은 또 다른 유용한 협조 제어 변수(Coordination Variable)를 제공한다. 각각의 신체 부분을 독립적으로 안정화하려는 대신 제어기는 모든 링크(Link)가 생성하는 결합된 선형 및 각운동량(Linear and Angular Momentum)을 조절할 수 있다. 따라서 팔, 몸통, 다리의 움직임은 외란(Disturbance), 조작력(Manipulation Force), 급격한 자세 변화에 대응하면서 전체적인 균형을 유지하도록 협력할 수 있다.

자세 조절(Posture Regulation)은 일반적으로 계층 구조에서 비교적 낮은 수준에 위치하지만 여전히 필수적이다. 적절한 자세 목표가 없다면 주요 데카르트 작업이 만족되더라도 여유 관절(Redundant Joints)이 바람직하지 않은 구성으로 이동할 수 있다. 기준 자세(Nominal Posture Reference)는 로봇을 기계적으로 유리한 구성 근처에 유지하며 관절 범위(Joint Range), 조작성(Manipulability), 에너지 사용(Energy Use), 가시성(Visibility), 충돌 회피(Collision Avoidance) 등의 선호 조건을 포함할 수 있다.

작업 우선순위(Task Priority)는 전체 동작 과정에서 반드시 고정될 필요가 없다. 보행(Walking), 뻗기(Reaching), 운반(Carrying), 밀기(Pushing), 복구(Recovery)는 각각 서로 다른 물리적 요구조건을 가진다. 상위 감독 제어기(Supervisory Controller)는 행동 상태(Behavioral State)에 따라 작업을 활성화하고 기준값을 변경하거나 상대적 중요도를 조절할 수 있다. 우선순위나 제약의 급격한 변화는 명령 가속도, 힘, 토크에 불연속(Discontinuity)을 발생시킬 수 있으므로 부드러운 전환(Smooth Transition)이 필요하다.

따라서 효과적인 아키텍처는 작업 생성(Task Generation)과 전신 해석(Whole-Body Resolution)을 분리한다. 보행(Locomotion), 조작(Manipulation), 균형(Balance), 상위 행동 모듈(High-Level Behavioral Module)은 로봇이 무엇을 수행해야 하는지를 정의하고, WBC는 사용 가능한 신체 좌표와 접촉력을 이용해 이러한 요청을 동시에 어떻게 구현할 것인지를 결정한다. 이러한 분리는 각각의 전문 플래너가 완전한 결합 신체 제어 문제를 독립적으로 해결하지 않고도 동작할 수 있도록 한다.

이러한 계층 구조는 보행과 조작을 통합하기 위한 자연스러운 인터페이스(Interface)도 제공한다. 물체를 운반하는 휴머노이드는 손 자세를 추종하고, 물체 방향을 조절하며, 몸통 자세를 유지하고, 무게중심 거동을 제어하면서 동시에 발 접촉을 유지해야 할 수 있다. 이러한 요구사항은 기계적으로 서로 영향을 주기 때문에 독립적인 팔 제어기와 보행 제어기는 충돌하는 명령을 생성할 수 있지만, WBC는 공통된 로봇 모델 안에서 이를 통합적으로 해결한다.

안전 제약(Safety Constraints)은 가능한 경우 제어기의 실행 가능 영역(Feasible Control Region)에 직접 포함되어야 한다. 관절 위치, 속도, 가속도, 토크 제한(Joint Position, Velocity, Acceleration, and Torque Limits)을 이용하여 명령이 액추에이터에 전달되기 전에 후보 해를 제한할 수 있다. 자기 충돌 회피(Self-Collision Avoidance)와 접촉력 제한(Contact-Force Bounds)도 유사하게 표현할 수 있다. 이를 통해 이미 실행 불가능한 명령을 사후에 단순 제한하는 대신 안전을 모션 생성 자체의 일부로 포함할 수 있다.

WBC 출력(Output)은 액추에이션 인터페이스(Actuation Interface)에 따라 달라진다. 가속도 수준 제어기(Acceleration-Level Controller)는 하위 수준 서보(Servo)를 위한 목표 관절 가속도를 생성할 수 있으며, 역동역학 WBC(Inverse-Dynamics WBC)는 직접 토크 명령을 계산할 수 있다. 토크 제어형 휴머노이드(Torque-Controlled Humanoid)는 명시적인 동역학적 일관성과 순응적 상호작용(Compliant Interaction)의 장점을 얻지만 정확한 모델, 신뢰성 있는 힘 추정, 결정론적 실행(Deterministic Execution), 강건한 액추에이터 수준 제어가 요구된다.

따라서 실시간 실행(Real-Time Execution)은 단순한 성능 최적화가 아니라 아키텍처 요구사항이다. 상태 추정, 모델 업데이트, 작업 구성, 제약 평가, 최적화, 명령 전송이 예측 가능한 제어 주기(Control Period) 내에서 완료되어야 한다. 특히 WBC가 휴머노이드 소프트웨어 스택의 고주파 제어 계층(High-Frequency Control Layer)에서 실행될 경우 접촉과 작업이 변화하더라도 계산 복잡도(Computational Complexity)가 제한된 범위 내에서 유지되어야 한다.

주변 장의 구성은 이러한 아키텍처 원리에서 보다 구체적인 구현으로 발전하는 흐름을 반영한다. WBC 아키텍처와 작업 공간 계층 구조는 운용 공간 제어(Operational-Space Control), 계층적 이차 계획법(Hierarchical Quadratic Programming), 접촉 및 렌치 모델링(Contact and Wrench Modeling), 통합 보행-조작(Unified Locomotion-Manipulation), 순응 토크 제어(Compliant Torque Control), 양손 작업 실행(Bimanual Execution), 실시간 최적화(Real-Time Optimization), 안전 제약(Safety Constraints), 최종 시뮬레이션-실환경 검증(Simulation-to-Real Validation)으로 이어지는 개념적 기반을 형성한다.

궁극적으로 휴머노이드 WBC는 실시간 물리적 중재 계층(Real-Time Physical Arbitration Layer)으로 이해할 수 있다. 상위 수준 모듈은 여러 행동을 동시에 요청할 수 있지만, 로봇은 하나의 물리적 환경과 상호작용하는 하나의 기계적으로 결합된 신체만을 가지고 있다. 제어기는 매 제어 주기마다 작업 우선순위, 로봇 동역학, 접촉 실행 가능성, 여유도, 액추에이터 능력, 안전 조건에 따라 이러한 요청을 지속적으로 조정하여 하나의 일관된 전신 동작(Whole-Body Action)을 생성한다.

##  

## 05.02. Operational Space Control OSC Framework [w/Code]

![](images/image2.png){width="7.268055555555556in" height="7.268055555555556in"}

Operational Space Control (OSC) provides a control framework in which humanoid motion objectives are expressed directly in physically meaningful task coordinates rather than individual joint coordinates. A controller can therefore regulate quantities such as hand position, foot pose, torso orientation, or center-of-mass motion while automatically coordinating the many joints required to realize each objective.

The foundation of OSC is the relationship between generalized robot motion and task-space motion. For a task variable \\(x\\), its velocity is related to generalized velocity through the task Jacobian \\(J\\), giving \\(\\dot{x}=J\\dot{q}\\). Differentiating this relationship produces task acceleration as \\(\\ddot{x}=J\\ddot{q}+\\dot{J}\\dot{q}\\), which provides the basic mapping required for acceleration-level task control.

For humanoid robots, the generalized coordinates normally include both the floating-base configuration and actuated joint coordinates. This distinction is essential because the six-dimensional floating base cannot be directly commanded by actuators. Its motion emerges from joint torques and external contact forces, requiring OSC to operate consistently with the full rigid-body dynamics rather than treating the humanoid as a conventional fixed-base manipulator.

The equations of motion can be represented using the generalized inertia matrix, nonlinear Coriolis and centrifugal effects, gravity, actuator torques, and external contact forces. OSC uses this dynamic model to determine how forces or accelerations specified in task space should be translated into generalized forces while respecting the physical coupling among all links of the humanoid body.

A central quantity in dynamically consistent OSC is the operational-space inertia matrix. Conceptually, this matrix describes the effective inertia observed at a selected task coordinate. Because the apparent resistance of a robot to motion depends on configuration, direction, and mass distribution, the operational-space inertia changes continuously as the humanoid moves its limbs, changes posture, or establishes different environmental contacts.

For a full-rank task Jacobian, the operational-space inertia is commonly written as \\(\\Lambda=(JM\^{-1}J\^T)\^{-1}\\), where \\(M\\) represents the generalized inertia matrix. This formulation maps task-space acceleration requirements into dynamically meaningful task forces. It also captures coupling that would be lost if Cartesian errors were converted into joint commands using only a purely kinematic inverse.

A desired task acceleration can be constructed from reference trajectory information and feedback regulation. Feedforward acceleration represents the intended motion, while proportional and derivative feedback terms compensate for task position and velocity errors. The resulting command specifies how the selected operational coordinate should evolve without requiring the planner to determine the motion of every individual joint.

The corresponding task-space force can then be determined using the operational-space dynamics. Depending on the formulation, compensation terms may account for velocity-dependent dynamics, gravity, and other nonlinear effects. The task command is subsequently mapped back into generalized forces through the transpose of the Jacobian, establishing a direct relationship between physically meaningful task objectives and joint-level actuation.

Dynamic consistency is one of the defining characteristics of OSC. A conventional Jacobian pseudoinverse distributes motion according to geometric criteria, but it does not necessarily account for the robot\'s mass distribution. A dynamically consistent inverse incorporates the inertia matrix so that redundancy is resolved according to the actual dynamics of the mechanism, which becomes particularly important for large, highly coupled humanoid systems.

Redundancy allows a humanoid to perform secondary objectives without disturbing a dominant operational-space task. Once the force or motion required by the primary task has been determined, remaining degrees of freedom can be used for posture regulation, joint-limit avoidance, manipulability improvement, torso alignment, or preparation for subsequent movements. Null-space projection provides the mathematical mechanism for separating these behaviors.

In dynamically consistent control, the null-space projector is constructed so that secondary commands do not generate acceleration in the controlled primary task direction. This property allows the hand, for example, to maintain a desired Cartesian pose while the elbow, shoulder, torso, and other redundant joints reorganize themselves according to secondary objectives. Such coordination is fundamental to natural humanoid motion.

Humanoid OSC becomes more complex when environmental contacts are introduced. During standing, one or both feet constrain portions of the robot motion, while during manipulation the hands may establish additional contacts. These constraints change the available motion space and the relationship between commanded joint torques, contact forces, floating-base acceleration, and operational-space behavior.

Contact-consistent control therefore projects task commands into directions compatible with active constraints. A stance foot intended to remain stationary should not receive a conflicting motion from a hand or posture controller. Instead, the contact constraints define an admissible subspace in which operational tasks can be executed while maintaining the required physical connection between the humanoid and its environment.

This principle is especially important during transitions between double support and single support. In double support, both feet constrain the floating body and distribute supporting forces. When one foot begins a swing phase, its contact constraint disappears and the same limb becomes an operational-space motion task. OSC must consequently update the relevant Jacobians, constraints, and dynamic mappings as the contact state changes.

The center of mass can also be represented as an operational task. Regulating CoM position or acceleration provides a direct mechanism for coordinating whole-body balance behavior, while torso orientation can be controlled as another task. Foot, hand, CoM, and torso objectives can therefore be expressed using a common task-space language even though their dimensions and physical functions differ significantly.

For manipulation, OSC enables the humanoid to command hand motion and interaction forces without explicitly specifying every arm and torso joint trajectory. A six-dimensional end-effector task may regulate translational and rotational behavior simultaneously. When interaction with an object or surface occurs, force objectives can be incorporated so that the controller manages both motion and physical interaction within the available dynamic constraints.

Multiple operational tasks require a mechanism for resolving competition. Weighted formulations can trade errors among objectives, whereas strict hierarchical formulations preserve higher-priority tasks before optimizing lower-priority ones. In humanoid applications, contact consistency and balance generally require stronger protection than secondary posture or manipulation preferences, motivating the hierarchical structures developed further in whole-body optimization methods.

OSC also provides an important basis for compliant behavior. Rather than forcing an end effector to follow a rigid geometric trajectory regardless of interaction, desired task-space stiffness and damping can define how the robot responds to displacement and external force. This makes operational coordinates particularly suitable for human interaction, object handling, assembly, pushing, and other contact-rich humanoid behaviors.

The effectiveness of OSC nevertheless depends strongly on model quality. Errors in link masses, centers of mass, inertial parameters, joint friction, actuator dynamics, or contact estimation can reduce the accuracy of dynamic compensation. Practical implementations therefore combine model-based control with feedback, filtering, state estimation, robust gains, and sometimes disturbance observers to tolerate uncertainty between the mathematical model and physical robot.

Numerical conditioning is another practical concern. Task Jacobians can approach singular configurations in which some desired Cartesian motions become difficult or impossible to produce. Operational-space inertia and inverse mappings must therefore be computed using numerically robust methods, potentially including regularization, damped inverses, singular-value thresholds, or task adaptation near kinematic singularities.

Real-time computation is critical because the robot model and all task-space quantities change continuously. Forward kinematics, Jacobians, Jacobian derivatives, inertia matrices, nonlinear dynamics, contact models, null-space mappings, and control commands must be updated within each control cycle. Efficient rigid-body dynamics libraries and deterministic memory and computation strategies are therefore important for high-frequency humanoid OSC.

Within the broader humanoid WBC architecture, OSC establishes the mathematical bridge between task-level intent and dynamically consistent whole-body actuation. It explains how Cartesian objectives can be transformed into forces and torques while exploiting redundancy and respecting the coupled dynamics of the body. More complex optimization-based controllers can then extend this foundation with explicit inequalities, actuator limits, friction constraints, and multiple contact conditions.

The chapter structure places OSC immediately after the WBC architecture and task-space hierarchy, followed by hierarchical quadratic programming, contact-constraint and wrench modeling, simultaneous locomotion and manipulation, compliant torque control, bimanual execution, real-time optimization, safety constraints, and simulation-to-real validation. This progression positions OSC as the fundamental dynamic task-space formulation upon which the subsequent constrained WBC methods are constructed.

운용 공간 제어(Operational Space Control, OSC)는 개별 관절 좌표(Joint Coordinates)가 아니라 물리적으로 의미 있는 작업 좌표(Task Coordinates)에서 휴머노이드의 운동 목표를 직접 표현하는 제어 프레임워크(Control Framework)를 제공한다. 따라서 제어기는 손 위치(Hand Position), 발 자세(Foot Pose), 몸통 방향(Torso Orientation), 무게중심 운동(Center-of-Mass Motion) 등의 물리량을 직접 조절하면서 각 목표를 실현하는 데 필요한 다수의 관절을 자동으로 협조 제어할 수 있다.

OSC의 기반은 일반화 로봇 운동(Generalized Robot Motion)과 작업 공간 운동(Task-Space Motion) 사이의 관계이다. 작업 변수(Task Variable) \\(x\\)에 대해 그 속도는 작업 자코비안(Task Jacobian) \\(J\\)를 통해 일반화 속도(Generalized Velocity)와 연결되며, \\(\\dot{x}=J\\dot{q}\\)로 표현된다. 이를 미분하면 작업 가속도(Task Acceleration)는 \\(\\ddot{x}=J\\ddot{q}+\\dot{J}\\dot{q}\\)가 되며, 이는 가속도 수준 작업 제어(Acceleration-Level Task Control)에 필요한 기본적인 매핑 관계를 제공한다.

휴머노이드 로봇(Humanoid Robot)의 일반화 좌표(Generalized Coordinates)는 일반적으로 부유 베이스 구성(Floating-Base Configuration)과 구동 관절 좌표(Actuated Joint Coordinates)를 모두 포함한다. 6차원 부유 베이스(Six-Dimensional Floating Base)는 액추에이터에 의해 직접 명령될 수 없기 때문에 이러한 구분은 매우 중요하다. 부유 베이스의 운동은 관절 토크(Joint Torque)와 외부 접촉력(External Contact Force)의 결과로 발생하므로 OSC는 휴머노이드를 일반적인 고정 베이스 매니퓰레이터(Fixed-Base Manipulator)처럼 취급하지 않고 전체 강체 동역학(Full Rigid-Body Dynamics)과 일관되게 동작해야 한다.

운동 방정식(Equations of Motion)은 일반화 관성 행렬(Generalized Inertia Matrix), 비선형 코리올리 및 원심 효과(Nonlinear Coriolis and Centrifugal Effects), 중력(Gravity), 액추에이터 토크(Actuator Torque), 외부 접촉력(External Contact Force)을 사용하여 표현할 수 있다. OSC는 이러한 동역학 모델(Dynamic Model)을 이용해 작업 공간에서 지정된 힘이나 가속도를 일반화 힘(Generalized Force)으로 변환하는 방법을 결정하면서 휴머노이드 신체의 모든 링크(Link) 사이에 존재하는 물리적 결합을 고려한다.

동역학적으로 일관된 OSC(Dynamically Consistent OSC)의 핵심 물리량 중 하나는 운용 공간 관성 행렬(Operational-Space Inertia Matrix)이다. 개념적으로 이 행렬은 선택된 작업 좌표에서 관찰되는 유효 관성(Effective Inertia)을 나타낸다. 로봇이 운동에 저항하는 겉보기 특성은 구성(Configuration), 운동 방향(Direction), 질량 분포(Mass Distribution)에 따라 달라지므로 운용 공간 관성은 휴머노이드가 팔다리를 움직이거나 자세를 변경하고 서로 다른 환경 접촉을 형성함에 따라 지속적으로 변화한다.

완전 계수 작업 자코비안(Full-Rank Task Jacobian)에 대해 운용 공간 관성은 일반적으로 \\(\\Lambda=(JM\^{-1}J\^T)\^{-1}\\)로 표현되며, 여기서 \\(M\\)은 일반화 관성 행렬(Generalized Inertia Matrix)을 나타낸다. 이 공식은 작업 공간 가속도 요구사항(Task-Space Acceleration Requirements)을 동역학적으로 의미 있는 작업 힘(Task Force)으로 변환한다. 또한 단순히 기구학적 역변환(Kinematic Inverse)만을 사용하여 데카르트 오차(Cartesian Error)를 관절 명령으로 변환할 경우 손실될 수 있는 동역학적 결합을 반영한다.

목표 작업 가속도(Desired Task Acceleration)는 기준 궤적 정보(Reference Trajectory Information)와 피드백 조절(Feedback Regulation)을 이용하여 구성할 수 있다. 피드포워드 가속도(Feedforward Acceleration)는 의도된 운동을 나타내며, 비례 및 미분 피드백 항(Proportional and Derivative Feedback Terms)은 작업 위치 및 속도 오차(Task Position and Velocity Errors)를 보상한다. 이렇게 생성된 명령은 플래너(Planner)가 각각의 개별 관절 운동을 직접 결정하지 않고도 선택된 운용 좌표가 어떻게 변화해야 하는지를 지정한다.

이에 대응하는 작업 공간 힘(Task-Space Force)은 운용 공간 동역학(Operational-Space Dynamics)을 이용하여 결정할 수 있다. 사용되는 공식에 따라 속도 의존 동역학(Velocity-Dependent Dynamics), 중력(Gravity), 기타 비선형 효과(Nonlinear Effects)를 보상하는 항이 포함될 수 있다. 이후 작업 명령은 자코비안 전치(Jacobian Transpose)를 통해 다시 일반화 힘으로 매핑되어 물리적으로 의미 있는 작업 목표와 관절 수준 액추에이션(Joint-Level Actuation) 사이에 직접적인 관계를 형성한다.

동역학적 일관성(Dynamic Consistency)은 OSC를 정의하는 핵심 특성 중 하나이다. 일반적인 자코비안 의사역행렬(Jacobian Pseudoinverse)은 기하학적 기준에 따라 운동을 분배하지만 로봇의 질량 분포를 반드시 고려하는 것은 아니다. 동역학적으로 일관된 역행렬(Dynamically Consistent Inverse)은 관성 행렬을 포함하여 실제 기구의 동역학에 따라 여유도(Redundancy)를 해석하며, 이는 크고 강하게 결합된 휴머노이드 시스템에서 특히 중요하다.

여유도(Redundancy)는 휴머노이드가 주요 운용 공간 작업(Operational-Space Task)을 방해하지 않으면서 부차적인 목표를 수행할 수 있도록 한다. 주 작업에 필요한 힘이나 운동이 결정된 이후 남은 자유도는 자세 조절(Posture Regulation), 관절 한계 회피(Joint-Limit Avoidance), 조작성 향상(Manipulability Improvement), 몸통 정렬(Torso Alignment), 후속 운동 준비 등에 사용할 수 있다. 영공간 투영(Null-Space Projection)은 이러한 행동을 분리하기 위한 수학적 메커니즘을 제공한다.

동역학적으로 일관된 제어(Dynamically Consistent Control)에서는 부차적인 명령이 제어되는 주 작업 방향으로 가속도를 발생시키지 않도록 영공간 투영기(Null-Space Projector)를 구성한다. 예를 들어 손은 원하는 데카르트 자세(Cartesian Pose)를 유지하면서 팔꿈치, 어깨, 몸통 및 기타 여유 관절을 부차적인 목표에 따라 재구성할 수 있다. 이러한 협조 제어는 자연스러운 휴머노이드 운동을 구현하기 위한 핵심 요소이다.

환경 접촉(Environmental Contact)이 도입되면 휴머노이드 OSC는 더욱 복잡해진다. 정지 상태(Standing)에서는 한쪽 또는 양쪽 발이 로봇 운동의 일부를 구속하며, 조작 과정에서는 손이 추가적인 접촉을 형성할 수 있다. 이러한 제약은 사용 가능한 운동 공간(Motion Space)뿐 아니라 명령된 관절 토크, 접촉력, 부유 베이스 가속도(Floating-Base Acceleration), 운용 공간 거동 사이의 관계도 변화시킨다.

따라서 접촉 일관 제어(Contact-Consistent Control)는 작업 명령을 활성 제약(Active Constraints)과 호환되는 방향으로 투영한다. 고정 상태를 유지해야 하는 지지 발(Stance Foot)은 손 또는 자세 제어기로부터 충돌하는 운동 명령을 받아서는 안 된다. 대신 접촉 제약(Contact Constraints)은 휴머노이드와 환경 사이의 필요한 물리적 연결을 유지하면서 운용 작업을 수행할 수 있는 허용 가능한 부분공간(Admissible Subspace)을 정의한다.

이 원리는 양발 지지(Double Support)와 한발 지지(Single Support) 사이의 전환에서 특히 중요하다. 양발 지지에서는 두 발이 모두 부유 신체(Floating Body)를 구속하면서 지지력을 분담한다. 한쪽 발이 스윙 단계(Swing Phase)를 시작하면 해당 발의 접촉 제약이 사라지고 동일한 다리는 운용 공간 운동 작업(Operational-Space Motion Task)으로 전환된다. 따라서 OSC는 접촉 상태가 변화할 때 관련 자코비안, 제약 조건, 동역학적 매핑을 함께 갱신해야 한다.

무게중심(Center of Mass, CoM) 역시 운용 작업(Operational Task)으로 표현할 수 있다. CoM 위치 또는 가속도를 조절하면 전신 균형 거동(Whole-Body Balance Behavior)을 직접 협조 제어할 수 있으며, 몸통 방향 역시 별도의 작업으로 제어할 수 있다. 따라서 발, 손, CoM, 몸통의 목표는 각각 차원과 물리적 기능이 크게 다르더라도 공통된 작업 공간 언어(Task-Space Language)를 사용하여 표현할 수 있다.

조작(Manipulation)에서 OSC는 휴머노이드가 모든 팔과 몸통 관절의 궤적을 명시적으로 지정하지 않고도 손의 운동과 상호작용 힘(Interaction Force)을 명령할 수 있도록 한다. 6차원 말단장치 작업(Six-Dimensional End-Effector Task)은 병진 및 회전 거동(Translational and Rotational Behavior)을 동시에 조절할 수 있다. 물체나 표면과의 상호작용이 발생하면 힘 목표(Force Objective)를 포함하여 사용 가능한 동역학적 제약 안에서 운동과 물리적 상호작용을 함께 관리할 수 있다.

다수의 운용 작업(Multiple Operational Tasks)을 동시에 수행하려면 작업 간 경쟁을 해결하는 메커니즘이 필요하다. 가중치 기반 공식(Weighted Formulation)은 여러 목표 사이에서 오차를 절충할 수 있으며, 엄격한 계층적 공식(Strict Hierarchical Formulation)은 하위 우선순위 목표를 최적화하기 전에 상위 우선순위 작업을 보존한다. 휴머노이드 응용에서는 일반적으로 접촉 일관성과 균형이 부차적인 자세 또는 조작 선호보다 강하게 보호되어야 하므로 이후 전신 최적화(Whole-Body Optimization) 방법에서 다루는 계층 구조가 필요하다.

OSC는 순응 거동(Compliant Behavior)을 구현하기 위한 중요한 기반도 제공한다. 상호작용 상태와 관계없이 말단장치가 경직된 기하학적 궤적을 강제로 추종하도록 하는 대신 원하는 작업 공간 강성(Task-Space Stiffness)과 감쇠(Task-Space Damping)를 정의하여 변위와 외력에 대한 로봇의 반응을 결정할 수 있다. 이러한 특성으로 인해 운용 좌표는 인간 상호작용(Human Interaction), 물체 취급(Object Handling), 조립(Assembly), 밀기(Pushing) 등의 접촉 중심 휴머노이드 행동에 특히 적합하다.

그러나 OSC의 효과는 모델 품질(Model Quality)에 크게 의존한다. 링크 질량(Link Mass), 무게중심 위치(Center of Mass), 관성 파라미터(Inertial Parameters), 관절 마찰(Joint Friction), 액추에이터 동역학(Actuator Dynamics), 접촉 추정(Contact Estimation)의 오차는 동역학적 보상의 정확도를 저하시킬 수 있다. 따라서 실제 구현에서는 수학적 모델과 실제 로봇 사이의 불확실성을 허용하기 위해 모델 기반 제어와 피드백, 필터링, 상태 추정, 강건한 게인(Robust Gains), 경우에 따라 외란 관측기(Disturbance Observer)를 함께 사용한다.

수치적 조건성(Numerical Conditioning) 역시 실용적인 관점에서 중요한 문제이다. 작업 자코비안은 특정 데카르트 운동을 생성하기 어렵거나 불가능해지는 특이 구성(Singular Configuration)에 접근할 수 있다. 따라서 운용 공간 관성과 역매핑(Inverse Mapping)은 정규화(Regularization), 감쇠 역행렬(Damped Inverse), 특이값 임계값(Singular-Value Threshold), 특이점 근처의 작업 적응(Task Adaptation) 등을 포함한 수치적으로 강건한 방법을 사용하여 계산해야 한다.

로봇 모델과 모든 작업 공간 물리량이 지속적으로 변화하기 때문에 실시간 계산(Real-Time Computation)은 필수적이다. 순기구학(Forward Kinematics), 자코비안, 자코비안 미분(Jacobian Derivatives), 관성 행렬, 비선형 동역학, 접촉 모델, 영공간 매핑(Null-Space Mapping), 제어 명령을 각 제어 주기마다 갱신해야 한다. 따라서 고주파 휴머노이드 OSC를 위해서는 효율적인 강체 동역학 라이브러리(Rigid-Body Dynamics Library)와 결정론적 메모리 및 계산 전략(Deterministic Memory and Computation Strategy)이 중요하다.

보다 광범위한 휴머노이드 WBC 아키텍처에서 OSC는 작업 수준 의도(Task-Level Intent)와 동역학적으로 일관된 전신 액추에이션(Whole-Body Actuation)을 연결하는 수학적 다리를 형성한다. OSC는 여유도를 활용하고 신체의 결합 동역학을 고려하면서 데카르트 목표를 힘과 토크로 변환하는 방법을 설명한다. 이후 보다 복잡한 최적화 기반 제어기는 이러한 기반을 확장하여 명시적인 부등식 제약(Inequality Constraints), 액추에이터 한계, 마찰 제약(Friction Constraints), 다중 접촉 조건(Multiple Contact Conditions)을 포함할 수 있다.

장의 구성은 OSC를 WBC 아키텍처 및 작업 공간 계층 구조(Task-Space Hierarchy) 바로 다음에 배치하고, 이후 계층적 이차 계획법(Hierarchical Quadratic Programming), 접촉 제약 및 렌치 모델링(Contact-Constraint and Wrench Modeling), 동시 보행 및 조작(Simultaneous Locomotion and Manipulation), 순응 토크 제어(Compliant Torque Control), 양손 작업 실행(Bimanual Execution), 실시간 최적화(Real-Time Optimization), 안전 제약(Safety Constraints), 시뮬레이션-실환경 검증(Simulation-to-Real Validation)으로 이어진다. 이러한 흐름에서 OSC는 이후의 제약 기반 WBC(Constrained WBC) 방법들이 구축되는 핵심 동역학적 작업 공간 공식(Dynamic Task-Space Formulation)으로 위치한다.

##  

## 05.03. Hierarchical QP with Inequality Constraints [w/Code]

![](images/image3.png){width="7.268055555555556in" height="7.268055555555556in"}

Hierarchical Quadratic Programming (HQP) provides a systematic optimization framework for whole-body control in which multiple humanoid tasks and physical constraints can be organized according to explicit priorities. Unlike a single weighted objective that trades competing errors against one another, HQP preserves the solution of higher-priority tasks before allowing lower-priority objectives to use the remaining feasible freedom.

A quadratic program represents control objectives through a quadratic cost while imposing equality and inequality constraints on the decision variables. In humanoid WBC, these variables may include generalized joint accelerations, actuator torques, contact forces, or combinations of them. This formulation allows task tracking and robot dynamics to be solved together rather than handled through independent control stages.

At the acceleration level, a Cartesian task can be expressed through the relationship \\(J\\ddot{q}+\\dot{J}\\dot{q}=\\ddot{x}_{des}\\). Because several tasks may request incompatible accelerations, the controller minimizes their residual errors subject to physical constraints. The optimization therefore determines the generalized motion that most closely realizes the requested task behavior within the currently feasible region.

The hierarchical structure extends this formulation by separating objectives into priority levels. A higher-priority optimization is solved first, and its achieved result is preserved while the next optimization is performed. Lower levels are permitted to improve secondary behavior only within the solution space that does not degrade the optimum already established by higher levels.

This property is especially valuable for humanoid robots because not all objectives have equal physical importance. Maintaining rigid-body dynamic consistency, valid environmental contacts, and safety limits is fundamentally more important than minimizing a hand trajectory error or achieving a preferred posture. HQP provides a mathematical mechanism for representing this distinction directly rather than relying only on manually selected numerical weights.

Equality constraints commonly represent physical relationships that must be satisfied exactly or within a tightly controlled tolerance. The floating-base equations of motion, active contact kinematics, and selected high-priority task equations can be represented in this form. These constraints define a feasible manifold upon which the remaining optimization objectives must operate.

Inequality constraints are essential because many important humanoid limitations define allowable ranges rather than exact values. Joint positions, velocities, accelerations, and actuator torques must remain between minimum and maximum bounds. Contact forces must satisfy friction and unilateral-contact conditions, while collision avoidance requires distances or relative motions to remain on the safe side of specified boundaries.

A generic inequality may be written as \\(A_{ineq}z \\leq b_{ineq}\\), where \\(z\\) contains the optimization variables. This compact representation can describe many different physical limits within the same solver interface. Bounds can also change at every control cycle according to robot configuration, contact state, estimated environment geometry, actuator condition, or task phase.

Friction constraints illustrate the importance of inequalities in humanoid control. A stance foot can push against the ground but normally cannot pull the ground toward itself, and tangential contact force must remain sufficiently small relative to the normal force to avoid slipping. A friction cone therefore defines the admissible contact-force region and is commonly approximated by linear inequalities for efficient real-time optimization.

Contact wrench constraints can further restrict the location and distribution of forces beneath a supporting foot. The resulting wrench should remain compatible with the finite contact surface and available friction. By incorporating these limits directly into HQP, the controller avoids generating mathematically attractive whole-body motions that would require physically impossible or unstable ground reaction forces.

Joint limits can be handled proactively rather than waiting until a mechanical boundary is reached. Position-dependent velocity or acceleration bounds can progressively restrict motion as a joint approaches its allowable range. This creates a feasible region that guides the optimizer away from dangerous configurations while still allowing the remaining degrees of freedom to contribute to the commanded whole-body behavior.

Torque inequalities provide another critical layer of feasibility. Even when a desired acceleration is kinematically achievable, the required actuator torque may exceed motor, gearbox, thermal, or electrical capability. Explicit torque bounds prevent the optimizer from assuming unavailable actuation authority and make the resulting motion more consistent with what the physical humanoid can actually execute.

The floating-base dynamics couple these actuator and contact constraints. Because the base itself is unactuated, its desired acceleration cannot be independently commanded. The optimizer must find joint torques and environmental contact forces whose combined effects generate the required whole-body acceleration while satisfying Newton-Euler dynamics, making contact-force variables particularly important in torque-level HQP formulations.

Task priorities can be organized according to the current humanoid behavior. Contact preservation, dynamic consistency, actuator feasibility, and critical safety constraints typically occupy the strongest levels. Balance-related quantities such as center-of-mass or momentum behavior can follow, while foot motion, hand manipulation, torso orientation, gaze, and nominal posture are arranged according to operational requirements.

During walking, for example, the stance foot may remain constrained while the swing foot tracks a trajectory and the center of mass follows a balance reference. During manipulation, hand pose or interaction objectives may become more important while the legs preserve support. HQP allows the hierarchy to reflect these changing behavioral requirements without abandoning the common whole-body optimization framework.

Strict priorities nevertheless require careful transition management. If a task is suddenly activated, removed, or promoted to a different level, the feasible solution can change abruptly and produce discontinuities in acceleration, contact force, or torque. Practical systems therefore use smooth reference generation, constraint ramping, task activation functions, or state-machine logic to manage transitions between control modes.

HQP must also address situations in which the requested task set becomes infeasible. A humanoid may be commanded to reach beyond its workspace, maintain incompatible contacts, or generate forces exceeding actuator capability. Hard enforcement of every request would leave the optimization problem without a solution, which is unacceptable for a real-time controller that must always produce a safe command.

Slack variables provide one mechanism for controlled relaxation. Instead of requiring every task or constraint to be satisfied exactly, selected conditions can include bounded violations that are heavily penalized in the objective. Critical safety constraints can remain hard, while less critical tracking objectives are softened. The resulting hierarchy explicitly determines which requirements may be sacrificed when perfect simultaneous satisfaction is impossible.

This distinction between hard constraints and soft objectives is fundamental to robust humanoid WBC. A hand may accept several centimeters of tracking error if necessary, but an actuator should not exceed a destructive torque limit merely to preserve that trajectory. Similarly, nominal posture can be sacrificed during disturbance recovery while contact stability and balance receive dominant priority.

Collision avoidance can also be incorporated through inequalities derived from distances between robot bodies or between the robot and its environment. When two geometries approach a minimum allowed separation, the controller constrains their relative velocity or acceleration. Such constraints allow self-collision protection to participate directly in whole-body motion generation instead of functioning only as an emergency downstream check.

Real-time performance places strong requirements on the HQP solver. The optimization must be reconstructed and solved repeatedly as Jacobians, dynamics, contacts, limits, and task references change. Warm starting, sparse matrix operations, active-set methods, efficient factorization, and predictable memory allocation can substantially reduce computation and improve deterministic execution at high control frequencies.

Numerical scaling is equally important because HQP may combine quantities with very different units and magnitudes, including meters, radians, newtons, newton-meters, accelerations, and task residuals. Poor scaling can degrade conditioning and solver convergence. Appropriate normalization and carefully designed tolerances help ensure that numerical artifacts do not unintentionally alter the intended physical hierarchy.

HQP therefore extends operational-space reasoning into a more general constrained optimization architecture. OSC establishes how meaningful Cartesian tasks relate to whole-body dynamics, while HQP provides explicit mechanisms for resolving multiple tasks under equality constraints, inequality bounds, contact feasibility, actuator limits, and safety requirements. The two perspectives are complementary rather than mutually exclusive.

Within the humanoid WBC progression, this constrained hierarchical formulation provides the foundation for more detailed contact and wrench distribution, simultaneous locomotion and manipulation, compliant torque control, bimanual coordination, real-time optimization, and safety enforcement. Its central role is to convert competing behavioral requests and physical limitations into one prioritized, dynamically feasible whole-body command at every control cycle.

계층적 이차 계획법(Hierarchical Quadratic Programming, HQP)은 여러 휴머노이드 작업과 물리적 제약을 명시적인 우선순위(Priority)에 따라 구성할 수 있는 전신 제어(Whole-Body Control, WBC)용 체계적 최적화 프레임워크(Optimization Framework)를 제공한다. 서로 경쟁하는 오차를 하나의 가중 목적함수(Weighted Objective)에서 절충하는 방식과 달리, HQP는 낮은 우선순위 목표가 남아 있는 실행 가능 자유도를 사용하기 전에 높은 우선순위 작업의 해를 보존한다.

이차 계획법(Quadratic Programming, QP)은 제어 목표를 이차 비용함수(Quadratic Cost)로 표현하면서 결정 변수(Decision Variables)에 등식 제약(Equality Constraints)과 부등식 제약(Inequality Constraints)을 적용한다. 휴머노이드 WBC에서 이러한 변수에는 일반화 관절 가속도(Generalized Joint Accelerations), 액추에이터 토크(Actuator Torques), 접촉력(Contact Forces) 또는 이들의 조합이 포함될 수 있다. 이를 통해 작업 추종(Task Tracking)과 로봇 동역학(Robot Dynamics)을 독립적인 제어 단계로 처리하지 않고 하나의 문제에서 함께 해결할 수 있다.

가속도 수준(Acceleration Level)에서 데카르트 작업(Cartesian Task)은 \\(J\\ddot{q}+\\dot{J}\\dot{q}=\\ddot{x}_{des}\\)의 관계로 표현할 수 있다. 여러 작업이 서로 양립할 수 없는 가속도를 요구할 수 있기 때문에 제어기는 물리적 제약을 만족하면서 작업 잔차 오차(Residual Error)를 최소화한다. 따라서 최적화 과정은 현재 실행 가능 영역(Feasible Region) 안에서 요청된 작업 거동을 가장 가깝게 구현하는 일반화 운동(Generalized Motion)을 결정한다.

계층 구조(Hierarchical Structure)는 목표를 여러 우선순위 수준(Priority Levels)으로 분리함으로써 이러한 공식을 확장한다. 높은 우선순위의 최적화 문제를 먼저 해결하고, 그 결과로 얻어진 해를 보존하면서 다음 수준의 최적화를 수행한다. 낮은 수준의 목표는 상위 수준에서 이미 결정된 최적해를 저하시키지 않는 해 공간(Solution Space) 안에서만 부차적인 거동을 개선할 수 있다.

이러한 특성은 모든 목표가 동일한 물리적 중요성을 갖지 않는 휴머노이드 로봇(Humanoid Robot)에서 특히 유용하다. 강체 동역학적 일관성(Rigid-Body Dynamic Consistency), 유효한 환경 접촉(Valid Environmental Contacts), 안전 한계(Safety Limits)를 유지하는 것은 손 궤적 오차를 최소화하거나 선호 자세를 달성하는 것보다 근본적으로 중요하다. HQP는 단순히 수치 가중치를 수동으로 선택하는 대신 이러한 차이를 직접 표현하는 수학적 메커니즘을 제공한다.

등식 제약(Equality Constraints)은 일반적으로 정확하게 또는 매우 엄격한 허용 오차 안에서 만족되어야 하는 물리적 관계를 표현한다. 부유 베이스 운동 방정식(Floating-Base Equations of Motion), 활성 접촉 기구학(Active Contact Kinematics), 선택된 높은 우선순위 작업 방정식을 이러한 형태로 표현할 수 있다. 이러한 제약은 나머지 최적화 목표가 동작해야 하는 실행 가능 다양체(Feasible Manifold)를 정의한다.

부등식 제약(Inequality Constraints)은 휴머노이드의 많은 중요한 제한 조건이 정확한 하나의 값이 아니라 허용 가능한 범위로 정의되기 때문에 필수적이다. 관절 위치, 속도, 가속도 및 액추에이터 토크는 최소값과 최대값 사이에 유지되어야 한다. 접촉력은 마찰 및 단방향 접촉 조건(Friction and Unilateral-Contact Conditions)을 만족해야 하며, 충돌 회피(Collision Avoidance)는 거리 또는 상대 운동이 지정된 안전 경계의 허용 영역에 머물도록 요구한다.

일반적인 부등식은 \\(A_{ineq}z \\leq b_{ineq}\\)로 표현할 수 있으며, 여기서 \\(z\\)는 최적화 변수(Optimization Variables)를 포함한다. 이러한 간결한 표현을 사용하면 다양한 물리적 제한 조건을 동일한 솔버 인터페이스(Solver Interface) 안에서 기술할 수 있다. 또한 제한값은 로봇 구성(Configuration), 접촉 상태(Contact State), 추정된 환경 형상(Environment Geometry), 액추에이터 상태(Actuator Condition), 작업 단계(Task Phase)에 따라 매 제어 주기마다 변경될 수 있다.

마찰 제약(Friction Constraints)은 휴머노이드 제어에서 부등식이 중요한 이유를 대표적으로 보여준다. 지지 발(Stance Foot)은 지면을 밀 수 있지만 일반적으로 지면을 자신 쪽으로 당길 수 없으며, 미끄러짐을 방지하려면 접선 접촉력(Tangential Contact Force)이 수직력(Normal Force)에 비해 충분히 작게 유지되어야 한다. 따라서 마찰 원뿔(Friction Cone)은 허용 가능한 접촉력 영역을 정의하며, 실시간 최적화 효율성을 위해 일반적으로 선형 부등식(Linear Inequalities)으로 근사된다.

접촉 렌치 제약(Contact Wrench Constraints)은 지지 발 아래에서 발생하는 힘의 위치와 분포를 추가적으로 제한할 수 있다. 생성되는 렌치(Wrench)는 유한한 접촉 표면(Finite Contact Surface)과 사용 가능한 마찰 조건에 부합해야 한다. 이러한 제한을 HQP에 직접 포함하면 제어기가 물리적으로 불가능하거나 불안정한 지면 반력(Ground Reaction Forces)을 필요로 하는 수학적으로만 매력적인 전신 운동을 생성하는 것을 방지할 수 있다.

관절 한계(Joint Limits)는 기계적 경계에 도달할 때까지 기다리지 않고 선제적으로 처리할 수 있다. 위치 의존 속도 또는 가속도 제한(Position-Dependent Velocity or Acceleration Bounds)은 관절이 허용 범위에 접근할수록 운동을 점진적으로 제한할 수 있다. 이를 통해 최적화기를 위험한 구성에서 벗어나도록 유도하면서 남아 있는 자유도가 명령된 전신 거동에 계속 기여할 수 있는 실행 가능 영역을 형성한다.

토크 부등식(Torque Inequalities)은 또 다른 중요한 실행 가능성 계층을 제공한다. 원하는 가속도가 기구학적으로 달성 가능하더라도 필요한 액추에이터 토크가 모터, 기어박스, 열적 또는 전기적 한계(Motor, Gearbox, Thermal, or Electrical Capability)를 초과할 수 있다. 명시적인 토크 제한은 최적화기가 실제로 사용할 수 없는 구동 능력을 가정하는 것을 방지하며, 결과적으로 생성되는 운동을 실제 휴머노이드가 수행할 수 있는 동작과 더욱 일치시킨다.

부유 베이스 동역학(Floating-Base Dynamics)은 이러한 액추에이터 제약과 접촉 제약을 서로 결합한다. 베이스 자체는 비구동(Unactuated)이므로 원하는 베이스 가속도를 독립적으로 명령할 수 없다. 최적화기는 뉴턴-오일러 동역학(Newton-Euler Dynamics)을 만족하면서 요구되는 전신 가속도를 생성할 수 있는 관절 토크와 환경 접촉력의 조합을 찾아야 하며, 이러한 이유로 토크 수준 HQP(Torque-Level HQP)에서는 접촉력 변수가 특히 중요하다.

작업 우선순위(Task Priorities)는 현재 휴머노이드가 수행하는 행동에 따라 구성할 수 있다. 접촉 유지(Contact Preservation), 동역학적 일관성(Dynamic Consistency), 액추에이터 실행 가능성(Actuator Feasibility), 핵심 안전 제약(Critical Safety Constraints)은 일반적으로 가장 강한 우선순위 수준을 차지한다. 그 다음 무게중심 또는 운동량 거동과 같은 균형 관련 물리량이 배치되며, 발 운동, 손 조작, 몸통 방향, 시선(Gaze), 기준 자세(Nominal Posture)는 운용 요구사항에 따라 구성된다.

예를 들어 보행(Walking) 중에는 지지 발이 구속된 상태를 유지하면서 스윙 발(Swing Foot)이 궤적을 추종하고 무게중심이 균형 기준(Balance Reference)을 따라갈 수 있다. 조작(Manipulation) 중에는 손 자세 또는 상호작용 목표가 더욱 중요해지는 동안 다리는 지지 상태를 유지한다. HQP는 공통된 전신 최적화 프레임워크를 유지하면서 이러한 변화하는 행동 요구조건을 계층 구조에 반영할 수 있다.

그러나 엄격한 우선순위(Strict Priorities)는 신중한 전환 관리(Transition Management)를 요구한다. 작업이 갑자기 활성화되거나 제거되거나 다른 우선순위 수준으로 이동하면 실행 가능 해가 급격하게 변화하여 가속도, 접촉력 또는 토크에 불연속(Discontinuity)이 발생할 수 있다. 따라서 실제 시스템에서는 제어 모드 사이의 전환을 관리하기 위해 부드러운 기준 생성(Smooth Reference Generation), 제약 램핑(Constraint Ramping), 작업 활성화 함수(Task Activation Functions), 상태 기계 로직(State-Machine Logic) 등을 사용한다.

HQP는 요청된 작업 집합이 실행 불가능(Infeasible)해지는 상황도 처리해야 한다. 휴머노이드는 작업 공간을 넘어서는 위치로 손을 뻗도록 명령받거나, 서로 양립할 수 없는 접촉을 유지하도록 요구받거나, 액추에이터 능력을 초과하는 힘을 생성하도록 요청받을 수 있다. 모든 요구사항을 강제적으로 적용하면 최적화 문제에 해가 존재하지 않을 수 있으며, 항상 안전한 명령을 생성해야 하는 실시간 제어기에서는 이러한 상황을 허용할 수 없다.

슬랙 변수(Slack Variables)는 제어된 완화(Controlled Relaxation)를 제공하는 하나의 방법이다. 모든 작업이나 제약을 정확하게 만족하도록 요구하는 대신 선택된 조건에 제한된 위반(Bounded Violation)을 허용하고 목적함수에서 이에 큰 페널티(Penalty)를 부여할 수 있다. 핵심 안전 제약은 강성 제약(Hard Constraints)으로 유지하면서 덜 중요한 추종 목표는 연성화(Softening)할 수 있다. 이를 통해 모든 요구조건을 완벽하게 동시에 만족할 수 없을 때 어떤 요구를 희생할 것인지 계층 구조가 명시적으로 결정한다.

이러한 강성 제약(Hard Constraints)과 연성 목표(Soft Objectives)의 구분은 강건한 휴머노이드 WBC(Robust Humanoid WBC)의 핵심이다. 필요한 경우 손은 수 센티미터의 추종 오차를 허용할 수 있지만, 해당 궤적을 유지하기 위해 액추에이터가 파괴적인 토크 한계를 초과해서는 안 된다. 마찬가지로 외란 복구(Disturbance Recovery) 중에는 기준 자세를 희생할 수 있지만 접촉 안정성과 균형에는 지배적인 우선순위를 부여할 수 있다.

충돌 회피(Collision Avoidance) 역시 로봇 신체 사이 또는 로봇과 환경 사이의 거리로부터 생성된 부등식을 이용하여 포함할 수 있다. 두 형상이 최소 허용 거리(Minimum Allowed Separation)에 접근하면 제어기는 두 형상 사이의 상대 속도 또는 가속도를 제한한다. 이러한 제약을 통해 자기 충돌 방지(Self-Collision Protection)를 단순한 비상 하위 단계 검사로만 사용하는 대신 전신 운동 생성 과정에 직접 포함할 수 있다.

실시간 성능(Real-Time Performance)은 HQP 솔버(Solver)에 강한 요구조건을 부과한다. 자코비안, 동역학, 접촉, 제한 조건, 작업 기준이 변화함에 따라 최적화 문제를 반복적으로 재구성하고 해결해야 한다. 웜 스타팅(Warm Starting), 희소 행렬 연산(Sparse Matrix Operations), 활성 집합 방법(Active-Set Methods), 효율적인 행렬 분해(Efficient Factorization), 예측 가능한 메모리 할당(Predictable Memory Allocation)은 계산량을 크게 줄이고 높은 제어 주파수에서 결정론적 실행을 향상시킬 수 있다.

HQP는 미터, 라디안, 뉴턴, 뉴턴미터, 가속도, 작업 잔차처럼 단위와 크기가 매우 다른 물리량을 하나의 문제에서 다룰 수 있기 때문에 수치 스케일링(Numerical Scaling) 역시 중요하다. 부적절한 스케일링은 수치 조건성(Conditioning)과 솔버 수렴(Solver Convergence)을 저하시킬 수 있다. 적절한 정규화(Normalization)와 신중하게 설계된 허용 오차(Tolerances)는 수치적 인공 효과가 의도된 물리적 계층 구조를 비의도적으로 변화시키지 않도록 한다.

따라서 HQP는 운용 공간 추론(Operational-Space Reasoning)을 보다 일반적인 제약 기반 최적화 아키텍처(Constrained Optimization Architecture)로 확장한다. 운용 공간 제어(Operational Space Control, OSC)가 의미 있는 데카르트 작업과 전신 동역학 사이의 관계를 정의한다면, HQP는 등식 제약, 부등식 제한, 접촉 실행 가능성, 액추에이터 한계, 안전 요구사항 아래에서 여러 작업을 명시적으로 해결하는 메커니즘을 제공한다. 두 관점은 서로 대체되는 것이 아니라 상호 보완적이다.

휴머노이드 WBC의 전체적인 발전 과정에서 이러한 제약 기반 계층적 공식(Constrained Hierarchical Formulation)은 보다 상세한 접촉 및 렌치 분배(Contact and Wrench Distribution), 동시 보행 및 조작(Simultaneous Locomotion and Manipulation), 순응 토크 제어(Compliant Torque Control), 양손 협조(Bimanual Coordination), 실시간 최적화(Real-Time Optimization), 안전 제약 적용(Safety Enforcement)을 위한 기반을 제공한다. 핵심 역할은 서로 경쟁하는 행동 요청과 물리적 한계를 매 제어 주기마다 하나의 우선순위화되고 동역학적으로 실행 가능한 전신 명령(Prioritized, Dynamically Feasible Whole-Body Command)으로 변환하는 것이다.

##  

## 05.04. Contact Constraint Modeling and Wrench Dist [w/Code]

![](images/image4.png){width="7.268055555555556in" height="7.268055555555556in"}

Contact constraint modeling is a fundamental component of humanoid whole-body control because a floating-base robot can regulate its global motion only through physical interaction with the environment. Feet, hands, knees, or other body surfaces may establish contacts that constrain motion and generate forces. The controller must represent these interactions consistently with robot dynamics while allowing contacts to appear, disappear, and change during task execution.

A rigid contact is commonly described by constraining the velocity or acceleration of a selected contact frame. If the contact should remain stationary, its velocity satisfies \\(J_c\\dot{q}=0\\), where \\(J_c\\) is the contact Jacobian. At the acceleration level, differentiation gives \\(J_c\\ddot{q}+\\dot{J}_c\\dot{q}=0\\), providing an equality constraint that can be incorporated directly into whole-body optimization.

These equations prevent commanded generalized accelerations from producing motion incompatible with the assumed contact state. During standing, for example, the stance feet should not translate or rotate relative to the supporting floor under an ideal rigid-contact assumption. During single support, only the stance foot remains constrained, while the swing foot is released from the contact model and becomes an actively controlled motion task.

Contact constraints interact directly with the floating-base equations of motion. A typical dynamic representation includes the generalized inertia matrix, nonlinear effects, actuator torques, and generalized forces generated by environmental contacts. Contact forces enter through the transpose of the contact Jacobian, allowing forces applied at feet or hands to influence both the unactuated floating base and the actuated joints.

The contact wrench extends this representation from a simple force to the combined effect of force and moment. For a planar foot contact, the wrench can include three force components and three moment components expressed in an appropriate contact frame. This six-dimensional representation is useful because humanoid feet have finite support areas and can transmit moments as well as translational forces under suitable contact conditions.

Not every mathematically possible wrench is physically realizable. A unilateral ground contact normally permits compressive normal force but not arbitrary tensile force. Tangential forces are limited by available friction, while contact moments are restricted by foot geometry and pressure distribution. Consequently, whole-body control must constrain contact wrenches to remain inside a physically admissible region.

Coulomb friction provides a basic model for tangential contact feasibility. The magnitude of tangential force must remain bounded relative to the normal force according to the coefficient of friction. Although the resulting friction cone is nonlinear, real-time quadratic programming commonly approximates it with a polyhedral friction pyramid represented by a collection of linear inequality constraints.

The normal-force constraint ensures that the supporting surface pushes against the robot rather than unrealistically pulling it. Lower and upper bounds may also be introduced to represent minimum support requirements or practical limits associated with the environment and robot hardware. Together with tangential friction limits, these conditions determine whether a candidate ground reaction force can be maintained without separation or slip.

For a finite foot, the center of pressure must remain within the support surface if the assumed planar contact is to remain valid. This requirement can be expressed through inequalities involving components of the contact wrench. Limiting the center of pressure prevents the optimizer from demanding a moment that would require pressure outside the physical foot, which could otherwise cause rotation about an edge or loss of contact.

Yaw moments around the surface normal can also require explicit limits. Although a planar foot may resist some torsional loading through distributed friction, that capability is finite. A wrench model that permits unlimited yaw moment can produce dynamically valid but physically unrealistic solutions. Practical wrench cones therefore constrain forces and moments together according to the properties of the contact surface.

Wrench distribution becomes important when several contacts simultaneously support the humanoid. During double-support standing, the total force and moment required to regulate whole-body motion can be shared between the left and right feet. During manipulation, one or both hands may additionally contribute environmental reaction forces, creating a multi-contact system with multiple feasible ways to generate the required net wrench.

The controller must determine how these contact wrenches should be distributed while preserving the desired global dynamics. The sum of appropriately transformed contact effects must provide the linear and angular momentum change required by the commanded motion. Because multiple contacts introduce redundancy, many different wrench combinations may satisfy the same overall dynamic requirement.

Optimization provides a natural mechanism for resolving this redundancy. A whole-body controller can minimize contact-force magnitude, balance loading between feet, maintain preferred center-of-pressure locations, or preserve margin from friction boundaries. These secondary criteria improve robustness without changing the primary requirement that the combined contact wrenches support the desired whole-body dynamics.

Centroidal dynamics offer a useful interpretation of wrench distribution. The rate of change of whole-body linear momentum is determined by external forces, while angular momentum evolution depends on external moments and their locations relative to the center of mass. Contact wrenches therefore provide the physical interface through which a humanoid regulates its global translational and rotational behavior.

During quiet standing, wrench distribution may favor approximately balanced loading between both feet. When the robot prepares to step, the controller progressively transfers load toward the future stance foot so that the other foot can be unloaded and released. This load transfer should be continuous because abrupt changes in contact force can generate undesirable acceleration, impact, or instability.

Contact transitions are consequently an important part of the model. A foot scheduled to lift should not instantly change from a heavily loaded rigid contact to an unconstrained swing task. The controller can gradually reduce its desired normal force, modify contact constraints, and activate the swing trajectory. The reverse process occurs during touchdown, where contact establishment must account for impact and force buildup.

Real contacts are not perfectly rigid. Foot soles, structural compliance, surface deformation, actuator elasticity, and estimation errors introduce motion even when the mathematical model assumes zero contact velocity. Excessively strict constraints can therefore amplify model mismatch. Practical controllers may use compliant contact models, softened constraints, damping, or feedback corrections to improve robustness on physical hardware.

Contact-state estimation is equally important because the optimization model must reflect the contacts that actually exist. Force-torque sensors, joint torque estimates, tactile sensors, kinematic consistency, and other measurements can help determine whether a foot or hand is supporting load. Incorrectly assuming a contact can make the controller rely on a reaction force that the environment cannot provide.

The contact model must also account for reference frames. Forces and moments may be measured or optimized in local foot frames, world coordinates, or other frames, while dynamic equations require consistent transformations. A wrench transformation must correctly account for both rotation and moment changes caused by different reference points, particularly when combining multiple contacts into a net whole-body effect.

In hierarchical QP, contact kinematics are typically treated as high-priority constraints because violating a required stance contact can immediately compromise balance. Friction, unilateral-force, center-of-pressure, and wrench limits enter naturally as inequalities. Lower-priority tasks such as hand tracking or nominal posture are then optimized only within the motion and force space allowed by these contact conditions.

Contact constraints also influence manipulation. When a humanoid pushes a wall, supports itself with a hand, carries a load against a surface, or performs contact-rich assembly, the hand is no longer merely a free-space pose task. Its interaction must be represented through contact geometry and admissible wrench conditions, allowing the same WBC framework to coordinate locomotion contacts and manipulation contacts.

Computational efficiency remains essential because contact Jacobians, feasible wrench regions, and force distributions can change every control cycle. The controller must update kinematics and dynamics, construct contact equalities and inequalities, solve for motion and forces, and transmit actuator commands within a deterministic period. Efficient matrix assembly and warm-started optimization are therefore valuable in real-time humanoid implementations.

Contact constraint modeling and wrench distribution ultimately connect abstract whole-body motion objectives to the physical mechanisms that make floating-base control possible. By representing contact kinematics, friction, unilateral support, finite contact geometry, and multi-contact force sharing within one optimization framework, WBC can generate motions that are not only mathematically consistent but physically executable.

Within the broader humanoid WBC structure, this contact formulation follows operational-space control and hierarchical QP and prepares the foundation for simultaneous locomotion and manipulation. Once the controller can reason explicitly about where the body is constrained and how environmental reaction wrenches should be distributed, feet and hands can be coordinated as parts of one dynamically coupled multi-contact system.

접촉 제약 모델링(Contact Constraint Modeling)은 부유 베이스 로봇(Floating-Base Robot)이 환경과의 물리적 상호작용을 통해서만 전체적인 운동을 조절할 수 있기 때문에 휴머노이드 전신 제어(Humanoid Whole-Body Control)의 핵심 구성 요소이다. 발, 손, 무릎 또는 기타 신체 표면은 운동을 구속하고 힘을 생성하는 접촉(Contact)을 형성할 수 있다. 제어기는 이러한 상호작용을 로봇 동역학(Robot Dynamics)과 일관되게 표현하면서 작업 수행 중 접촉이 생성되고 제거되거나 변화할 수 있도록 해야 한다.

강체 접촉(Rigid Contact)은 일반적으로 선택된 접촉 프레임(Contact Frame)의 속도 또는 가속도를 구속하는 방식으로 표현된다. 접촉이 정지 상태를 유지해야 한다면 그 속도는 \\(J_c\\dot{q}=0\\)을 만족하며, 여기서 \\(J_c\\)는 접촉 자코비안(Contact Jacobian)이다. 이를 가속도 수준에서 미분하면 \\(J_c\\ddot{q}+\\dot{J}_c\\dot{q}=0\\)이 되며, 전신 최적화(Whole-Body Optimization)에 직접 포함할 수 있는 등식 제약(Equality Constraint)을 제공한다.

이러한 방정식은 명령된 일반화 가속도(Generalized Acceleration)가 가정된 접촉 상태(Contact State)와 양립할 수 없는 운동을 생성하지 못하도록 한다. 예를 들어 정지 상태(Standing)에서는 이상적인 강체 접촉 가정 아래 지지 발(Stance Feet)이 지지 바닥에 대해 병진하거나 회전해서는 안 된다. 한발 지지(Single Support)에서는 지지 발만 계속 구속되며, 스윙 발(Swing Foot)은 접촉 모델에서 해제되어 능동적으로 제어되는 운동 작업(Motion Task)으로 전환된다.

접촉 제약(Contact Constraints)은 부유 베이스 운동 방정식(Floating-Base Equations of Motion)과 직접적으로 상호작용한다. 일반적인 동역학 표현에는 일반화 관성 행렬(Generalized Inertia Matrix), 비선형 효과(Nonlinear Effects), 액추에이터 토크(Actuator Torques), 환경 접촉에 의해 생성되는 일반화 힘(Generalized Forces)이 포함된다. 접촉력(Contact Forces)은 접촉 자코비안의 전치(Contact Jacobian Transpose)를 통해 작용하여 발이나 손에 가해지는 힘이 비구동 부유 베이스와 구동 관절 모두에 영향을 줄 수 있도록 한다.

접촉 렌치(Contact Wrench)는 단순한 힘의 표현을 힘과 모멘트(Force and Moment)의 결합 효과로 확장한다. 평면 발 접촉(Planar Foot Contact)의 경우 렌치는 적절한 접촉 프레임에서 표현되는 세 개의 힘 성분과 세 개의 모멘트 성분을 포함할 수 있다. 이러한 6차원 표현(Six-Dimensional Representation)은 휴머노이드의 발이 유한한 지지 면적을 가지며 적절한 접촉 조건에서 병진력뿐 아니라 모멘트도 전달할 수 있기 때문에 유용하다.

수학적으로 가능한 모든 렌치가 물리적으로 실현 가능한 것은 아니다. 단방향 지면 접촉(Unilateral Ground Contact)은 일반적으로 압축 수직력(Compressive Normal Force)은 허용하지만 임의의 인장력(Tensile Force)은 허용하지 않는다. 접선력(Tangential Force)은 사용 가능한 마찰에 의해 제한되고 접촉 모멘트(Contact Moment)는 발의 형상과 압력 분포(Pressure Distribution)에 의해 제한된다. 따라서 전신 제어기는 접촉 렌치가 물리적으로 허용 가능한 영역(Physically Admissible Region) 안에 유지되도록 제한해야 한다.

쿨롱 마찰(Coulomb Friction)은 접선 방향 접촉의 실행 가능성을 나타내는 기본 모델을 제공한다. 접선력의 크기는 마찰 계수(Coefficient of Friction)에 따라 수직력에 대한 일정 범위 내로 제한되어야 한다. 이로 인해 생성되는 마찰 원뿔(Friction Cone)은 비선형이지만, 실시간 이차 계획법(Real-Time Quadratic Programming)에서는 일반적으로 여러 개의 선형 부등식 제약(Linear Inequality Constraints)으로 표현되는 다면체 마찰 피라미드(Polyhedral Friction Pyramid)로 근사한다.

수직력 제약(Normal-Force Constraint)은 지지 표면이 로봇을 비현실적으로 끌어당기는 것이 아니라 로봇을 밀어 지지하도록 보장한다. 최소 지지 요구조건이나 환경 및 로봇 하드웨어와 관련된 실질적인 한계를 표현하기 위해 하한 및 상한(Lower and Upper Bounds)을 추가할 수도 있다. 이러한 조건은 접선 방향 마찰 한계와 함께 후보 지면 반력(Ground Reaction Force)이 접촉 분리나 미끄러짐 없이 유지될 수 있는지를 결정한다.

유한한 크기의 발에서는 가정된 평면 접촉이 유지되기 위해 압력 중심(Center of Pressure, CoP)이 지지 표면(Support Surface) 내부에 존재해야 한다. 이러한 요구사항은 접촉 렌치의 성분과 관련된 부등식으로 표현할 수 있다. CoP를 제한하면 최적화기가 실제 발의 외부에 압력이 존재해야만 생성 가능한 모멘트를 요구하는 것을 방지하며, 그렇지 않을 경우 발의 모서리를 중심으로 한 회전이나 접촉 손실이 발생할 수 있다.

표면 법선(Surface Normal)을 중심으로 하는 요 모멘트(Yaw Moment) 역시 명시적인 제한이 필요할 수 있다. 평면 발은 분포된 마찰(Distributed Friction)을 통해 일정 수준의 비틀림 하중(Torsional Loading)에 저항할 수 있지만 그 능력에는 한계가 있다. 무제한의 요 모멘트를 허용하는 렌치 모델은 동역학적으로는 유효하지만 물리적으로 비현실적인 해를 생성할 수 있다. 따라서 실제적인 렌치 원뿔(Wrench Cone)은 접촉 표면의 특성에 따라 힘과 모멘트를 함께 제한한다.

여러 접촉이 동시에 휴머노이드를 지지할 때는 렌치 분배(Wrench Distribution)가 중요해진다. 양발 지지(Double-Support) 상태에서는 전신 운동을 조절하는 데 필요한 전체 힘과 모멘트를 왼발과 오른발이 분담할 수 있다. 조작(Manipulation) 중에는 한 손 또는 양손이 추가적으로 환경 반력(Environmental Reaction Force)에 기여할 수 있으므로 필요한 순 렌치(Net Wrench)를 생성할 수 있는 여러 방법을 가진 다중 접촉 시스템(Multi-Contact System)이 형성된다.

제어기는 원하는 전체 동역학(Global Dynamics)을 유지하면서 이러한 접촉 렌치를 어떻게 분배할 것인지 결정해야 한다. 적절하게 좌표 변환된 접촉 효과의 합은 명령된 운동에 필요한 선형 및 각운동량 변화(Linear and Angular Momentum Change)를 제공해야 한다. 여러 접촉은 여유도(Redundancy)를 생성하므로 동일한 전체 동역학적 요구조건을 만족하는 다양한 렌치 조합이 존재할 수 있다.

최적화(Optimization)는 이러한 여유성을 해결하기 위한 자연스러운 메커니즘을 제공한다. 전신 제어기는 접촉력의 크기를 최소화하거나, 양발 사이의 하중을 균형 있게 분배하고, 선호하는 압력 중심 위치를 유지하거나, 마찰 경계(Friction Boundaries)로부터 충분한 여유를 확보하도록 최적화할 수 있다. 이러한 부차적인 기준은 결합된 접촉 렌치가 원하는 전신 동역학을 지지해야 한다는 주요 요구조건을 변경하지 않으면서 강건성(Robustness)을 향상시킨다.

중심 동역학(Centroidal Dynamics)은 렌치 분배를 이해하기 위한 유용한 관점을 제공한다. 전신 선형 운동량(Whole-Body Linear Momentum)의 변화율은 외력에 의해 결정되며, 각운동량(Angular Momentum)의 변화는 외부 모멘트와 무게중심(Center of Mass, CoM)에 대한 외력의 작용 위치에 의해 결정된다. 따라서 접촉 렌치는 휴머노이드가 전체적인 병진 및 회전 거동을 조절하는 물리적 인터페이스(Physical Interface)를 제공한다.

정적인 직립 상태(Quiet Standing)에서는 렌치 분배가 양발 사이의 대략적인 균형 하중을 선호하도록 구성될 수 있다. 로봇이 보행을 준비할 때는 다른 발의 하중을 제거하여 지면에서 해제할 수 있도록 제어기가 하중을 미래의 지지 발(Future Stance Foot) 쪽으로 점진적으로 이동시킨다. 접촉력의 급격한 변화는 바람직하지 않은 가속도, 충격(Impact), 불안정성을 발생시킬 수 있으므로 이러한 하중 이동(Load Transfer)은 연속적으로 이루어져야 한다.

따라서 접촉 전환(Contact Transition)은 접촉 모델에서 중요한 부분을 차지한다. 들어 올릴 예정인 발이 높은 하중을 받는 강체 접촉 상태에서 갑자기 제약이 없는 스윙 작업으로 변화해서는 안 된다. 제어기는 목표 수직력(Desired Normal Force)을 점진적으로 감소시키고 접촉 제약을 수정하면서 스윙 궤적(Swing Trajectory)을 활성화할 수 있다. 착지(Touchdown)에서는 반대 과정이 수행되며, 접촉 형성 과정에서 충격과 힘의 증가를 고려해야 한다.

실제 접촉(Real Contact)은 완전한 강체가 아니다. 발바닥(Foot Sole), 구조적 순응성(Structural Compliance), 표면 변형(Surface Deformation), 액추에이터 탄성(Actuator Elasticity), 추정 오차(Estimation Error)로 인해 수학적 모델에서 접촉 속도를 0으로 가정하더라도 실제로는 움직임이 발생할 수 있다. 지나치게 엄격한 제약은 이러한 모델 불일치를 증폭시킬 수 있으므로 실제 제어기에서는 순응 접촉 모델(Compliant Contact Model), 연성화된 제약(Softened Constraints), 감쇠(Damping), 피드백 보정(Feedback Correction) 등을 사용할 수 있다.

접촉 상태 추정(Contact-State Estimation) 역시 최적화 모델이 실제로 존재하는 접촉 상태를 반영해야 하므로 중요하다. 힘-토크 센서(Force-Torque Sensor), 관절 토크 추정(Joint Torque Estimation), 촉각 센서(Tactile Sensor), 기구학적 일관성(Kinematic Consistency), 기타 측정 정보를 이용하여 발이나 손이 실제로 하중을 지지하고 있는지를 판단할 수 있다. 존재하지 않는 접촉을 잘못 가정하면 제어기가 환경에서 제공할 수 없는 반력에 의존하게 될 수 있다.

접촉 모델은 기준 좌표계(Reference Frames)도 고려해야 한다. 힘과 모멘트는 로컬 발 좌표계(Local Foot Frame), 월드 좌표계(World Coordinates) 또는 다른 좌표계에서 측정하거나 최적화할 수 있지만, 동역학 방정식에서는 일관된 좌표 변환이 필요하다. 렌치 변환(Wrench Transformation)은 회전뿐 아니라 기준점 차이로 발생하는 모멘트 변화까지 올바르게 고려해야 하며, 여러 접촉을 하나의 순 전신 효과(Net Whole-Body Effect)로 결합할 때 특히 중요하다.

계층적 이차 계획법(Hierarchical Quadratic Programming, HQP)에서는 요구되는 지지 접촉의 위반이 즉각적으로 균형을 손상시킬 수 있기 때문에 접촉 기구학(Contact Kinematics)을 일반적으로 높은 우선순위 제약으로 처리한다. 마찰, 단방향 힘(Unilateral Force), 압력 중심, 렌치 제한은 자연스럽게 부등식 제약(Inequality Constraints)으로 포함된다. 이후 손 추종(Hand Tracking)이나 기준 자세(Nominal Posture)와 같은 낮은 우선순위 작업은 이러한 접촉 조건이 허용하는 운동 및 힘 공간 안에서만 최적화된다.

접촉 제약은 조작(Manipulation)에도 영향을 준다. 휴머노이드가 벽을 밀거나 손으로 신체를 지지하고, 표면에 물체를 대고 운반하거나 접촉 중심 조립(Contact-Rich Assembly)을 수행할 때 손은 더 이상 단순한 자유 공간 자세 작업(Free-Space Pose Task)이 아니다. 손의 상호작용은 접촉 형상(Contact Geometry)과 허용 가능한 렌치 조건을 통해 표현되어야 하며, 이를 통해 동일한 WBC 프레임워크에서 보행 접촉과 조작 접촉을 함께 협조 제어할 수 있다.

접촉 자코비안, 실행 가능한 렌치 영역(Feasible Wrench Region), 힘 분배가 매 제어 주기마다 변화할 수 있기 때문에 계산 효율성(Computational Efficiency)은 여전히 필수적이다. 제어기는 결정론적인 제어 주기(Deterministic Control Period) 내에서 기구학과 동역학을 갱신하고, 접촉 등식 및 부등식을 구성하며, 운동과 힘을 계산하고, 액추에이터 명령을 전송해야 한다. 따라서 효율적인 행렬 구성(Matrix Assembly)과 웜 스타트 최적화(Warm-Started Optimization)는 실시간 휴머노이드 구현에서 중요한 역할을 한다.

접촉 제약 모델링과 렌치 분배는 궁극적으로 추상적인 전신 운동 목표를 부유 베이스 제어를 실제로 가능하게 하는 물리적 메커니즘과 연결한다. 접촉 기구학, 마찰, 단방향 지지(Unilateral Support), 유한 접촉 형상(Finite Contact Geometry), 다중 접촉 힘 분배(Multi-Contact Force Sharing)를 하나의 최적화 프레임워크 안에서 표현함으로써 WBC는 수학적으로 일관될 뿐 아니라 물리적으로 실행 가능한 운동을 생성할 수 있다.

전체 휴머노이드 WBC 구조에서 이러한 접촉 공식(Contact Formulation)은 운용 공간 제어(Operational Space Control)와 계층적 이차 계획법(Hierarchical QP)의 다음 단계에 위치하며, 동시 보행 및 조작(Simultaneous Locomotion and Manipulation)을 위한 기반을 제공한다. 제어기가 신체의 어느 부분이 구속되어 있는지, 그리고 환경 반력 렌치(Environmental Reaction Wrench)를 어떻게 분배해야 하는지를 명시적으로 추론할 수 있게 되면 발과 손을 하나의 동역학적으로 결합된 다중 접촉 시스템(Dynamically Coupled Multi-Contact System)의 구성 요소로 통합하여 협조 제어할 수 있다.

##  

## 05.05. Simultaneous Loco and Manipulation WBC [w/Code]

![](images/image5.png){width="7.268055555555556in" height="7.268055555555556in"}

Simultaneous locomotion and manipulation requires a humanoid to coordinate walking, balance, posture, and object interaction as one dynamically coupled control problem. Arm motion changes the distribution of mass and momentum, while stepping changes the support geometry available to manipulation. Whole-body control therefore avoids treating locomotion and manipulation as independent subsystems whose commands are combined only at the joint level.

The control architecture receives references from locomotion and manipulation planners and resolves them through a common whole-body model. Locomotion may specify center-of-mass motion, stance contacts, swing-foot trajectories, and torso orientation, while manipulation provides hand poses, interaction forces, or object trajectories. WBC determines joint motion, torque, and contact forces that satisfy these objectives within physical constraints.

The floating-base dynamics provide the common physical foundation. Neither the pelvis nor torso is directly actuated in Cartesian space, so global body motion results from internal joint torques together with environmental reaction forces. Consequently, an arm command can affect balance, and a leg command can change hand motion unless both are coordinated through the coupled equations of motion.

A task-space representation makes this integration natural. Feet, hands, center of mass, pelvis, torso, and other body frames can each be represented by Cartesian tasks with associated Jacobians. The controller can then reason about these objectives in physically meaningful coordinates while using generalized coordinates internally to determine a single consistent motion of the complete humanoid.

Task hierarchy becomes essential when locomotion and manipulation objectives compete. Contact preservation, dynamic consistency, actuator feasibility, and safety constraints normally receive the strongest protection. Balance and support objectives follow, while swing-foot motion, hand tracking, torso regulation, and nominal posture are assigned priorities according to the current phase and operational purpose of the behavior.

Consider a humanoid walking toward a workstation while carrying an object. The hands must preserve a stable grasp and maintain the object near a desired pose while the legs generate alternating support and swing motions. The torso and arms may compensate for walking-induced disturbances, while the center of mass must remain compatible with the evolving support configuration throughout the movement.

This problem cannot be solved reliably by simply superimposing an arm controller on a walking controller. Manipulation motion modifies whole-body inertia and momentum, while locomotion continuously moves the pelvis beneath the arms. Independent controllers can therefore request conflicting joint accelerations or torques, producing tracking degradation, unnecessary internal forces, or loss of balance.

A unified WBC instead evaluates the consequences of both behaviors within the same optimization. Generalized accelerations, actuator torques, and contact forces can be selected as decision variables, while locomotion and manipulation objectives enter as task equations or costs. Equality and inequality constraints enforce dynamics, contacts, friction, joint limits, torque limits, and other feasibility conditions.

Contact scheduling provides the structural link between walking and whole-body optimization. During double support, both feet contribute reaction wrenches and constrain body motion. During single support, one foot becomes a swing task while the remaining stance foot must provide the reaction forces necessary for balance, manipulation, and acceleration of the rest of the body.

Manipulation may introduce additional contacts. A hand pushing against a wall, operating a tool, or supporting the body can generate an environmental reaction wrench that contributes to whole-body stability. The controller can therefore treat feet and hands within a common multi-contact formulation rather than assuming that only the legs participate in maintaining global dynamic equilibrium.

Centroidal momentum provides a useful coupling variable between locomotion and upper-body motion. Rapid arm or object movement can generate significant angular momentum that must be compensated by the torso, legs, or contact forces. Momentum regulation allows WBC to coordinate these effects globally instead of forcing individual body segments to remain independently fixed during manipulation.

Center-of-mass control must also account for the manipulated object when its mass is significant. Carrying a heavy payload changes the effective mass distribution and may shift the combined center of mass away from the unloaded robot model. If the controller ignores this effect, planned support forces and balance behavior can become inaccurate even when hand tracking remains geometrically correct.

Object-aware control can incorporate estimated payload properties into the dynamic model or represent their influence through measured interaction wrenches. The resulting controller can modify posture, foot loading, and arm configuration according to the payload. This is particularly important when lifting, carrying, pushing, or pulling objects whose forces are large relative to the humanoid\'s own dynamic margins.

Redundancy enables the body to assist manipulation without abandoning locomotion. If a hand target approaches the boundary of the arm workspace, the torso can rotate, the pelvis can translate, or the robot can take an additional step. Whole-body coordination therefore expands the effective manipulation workspace beyond what can be achieved by arm joints alone.

This capability also supports mobile reaching. Rather than requiring a navigation system to place the pelvis at one exact pose before manipulation begins, the controller can allow base motion and hand motion to evolve together. The robot may continue stepping while extending an arm, provided that contact feasibility, balance, collision avoidance, and task priorities remain satisfied.

Bimanual manipulation increases the coupling further because both hands may constrain the pose of the same object. Relative hand geometry, object pose, internal grasp forces, torso orientation, and lower-body balance must then be coordinated simultaneously. Excessively rigid independent hand commands can overconstrain the system, making compliant or object-centered task representations valuable.

Force interaction also changes locomotion requirements. When the humanoid pushes a heavy object, the reaction force transmitted through the hands must ultimately be balanced by ground reaction forces at the feet. Friction limits at both hand and foot contacts therefore constrain the maximum usable pushing force, and WBC can distribute the required wrench while maintaining feasible contact conditions.

Task priorities may change continuously throughout a loco-manipulation sequence. During a step, swing-foot clearance and stance stability may temporarily dominate hand accuracy. During insertion or assembly, precise end-effector motion may become more important while the lower body maintains a nearly stationary support configuration. Smooth priority transitions prevent abrupt changes in acceleration or torque.

Collision constraints are especially important because arm, leg, torso, object, and environment motions occur concurrently. The controller must avoid self-collision while also maintaining clearance from nearby structures. Distance-based inequalities can restrict dangerous relative motion and use redundant degrees of freedom to redirect the body without unnecessarily interrupting the primary manipulation or locomotion task.

Actuator limits provide another source of coupling. Carrying a payload increases arm torque requirements, but compensatory torso and leg motion can also increase lower-body loading. A dynamically feasible WBC accounts for these limits jointly, preventing a motion that is kinematically valid but impossible because one or more joints would exceed torque, velocity, acceleration, or thermal capability.

State estimation must provide a consistent description of the entire interaction. Floating-base pose and velocity, joint states, foot contacts, hand forces, object interaction, and potentially payload estimates must be synchronized with the robot model. Errors in contact or base estimation can propagate directly into both manipulation accuracy and balance, making reliable estimation central to unified control.

Real-time execution requires the optimization to adapt as support contacts, manipulation objectives, and constraints change. Kinematics, dynamics, task Jacobians, contact models, wrench limits, and collision conditions must be updated within each control cycle. Warm-starting and efficient numerical methods help maintain deterministic execution when the number and type of active tasks vary.

A practical architecture also preserves separation between planning and control. Higher-level modules decide where to walk, what object to manipulate, and what task sequence to execute. WBC does not replace these planners; instead, it acts as the real-time physical coordination layer that converts their concurrent references into one dynamically consistent command for the complete humanoid body.

Simultaneous loco-manipulation WBC therefore represents an important transition from specialized humanoid behaviors toward integrated physical autonomy. Walking, reaching, carrying, pushing, and interacting are no longer isolated modes but different combinations of task objectives and contact conditions handled through a shared whole-body framework. This enables the humanoid to use its entire body as one coordinated mechanical system.

Within the chapter progression, this formulation builds directly on task-space hierarchy, operational-space control, hierarchical QP, and contact-wrench modeling. It establishes the integrated control foundation required for the subsequent treatment of compliant joint torque control, bimanual execution, high-frequency real-time optimization, safety constraints, and simulation-to-real validation in humanoid WBC.

동시 보행 및 조작(Simultaneous Locomotion and Manipulation)은 휴머노이드가 보행, 균형, 자세, 물체 상호작용을 하나의 동역학적으로 결합된 제어 문제(Dynamically Coupled Control Problem)로 협조 제어할 것을 요구한다. 팔의 운동은 질량 및 운동량 분포를 변화시키고, 보행은 조작에 사용할 수 있는 지지 형상(Support Geometry)을 변화시킨다. 따라서 전신 제어(Whole-Body Control, WBC)는 보행과 조작을 독립적인 하위 시스템으로 처리한 뒤 관절 수준에서만 명령을 결합하는 방식을 피한다.

제어 아키텍처(Control Architecture)는 보행 및 조작 플래너(Locomotion and Manipulation Planner)로부터 기준값을 받아 공통 전신 모델(Common Whole-Body Model)을 통해 이를 통합적으로 해결한다. 보행은 무게중심(Center of Mass, CoM) 운동, 지지 접촉(Stance Contacts), 스윙 발 궤적(Swing-Foot Trajectory), 몸통 방향(Torso Orientation)을 지정할 수 있으며, 조작은 손 자세(Hand Pose), 상호작용 힘(Interaction Force), 물체 궤적(Object Trajectory)을 제공한다. WBC는 물리적 제약 안에서 이러한 목표를 만족하는 관절 운동, 토크, 접촉력을 결정한다.

부유 베이스 동역학(Floating-Base Dynamics)은 두 동작을 통합하는 공통된 물리적 기반을 제공한다. 골반이나 몸통은 데카르트 공간(Cartesian Space)에서 직접 구동되지 않으므로 전체 신체 운동은 내부 관절 토크와 환경 반력(Environmental Reaction Force)의 결합으로 발생한다. 따라서 두 동작이 결합된 운동 방정식(Equations of Motion)을 통해 협조 제어되지 않으면 팔 명령이 균형에 영향을 주고 다리 명령이 손의 운동을 변화시킬 수 있다.

작업 공간 표현(Task-Space Representation)은 이러한 통합을 자연스럽게 만든다. 발, 손, 무게중심, 골반, 몸통 및 기타 신체 프레임(Body Frames)은 각각 관련 자코비안(Jacobian)을 가진 데카르트 작업(Cartesian Task)으로 표현할 수 있다. 제어기는 물리적으로 의미 있는 좌표에서 이러한 목표를 다루면서 내부적으로 일반화 좌표(Generalized Coordinates)를 사용하여 전체 휴머노이드에 대한 하나의 일관된 운동을 결정할 수 있다.

보행과 조작 목표가 서로 경쟁할 때 작업 계층 구조(Task Hierarchy)는 필수적이다. 접촉 유지(Contact Preservation), 동역학적 일관성(Dynamic Consistency), 액추에이터 실행 가능성(Actuator Feasibility), 안전 제약(Safety Constraints)은 일반적으로 가장 강하게 보호된다. 이후 균형 및 지지 목표가 배치되며, 스윙 발 운동, 손 추종, 몸통 조절, 기준 자세(Nominal Posture)는 현재 행동 단계와 운용 목적에 따라 우선순위가 결정된다.

휴머노이드가 물체를 운반하면서 작업대로 걸어가는 상황을 생각할 수 있다. 손은 안정적인 파지(Stable Grasp)를 유지하고 물체를 원하는 자세 근처에 유지해야 하며, 다리는 교대로 지지 및 스윙 운동을 생성해야 한다. 몸통과 팔은 보행으로 발생하는 외란을 보상할 수 있어야 하며, 무게중심은 전체 이동 과정에서 계속 변화하는 지지 구성(Support Configuration)과 양립할 수 있는 상태를 유지해야 한다.

이 문제는 보행 제어기(Walking Controller)에 단순히 팔 제어기(Arm Controller)를 중첩하는 방식으로는 신뢰성 있게 해결할 수 없다. 조작 운동은 전신 관성과 운동량을 변화시키며, 보행은 팔 아래에 위치한 골반을 지속적으로 이동시킨다. 따라서 독립적인 제어기는 서로 충돌하는 관절 가속도나 토크를 요구할 수 있으며, 그 결과 추종 성능 저하, 불필요한 내부 힘(Internal Force), 균형 상실이 발생할 수 있다.

통합 WBC(Unified WBC)는 두 행동의 영향을 동일한 최적화(Optimization) 안에서 평가한다. 일반화 가속도(Generalized Accelerations), 액추에이터 토크(Actuator Torques), 접촉력(Contact Forces)을 결정 변수(Decision Variables)로 선택할 수 있으며, 보행 및 조작 목표는 작업 방정식(Task Equations)이나 비용함수(Costs)로 포함된다. 등식 및 부등식 제약(Equality and Inequality Constraints)은 동역학, 접촉, 마찰, 관절 한계, 토크 한계 및 기타 실행 가능성 조건을 강제한다.

접촉 스케줄링(Contact Scheduling)은 보행과 전신 최적화를 연결하는 구조적인 역할을 한다. 양발 지지(Double Support)에서는 두 발 모두 반력 렌치(Reaction Wrench)를 제공하고 신체 운동을 구속한다. 한발 지지(Single Support)에서는 한쪽 발이 스윙 작업으로 전환되고 남은 지지 발은 균형, 조작, 나머지 신체의 가속에 필요한 반력을 제공해야 한다.

조작은 추가적인 접촉을 도입할 수 있다. 벽을 미는 손, 도구를 조작하는 손 또는 신체를 지지하는 손은 전신 안정성에 기여하는 환경 반력 렌치(Environmental Reaction Wrench)를 생성할 수 있다. 따라서 제어기는 전체적인 동적 평형(Global Dynamic Equilibrium)을 유지하는 데 다리만 참여한다고 가정하지 않고 발과 손을 공통된 다중 접촉 공식(Multi-Contact Formulation) 안에서 처리할 수 있다.

중심 운동량(Centroidal Momentum)은 보행과 상체 운동 사이를 결합하는 유용한 변수이다. 빠른 팔 또는 물체 운동은 상당한 각운동량(Angular Momentum)을 발생시킬 수 있으며, 이는 몸통, 다리 또는 접촉력을 통해 보상되어야 한다. 운동량 조절(Momentum Regulation)은 조작 중 각각의 신체 부분을 독립적으로 고정하려는 대신 이러한 영향을 전신 수준에서 협조 제어할 수 있도록 한다.

조작하는 물체의 질량이 상당한 경우 무게중심 제어(Center-of-Mass Control)는 물체의 영향도 고려해야 한다. 무거운 페이로드(Payload)를 운반하면 유효 질량 분포(Effective Mass Distribution)가 변화하고 결합 무게중심(Combined Center of Mass)이 무부하 로봇 모델의 CoM에서 벗어날 수 있다. 이러한 영향을 무시하면 손의 기하학적 추종이 정확하더라도 계획된 지지력과 균형 거동이 부정확해질 수 있다.

물체 인식 제어(Object-Aware Control)는 추정된 페이로드 특성을 동역학 모델에 포함하거나 측정된 상호작용 렌치(Interaction Wrench)를 통해 그 영향을 표현할 수 있다. 제어기는 이를 기반으로 페이로드에 따라 자세, 발 하중, 팔 구성을 조정할 수 있다. 이는 물체에서 발생하는 힘이 휴머노이드 자체의 동역학적 여유에 비해 큰 들어 올리기, 운반, 밀기, 당기기 작업에서 특히 중요하다.

여유도(Redundancy)는 보행을 포기하지 않으면서 신체 전체가 조작을 지원할 수 있도록 한다. 손 목표가 팔의 작업 공간 경계(Workspace Boundary)에 접근하면 몸통을 회전시키거나 골반을 이동시키거나 로봇이 추가적인 보행 스텝을 수행할 수 있다. 따라서 전신 협조 제어는 팔 관절만을 사용하는 경우보다 실질적인 조작 작업 공간(Manipulation Workspace)을 확장한다.

이러한 능력은 이동 중 뻗기(Mobile Reaching)도 지원한다. 조작을 시작하기 전에 내비게이션 시스템(Navigation System)이 골반을 정확히 하나의 특정 자세에 배치하도록 요구하는 대신, 제어기는 베이스 운동과 손 운동이 함께 진행되도록 할 수 있다. 접촉 실행 가능성, 균형, 충돌 회피, 작업 우선순위가 만족되는 한 로봇은 팔을 뻗으면서 계속 보행할 수 있다.

양손 조작(Bimanual Manipulation)은 양손이 동일한 물체의 자세를 구속할 수 있기 때문에 결합도를 더욱 증가시킨다. 이 경우 양손 사이의 상대 형상(Relative Hand Geometry), 물체 자세, 내부 파지력(Internal Grasp Forces), 몸통 방향, 하체 균형을 동시에 협조 제어해야 한다. 지나치게 강성인 독립적 손 명령은 시스템을 과도하게 구속할 수 있으므로 순응적(Compliant) 또는 물체 중심 작업 표현(Object-Centered Task Representation)이 유용하다.

힘 상호작용(Force Interaction)은 보행 요구조건에도 영향을 준다. 휴머노이드가 무거운 물체를 밀 때 손을 통해 전달되는 반력은 궁극적으로 발의 지면 반력(Ground Reaction Forces)에 의해 균형을 이루어야 한다. 따라서 손과 발 접촉 모두의 마찰 한계(Friction Limits)가 사용할 수 있는 최대 밀기 힘을 제한하며, WBC는 실행 가능한 접촉 조건을 유지하면서 필요한 렌치를 분배할 수 있다.

작업 우선순위(Task Priorities)는 보행-조작 시퀀스(Loco-Manipulation Sequence) 전체에서 지속적으로 변화할 수 있다. 보행 스텝 중에는 스윙 발의 지면 여유(Swing-Foot Clearance)와 지지 안정성이 일시적으로 손의 정확도보다 우선할 수 있다. 삽입(Insertion)이나 조립(Assembly) 과정에서는 정밀한 말단장치 운동이 더 중요해지는 반면 하체는 거의 정지된 지지 구성을 유지할 수 있다. 부드러운 우선순위 전환(Smooth Priority Transition)은 가속도나 토크의 급격한 변화를 방지한다.

팔, 다리, 몸통, 물체, 환경의 운동이 동시에 발생하기 때문에 충돌 제약(Collision Constraints)은 특히 중요하다. 제어기는 자기 충돌(Self-Collision)을 방지하면서 주변 구조물과의 안전거리도 유지해야 한다. 거리 기반 부등식(Distance-Based Inequalities)은 위험한 상대 운동을 제한하고 여유 자유도를 이용해 주요 조작이나 보행 작업을 불필요하게 중단하지 않으면서 신체의 움직임을 다른 방향으로 유도할 수 있다.

액추에이터 한계(Actuator Limits)는 또 다른 결합 요인을 제공한다. 페이로드를 운반하면 팔의 토크 요구량이 증가하지만 이를 보상하는 몸통과 다리의 운동 역시 하체 부하를 증가시킬 수 있다. 동역학적으로 실행 가능한 WBC는 이러한 한계를 통합적으로 고려하여 기구학적으로는 유효하지만 하나 이상의 관절이 토크, 속도, 가속도 또는 열적 능력(Thermal Capability)을 초과하여 실행할 수 없는 운동을 방지한다.

상태 추정(State Estimation)은 전체 상호작용에 대한 일관된 정보를 제공해야 한다. 부유 베이스 자세 및 속도, 관절 상태, 발 접촉, 손의 힘, 물체 상호작용, 필요한 경우 페이로드 추정값을 로봇 모델과 동기화해야 한다. 접촉 또는 베이스 추정 오차는 조작 정확도와 균형 모두에 직접 전파될 수 있으므로 신뢰성 높은 상태 추정은 통합 제어(Unified Control)의 핵심 요소이다.

실시간 실행(Real-Time Execution)을 위해서는 지지 접촉, 조작 목표, 제약 조건이 변화함에 따라 최적화 문제도 적응해야 한다. 기구학, 동역학, 작업 자코비안(Task Jacobians), 접촉 모델, 렌치 한계, 충돌 조건을 각 제어 주기마다 갱신해야 한다. 웜 스타팅(Warm Starting)과 효율적인 수치 해석 방법(Numerical Methods)은 활성 작업의 수와 종류가 변화할 때도 결정론적 실행(Deterministic Execution)을 유지하는 데 도움을 준다.

실용적인 아키텍처는 계획(Planning)과 제어(Control) 사이의 분리도 유지한다. 상위 수준 모듈은 어디로 걸어갈 것인지, 어떤 물체를 조작할 것인지, 어떤 작업 순서를 수행할 것인지를 결정한다. WBC는 이러한 플래너를 대체하지 않는다. 대신 동시에 전달되는 여러 기준값을 전체 휴머노이드 신체를 위한 하나의 동역학적으로 일관된 명령으로 변환하는 실시간 물리적 협조 계층(Real-Time Physical Coordination Layer)의 역할을 수행한다.

따라서 동시 보행-조작 WBC(Simultaneous Loco-Manipulation WBC)는 특화된 휴머노이드 행동에서 통합 물리 자율성(Integrated Physical Autonomy)으로 발전하는 중요한 전환점을 나타낸다. 보행, 뻗기, 운반, 밀기, 상호작용은 더 이상 서로 분리된 모드가 아니라 공유된 전신 프레임워크를 통해 처리되는 작업 목표와 접촉 조건의 서로 다른 조합이 된다. 이를 통해 휴머노이드는 자신의 전체 신체를 하나의 협조된 기계 시스템(Coordinated Mechanical System)으로 활용할 수 있다.

전체 장의 흐름에서 이러한 공식은 작업 공간 계층 구조(Task-Space Hierarchy), 운용 공간 제어(Operational Space Control), 계층적 이차 계획법(Hierarchical QP), 접촉 렌치 모델링(Contact-Wrench Modeling)을 직접적인 기반으로 한다. 또한 휴머노이드 WBC에서 이후 다루게 되는 순응 관절 토크 제어(Compliant Joint Torque Control), 양손 작업 실행(Bimanual Execution), 고주파 실시간 최적화(High-Frequency Real-Time Optimization), 안전 제약(Safety Constraints), 시뮬레이션-실환경 검증(Simulation-to-Real Validation)에 필요한 통합 제어 기반을 확립한다.

##  

## 05.06. Torque Control for Compliant Humanoid Joints [w/Code]

![](images/image6.png){width="7.268055555555556in" height="7.268055555555556in"}

Torque control for compliant humanoid joints provides the low-level physical interface through which whole-body control can regulate motion, force, and interaction with the environment. Instead of commanding only joint positions, a torque-controlled humanoid directly regulates actuator effort, allowing the mechanical response of each joint to be shaped according to task dynamics, contact conditions, and desired compliance.

This capability is important because humanoids operate through frequent and uncertain physical contact. Feet interact with the ground, hands manipulate objects, and the body may encounter people or surrounding structures. A purely stiff position controller can generate large forces when model errors or unexpected contacts occur, whereas compliant torque control allows the robot to absorb deviations while maintaining controlled behavior.

The commanded joint torque can be interpreted as the actuator contribution required by the whole-body equations of motion. A WBC solver may compute desired generalized accelerations and contact forces and then determine actuator torques consistent with rigid-body dynamics. These commands are passed to high-bandwidth joint controllers that regulate motor current or another actuator quantity closely related to generated torque.

Accurate torque production depends on the actuator architecture. Electric motors commonly generate torque approximately proportional to motor current, but transmissions, friction, elasticity, temperature, and nonlinear effects modify the relationship observed at the output joint. Consequently, practical torque control requires calibration and compensation rather than assuming that a requested motor current always produces an exact joint torque.

Joint torque sensing can be implemented through direct torque sensors, strain-based measurements, series elastic elements, motor-current estimation, or combinations of these methods. Direct measurements improve interaction-force observability, while current-based estimates can reduce hardware complexity. The appropriate design depends on actuator transmission, required bandwidth, mechanical compliance, cost, and safety requirements.

Series elastic actuation introduces a compliant element between the motor and load so that joint torque can be estimated from elastic deformation. The spring also provides mechanical energy storage and impact tolerance, which can be beneficial during contact. However, elasticity introduces additional dynamics that must be considered when designing torque bandwidth, damping, position regulation, and whole-body control.

Quasi-direct-drive actuators use relatively low transmission ratios and high-torque motors to reduce reflected inertia and improve backdrivability. This architecture can produce responsive force control because external loads are less isolated from the motor by a high-ratio transmission. The tradeoff involves motor size, thermal management, current demand, packaging, and the torque density required for humanoid joints.

A common compliant joint strategy combines feedforward torque with impedance feedback. The feedforward component compensates for predicted dynamic loads, while stiffness and damping terms regulate deviation from a desired joint state. Conceptually, the command can be expressed as a desired model-based torque plus proportional position correction and derivative velocity correction.

Joint impedance determines how strongly the actuator resists displacement from its reference. High stiffness produces accurate position tracking but increases sensitivity to unexpected contact and modeling error. Lower stiffness permits larger deviations and softer interaction but may reduce tracking precision. Humanoid control therefore adjusts stiffness and damping according to the physical requirements of each task.

Task-space impedance extends this concept from individual joints to meaningful Cartesian coordinates. A hand can be commanded to behave like a virtual spring and damper around a desired pose, while joint torques are generated through the corresponding Jacobian. This allows the end effector to yield naturally when interacting with objects even though many arm and torso joints participate in the motion.

Compliance must be coordinated across the whole body rather than configured independently for each actuator. During standing, insufficient lower-body stiffness may reduce posture stability, while excessive stiffness can transmit impacts and amplify contact errors. During manipulation, the arms may require softer interaction while the stance legs preserve stronger support, producing a task-dependent distribution of mechanical behavior.

Torque-controlled WBC provides a systematic way to coordinate these requirements. The optimization can compute torques and contact forces while satisfying floating-base dynamics, stance constraints, friction limits, joint bounds, and task priorities. Desired compliance can then be incorporated through task feedback or lower-level impedance loops without violating the physical consistency of the complete humanoid.

Gravity compensation is an important component of compliant control. If gravitational loads are accurately predicted and compensated through feedforward torque, feedback gains do not need to remain artificially high simply to hold the robot against gravity. The joints can therefore exhibit lower apparent stiffness while maintaining posture, improving backdrivability and physical interaction.

Coriolis, centrifugal, and inertial effects become increasingly important during dynamic motion. Feedforward inverse dynamics can compensate for these effects so that feedback primarily handles tracking error and uncertainty. The quality of this compensation depends on the robot model, state estimates, payload information, and actuator characteristics, requiring robust feedback even when sophisticated model-based control is available.

External disturbance rejection must be balanced against compliance. A controller that rejects every displacement aggressively behaves rigidly, while one that yields excessively may fail to preserve balance or task accuracy. Appropriate impedance therefore depends on whether the disturbance should be resisted, absorbed, or used as information about environmental interaction.

Contact transitions require particular care in torque-controlled humanoids. When a foot touches the ground or a hand contacts an object, interaction force can rise rapidly. Abrupt torque commands or high stiffness can produce impact peaks and oscillation. Smooth force references, damping, contact detection, trajectory adaptation, and controlled stiffness transitions help reduce these effects.

Torque limits must remain explicit throughout the control stack. WBC should not request actuator effort beyond continuous or peak capabilities, and the low-level controller should independently enforce safe bounds. Velocity, temperature, current, voltage, gearbox loading, and mechanical stress can further restrict the torque available at a particular operating condition.

Rate limits on commanded torque are also useful because a numerically feasible torque step may still excite structural vibration or actuator dynamics. Limiting torque derivatives and filtering references can improve physical behavior, although excessive filtering introduces delay. The design must balance smoothness with the bandwidth required for balance recovery and dynamic contact regulation.

Friction compensation presents another practical challenge. Static friction, Coulomb friction, viscous effects, gearbox losses, and transmission hysteresis can prevent small commanded torques from appearing accurately at the joint. Model-based compensation can improve performance, but overly aggressive cancellation may destabilize interaction, particularly when friction parameters vary with temperature or wear.

Sensor quality and synchronization strongly influence torque-control performance. Joint position, velocity, torque, motor current, inertial measurements, and force-torque data should correspond to a consistent control instant. Noise in velocity or torque signals can be amplified by high-bandwidth feedback, requiring carefully designed filtering that preserves sufficient phase margin for stable control.

Communication and computation latency are equally important. A whole-body optimizer may calculate physically correct torques, but delayed delivery changes the state to which those commands apply. Deterministic real-time execution, synchronized sensing, bounded communication delay, and high-rate actuator loops are therefore fundamental architectural requirements for stable torque-controlled humanoid systems.

Safety monitoring should remain independent of nominal torque regulation. Hardware or low-level software can detect excessive current, torque, velocity, position error, temperature, communication loss, or sensor inconsistency and transition the actuator toward a safe state. Such protection is necessary because optimization-level constraints alone cannot cover every hardware fault or timing failure.

Compliant torque control also supports safer human-robot interaction by reducing the mechanical severity of unintended contact. However, compliance alone does not guarantee safety. Effective protection requires appropriate force limits, collision detection, speed regulation, mechanical design, supervisory logic, and task-dependent constraints coordinated with the whole-body controller.

Within humanoid WBC, torque control therefore forms the execution layer that converts dynamically consistent optimization results into physical joint behavior. Accurate torque generation provides authority, while impedance and compliance determine how that authority responds to uncertainty and contact. Together they allow the humanoid to remain stable and precise without behaving as an unnecessarily rigid mechanical structure.

This torque-control foundation naturally supports subsequent bimanual WBC, where both arms must coordinate forces and motion while the lower body preserves balance. It also motivates high-frequency real-time optimization, explicit safety constraints, and simulation-to-real validation, because compliant whole-body behavior depends on the complete chain from dynamic modeling and optimization to sensing, communication, and actuator execution.

순응형 휴머노이드 관절(Compliant Humanoid Joints)을 위한 토크 제어(Torque Control)는 전신 제어(Whole-Body Control, WBC)가 운동, 힘, 환경과의 상호작용을 조절할 수 있도록 하는 하위 수준의 물리적 인터페이스(Physical Interface)를 제공한다. 관절 위치만을 명령하는 대신 토크 제어형 휴머노이드(Torque-Controlled Humanoid)는 액추에이터 출력(Actuator Effort)을 직접 조절함으로써 작업 동역학, 접촉 조건, 원하는 순응성(Compliance)에 따라 각 관절의 기계적 반응을 조정할 수 있다.

이러한 능력은 휴머노이드가 빈번하고 불확실한 물리적 접촉(Physical Contact)을 통해 동작하기 때문에 중요하다. 발은 지면과 상호작용하고 손은 물체를 조작하며 신체는 사람이나 주변 구조물과 접촉할 수 있다. 지나치게 강성인 위치 제어기(Position Controller)는 모델 오차나 예상하지 못한 접촉이 발생했을 때 큰 힘을 생성할 수 있지만, 순응 토크 제어(Compliant Torque Control)는 제어된 거동을 유지하면서 로봇이 이러한 편차를 흡수하도록 한다.

명령 관절 토크(Commanded Joint Torque)는 전신 운동 방정식(Whole-Body Equations of Motion)을 만족하기 위해 필요한 액추에이터의 기여분으로 해석할 수 있다. WBC 솔버(Solver)는 원하는 일반화 가속도(Generalized Acceleration)와 접촉력(Contact Force)을 계산한 다음 강체 동역학(Rigid-Body Dynamics)과 일관된 액추에이터 토크를 결정할 수 있다. 이러한 명령은 모터 전류 또는 생성 토크와 밀접하게 관련된 다른 액추에이터 물리량을 조절하는 고대역폭 관절 제어기(High-Bandwidth Joint Controller)로 전달된다.

정확한 토크 생성은 액추에이터 아키텍처(Actuator Architecture)에 따라 달라진다. 전기 모터(Electric Motor)는 일반적으로 모터 전류에 대략 비례하는 토크를 생성하지만 변속기(Transmission), 마찰(Friction), 탄성(Elasticity), 온도(Temperature), 비선형 효과(Nonlinear Effects)는 출력 관절에서 관측되는 관계를 변화시킨다. 따라서 실제 토크 제어에서는 요청된 모터 전류가 항상 정확한 관절 토크를 생성한다고 가정하는 대신 보정(Calibration)과 보상(Compensation)이 필요하다.

관절 토크 감지(Joint Torque Sensing)는 직접 토크 센서(Direct Torque Sensor), 변형률 기반 측정(Strain-Based Measurement), 직렬 탄성 요소(Series Elastic Element), 모터 전류 추정(Motor-Current Estimation) 또는 이러한 방법의 조합을 통해 구현할 수 있다. 직접 측정은 상호작용 힘의 관측 가능성(Interaction-Force Observability)을 향상시키며, 전류 기반 추정은 하드웨어 복잡도를 줄일 수 있다. 적절한 설계는 액추에이터 변속 구조, 요구 대역폭, 기계적 순응성, 비용, 안전 요구조건에 따라 결정된다.

직렬 탄성 구동(Series Elastic Actuation)은 모터와 부하 사이에 순응 요소(Compliant Element)를 배치하여 탄성 변형으로부터 관절 토크를 추정할 수 있도록 한다. 스프링은 기계적 에너지 저장(Mechanical Energy Storage)과 충격 허용 능력(Impact Tolerance)도 제공하므로 접촉 상황에서 유용할 수 있다. 그러나 탄성은 추가적인 동역학을 발생시키므로 토크 대역폭, 감쇠(Damping), 위치 조절, 전신 제어를 설계할 때 이를 고려해야 한다.

준직접 구동 액추에이터(Quasi-Direct-Drive Actuator)는 비교적 낮은 감속비와 고토크 모터를 사용하여 반사 관성(Reflected Inertia)을 감소시키고 역구동성(Backdrivability)을 향상시킨다. 외부 부하가 높은 감속비의 변속기에 의해 모터로부터 크게 분리되지 않기 때문에 반응성이 높은 힘 제어(Force Control)를 구현할 수 있다. 반면 모터 크기, 열 관리(Thermal Management), 전류 요구량, 패키징, 휴머노이드 관절에 필요한 토크 밀도(Torque Density) 사이의 절충이 필요하다.

일반적인 순응 관절 제어 전략은 피드포워드 토크(Feedforward Torque)와 임피던스 피드백(Impedance Feedback)을 결합한다. 피드포워드 성분은 예측된 동역학적 부하를 보상하며, 강성(Stiffness)과 감쇠 항은 원하는 관절 상태로부터 발생하는 편차를 조절한다. 개념적으로 명령은 원하는 모델 기반 토크(Model-Based Torque)에 비례 위치 보정(Proportional Position Correction)과 미분 속도 보정(Derivative Velocity Correction)을 더한 형태로 표현할 수 있다.

관절 임피던스(Joint Impedance)는 액추에이터가 기준 상태로부터의 변위에 얼마나 강하게 저항하는지를 결정한다. 높은 강성은 정확한 위치 추종을 제공하지만 예상하지 못한 접촉과 모델링 오차에 대한 민감도를 증가시킨다. 낮은 강성은 더 큰 편차와 부드러운 상호작용을 허용하지만 추종 정밀도를 감소시킬 수 있다. 따라서 휴머노이드 제어에서는 각 작업의 물리적 요구조건에 따라 강성과 감쇠를 조절한다.

작업 공간 임피던스(Task-Space Impedance)는 이러한 개념을 개별 관절에서 물리적으로 의미 있는 데카르트 좌표(Cartesian Coordinates)로 확장한다. 손은 원하는 자세 주변에서 가상 스프링 및 댐퍼(Virtual Spring and Damper)처럼 동작하도록 명령할 수 있으며, 해당 자코비안(Jacobian)을 통해 관절 토크가 생성된다. 이를 통해 여러 팔과 몸통 관절이 운동에 참여하더라도 말단장치(End Effector)가 물체와 상호작용할 때 자연스럽게 순응하도록 만들 수 있다.

순응성(Compliance)은 각 액추에이터에 독립적으로 설정하는 대신 전신에 걸쳐 협조되어야 한다. 정지 상태(Standing)에서 하체 강성이 부족하면 자세 안정성이 감소할 수 있으며, 지나치게 높은 강성은 충격을 전달하고 접촉 오차를 증폭시킬 수 있다. 조작 중에는 팔에 더 부드러운 상호작용이 요구되는 반면 지지 다리는 더 강한 지지 특성을 유지해야 할 수 있으므로 작업에 따라 기계적 거동을 다르게 분배해야 한다.

토크 제어형 WBC(Torque-Controlled WBC)는 이러한 요구조건을 체계적으로 협조하기 위한 방법을 제공한다. 최적화(Optimization)는 부유 베이스 동역학(Floating-Base Dynamics), 지지 접촉 제약(Stance Constraints), 마찰 한계(Friction Limits), 관절 제한(Joint Bounds), 작업 우선순위(Task Priorities)를 만족하면서 토크와 접촉력을 계산할 수 있다. 이후 원하는 순응성을 작업 피드백이나 하위 수준 임피던스 루프(Impedance Loop)에 포함하면서 전체 휴머노이드의 물리적 일관성을 유지할 수 있다.

중력 보상(Gravity Compensation)은 순응 제어의 중요한 구성 요소이다. 중력 부하를 정확하게 예측하고 피드포워드 토크를 통해 보상할 수 있다면 로봇을 중력에 대항하여 유지하기 위해 피드백 게인을 불필요하게 높게 설정할 필요가 없다. 따라서 관절은 자세를 유지하면서도 더 낮은 겉보기 강성(Apparent Stiffness)을 나타낼 수 있으며, 역구동성과 물리적 상호작용 성능을 향상시킬 수 있다.

코리올리 효과(Coriolis Effects), 원심 효과(Centrifugal Effects), 관성 효과(Inertial Effects)는 동적 운동에서 더욱 중요해진다. 피드포워드 역동역학(Feedforward Inverse Dynamics)을 이용하여 이러한 효과를 보상하면 피드백은 주로 추종 오차와 불확실성을 처리할 수 있다. 이러한 보상의 품질은 로봇 모델, 상태 추정값(State Estimates), 페이로드 정보(Payload Information), 액추에이터 특성에 따라 달라지므로 정교한 모델 기반 제어에서도 강건한 피드백(Robust Feedback)이 필요하다.

외란 제거(Disturbance Rejection)는 순응성과 균형을 이루어야 한다. 모든 변위를 공격적으로 제거하는 제어기는 강체처럼 동작하지만 지나치게 순응하는 제어기는 균형이나 작업 정확도를 유지하지 못할 수 있다. 따라서 적절한 임피던스는 외란을 저항해야 하는지, 흡수해야 하는지, 또는 환경 상호작용에 대한 정보로 활용해야 하는지에 따라 달라진다.

토크 제어형 휴머노이드에서는 접촉 전환(Contact Transition)을 특히 신중하게 처리해야 한다. 발이 지면에 닿거나 손이 물체와 접촉하면 상호작용 힘이 빠르게 증가할 수 있다. 급격한 토크 명령이나 높은 강성은 충격 피크(Impact Peak)와 진동(Oscillation)을 발생시킬 수 있다. 부드러운 힘 기준(Smooth Force Reference), 감쇠, 접촉 감지(Contact Detection), 궤적 적응(Trajectory Adaptation), 제어된 강성 전환을 통해 이러한 영향을 감소시킬 수 있다.

토크 제한(Torque Limits)은 전체 제어 스택(Control Stack)에 걸쳐 명시적으로 유지되어야 한다. WBC는 액추에이터의 연속 또는 최대 성능을 초과하는 출력을 요구해서는 안 되며, 하위 수준 제어기 역시 독립적으로 안전 한계를 적용해야 한다. 속도, 온도, 전류, 전압, 기어박스 부하(Gearbox Loading), 기계적 응력(Mechanical Stress)은 특정 운전 조건에서 사용 가능한 토크를 추가로 제한할 수 있다.

명령 토크의 변화율 제한(Torque Rate Limits) 역시 유용하다. 수치적으로 실행 가능한 토크의 급격한 변화도 구조적 진동이나 액추에이터 동역학을 자극할 수 있기 때문이다. 토크 미분값을 제한하고 기준값을 필터링하면 물리적 거동을 개선할 수 있지만 과도한 필터링은 지연(Delay)을 발생시킨다. 따라서 부드러운 동작과 균형 복구 및 동적 접촉 조절에 필요한 대역폭 사이에서 적절한 균형이 필요하다.

마찰 보상(Friction Compensation)은 또 다른 실용적인 과제를 제공한다. 정지 마찰(Static Friction), 쿨롱 마찰(Coulomb Friction), 점성 효과(Viscous Effects), 기어박스 손실, 변속기 히스테리시스(Transmission Hysteresis)는 작은 명령 토크가 관절에서 정확하게 나타나는 것을 방해할 수 있다. 모델 기반 보상은 성능을 향상시킬 수 있지만 지나치게 공격적인 마찰 상쇄는 특히 마찰 파라미터가 온도나 마모에 따라 변화할 때 상호작용을 불안정하게 만들 수 있다.

센서 품질과 동기화(Synchronization)는 토크 제어 성능에 큰 영향을 준다. 관절 위치, 속도, 토크, 모터 전류, 관성 측정(Inertial Measurements), 힘-토크 데이터(Force-Torque Data)는 일관된 제어 시점(Control Instant)에 대응해야 한다. 속도 또는 토크 신호의 잡음은 고대역폭 피드백에 의해 증폭될 수 있으므로 안정적인 제어에 필요한 충분한 위상 여유(Phase Margin)를 보존하면서 신중하게 설계된 필터링이 필요하다.

통신 및 계산 지연(Communication and Computation Latency)도 마찬가지로 중요하다. 전신 최적화기가 물리적으로 정확한 토크를 계산하더라도 전달이 지연되면 해당 명령이 적용되는 로봇 상태가 달라진다. 따라서 결정론적 실시간 실행(Deterministic Real-Time Execution), 동기화된 센싱(Synchronized Sensing), 제한된 통신 지연(Bounded Communication Delay), 고주파 액추에이터 루프(High-Rate Actuator Loops)는 안정적인 토크 제어형 휴머노이드 시스템의 핵심 아키텍처 요구사항이다.

안전 모니터링(Safety Monitoring)은 정상적인 토크 조절과 독립적으로 유지되어야 한다. 하드웨어 또는 하위 수준 소프트웨어는 과도한 전류, 토크, 속도, 위치 오차, 온도, 통신 손실, 센서 불일치 등을 감지하여 액추에이터를 안전 상태(Safe State)로 전환할 수 있어야 한다. 최적화 수준의 제약만으로는 모든 하드웨어 고장이나 타이밍 오류를 처리할 수 없기 때문에 이러한 보호 계층이 필요하다.

순응 토크 제어는 의도하지 않은 접촉의 기계적 충격을 줄임으로써 더욱 안전한 인간-로봇 상호작용(Human-Robot Interaction)을 지원한다. 그러나 순응성 자체가 안전을 보장하는 것은 아니다. 효과적인 보호를 위해서는 적절한 힘 제한(Force Limits), 충돌 감지(Collision Detection), 속도 조절(Speed Regulation), 기계적 설계(Mechanical Design), 감독 로직(Supervisory Logic), 작업 의존적 제약(Task-Dependent Constraints)이 전신 제어기와 함께 협조되어야 한다.

따라서 휴머노이드 WBC에서 토크 제어는 동역학적으로 일관된 최적화 결과를 실제 관절 거동으로 변환하는 실행 계층(Execution Layer)을 형성한다. 정확한 토크 생성은 필요한 제어 권한(Control Authority)을 제공하며, 임피던스와 순응성은 이러한 제어력이 불확실성과 접촉에 어떻게 반응할지를 결정한다. 이들의 결합을 통해 휴머노이드는 불필요하게 강성인 기계 구조처럼 동작하지 않으면서 안정성과 정밀성을 유지할 수 있다.

이러한 토크 제어 기반은 이후의 양손 전신 제어(Bimanual WBC)를 자연스럽게 지원한다. 양팔이 힘과 운동을 협조하는 동안 하체는 균형을 유지해야 하기 때문이다. 또한 순응적인 전신 거동은 동역학 모델링과 최적화부터 센싱, 통신, 액추에이터 실행까지 전체 제어 체인의 성능에 의존하므로 고주파 실시간 최적화(High-Frequency Real-Time Optimization), 명시적 안전 제약(Explicit Safety Constraints), 시뮬레이션-실환경 검증(Simulation-to-Real Validation)의 필요성으로 이어진다.

##  

## 05.07. WBC for Bimanual Task Execution [w/Code]

![](images/image7.png){width="7.268055555555556in" height="7.268055555555556in"}

Bimanual task execution requires a humanoid to coordinate two arms, two hands, the torso, and the lower body as parts of one mechanically coupled system. Unlike independent single-arm manipulation, the motion or force generated by one hand can directly affect the requirements of the other hand and the balance of the robot. Whole-body control provides the framework needed to resolve these interactions consistently.

A bimanual task can involve two independent end-effectors performing separate actions or both hands cooperating on the same object. Independent tasks may require one hand to hold a component while the other operates a tool. Cooperative tasks include lifting a box, carrying a tray, manipulating a large panel, or assembling an object whose pose is constrained simultaneously by both hands.

Each hand can be represented as a task-space objective using its position and orientation relative to a selected reference frame. The corresponding left- and right-hand Jacobians map generalized humanoid motion into end-effector motion. WBC combines these mappings with torso, center-of-mass, foot, and posture tasks so that both arms are coordinated through the complete kinematic and dynamic model.

Controlling both hands independently in world coordinates is useful when their goals are unrelated, but cooperative manipulation often benefits from an object-centered representation. The controller can define the desired pose of a virtual object frame together with the relative configuration between the two hands. This separates motion of the manipulated object from internal adjustments of the bimanual grasp.

Relative hand constraints are particularly important when both hands rigidly grasp the same object. If the commanded trajectories imply inconsistent separation or orientation between the hands, large internal forces can develop even when individual pose errors appear small. A coordinated formulation therefore preserves grasp geometry while allowing the whole body to move in ways compatible with the shared object constraint.

Bimanual manipulation contains both object motion and internal force components. Forces that contribute to the net object wrench accelerate or support the object, while opposing forces between the hands may change grasp pressure without producing significant object motion. WBC can distinguish these components and regulate internal forces so that the grasp remains secure without unnecessarily stressing the object, hands, or actuators.

The manipulated object becomes part of the coupled dynamic problem when its mass or inertia is significant. Lifting a heavy box changes the effective loading of the arms and shifts the combined center of mass of the robot-object system. Accurate whole-body behavior therefore requires payload mass, center of mass, inertia, or measured interaction forces to be considered when determining posture and support forces.

Balance constraints remain active throughout bimanual execution. Even if both hands accurately track an object trajectory, the task is unsuccessful if the resulting forces destabilize the robot. Center-of-mass behavior, centroidal momentum, foot contacts, and ground reaction wrenches must therefore be coordinated with the arm commands so that manipulation remains compatible with the available support configuration.

The torso provides valuable redundancy between upper- and lower-body objectives. Rotating or translating the torso can extend the effective workspace of both arms, improve manipulability, reduce joint loading, and maintain more favorable elbow configurations. Instead of treating the torso as a rigid platform for the arms, WBC can use it actively while ensuring that the resulting motion does not compromise balance.

Lower-body motion can extend this redundancy further. When an object moves beyond the comfortable reach of the arms, the humanoid may shift its pelvis, change stance, or take a step while maintaining the bimanual grasp. This transforms stationary dual-arm manipulation into whole-body mobile manipulation, where locomotion and object control become parts of the same coordinated behavior.

Hierarchical control determines how these objectives compete. Contact preservation, dynamic consistency, collision avoidance, actuator limits, and balance generally receive high priority. Object pose, relative hand geometry, and grasp forces can then be assigned according to task requirements, while elbow configuration, torso posture, manipulability, and nominal joint posture occupy lower levels when sufficient redundancy remains.

Hierarchical quadratic programming provides a practical implementation mechanism. Generalized accelerations, joint torques, and contact forces can be optimized while equality constraints represent rigid contacts or grasp relationships and inequalities enforce friction, torque, joint, and collision limits. Bimanual tracking objectives can then be solved without violating the physical feasibility of the complete humanoid system.

Compliance is especially important because rigidly commanding two end-effectors can easily overconstrain a real object. Small calibration errors, structural flexibility, inaccurate object geometry, or uncertain contact locations can create conflicting commands. Task-space impedance or compliant grasp control allows the hands to absorb these discrepancies while preserving the overall manipulation objective.

Different compliance can be assigned to different directions. When carrying a tray, for example, vertical support may require relatively strong regulation while selected horizontal or rotational directions can remain softer. During insertion or assembly, one hand may stabilize the object while the other performs a compliant motion that follows environmental constraints rather than enforcing a perfectly rigid trajectory.

Force and torque sensing improves bimanual coordination by revealing how loads are shared between the hands. Wrist force-torque sensors, joint torque sensing, or estimated external forces can identify asymmetric loading and unexpected contact. The controller can use this information to redistribute effort, modify grasp forces, or adjust body posture before one arm approaches its mechanical limits.

Load sharing should account for differences in arm configuration and actuator capability. Equal force distribution is not always optimal because one arm may have a more favorable posture or larger available torque margin. Optimization can distribute the object wrench according to manipulability, joint loading, thermal condition, or distance from actuator limits while preserving the required net effect on the object.

Self-collision avoidance becomes more challenging when both arms operate in overlapping workspace. Hands, forearms, elbows, torso, and the manipulated object can approach one another while each end-effector still satisfies its nominal task. Distance-based constraints allow WBC to exploit redundant joints and torso motion to maintain safe separation without unnecessarily abandoning the bimanual objective.

Environmental collision constraints must also include the object when appropriate. A controller that protects only the robot links may move the carried object into a table, wall, shelf, or person. Object geometry and estimated environment geometry can therefore contribute collision constraints so that the combined robot-object system is treated consistently during whole-body motion generation.

Task transitions require coordinated management. A typical sequence may involve reaching with both hands, establishing contact, increasing grasp force, lifting the object, transporting it, placing it, releasing the grasp, and withdrawing the arms. Each phase changes the active tasks and constraints, and abrupt switching can generate discontinuities in torque or force. Smooth activation functions and reference interpolation reduce these effects.

Grasp establishment is particularly sensitive because the two hands rarely contact an object at exactly the same instant. The first hand may need to remain compliant while the second approaches, after which the controller gradually activates the closed-chain bimanual constraint. Similar logic is required during release so that removing one contact does not unexpectedly transfer the complete load to the other arm.

State estimation must maintain consistent information about both arms, the floating base, contact states, and the manipulated object. Errors in object pose or hand contact geometry can appear as artificial constraint violations and produce unnecessary internal forces. Sensor fusion between vision, proprioception, force sensing, and tactile information can therefore improve robustness during precise cooperative manipulation.

Real-time performance is essential because the controller must repeatedly update dual-arm Jacobians, robot dynamics, contact conditions, object relationships, collision distances, and optimization constraints. Efficient rigid-body computation and warm-started optimization allow the task hierarchy to be resolved at high frequency while maintaining predictable latency as the number of active bimanual constraints changes.

Failure handling should be integrated into the execution architecture. Loss of grasp, unexpected object motion, excessive internal force, actuator saturation, or balance degradation may require the controller to relax a manipulation objective, lower the object, increase support, or transition toward a safe posture. Preserving every hand trajectory is less important than maintaining physical feasibility and robot safety.

Bimanual WBC therefore transforms dual-arm manipulation from two synchronized arm trajectories into a unified whole-body interaction problem. Object motion, relative hand geometry, internal forces, torso redundancy, balance, contact wrenches, actuator limits, and collision constraints are resolved together, allowing the humanoid to use its complete body to perform coordinated manipulation with greater robustness.

Within the broader humanoid WBC progression, bimanual execution builds directly on operational-space control, hierarchical QP, contact-wrench modeling, simultaneous locomotion-manipulation, and compliant torque control. It also creates demanding computational and safety requirements that motivate the subsequent focus on high-frequency real-time WBC optimization, explicit safety constraints, and simulation-to-real validation.

양손 작업 실행(Bimanual Task Execution)은 휴머노이드가 두 팔, 두 손, 몸통, 하체를 하나의 기계적으로 결합된 시스템(Mechanically Coupled System)의 구성 요소로 협조 제어할 것을 요구한다. 독립적인 단일 팔 조작(Single-Arm Manipulation)과 달리 한 손에서 발생하는 운동이나 힘은 다른 손의 요구조건과 로봇의 균형에 직접 영향을 줄 수 있다. 전신 제어(Whole-Body Control, WBC)는 이러한 상호작용을 일관되게 해결하는 데 필요한 프레임워크를 제공한다.

양손 작업(Bimanual Task)은 두 개의 독립적인 말단장치(End-Effector)가 서로 다른 동작을 수행하거나 두 손이 동일한 물체를 협력하여 조작하는 형태로 구성할 수 있다. 독립적인 작업에서는 한 손이 부품을 잡고 다른 손이 도구를 조작할 수 있다. 협력 작업(Cooperative Task)에는 상자 들어 올리기, 트레이 운반, 대형 패널 조작 또는 양손에 의해 동시에 자세가 구속되는 물체의 조립 등이 포함된다.

각 손은 선택된 기준 좌표계(Reference Frame)에 대한 위치와 방향을 이용하여 작업 공간 목표(Task-Space Objective)로 표현할 수 있다. 이에 대응하는 왼손과 오른손의 자코비안(Jacobian)은 일반화 휴머노이드 운동(Generalized Humanoid Motion)을 말단장치 운동으로 매핑한다. WBC는 이러한 매핑을 몸통, 무게중심(Center of Mass, CoM), 발, 자세 작업과 결합하여 전체 기구학 및 동역학 모델을 통해 양팔을 협조 제어한다.

두 손의 목표가 서로 관련되지 않은 경우 월드 좌표계(World Coordinates)에서 각각의 손을 독립적으로 제어하는 것이 유용하지만, 협력 조작(Cooperative Manipulation)에서는 물체 중심 표현(Object-Centered Representation)이 효과적인 경우가 많다. 제어기는 가상 물체 프레임(Virtual Object Frame)의 목표 자세와 함께 양손 사이의 상대 구성을 정의할 수 있다. 이를 통해 조작 물체 자체의 운동과 양손 파지 내부의 조정을 분리할 수 있다.

양손이 동일한 물체를 강체 파지(Rigid Grasp)할 때는 상대 손 제약(Relative Hand Constraints)이 특히 중요하다. 명령 궤적이 양손 사이의 거리나 방향에 대해 서로 일관되지 않으면 개별 손의 자세 오차가 작더라도 큰 내부 힘(Internal Force)이 발생할 수 있다. 따라서 협조 제어 공식은 공유된 물체 제약과 양립할 수 있는 방식으로 전신이 움직이도록 허용하면서 파지 형상(Grasp Geometry)을 유지한다.

양손 조작에는 물체 운동(Object Motion)과 내부 힘 성분(Internal Force Components)이 동시에 존재한다. 순 물체 렌치(Net Object Wrench)에 기여하는 힘은 물체를 가속하거나 지지하지만, 양손 사이에서 서로 반대 방향으로 작용하는 힘은 물체의 운동을 크게 변화시키지 않으면서 파지 압력(Grasp Pressure)을 변화시킬 수 있다. WBC는 이러한 성분을 구분하여 물체, 손, 액추에이터에 불필요한 부하를 가하지 않으면서 안정적인 파지를 유지하도록 내부 힘을 조절할 수 있다.

조작되는 물체의 질량이나 관성(Inertia)이 상당한 경우 해당 물체는 결합된 동역학 문제(Coupled Dynamic Problem)의 일부가 된다. 무거운 상자를 들어 올리면 팔에 작용하는 유효 부하가 변화하고 로봇-물체 시스템(Robot-Object System)의 결합 무게중심(Combined Center of Mass)이 이동한다. 따라서 정확한 전신 거동을 위해서는 자세와 지지력을 결정할 때 페이로드 질량(Payload Mass), 무게중심, 관성 또는 측정된 상호작용 힘을 고려해야 한다.

균형 제약(Balance Constraints)은 양손 작업 실행 전체 과정에서 계속 활성 상태로 유지된다. 양손이 물체 궤적을 정확하게 추종하더라도 그 결과 발생하는 힘이 로봇을 불안정하게 만든다면 작업은 성공적이라고 할 수 없다. 따라서 무게중심 거동, 중심 운동량(Centroidal Momentum), 발 접촉(Foot Contacts), 지면 반력 렌치(Ground Reaction Wrenches)를 팔 명령과 협조하여 조작이 사용 가능한 지지 구성(Support Configuration)과 양립하도록 해야 한다.

몸통(Torso)은 상체와 하체 목표 사이에서 중요한 여유도(Redundancy)를 제공한다. 몸통을 회전하거나 이동시키면 양팔의 유효 작업 공간을 확장하고 조작성(Manipulability)을 향상시키며 관절 부하를 줄이고 팔꿈치를 보다 유리한 구성으로 유지할 수 있다. WBC는 몸통을 팔을 위한 고정 플랫폼으로 취급하는 대신 능동적으로 활용하면서 그 운동이 균형을 손상시키지 않도록 할 수 있다.

하체 운동(Lower-Body Motion)은 이러한 여유도를 더욱 확장할 수 있다. 물체가 팔의 편안한 도달 범위를 벗어나 이동하면 휴머노이드는 양손 파지를 유지하면서 골반을 이동하고 자세를 변경하거나 추가적인 스텝을 수행할 수 있다. 이를 통해 정적인 양팔 조작은 보행과 물체 제어가 하나의 협조된 행동을 구성하는 전신 이동 조작(Whole-Body Mobile Manipulation)으로 확장된다.

계층적 제어(Hierarchical Control)는 이러한 목표들이 서로 경쟁할 때 우선순위를 결정한다. 접촉 유지(Contact Preservation), 동역학적 일관성(Dynamic Consistency), 충돌 회피(Collision Avoidance), 액추에이터 한계(Actuator Limits), 균형은 일반적으로 높은 우선순위를 갖는다. 이후 물체 자세, 상대 손 형상, 파지력(Grasp Force)은 작업 요구조건에 따라 배치되며, 충분한 여유도가 남아 있을 경우 팔꿈치 구성, 몸통 자세, 조작성, 기준 관절 자세(Nominal Joint Posture)가 낮은 수준에 배치된다.

계층적 이차 계획법(Hierarchical Quadratic Programming, HQP)은 이를 구현하기 위한 실용적인 메커니즘을 제공한다. 일반화 가속도(Generalized Accelerations), 관절 토크(Joint Torques), 접촉력(Contact Forces)을 최적화하면서 강체 접촉이나 파지 관계를 등식 제약(Equality Constraints)으로 표현하고, 마찰, 토크, 관절, 충돌 한계를 부등식 제약(Inequality Constraints)으로 적용할 수 있다. 이를 통해 전체 휴머노이드 시스템의 물리적 실행 가능성을 위반하지 않으면서 양손 추종 목표를 해결할 수 있다.

두 개의 말단장치를 강체 방식으로 명령하면 실제 물체를 쉽게 과구속(Overconstrain)할 수 있기 때문에 순응성(Compliance)은 특히 중요하다. 작은 캘리브레이션 오차, 구조적 유연성(Structural Flexibility), 부정확한 물체 형상 또는 불확실한 접촉 위치도 서로 충돌하는 명령을 생성할 수 있다. 작업 공간 임피던스(Task-Space Impedance) 또는 순응 파지 제어(Compliant Grasp Control)는 전체적인 조작 목표를 유지하면서 양손이 이러한 불일치를 흡수할 수 있도록 한다.

서로 다른 방향에 서로 다른 순응성을 적용할 수도 있다. 예를 들어 트레이를 운반할 때 수직 방향 지지는 비교적 강한 조절이 필요할 수 있지만 특정 수평 또는 회전 방향은 더 부드럽게 설정할 수 있다. 삽입(Insertion)이나 조립(Assembly)에서는 한 손이 물체를 안정화하는 동안 다른 손이 완전히 강성인 궤적을 강제하기보다 환경 제약을 따르는 순응 운동(Compliant Motion)을 수행할 수 있다.

힘 및 토크 감지(Force and Torque Sensing)는 양손 사이에서 부하가 어떻게 분배되는지를 파악함으로써 양손 협조 성능을 향상시킨다. 손목 힘-토크 센서(Wrist Force-Torque Sensor), 관절 토크 센싱(Joint Torque Sensing), 외력 추정(External Force Estimation)을 이용하여 비대칭 부하와 예상하지 못한 접촉을 감지할 수 있다. 제어기는 이러한 정보를 사용하여 한쪽 팔이 기계적 한계에 접근하기 전에 힘을 재분배하거나 파지력을 변경하고 신체 자세를 조정할 수 있다.

하중 분배(Load Sharing)는 양팔의 구성과 액추에이터 능력 차이를 고려해야 한다. 한쪽 팔이 더 유리한 자세나 더 큰 토크 여유(Torque Margin)를 가질 수 있기 때문에 동일한 힘을 양팔에 분배하는 것이 항상 최적은 아니다. 최적화는 물체에 필요한 순 효과(Net Effect)를 유지하면서 조작성, 관절 부하, 열적 상태(Thermal Condition), 액추에이터 한계까지의 여유에 따라 물체 렌치를 분배할 수 있다.

양팔이 서로 겹치는 작업 공간에서 동작하기 때문에 자기 충돌 회피(Self-Collision Avoidance)는 더욱 어려워진다. 각 말단장치가 목표 작업을 만족하고 있더라도 손, 전완(Forearm), 팔꿈치, 몸통, 조작 물체가 서로 가까워질 수 있다. 거리 기반 제약(Distance-Based Constraints)을 이용하면 WBC가 여유 관절과 몸통 운동을 활용하여 양손 작업을 불필요하게 포기하지 않으면서 안전한 이격 거리(Safe Separation)를 유지할 수 있다.

필요한 경우 환경 충돌 제약(Environmental Collision Constraints)에는 조작 물체도 포함해야 한다. 로봇 링크만 보호하는 제어기는 운반 중인 물체를 테이블, 벽, 선반 또는 사람과 충돌시킬 수 있다. 따라서 물체 형상(Object Geometry)과 추정된 환경 형상(Environment Geometry)을 충돌 제약에 포함하여 전신 운동 생성 과정에서 결합된 로봇-물체 시스템을 일관되게 처리할 수 있다.

작업 전환(Task Transition)은 협조된 방식으로 관리해야 한다. 일반적인 시퀀스는 양손 뻗기, 접촉 형성, 파지력 증가, 물체 들어 올리기, 운반, 배치, 파지 해제, 팔 후퇴의 과정으로 구성될 수 있다. 각 단계에서 활성 작업과 제약 조건이 변화하며 갑작스러운 전환은 토크나 힘의 불연속(Discontinuity)을 발생시킬 수 있다. 부드러운 활성화 함수(Smooth Activation Functions)와 기준값 보간(Reference Interpolation)은 이러한 영향을 감소시킨다.

파지 형성(Grasp Establishment)은 양손이 정확히 동일한 순간에 물체와 접촉하는 경우가 드물기 때문에 특히 민감하다. 첫 번째 손은 두 번째 손이 접근하는 동안 순응 상태를 유지해야 할 수 있으며, 이후 제어기는 폐쇄 체인 양손 제약(Closed-Chain Bimanual Constraint)을 점진적으로 활성화한다. 파지 해제 과정에서도 한쪽 접촉을 제거하는 순간 전체 부하가 예상하지 못하게 다른 팔로 전달되지 않도록 유사한 로직이 필요하다.

상태 추정(State Estimation)은 양팔, 부유 베이스(Floating Base), 접촉 상태, 조작 물체에 대한 일관된 정보를 유지해야 한다. 물체 자세 또는 손 접촉 형상의 오차는 인위적인 제약 위반으로 나타나 불필요한 내부 힘을 생성할 수 있다. 따라서 비전(Vision), 고유감각(Proprioception), 힘 센싱(Force Sensing), 촉각 정보(Tactile Information)를 결합하는 센서 융합(Sensor Fusion)은 정밀 협력 조작의 강건성을 향상시킬 수 있다.

제어기는 양팔 자코비안(Dual-Arm Jacobians), 로봇 동역학, 접촉 조건, 물체 관계, 충돌 거리, 최적화 제약을 반복적으로 갱신해야 하므로 실시간 성능(Real-Time Performance)이 필수적이다. 효율적인 강체 계산(Rigid-Body Computation)과 웜 스타트 최적화(Warm-Started Optimization)를 이용하면 활성화된 양손 제약의 수가 변화하더라도 예측 가능한 지연을 유지하면서 높은 주파수에서 작업 계층 구조를 해결할 수 있다.

고장 처리(Failure Handling)는 실행 아키텍처에 통합되어야 한다. 파지 손실(Loss of Grasp), 예상하지 못한 물체 운동, 과도한 내부 힘, 액추에이터 포화(Actuator Saturation), 균형 성능 저하는 제어기가 조작 목표를 완화하거나 물체를 낮추고, 지지력을 증가시키거나, 안전 자세(Safe Posture)로 전환하도록 요구할 수 있다. 모든 손 궤적을 유지하는 것보다 물리적 실행 가능성과 로봇 안전을 보존하는 것이 더 중요하다.

따라서 양손 WBC(Bimanual WBC)는 양팔 조작을 단순히 동기화된 두 개의 팔 궤적에서 하나의 통합된 전신 상호작용 문제(Unified Whole-Body Interaction Problem)로 전환한다. 물체 운동, 상대 손 형상, 내부 힘, 몸통 여유도, 균형, 접촉 렌치(Contact Wrenches), 액추에이터 한계, 충돌 제약을 함께 해결함으로써 휴머노이드는 전체 신체를 활용하여 더욱 강건한 협조 조작(Coordinated Manipulation)을 수행할 수 있다.

전체 휴머노이드 WBC의 흐름에서 양손 작업 실행은 운용 공간 제어(Operational-Space Control), 계층적 이차 계획법(Hierarchical QP), 접촉 렌치 모델링(Contact-Wrench Modeling), 동시 보행-조작(Simultaneous Locomotion-Manipulation), 순응 토크 제어(Compliant Torque Control)를 직접적인 기반으로 한다. 또한 높은 계산 성능과 안전 요구조건을 발생시키므로 이후 다루는 고주파 실시간 WBC 최적화(High-Frequency Real-Time WBC Optimization), 명시적 안전 제약(Explicit Safety Constraints), 시뮬레이션-실환경 검증(Simulation-to-Real Validation)의 필요성으로 이어진다.

##  

## 05.08. WBC Real Time 1kHz Pinocchio Optimization [w/Code]

![](images/image8.png){width="7.268055555555556in" height="7.268055555555556in"}

Real-time whole-body control at 1 kHz requires the complete sensing, model update, optimization, and command-generation pipeline to finish within approximately one millisecond. This timing requirement is demanding for a humanoid because dozens of generalized coordinates, multiple contacts, task Jacobians, rigid-body dynamics, and inequality constraints must be evaluated repeatedly while maintaining deterministic behavior.

A 1 kHz control frequency provides rapid response to contact changes, disturbances, joint motion, and interaction forces. High update rates are particularly valuable for torque-controlled humanoids because delayed corrections can degrade impedance behavior and balance stability. However, nominal frequency alone is insufficient; low timing jitter and bounded worst-case computation are equally important for predictable physical control.

Pinocchio provides efficient algorithms for rigid-body kinematics and dynamics that are well suited to high-frequency WBC implementations. A humanoid model can be represented as a multibody tree containing the floating base, links, joints, inertial properties, and reference frames. The same model supports repeated computation of poses, Jacobians, velocities, accelerations, inertia quantities, and nonlinear dynamic terms.

At the beginning of each control cycle, the controller receives the latest estimated floating-base state, joint positions, joint velocities, contact states, and relevant force measurements. These quantities update the generalized configuration \\(q\\) and velocity \\(\\dot{q}\\). Consistent state timing is essential because even a fast dynamics library cannot compensate for measurements that correspond to significantly different physical instants.

Forward kinematics determines the current configuration of task frames such as feet, hands, pelvis, torso, and sensors. Pinocchio can propagate link transformations through the kinematic tree and provide frame placements needed by WBC. These results form the basis for computing tracking errors between measured task states and references supplied by locomotion, manipulation, or posture planners.

Task Jacobians map generalized velocity into Cartesian motion and therefore appear throughout operational-space and optimization-based WBC. At 1 kHz, repeatedly constructing Jacobians inefficiently can consume a significant fraction of the available computation budget. Reusing intermediate kinematic results and avoiding unnecessary recomputation are therefore important implementation principles.

Acceleration-level tasks additionally require terms associated with Jacobian variation, commonly represented through \\(\\dot{J}\\dot{q}\\). These terms influence accurate Cartesian acceleration tracking during dynamic motion. Efficient computation of frame accelerations and related kinematic quantities allows the controller to construct task equations without relying on expensive numerical differentiation of Jacobians between consecutive cycles.

The generalized inertia matrix \\(M(q)\\) is central to dynamically consistent control. Pinocchio can compute this quantity efficiently using rigid-body algorithms such as the Composite Rigid Body Algorithm. Depending on the WBC formulation, the controller may use the complete matrix, selected blocks, or matrix factorizations rather than repeatedly forming explicit inverses.

Nonlinear effects containing gravity, Coriolis, and centrifugal contributions are also required for inverse-dynamics-based control. Recursive Newton-Euler computations provide an efficient mechanism for obtaining these terms. Combining inertia and nonlinear dynamics with contact Jacobians allows the optimizer to impose the floating-base equations of motion as explicit equality constraints.

The optimization layer converts updated model quantities into a feasible whole-body command. Decision variables may include generalized accelerations, actuator torques, contact forces, or a combined vector containing all three. The selected formulation affects problem size, numerical conditioning, constraint structure, and the amount of computation required during every one-millisecond cycle.

Contact constraints introduce additional rows into the optimization problem. Stance feet may require zero contact acceleration, while friction pyramids, normal-force bounds, center-of-pressure conditions, and wrench limits appear as inequalities. When contacts change during walking or manipulation, the solver structure must adapt without introducing excessive allocation, reconstruction overhead, or discontinuity.

Task objectives are assembled from references for center of mass, momentum, swing feet, hands, torso, posture, and other operational coordinates. Hierarchical WBC may solve several prioritized optimization stages, whereas weighted QP combines selected objectives into one cost function. Strict hierarchy provides clearer priority preservation but can increase computational complexity when several optimization problems must be solved sequentially.

A practical 1 kHz architecture therefore minimizes work that does not need to occur at the servo rate. High-level trajectory generation, perception, global planning, and complex behavior decisions can operate in slower threads. The fast WBC loop receives compact references and concentrates on state-dependent kinematics, dynamics, constraints, optimization, and actuator command generation.

Memory management is as important as arithmetic performance. Dynamic allocation inside the real-time loop can introduce unpredictable latency through heap operations and operating-system behavior. Matrices, vectors, solver workspaces, task buffers, and contact structures should therefore be preallocated whenever possible, with fixed maximum dimensions chosen for the expected humanoid configuration.

Sparse structure can provide substantial computational advantages because many optimization matrices contain predictable blocks and zeros. Dynamics, contact constraints, bounds, and task equations often preserve similar sparsity patterns between cycles. Reusing symbolic factorizations or solver structures can reduce overhead when only numerical values change from one iteration to the next.

Warm starting exploits the fact that robot states and optimal commands usually change continuously between adjacent 1 ms cycles. The previous solution can provide an initial estimate for generalized acceleration, contact forces, torques, or active constraints. For suitable QP solvers, this can significantly reduce the number of iterations required to obtain the next solution.

Explicit matrix inversion should generally be avoided when a linear solve or factorization can produce the required result more efficiently and robustly. This is particularly relevant for inertia matrices, operational-space quantities, and KKT systems. Numerical methods based on Cholesky, LDLT, QR, or other structured factorizations can improve both speed and conditioning.

Numerical scaling becomes critical when optimization variables combine accelerations, torques, forces, and Cartesian errors with very different magnitudes and units. Poor scaling can increase solver iterations or cause inaccurate constraint handling. Normalization, suitable variable scaling, and physically meaningful solver tolerances help maintain stable convergence within the limited real-time computation budget.

The controller must also define behavior for optimization failure or deadline overrun. A solver that occasionally requires several milliseconds can be unacceptable even if its average computation time is low. Practical implementations monitor solve status and elapsed time and may reuse a previous safe command, apply a simplified fallback controller, or transition toward a protected operating mode.

Real-time operating-system configuration contributes directly to deterministic performance. The WBC thread can be assigned real-time scheduling priority and isolated processor resources while noncritical processes are prevented from interfering with its execution. Memory locking, CPU affinity, careful interrupt handling, and avoidance of blocking operations further reduce timing variability.

Communication with actuator controllers must fit within the same timing architecture. Computing torques in 600 microseconds provides little benefit if fieldbus transmission introduces another uncontrolled millisecond of delay. Sensor acquisition, state estimation, WBC computation, command transport, and actuator execution must therefore be designed as one end-to-end latency chain.

Profiling is necessary because theoretical algorithmic efficiency does not guarantee a 1 kHz implementation. Developers should measure execution time for state updates, Pinocchio kinematics, Jacobians, dynamics, constraint assembly, QP solution, and command output separately. Histograms and worst-case timing are more informative than average timing when evaluating real-time feasibility.

Model complexity should also be selected deliberately. Collision geometry, detailed actuator models, and numerous auxiliary frames can increase computation. Some quantities can be evaluated at slower rates or only when relevant, while safety-critical dynamics and contact constraints remain in the high-frequency loop. Multi-rate computation therefore provides a practical compromise between model richness and deterministic execution.

Pinocchio integration benefits from maintaining a clear separation between model data and control logic. Robot descriptions define the kinematic and inertial structure, while runtime data structures store intermediate calculations for the current state. Task modules can consume these model results without independently recomputing the same dynamics, reducing duplicated work and improving software maintainability.

Verification of the 1 kHz loop should include not only numerical correctness but timing behavior under worst-case task configurations. Maximum contact count, bimanual manipulation, collision constraints, near-singular configurations, and rapid task transitions can produce heavier computation than nominal standing. Stress testing these conditions reveals whether the real-time budget remains valid across the intended operating envelope.

A well-optimized WBC pipeline therefore treats Pinocchio, the optimization solver, state estimation, and real-time software architecture as parts of one computational system. Fast rigid-body algorithms alone do not guarantee high-frequency control; deterministic memory usage, solver design, communication latency, task scheduling, numerical conditioning, and fallback behavior collectively determine whether 1 kHz operation is reliable.

Real-time 1 kHz WBC forms the execution infrastructure required by advanced humanoid behaviors such as dynamic locomotion, compliant manipulation, and coordinated bimanual tasks. By updating the complete-body model and constrained optimization at servo rate, the controller can continuously reconcile task objectives with contacts, actuator capability, and rapidly changing robot state.

Within the broader WBC progression, this real-time implementation transforms operational-space control, hierarchical QP, contact modeling, loco-manipulation, compliant torque control, and bimanual coordination from mathematical formulations into executable robot software. It also establishes the computational foundation for explicit safety constraints and systematic simulation-to-real validation under realistic humanoid operating conditions.

1kHz 실시간 전신 제어(Real-Time Whole-Body Control)를 구현하려면 전체 센싱(Sensing), 모델 갱신(Model Update), 최적화(Optimization), 명령 생성(Command Generation) 파이프라인을 약 1밀리초 이내에 완료해야 한다. 이는 수십 개의 일반화 좌표(Generalized Coordinates), 다중 접촉(Multiple Contacts), 작업 자코비안(Task Jacobians), 강체 동역학(Rigid-Body Dynamics), 부등식 제약(Inequality Constraints)을 결정론적 거동(Deterministic Behavior)을 유지하면서 반복적으로 계산해야 하는 휴머노이드에서 매우 까다로운 요구조건이다.

1kHz 제어 주파수(Control Frequency)는 접촉 변화, 외란(Disturbance), 관절 운동, 상호작용 힘(Interaction Forces)에 빠르게 대응할 수 있도록 한다. 높은 갱신 주파수는 지연된 보정이 임피던스 거동(Impedance Behavior)과 균형 안정성(Balance Stability)을 저하시킬 수 있는 토크 제어형 휴머노이드(Torque-Controlled Humanoid)에서 특히 중요하다. 그러나 명목상의 주파수만으로는 충분하지 않으며, 낮은 타이밍 지터(Timing Jitter)와 제한된 최악 조건 계산 시간(Bounded Worst-Case Computation) 역시 예측 가능한 물리 제어를 위해 중요하다.

Pinocchio는 고주파 WBC 구현에 적합한 효율적인 강체 기구학 및 동역학(Rigid-Body Kinematics and Dynamics) 알고리즘을 제공한다. 휴머노이드 모델은 부유 베이스(Floating Base), 링크(Link), 관절(Joint), 관성 특성(Inertial Properties), 기준 프레임(Reference Frames)을 포함하는 다물체 트리(Multibody Tree)로 표현할 수 있다. 동일한 모델을 이용하여 자세, 자코비안, 속도, 가속도, 관성 물리량, 비선형 동역학 항을 반복적으로 계산할 수 있다.

각 제어 주기의 시작에서 제어기는 최신 추정 부유 베이스 상태, 관절 위치, 관절 속도, 접촉 상태(Contact States), 관련 힘 측정값을 수신한다. 이러한 물리량을 이용하여 일반화 구성(Generalized Configuration) \\(q\\)와 속도 \\(\\dot{q}\\)를 갱신한다. 빠른 동역학 라이브러리를 사용하더라도 서로 크게 다른 물리적 시점에 대응하는 측정값은 보상할 수 없기 때문에 일관된 상태 타이밍(Consistent State Timing)이 필수적이다.

순기구학(Forward Kinematics)은 발, 손, 골반, 몸통, 센서와 같은 작업 프레임(Task Frames)의 현재 구성을 결정한다. Pinocchio는 기구학 트리(Kinematic Tree)를 따라 링크 변환(Link Transformations)을 전파하고 WBC에 필요한 프레임 배치(Frame Placements)를 제공할 수 있다. 이러한 결과는 측정된 작업 상태와 보행, 조작 또는 자세 플래너가 제공하는 기준값 사이의 추종 오차(Tracking Errors)를 계산하는 기반이 된다.

작업 자코비안(Task Jacobians)은 일반화 속도를 데카르트 운동(Cartesian Motion)으로 매핑하므로 운용 공간 제어(Operational-Space Control)와 최적화 기반 WBC 전반에서 사용된다. 1kHz 환경에서는 자코비안을 비효율적으로 반복 구성할 경우 사용 가능한 계산 시간의 상당 부분을 소비할 수 있다. 따라서 중간 기구학 계산 결과를 재사용하고 불필요한 재계산을 방지하는 것이 중요한 구현 원칙이다.

가속도 수준 작업(Acceleration-Level Tasks)은 추가적으로 일반적으로 \\(\\dot{J}\\dot{q}\\)로 표현되는 자코비안 변화 관련 항을 필요로 한다. 이러한 항은 동적 운동 중 정확한 데카르트 가속도 추종(Cartesian Acceleration Tracking)에 영향을 준다. 프레임 가속도(Frame Accelerations)와 관련 기구학 물리량을 효율적으로 계산하면 연속된 제어 주기 사이에서 비용이 큰 자코비안 수치 미분(Numerical Differentiation)에 의존하지 않고 작업 방정식을 구성할 수 있다.

일반화 관성 행렬(Generalized Inertia Matrix) \\(M(q)\\)은 동역학적으로 일관된 제어(Dynamically Consistent Control)의 핵심이다. Pinocchio는 복합 강체 알고리즘(Composite Rigid Body Algorithm, CRBA)과 같은 강체 알고리즘을 사용하여 이 물리량을 효율적으로 계산할 수 있다. WBC 공식에 따라 제어기는 명시적인 역행렬을 반복적으로 계산하는 대신 전체 행렬, 선택된 블록 또는 행렬 분해(Matrix Factorizations)를 사용할 수 있다.

중력(Gravity), 코리올리(Coriolis), 원심 효과(Centrifugal Effects)를 포함하는 비선형 효과(Nonlinear Effects) 역시 역동역학 기반 제어(Inverse-Dynamics-Based Control)에 필요하다. 재귀 뉴턴-오일러 계산(Recursive Newton-Euler Computation)은 이러한 항을 효율적으로 구하는 방법을 제공한다. 관성 및 비선형 동역학과 접촉 자코비안(Contact Jacobians)을 결합하면 최적화기가 부유 베이스 운동 방정식(Floating-Base Equations of Motion)을 명시적인 등식 제약(Equality Constraints)으로 적용할 수 있다.

최적화 계층(Optimization Layer)은 갱신된 모델 물리량을 실행 가능한 전신 명령(Feasible Whole-Body Command)으로 변환한다. 결정 변수(Decision Variables)는 일반화 가속도(Generalized Accelerations), 액추에이터 토크(Actuator Torques), 접촉력(Contact Forces) 또는 이 세 가지를 모두 포함하는 결합 벡터로 구성할 수 있다. 선택된 공식은 문제 크기, 수치 조건성(Numerical Conditioning), 제약 구조, 매 1밀리초 주기마다 필요한 계산량에 영향을 준다.

접촉 제약(Contact Constraints)은 최적화 문제에 추가적인 행을 도입한다. 지지 발(Stance Feet)은 접촉 가속도 0을 요구할 수 있으며, 마찰 피라미드(Friction Pyramids), 수직력 제한(Normal-Force Bounds), 압력 중심 조건(Center-of-Pressure Conditions), 렌치 제한(Wrench Limits)은 부등식으로 표현된다. 보행이나 조작 중 접촉이 변화할 때 솔버 구조(Solver Structure)는 과도한 메모리 할당, 문제 재구성 오버헤드 또는 불연속을 발생시키지 않으면서 적응해야 한다.

작업 목표(Task Objectives)는 무게중심(Center of Mass), 운동량(Momentum), 스윙 발(Swing Feet), 손, 몸통, 자세 및 기타 운용 좌표(Operational Coordinates)에 대한 기준값으로 구성된다. 계층적 WBC(Hierarchical WBC)는 여러 우선순위 최적화 단계를 순차적으로 해결할 수 있으며, 가중 이차 계획법(Weighted QP)은 선택된 목표를 하나의 비용함수로 결합한다. 엄격한 계층 구조(Strict Hierarchy)는 우선순위를 보다 명확하게 보존하지만 여러 최적화 문제를 순차적으로 해결해야 하는 경우 계산 복잡도가 증가할 수 있다.

따라서 실용적인 1kHz 아키텍처는 서보 주파수(Servo Rate)에서 수행할 필요가 없는 계산을 최소화한다. 상위 수준 궤적 생성(High-Level Trajectory Generation), 인지(Perception), 전역 계획(Global Planning), 복잡한 행동 결정(Behavior Decisions)은 더 느린 스레드에서 실행할 수 있다. 고속 WBC 루프는 간결한 기준값을 받아 상태 의존적 기구학, 동역학, 제약, 최적화, 액추에이터 명령 생성에 집중한다.

메모리 관리(Memory Management)는 산술 연산 성능만큼 중요하다. 실시간 루프 내부의 동적 메모리 할당(Dynamic Allocation)은 힙 연산(Heap Operations)과 운영체제 동작으로 인해 예측하기 어려운 지연을 발생시킬 수 있다. 따라서 행렬, 벡터, 솔버 작업 공간(Solver Workspaces), 작업 버퍼(Task Buffers), 접촉 구조(Contact Structures)는 가능한 한 사전 할당(Preallocation)해야 하며, 예상되는 휴머노이드 구성에 맞춰 고정된 최대 차원을 설정하는 것이 바람직하다.

희소 구조(Sparse Structure)는 많은 최적화 행렬이 예측 가능한 블록과 영(Zero) 성분을 포함하기 때문에 상당한 계산상의 이점을 제공할 수 있다. 동역학, 접촉 제약, 경계 조건(Bounds), 작업 방정식은 제어 주기 사이에서도 유사한 희소 패턴(Sparsity Patterns)을 유지하는 경우가 많다. 수치값만 변화할 때 기호적 행렬 분해(Symbolic Factorizations) 또는 솔버 구조를 재사용하면 오버헤드를 줄일 수 있다.

웜 스타팅(Warm Starting)은 인접한 1밀리초 제어 주기 사이에서 로봇 상태와 최적 명령이 일반적으로 연속적으로 변화한다는 특성을 활용한다. 이전 해를 일반화 가속도, 접촉력, 토크 또는 활성 제약(Active Constraints)에 대한 초기 추정값으로 사용할 수 있다. 적절한 QP 솔버에서는 이를 통해 다음 해를 구하는 데 필요한 반복 횟수를 크게 줄일 수 있다.

명시적 행렬 역산(Explicit Matrix Inversion)은 선형 방정식 풀이(Linear Solve) 또는 행렬 분해를 통해 필요한 결과를 더 효율적이고 강건하게 얻을 수 있는 경우 일반적으로 피해야 한다. 이는 관성 행렬, 운용 공간 물리량(Operational-Space Quantities), KKT 시스템(Karush-Kuhn-Tucker System)에서 특히 중요하다. 촐레스키(Cholesky), LDLT, QR 또는 기타 구조적 행렬 분해에 기반한 수치 방법은 속도와 수치 조건성을 모두 향상시킬 수 있다.

최적화 변수가 서로 매우 다른 크기와 단위를 갖는 가속도, 토크, 힘, 데카르트 오차를 함께 포함할 때 수치 스케일링(Numerical Scaling)은 매우 중요해진다. 부적절한 스케일링은 솔버 반복 횟수를 증가시키거나 제약 처리 정확도를 저하시킬 수 있다. 정규화(Normalization), 적절한 변수 스케일링(Variable Scaling), 물리적으로 의미 있는 솔버 허용 오차(Solver Tolerances)는 제한된 실시간 계산 예산 안에서 안정적인 수렴을 유지하는 데 도움을 준다.

제어기는 최적화 실패(Optimization Failure) 또는 계산 기한 초과(Deadline Overrun)에 대한 동작도 정의해야 한다. 평균 계산 시간이 짧더라도 솔버가 간헐적으로 수 밀리초를 필요로 한다면 실시간 제어에서는 허용하기 어려울 수 있다. 실제 구현에서는 솔버 상태와 경과 시간을 감시하고 이전의 안전한 명령을 재사용하거나 단순화된 폴백 제어기(Fallback Controller)를 적용하거나 보호 운전 모드(Protected Operating Mode)로 전환할 수 있다.

실시간 운영체제 설정(Real-Time Operating-System Configuration)은 결정론적 성능에 직접적으로 기여한다. WBC 스레드에는 실시간 스케줄링 우선순위(Real-Time Scheduling Priority)와 격리된 프로세서 자원을 할당할 수 있으며 중요하지 않은 프로세스가 실행을 방해하지 않도록 구성할 수 있다. 메모리 잠금(Memory Locking), CPU 친화성(CPU Affinity), 신중한 인터럽트 처리(Interrupt Handling), 블로킹 연산(Blocking Operations) 회피를 통해 타이밍 변동을 더욱 줄일 수 있다.

액추에이터 제어기와의 통신 역시 동일한 타이밍 아키텍처(Timing Architecture) 안에 포함되어야 한다. 600마이크로초 안에 토크를 계산하더라도 필드버스(Fieldbus) 전송에서 제어되지 않은 추가 1밀리초 지연이 발생한다면 얻을 수 있는 이점은 제한적이다. 따라서 센서 획득, 상태 추정, WBC 계산, 명령 전송, 액추에이터 실행을 하나의 종단 간 지연 체인(End-to-End Latency Chain)으로 설계해야 한다.

이론적인 알고리즘 효율성만으로 1kHz 구현이 보장되는 것은 아니므로 프로파일링(Profiling)이 필요하다. 개발자는 상태 갱신, Pinocchio 기구학, 자코비안, 동역학, 제약 구성, QP 풀이, 명령 출력에 필요한 실행 시간을 각각 측정해야 한다. 실시간 실행 가능성을 평가할 때는 평균 실행 시간보다 실행 시간 분포(Histograms)와 최악 조건 타이밍(Worst-Case Timing)이 더 중요한 정보를 제공한다.

모델 복잡도(Model Complexity) 역시 신중하게 선택해야 한다. 충돌 형상(Collision Geometry), 상세 액추에이터 모델, 다수의 보조 프레임(Auxiliary Frames)은 계산량을 증가시킬 수 있다. 일부 물리량은 더 낮은 주파수에서 계산하거나 필요한 경우에만 평가하고, 안전에 중요한 동역학과 접촉 제약은 고주파 루프에 유지할 수 있다. 따라서 다중 주파수 계산(Multi-Rate Computation)은 모델의 풍부함과 결정론적 실행 사이의 실용적인 절충안을 제공한다.

Pinocchio 통합에서는 모델 데이터(Model Data)와 제어 로직(Control Logic)을 명확하게 분리하는 것이 유용하다. 로봇 기술 정보(Robot Description)는 기구학 및 관성 구조를 정의하고, 런타임 데이터 구조(Runtime Data Structures)는 현재 상태에 대한 중간 계산 결과를 저장한다. 작업 모듈(Task Modules)은 동일한 동역학을 독립적으로 재계산하지 않고 이러한 모델 결과를 공유하여 중복 계산을 줄이고 소프트웨어 유지보수성을 향상시킬 수 있다.

1kHz 루프의 검증(Verification)은 수치적 정확성뿐 아니라 최악 조건 작업 구성에서의 타이밍 거동도 포함해야 한다. 최대 접촉 수(Maximum Contact Count), 양손 조작(Bimanual Manipulation), 충돌 제약(Collision Constraints), 특이점에 가까운 구성(Near-Singular Configurations), 빠른 작업 전환(Rapid Task Transitions)은 일반적인 정지 상태보다 많은 계산량을 요구할 수 있다. 이러한 조건을 스트레스 테스트(Stress Testing)하면 목표 운용 범위 전체에서 실시간 계산 예산이 유지되는지 확인할 수 있다.

잘 최적화된 WBC 파이프라인은 Pinocchio, 최적화 솔버(Optimization Solver), 상태 추정(State Estimation), 실시간 소프트웨어 아키텍처(Real-Time Software Architecture)를 하나의 계산 시스템(Computational System)의 구성 요소로 취급한다. 빠른 강체 알고리즘만으로 고주파 제어가 보장되는 것은 아니다. 결정론적 메모리 사용, 솔버 설계, 통신 지연, 작업 스케줄링, 수치 조건성, 폴백 동작이 함께 1kHz 운용의 신뢰성을 결정한다.

1kHz 실시간 WBC는 동적 보행(Dynamic Locomotion), 순응 조작(Compliant Manipulation), 협조 양손 작업(Coordinated Bimanual Tasks)과 같은 고급 휴머노이드 행동에 필요한 실행 인프라(Execution Infrastructure)를 형성한다. 서보 주파수에서 전신 모델과 제약 기반 최적화를 지속적으로 갱신함으로써 제어기는 작업 목표를 접촉 상태, 액추에이터 능력, 빠르게 변화하는 로봇 상태와 실시간으로 조정할 수 있다.

전체 WBC의 발전 흐름에서 이러한 실시간 구현은 운용 공간 제어(Operational-Space Control), 계층적 이차 계획법(Hierarchical QP), 접촉 모델링(Contact Modeling), 보행-조작 통합(Loco-Manipulation), 순응 토크 제어(Compliant Torque Control), 양손 협조(Bimanual Coordination)와 같은 수학적 공식을 실제 실행 가능한 로봇 소프트웨어(Executable Robot Software)로 전환한다. 또한 실제 휴머노이드 운용 조건에서 명시적 안전 제약(Explicit Safety Constraints)과 체계적인 시뮬레이션-실환경 검증(Simulation-to-Real Validation)을 수행하기 위한 계산 기반을 확립한다.

##  

## 05.09. WBC Safety Torque Limit Self Collision [w/Code]

![](images/image9.png){width="7.268055555555556in" height="7.268055555555556in"}

Whole-body control safety must be embedded directly into motion and torque generation rather than treated only as a supervisory function after commands have been computed. A humanoid combines high actuator power, large moving links, multiple contacts, and close interaction with people and objects. WBC must therefore preserve physical feasibility while continuously preventing commands that could damage the robot or its surroundings.

Torque limits form one of the most fundamental safety constraints. Every actuator has continuous and peak torque capabilities determined by motor characteristics, transmission design, power electronics, thermal state, and mechanical strength. The WBC optimizer should explicitly constrain commanded joint torque so that task tracking cannot demand effort beyond the physically available and safely permitted actuator envelope.

A simple torque constraint can be represented as \\(\\tau_{min}\\leq\\tau\\leq\\tau_{max}\\), but practical limits may vary with operating conditions. Available torque can decrease with motor temperature, joint velocity, battery voltage, or sustained loading. Safety-aware WBC can therefore use state-dependent limits rather than assuming that one fixed torque range remains valid throughout every humanoid behavior.

Continuous and peak limits should also be distinguished. Short dynamic actions may safely use temporary peak torque, while sustained manipulation or standing requires operation closer to continuous ratings. Thermal estimators can track accumulated actuator loading and progressively derate available torque before overheating occurs, allowing the optimization layer to adapt motion instead of relying only on emergency shutdown.

Torque-rate constraints provide another protective mechanism. Even when two consecutive torque values are individually within valid bounds, a large instantaneous change can excite structural vibration, drivetrain elasticity, or impact-like behavior. Limiting \\(\\dot{\\tau}\\) or the discrete torque increment between control cycles produces smoother actuator commands and reduces mechanical stress while preserving sufficient bandwidth for disturbance recovery.

Joint position limits must be enforced before mechanical stops are reached. Rather than allowing a joint to move freely until it reaches a hard boundary, WBC can construct position-dependent velocity or acceleration limits that become progressively restrictive near the edge of the safe range. This creates a braking region in which the optimizer redirects motion using the remaining whole-body redundancy.

Velocity and acceleration bounds complement position protection. Excessive joint velocity can increase impact energy and reduce actuator controllability, while aggressive acceleration can generate large inertial loads. Incorporating these quantities as inequality constraints ensures that a kinematically valid task cannot force the humanoid into motion that violates actuator, structural, or operational safety requirements.

Self-collision avoidance is particularly important for humanoids because arms, hands, legs, torso, and head operate in overlapping workspaces. A desired hand trajectory may be geometrically reachable while causing the elbow to strike the torso or one arm to collide with the other. Whole-body safety must therefore reason about the complete robot geometry rather than monitoring only end-effector positions.

Collision models commonly approximate robot links with capsules, spheres, boxes, convex shapes, or simplified meshes. The controller evaluates distances between selected collision pairs and compares them with predefined safety margins. Simplified geometry is often preferred in high-frequency WBC because exact mesh-to-mesh collision calculations may be too expensive for deterministic servo-rate execution.

A minimum-distance constraint can be converted into a velocity- or acceleration-level inequality. As two body geometries approach one another, the controller restricts relative motion in the direction that would reduce separation further. Redundant joints remain free to move tangentially or away from the collision, allowing WBC to preserve the primary task whenever a safe alternative configuration exists.

Safety margins should account for more than geometric contact alone. State-estimation uncertainty, communication latency, structural flexibility, tracking error, and finite braking capability mean that zero geometric clearance is not a suitable control boundary. A larger protective distance provides time for the controller to react before physical collision becomes unavoidable.

Collision constraints can also be prioritized according to severity. Preventing contact between mechanically vulnerable links or protecting the head may require stronger margins than avoiding benign proximity between other structures. However, safety-critical collision constraints should generally remain hard or highly protected so that lower-priority hand tracking or posture objectives cannot override them.

Environmental collision avoidance extends the same principle beyond self-collision. The humanoid may operate near walls, tables, machinery, shelves, tools, or people. Environment geometry from perception or a maintained scene model can generate distance constraints for relevant robot links, allowing the WBC optimizer to modify posture or task motion before an unsafe approach occurs.

Human proximity requires additional consideration because the environment is dynamic and uncertain. Speed and force limits can be reduced as a person approaches the robot, and larger protective margins can compensate for perception latency or motion prediction uncertainty. Such adaptations should complement, rather than replace, independent safety monitoring and hardware-level protective mechanisms.

Contact-force limits are another important component of WBC safety. A dynamically feasible motion may still produce excessive ground reaction force, hand interaction force, or contact moment. Bounding normal forces, tangential forces, and contact wrenches protects the robot, manipulated objects, environmental structures, and potentially nearby people from unnecessarily large interaction loads.

Friction constraints simultaneously contribute to feasibility and safety. If the optimizer requests a contact force outside the available friction region, the foot or hand may slip even though the commanded joint torques remain within limits. Friction-cone or friction-pyramid inequalities therefore prevent the controller from relying on contact forces that cannot be physically maintained.

Balance-related constraints protect the humanoid from configurations that may lead to falling. Depending on the WBC formulation, these can involve center-of-pressure limits, centroidal momentum regulation, support-wrench feasibility, contact-force distribution, or center-of-mass behavior. Safety should be evaluated dynamically because purely geometric support-polygon reasoning is insufficient during fast motion.

Task-space safety boundaries can restrict operational coordinates directly. A hand may be prevented from entering sensitive regions near the face, a foot may have minimum clearance requirements during swing, and the torso may be constrained from excessive inclination. These boundaries translate application-specific safety requirements into forms that can participate directly in whole-body optimization.

Hierarchical QP provides a natural structure for separating safety constraints from performance objectives. Torque limits, collision avoidance, required contacts, and critical stability conditions can occupy protected levels, while hand tracking, posture optimization, gaze, or manipulability are allowed to degrade when necessary. Safety is therefore expressed as priority rather than merely as a large numerical weight.

Hard constraints must nevertheless be selected carefully because conflicting hard requirements can make the optimization infeasible. If collision avoidance, contact preservation, and joint limits cannot all be satisfied simultaneously, the controller requires a predefined recovery strategy. Safety architecture should identify which constraints can be softened, which must never be violated, and which emergency behavior should replace nominal optimization.

Slack variables can provide controlled relaxation for selected noncritical constraints. For example, a desired posture or manipulation trajectory may accept error to preserve torque and collision limits. The penalty associated with each slack variable reflects the cost of violation, enabling the controller to sacrifice task performance systematically instead of failing unpredictably when the requested behavior becomes infeasible.

Solver health is itself a safety consideration in real-time WBC. An optimization result should not be applied blindly if the solver reports infeasibility, numerical failure, excessive residuals, or deadline overrun. The execution layer should validate solution status and command bounds before transmitting torques to the actuators and should maintain a deterministic fallback path when the nominal solution is unavailable.

Fallback control can preserve a previous validated command briefly, reduce commanded motion, increase damping, transition toward a stable posture, or initiate controlled stopping depending on the failure condition. The appropriate response depends on whether the robot is standing, walking, carrying an object, or physically interacting with the environment. A single universal fallback is rarely sufficient for all humanoid states.

Independent low-level protection remains necessary even when WBC contains comprehensive constraints. Motor drives and joint controllers can enforce current, torque, velocity, temperature, and position limits locally. Emergency-stop logic, communication watchdogs, hardware interlocks, and power-stage protection provide additional layers that remain effective if the optimization process or higher-level computer fails.

Safety monitoring should therefore use defense in depth. The WBC layer prevents unsafe commands during normal operation, the real-time execution layer checks numerical and timing validity, joint controllers enforce local actuator limits, and independent supervisory or hardware mechanisms handle severe faults. No single software optimization layer should be treated as the sole protection mechanism.

High-frequency implementation introduces an important computational tradeoff. Evaluating many collision pairs and dynamic safety constraints at 1 kHz can become expensive. Critical nearby collision pairs can be checked at servo rate, while broader environment geometry may be updated more slowly. Conservative bounds and multi-rate processing allow computational efficiency without removing essential protective behavior.

Testing must deliberately target boundary conditions rather than only nominal motion. Validation should include near-joint-limit configurations, torque saturation, close self-collision, reduced friction, unexpected contact, solver infeasibility, sensor delay, communication loss, and thermal derating. These scenarios reveal whether safety constraints remain effective when several protective mechanisms become active simultaneously.

Safety-aware WBC ultimately converts actuator limits, geometric separation, contact feasibility, stability margins, and computational validity into explicit conditions on whole-body motion. The humanoid can then exploit redundancy to continue a task when safe alternatives exist and intentionally sacrifice tracking performance when physical limits require it, rather than pursuing an objective regardless of risk.

Within the humanoid WBC architecture, torque limiting and self-collision avoidance form the protective layer surrounding operational-space tasks, hierarchical optimization, contact control, loco-manipulation, compliant torque execution, bimanual coordination, and 1 kHz computation. This safety structure provides the necessary foundation for systematic simulation-to-real validation before increasingly dynamic whole-body behaviors are deployed on physical hardware.

전신 제어 안전성(Whole-Body Control Safety)은 명령이 계산된 이후에만 적용되는 감독 기능(Supervisory Function)으로 처리하는 것이 아니라 운동 및 토크 생성 과정에 직접 포함되어야 한다. 휴머노이드는 높은 액추에이터 출력(Actuator Power), 큰 운동 링크(Moving Links), 다중 접촉(Multiple Contacts), 사람 및 물체와의 근접 상호작용을 결합한다. 따라서 WBC는 물리적 실행 가능성(Physical Feasibility)을 유지하면서 로봇이나 주변 환경을 손상시킬 수 있는 명령을 지속적으로 방지해야 한다.

토크 제한(Torque Limits)은 가장 기본적인 안전 제약(Safety Constraints) 중 하나이다. 모든 액추에이터는 모터 특성, 변속기 설계(Transmission Design), 전력 전자 장치(Power Electronics), 열적 상태(Thermal State), 기계적 강도에 의해 결정되는 연속 및 최대 토크 능력(Continuous and Peak Torque Capabilities)을 가진다. WBC 최적화기(Optimizer)는 작업 추종이 물리적으로 사용 가능하고 안전하게 허용된 액추에이터 범위를 초과하는 출력을 요구하지 않도록 명령 관절 토크를 명시적으로 제한해야 한다.

단순한 토크 제약은 \\(\\tau_{min}\\leq\\tau\\leq\\tau_{max}\\)로 표현할 수 있지만 실제 한계는 운전 조건에 따라 달라질 수 있다. 사용 가능한 토크는 모터 온도, 관절 속도, 배터리 전압 또는 지속적인 부하에 따라 감소할 수 있다. 따라서 안전성을 고려하는 WBC(Safety-Aware WBC)는 모든 휴머노이드 행동에서 하나의 고정된 토크 범위가 항상 유효하다고 가정하는 대신 상태 의존적 제한(State-Dependent Limits)을 사용할 수 있다.

연속 한계(Continuous Limits)와 최대 한계(Peak Limits)도 구분해야 한다. 짧은 동적 동작에서는 일시적으로 최대 토크를 안전하게 사용할 수 있지만 지속적인 조작이나 직립 상태에서는 연속 정격에 가까운 범위에서 동작해야 한다. 열 추정기(Thermal Estimator)는 누적된 액추에이터 부하를 추적하고 과열이 발생하기 전에 사용 가능한 토크를 점진적으로 디레이팅(Derating)하여 비상 정지에만 의존하지 않고 최적화 계층이 동작을 조정할 수 있도록 한다.

토크 변화율 제약(Torque-Rate Constraints)은 또 다른 보호 메커니즘을 제공한다. 연속된 두 토크 값이 각각 허용 범위 내에 있더라도 순간적인 변화가 크면 구조적 진동(Structural Vibration), 구동계 탄성(Drivetrain Elasticity), 충격과 유사한 거동을 유발할 수 있다. \\(\\dot{\\tau}\\) 또는 제어 주기 사이의 이산 토크 증분(Discrete Torque Increment)을 제한하면 외란 복구(Disturbance Recovery)에 필요한 충분한 대역폭을 유지하면서 더욱 부드러운 액추에이터 명령을 생성하고 기계적 응력을 감소시킬 수 있다.

관절 위치 제한(Joint Position Limits)은 기계적 스토퍼(Mechanical Stops)에 도달하기 전에 적용되어야 한다. 관절이 강성 경계(Hard Boundary)에 도달할 때까지 자유롭게 움직이도록 허용하는 대신 WBC는 안전 범위의 가장자리에 가까워질수록 점진적으로 엄격해지는 위치 의존 속도 또는 가속도 제한(Position-Dependent Velocity or Acceleration Limits)을 구성할 수 있다. 이를 통해 최적화기가 남아 있는 전신 여유도(Whole-Body Redundancy)를 이용해 운동 방향을 변경하는 제동 영역(Braking Region)을 형성할 수 있다.

속도 및 가속도 제한(Velocity and Acceleration Bounds)은 위치 보호를 보완한다. 과도한 관절 속도는 충격 에너지(Impact Energy)를 증가시키고 액추에이터의 제어 가능성을 감소시킬 수 있으며, 급격한 가속은 큰 관성 부하(Inertial Loads)를 발생시킬 수 있다. 이러한 물리량을 부등식 제약(Inequality Constraints)으로 포함하면 기구학적으로 유효한 작업이라도 액추에이터, 구조적 또는 운용 안전 요구조건을 위반하는 운동을 휴머노이드에 강제하지 못하도록 할 수 있다.

자기 충돌 회피(Self-Collision Avoidance)는 팔, 손, 다리, 몸통, 머리가 서로 겹치는 작업 공간에서 움직이는 휴머노이드에서 특히 중요하다. 원하는 손 궤적이 기하학적으로 도달 가능하더라도 팔꿈치가 몸통에 충돌하거나 한쪽 팔이 다른 팔과 충돌할 수 있다. 따라서 전신 안전은 말단장치 위치(End-Effector Position)만 감시하는 것이 아니라 전체 로봇 형상(Complete Robot Geometry)을 고려해야 한다.

충돌 모델(Collision Models)은 일반적으로 로봇 링크를 캡슐(Capsules), 구(Spheres), 박스(Boxes), 볼록 형상(Convex Shapes) 또는 단순화된 메시(Simplified Meshes)로 근사한다. 제어기는 선택된 충돌 쌍(Collision Pairs) 사이의 거리를 계산하고 이를 사전에 정의된 안전 여유(Safety Margins)와 비교한다. 정확한 메시 간 충돌 계산(Exact Mesh-to-Mesh Collision Calculation)은 결정론적 서보 주기 실행에 지나치게 많은 계산량을 요구할 수 있기 때문에 고주파 WBC에서는 단순화된 형상을 사용하는 경우가 많다.

최소 거리 제약(Minimum-Distance Constraint)은 속도 또는 가속도 수준의 부등식으로 변환할 수 있다. 두 신체 형상이 서로 접근하면 제어기는 두 형상 사이의 거리를 추가적으로 감소시키는 방향의 상대 운동을 제한한다. 여유 관절(Redundant Joints)은 접선 방향이나 충돌로부터 멀어지는 방향으로 계속 움직일 수 있으므로 안전한 대체 구성이 존재하는 경우 WBC는 주요 작업을 유지할 수 있다.

안전 여유(Safety Margins)는 단순한 기하학적 접촉 이상의 요소를 고려해야 한다. 상태 추정 불확실성(State-Estimation Uncertainty), 통신 지연(Communication Latency), 구조적 유연성(Structural Flexibility), 추종 오차(Tracking Error), 제한된 제동 능력(Finite Braking Capability)을 고려하면 기하학적 간격이 0이 되는 지점을 적절한 제어 경계로 사용할 수 없다. 더 큰 보호 거리(Protective Distance)를 설정하면 물리적 충돌이 불가피해지기 전에 제어기가 대응할 시간을 확보할 수 있다.

충돌 제약(Collision Constraints)은 위험의 심각도에 따라 우선순위를 설정할 수도 있다. 기계적으로 취약한 링크 사이의 접촉을 방지하거나 머리를 보호하는 경우에는 다른 구조 사이의 비교적 위험하지 않은 근접 상태보다 더 큰 안전 여유가 필요할 수 있다. 그러나 안전에 중요한 충돌 제약은 일반적으로 강성 제약(Hard Constraints) 또는 매우 강하게 보호되는 제약으로 유지하여 낮은 우선순위의 손 추종이나 자세 목표가 이를 무시하지 못하도록 해야 한다.

환경 충돌 회피(Environmental Collision Avoidance)는 동일한 원리를 자기 충돌의 범위를 넘어 외부 환경으로 확장한다. 휴머노이드는 벽, 테이블, 기계, 선반, 도구 또는 사람 주변에서 동작할 수 있다. 인지(Perception) 또는 유지 관리되는 장면 모델(Scene Model)로부터 얻은 환경 형상을 이용하여 관련 로봇 링크에 거리 제약을 생성하면 WBC 최적화기가 위험한 접근이 발생하기 전에 자세나 작업 운동을 수정할 수 있다.

사람 근접성(Human Proximity)은 환경이 동적이고 불확실하기 때문에 추가적인 고려가 필요하다. 사람이 로봇에 접근함에 따라 속도와 힘 제한을 낮출 수 있으며, 더 큰 보호 여유를 적용하여 인지 지연(Perception Latency)이나 운동 예측 불확실성(Motion Prediction Uncertainty)을 보상할 수 있다. 이러한 적응은 독립적인 안전 모니터링(Safety Monitoring)과 하드웨어 수준 보호 메커니즘을 대체하는 것이 아니라 이를 보완해야 한다.

접촉력 제한(Contact-Force Limits)은 WBC 안전성의 또 다른 중요한 구성 요소이다. 동역학적으로 실행 가능한 운동이라도 과도한 지면 반력(Ground Reaction Force), 손 상호작용 힘(Hand Interaction Force), 접촉 모멘트(Contact Moment)를 발생시킬 수 있다. 수직력, 접선력, 접촉 렌치(Contact Wrenches)를 제한하면 로봇, 조작 물체, 환경 구조물 및 주변 사람에게 불필요하게 큰 상호작용 하중이 가해지는 것을 방지할 수 있다.

마찰 제약(Friction Constraints)은 실행 가능성과 안전성 모두에 기여한다. 최적화기가 사용 가능한 마찰 영역(Friction Region)을 벗어나는 접촉력을 요구하면 명령된 관절 토크가 제한 범위 안에 있더라도 발이나 손이 미끄러질 수 있다. 따라서 마찰 원뿔(Friction Cone) 또는 마찰 피라미드(Friction Pyramid) 부등식은 물리적으로 유지할 수 없는 접촉력에 제어기가 의존하는 것을 방지한다.

균형 관련 제약(Balance-Related Constraints)은 휴머노이드가 넘어질 가능성이 있는 구성으로 진입하는 것을 방지한다. WBC 공식에 따라 압력 중심 제한(Center-of-Pressure Limits), 중심 운동량 조절(Centroidal Momentum Regulation), 지지 렌치 실행 가능성(Support-Wrench Feasibility), 접촉력 분배(Contact-Force Distribution), 무게중심 거동(Center-of-Mass Behavior)을 포함할 수 있다. 빠른 운동에서는 단순한 기하학적 지지 다각형(Support Polygon)만으로 충분하지 않기 때문에 안전성을 동적으로 평가해야 한다.

작업 공간 안전 경계(Task-Space Safety Boundaries)는 운용 좌표(Operational Coordinates)를 직접 제한할 수 있다. 손이 얼굴 주변의 민감한 영역에 진입하지 못하도록 하거나 스윙 중인 발에 최소 지면 여유(Minimum Clearance)를 요구하고 몸통이 과도하게 기울어지는 것을 제한할 수 있다. 이러한 경계는 응용 분야별 안전 요구조건을 전신 최적화에 직접 포함할 수 있는 형태로 변환한다.

계층적 이차 계획법(Hierarchical Quadratic Programming, HQP)은 안전 제약과 성능 목표(Performance Objectives)를 분리하기 위한 자연스러운 구조를 제공한다. 토크 제한, 충돌 회피, 필수 접촉(Required Contacts), 핵심 안정성 조건(Critical Stability Conditions)을 보호된 우선순위 수준에 배치할 수 있으며, 필요한 경우 손 추종, 자세 최적화, 시선(Gaze), 조작성(Manipulability)의 성능을 저하시킬 수 있다. 따라서 안전은 단순히 큰 수치 가중치가 아니라 우선순위(Priority) 자체로 표현된다.

그러나 강성 제약(Hard Constraints)은 서로 충돌할 경우 최적화 문제를 실행 불가능(Infeasible)하게 만들 수 있으므로 신중하게 선택해야 한다. 충돌 회피, 접촉 유지, 관절 제한을 동시에 만족할 수 없는 경우 제어기는 사전에 정의된 복구 전략(Recovery Strategy)을 필요로 한다. 안전 아키텍처는 어떤 제약을 완화할 수 있는지, 어떤 제약은 절대로 위반해서는 안 되는지, 그리고 어떤 비상 행동(Emergency Behavior)이 정상 최적화를 대체해야 하는지를 정의해야 한다.

슬랙 변수(Slack Variables)는 선택된 비핵심 제약에 대해 제어된 완화(Controlled Relaxation)를 제공할 수 있다. 예를 들어 원하는 자세나 조작 궤적은 토크 및 충돌 제한을 유지하기 위해 오차를 허용할 수 있다. 각 슬랙 변수에 부여되는 페널티(Penalty)는 위반 비용을 나타내므로 요청된 행동이 실행 불가능해졌을 때 제어기가 예측할 수 없는 방식으로 실패하는 대신 체계적으로 작업 성능을 희생하도록 할 수 있다.

솔버 상태(Solver Health) 자체도 실시간 WBC의 안전 요소이다. 솔버가 실행 불가능, 수치 계산 실패(Numerical Failure), 과도한 잔차(Excessive Residuals), 계산 기한 초과(Deadline Overrun)를 보고한 경우 최적화 결과를 그대로 적용해서는 안 된다. 실행 계층(Execution Layer)은 토크를 액추에이터로 전송하기 전에 해의 상태와 명령 제한을 검증하고 정상적인 해를 사용할 수 없을 경우를 위한 결정론적 폴백 경로(Deterministic Fallback Path)를 유지해야 한다.

폴백 제어(Fallback Control)는 고장 조건에 따라 이전에 검증된 명령을 짧은 시간 동안 유지하거나 명령 운동을 감소시키고, 감쇠(Damping)를 증가시키거나 안정된 자세로 전환하거나 제어된 정지(Controlled Stopping)를 시작할 수 있다. 적절한 대응은 로봇이 정지 상태인지, 보행 중인지, 물체를 운반 중인지 또는 환경과 물리적으로 상호작용하고 있는지에 따라 달라진다. 하나의 보편적인 폴백만으로 모든 휴머노이드 상태를 처리하기는 어렵다.

WBC에 포괄적인 제약이 포함되어 있더라도 독립적인 하위 수준 보호(Independent Low-Level Protection)는 여전히 필요하다. 모터 드라이브와 관절 제어기는 전류, 토크, 속도, 온도, 위치 제한을 로컬 수준에서 강제할 수 있다. 비상 정지 로직(Emergency-Stop Logic), 통신 워치독(Communication Watchdogs), 하드웨어 인터록(Hardware Interlocks), 전력단 보호(Power-Stage Protection)는 최적화 과정이나 상위 수준 컴퓨터가 고장난 경우에도 동작하는 추가적인 보호 계층을 제공한다.

따라서 안전 모니터링은 심층 방어(Defense in Depth) 구조를 사용해야 한다. WBC 계층은 정상 운전 중 위험한 명령을 방지하고, 실시간 실행 계층은 수치적 및 타이밍 유효성을 검사하며, 관절 제어기는 로컬 액추에이터 제한을 적용하고, 독립적인 감독 또는 하드웨어 메커니즘은 심각한 고장을 처리한다. 어떠한 단일 소프트웨어 최적화 계층도 유일한 보호 메커니즘으로 간주해서는 안 된다.

고주파 구현(High-Frequency Implementation)은 중요한 계산상의 절충을 발생시킨다. 많은 충돌 쌍과 동적 안전 제약을 1kHz로 평가하면 계산 비용이 커질 수 있다. 중요한 근접 충돌 쌍은 서보 주파수(Servo Rate)에서 검사하고 더 광범위한 환경 형상은 낮은 주파수에서 갱신할 수 있다. 보수적인 경계(Conservative Bounds)와 다중 주파수 처리(Multi-Rate Processing)를 사용하면 필수적인 보호 기능을 제거하지 않으면서 계산 효율성을 확보할 수 있다.

시험(Testing)은 정상적인 운동뿐 아니라 의도적으로 경계 조건(Boundary Conditions)을 대상으로 수행해야 한다. 검증에는 관절 한계에 근접한 구성, 토크 포화(Torque Saturation), 자기 충돌에 가까운 상태, 감소된 마찰, 예상하지 못한 접촉, 솔버 실행 불가능성(Solver Infeasibility), 센서 지연, 통신 손실, 열적 디레이팅(Thermal Derating)이 포함되어야 한다. 이러한 시나리오는 여러 보호 메커니즘이 동시에 활성화될 때도 안전 제약이 효과적으로 유지되는지를 확인할 수 있도록 한다.

안전성을 고려한 WBC는 궁극적으로 액추에이터 제한, 기하학적 이격 거리(Geometric Separation), 접촉 실행 가능성(Contact Feasibility), 안정성 여유(Stability Margins), 계산 유효성(Computational Validity)을 전신 운동에 대한 명시적인 조건으로 변환한다. 이를 통해 휴머노이드는 안전한 대안이 존재할 경우 여유도를 활용하여 작업을 계속 수행하고, 물리적 한계가 요구할 경우 위험을 감수하면서 목표를 추종하는 대신 의도적으로 추종 성능을 희생할 수 있다.

휴머노이드 WBC 아키텍처에서 토크 제한과 자기 충돌 회피는 운용 공간 작업(Operational-Space Tasks), 계층적 최적화(Hierarchical Optimization), 접촉 제어(Contact Control), 보행-조작 통합(Loco-Manipulation), 순응 토크 실행(Compliant Torque Execution), 양손 협조(Bimanual Coordination), 1kHz 계산을 둘러싸는 보호 계층(Protective Layer)을 형성한다. 이러한 안전 구조는 더욱 동적인 전신 행동을 실제 하드웨어에 배포하기 전에 체계적인 시뮬레이션-실환경 검증(Simulation-to-Real Validation)을 수행하기 위한 필수적인 기반을 제공한다.

##  

## 05.10. WBC Validation Sim and Real Comparison

![](images/image10.png){width="7.268055555555556in" height="7.268055555555556in"}

Validation of humanoid whole-body control requires systematic comparison between simulation behavior and physical robot performance. A controller that succeeds in an ideal simulator may fail on hardware because real systems contain sensing noise, communication delay, actuator dynamics, friction, structural compliance, contact uncertainty, and modeling error. Validation must therefore determine not only whether tasks succeed, but whether the assumptions supporting WBC remain valid in reality.

Simulation provides the first environment for evaluating the complete WBC architecture before exposing expensive hardware to potentially unstable commands. The same task hierarchy, contact model, torque limits, trajectory references, and control interfaces intended for the robot should be exercised whenever possible. Maintaining software similarity between simulation and hardware reduces the risk that validation tests a controller different from the one ultimately deployed.

Initial validation should establish basic consistency of kinematics and dynamics. Joint conventions, coordinate frames, inertial parameters, center-of-mass location, Jacobians, gravity direction, and actuator signs must agree between the WBC model and simulator. Small convention errors can remain hidden during simple posture tests but become severe when contact forces, momentum regulation, or coordinated manipulation are activated.

Static standing provides a useful baseline because expected behavior is relatively easy to interpret. The humanoid should maintain posture while producing plausible ground reaction forces, joint torques, center-of-pressure locations, and center-of-mass errors. Simulation and hardware measurements can then be compared to identify systematic differences in load distribution, model compensation, sensor bias, or actuator output.

Dynamic validation introduces controlled motions of increasing complexity. Squatting, torso rotation, arm movement, center-of-mass shifting, and stepping can reveal errors that are not visible during static tests. Tracking error, commanded torque, measured torque, contact force, momentum, and solver behavior should be recorded synchronously so that discrepancies can be associated with specific dynamic events.

Contact behavior is one of the largest sources of simulation-to-real difference. Simulators approximate contact using rigid or compliant numerical models with selected stiffness, damping, friction, restitution, and solver parameters. Physical contact depends on sole material, floor properties, structural flexibility, surface irregularity, and unmodeled deformation. WBC validation must therefore examine contact behavior rather than assuming identical force profiles.

Foot touchdown is especially informative because it exposes timing and compliance differences. A simulated foot may establish contact cleanly at the planned instant, whereas the physical robot can experience early contact, delayed contact, impact oscillation, or partial sole contact. Comparing vertical force rise, foot velocity, contact detection timing, and post-impact motion helps identify whether trajectory, damping, or contact-transition logic requires adjustment.

Friction validation should include motions that approach but do not intentionally exceed expected contact limits. Lateral center-of-mass shifts, controlled pushing, or asymmetric loading can reveal whether the friction coefficient assumed by WBC is realistic. A conservative friction estimate is generally preferable to a model that permits forces the physical surface cannot support, because optimistic assumptions can produce unexpected slipping.

Torque validation requires comparison between optimizer commands and actual actuator behavior. Requested torque, estimated or measured joint torque, motor current, joint velocity, and temperature can be recorded to evaluate tracking accuracy. Persistent differences may indicate friction, transmission losses, calibration error, saturation, actuator bandwidth limitations, or dynamic effects that are absent from the rigid-body model.

Frequency response is important because a controller may reproduce slow motions accurately while diverging during rapid behavior. Tests with progressively faster references can reveal phase lag, attenuation, structural resonance, and communication delay. The objective is not to force simulation and hardware traces to become identical, but to understand the bandwidth within which the model predicts physical behavior sufficiently well for WBC.

Latency should be measured as an end-to-end property. Sensor acquisition, state estimation, WBC computation, network transmission, actuator processing, and mechanical response each contribute delay. Simulation often executes these stages with negligible or perfectly deterministic timing. Introducing measured delays into simulation can significantly improve its value for predicting real control performance.

State-estimation validation is equally important because WBC acts on estimated rather than perfect robot state. Simulation experiments should therefore include realistic noise, bias, latency, and contact-state uncertainty instead of relying exclusively on ground-truth states. Hardware logs can characterize these effects and provide distributions that are later reproduced in simulation for more representative testing.

Disturbance-rejection tests evaluate whether the controller maintains stability when assumptions are violated temporarily. External pushes, payload changes, reference perturbations, and contact-force disturbances can be applied in controlled conditions. Comparisons should examine recovery time, center-of-mass displacement, momentum response, foot loading, joint torque peaks, and whether safety constraints become active during recovery.

Manipulation validation adds interaction forces and payload uncertainty. A humanoid can lift objects with known masses in simulation and hardware while hand pose error, wrist wrench, arm torque, torso compensation, and foot loading are compared. Increasing payload gradually reveals whether the controller correctly redistributes whole-body effort or relies on unrealistically accurate model compensation.

Bimanual tests should additionally evaluate relative hand error and internal force. Both hands may track their commanded poses while still generating excessive opposing forces because of calibration or object-geometry mismatch. Comparing grasp-force distribution and object motion between simulation and reality helps validate compliant task formulations and closed-chain constraints used by bimanual WBC.

Locomotion-manipulation validation should examine the coupling between upper- and lower-body tasks. Carrying an object while shifting stance or walking can expose momentum, payload, and contact-model errors simultaneously. Useful metrics include object-pose error, foot placement, center-of-mass tracking, support wrench, hand force, joint torque margin, and the frequency with which lower-priority objectives must be relaxed.

Safety constraints require dedicated validation rather than passive observation. Tests should deliberately approach torque bounds, joint limits, self-collision margins, friction boundaries, and task-space safety regions without crossing unsafe physical thresholds. The resulting logs should confirm that WBC modifies motion before the protected boundary is reached and that lower-priority tracking errors increase as intended.

Self-collision validation can begin entirely in simulation, where trajectories can explore difficult configurations without hardware risk. Minimum distances between protected link pairs should be monitored while manipulation and posture tasks compete. Hardware tests can then use larger margins and slower motion to verify that geometric models, joint calibration, and real structural dimensions agree sufficiently with the simulated safety representation.

Real-time performance must also be compared across environments. Simulation may provide abundant computational resources or non-real-time execution, hiding deadline problems. Hardware validation should record WBC computation time, solver iterations, numerical residuals, deadline overruns, and communication timing while executing worst-case configurations such as multi-contact bimanual manipulation with active collision constraints.

A useful validation process defines quantitative metrics before testing. Cartesian tracking error, orientation error, joint error, torque error, contact-force error, center-of-mass deviation, momentum error, minimum collision distance, solver time, and constraint margin can be evaluated consistently across simulation and hardware. Repeated trials provide distributions rather than relying on one successful demonstration.

Direct trajectory comparison should be time-aligned carefully. Different contact timing or communication latency can make two physically similar responses appear different if samples are compared only by timestamp. Event-based alignment using touchdown, lift-off, grasp establishment, or disturbance onset can separate timing discrepancies from genuine differences in motion and force response.

Parameter identification can reduce persistent simulation-to-real mismatch. Link inertial parameters, joint friction, motor constants, transmission efficiency, actuator delay, stiffness, damping, and contact properties may be estimated from dedicated experiments. Updated parameters should improve predictive accuracy without turning the simulator into an overfitted reproduction of one specific trajectory.

Randomization provides a complementary strategy when exact identification is impossible. Simulation can vary friction, payload, latency, sensor noise, contact stiffness, and selected inertial parameters within plausible ranges. WBC that remains stable across these variations is less dependent on one nominal model and is more likely to tolerate the uncertainty encountered on physical hardware.

Validation should progress through controlled gates rather than moving immediately from simulation to dynamic full-body experiments. Offline model checks can be followed by simulation, software-in-the-loop testing, hardware interfaces with restricted actuation, supported or tethered robot tests, low-energy physical motion, and finally representative operational tasks. Each stage should have explicit acceptance criteria.

Regression testing is necessary after controller modifications. Changes to task weights, hierarchy, contact logic, model parameters, solver configuration, or safety margins can improve one behavior while degrading another. A standardized simulation suite can automatically replay standing, stepping, manipulation, bimanual, disturbance, and boundary-condition scenarios before new software is authorized for hardware testing.

The purpose of simulation-to-real comparison is therefore not to demonstrate that simulation perfectly reproduces the physical robot. Its purpose is to quantify where the models agree, identify where uncertainty matters, verify that WBC remains stable and safe despite those differences, and establish evidence that controller performance transfers across the boundary between numerical models and real physical interaction.

A mature WBC validation framework closes the loop between simulation and hardware. Real measurements improve model parameters and uncertainty ranges, updated simulation exposes the controller to broader conditions, and physical experiments confirm the resulting robustness. This iterative process converts simulation from a demonstration environment into an engineering instrument for developing reliable humanoid whole-body control.

Within the complete WBC architecture, simulation-to-real validation integrates every preceding component: task-space hierarchy, operational-space control, hierarchical QP, contact-wrench distribution, simultaneous locomotion and manipulation, compliant torque control, bimanual execution, 1 kHz optimization, and safety constraints. Validation demonstrates that these components operate together as a coherent physical control system rather than only as independent mathematical formulations.

휴머노이드 전신 제어(Humanoid Whole-Body Control)의 검증에는 시뮬레이션 거동과 실제 로봇 성능 사이의 체계적인 비교가 필요하다. 이상적인 시뮬레이터에서 성공하는 제어기라도 실제 시스템에는 센싱 잡음(Sensing Noise), 통신 지연(Communication Delay), 액추에이터 동역학(Actuator Dynamics), 마찰(Friction), 구조적 순응성(Structural Compliance), 접촉 불확실성(Contact Uncertainty), 모델링 오차(Modeling Error)가 존재하기 때문에 하드웨어에서 실패할 수 있다. 따라서 검증은 작업의 성공 여부뿐 아니라 WBC를 뒷받침하는 가정이 실제 환경에서도 유효한지를 판단해야 한다.

시뮬레이션(Simulation)은 잠재적으로 불안정한 명령에 고가의 하드웨어를 노출하기 전에 전체 WBC 아키텍처를 평가하는 첫 번째 환경을 제공한다. 가능한 경우 실제 로봇에 적용할 것과 동일한 작업 계층 구조(Task Hierarchy), 접촉 모델(Contact Model), 토크 제한(Torque Limits), 궤적 기준(Trajectory References), 제어 인터페이스(Control Interfaces)를 사용해야 한다. 시뮬레이션과 하드웨어 사이의 소프트웨어 유사성을 유지하면 최종적으로 배포될 제어기와 다른 제어기를 검증하게 되는 위험을 줄일 수 있다.

초기 검증에서는 기구학(Kinematics)과 동역학(Dynamics)의 기본적인 일관성을 확인해야 한다. 관절 규약(Joint Conventions), 좌표 프레임(Coordinate Frames), 관성 파라미터(Inertial Parameters), 무게중심 위치(Center-of-Mass Location), 자코비안(Jacobians), 중력 방향(Gravity Direction), 액추에이터 부호(Actuator Signs)가 WBC 모델과 시뮬레이터 사이에서 일치해야 한다. 작은 규약 오류는 단순한 자세 시험에서는 드러나지 않을 수 있지만 접촉력, 운동량 조절(Momentum Regulation), 협조 조작(Coordinated Manipulation)이 활성화되면 심각한 문제로 나타날 수 있다.

정적 직립(Static Standing)은 예상되는 거동을 비교적 쉽게 해석할 수 있기 때문에 유용한 기준 시험을 제공한다. 휴머노이드는 타당한 지면 반력(Ground Reaction Forces), 관절 토크(Joint Torques), 압력 중심 위치(Center-of-Pressure Locations), 무게중심 오차(Center-of-Mass Errors)를 생성하면서 자세를 유지해야 한다. 이후 시뮬레이션과 하드웨어 측정값을 비교하여 하중 분배, 모델 보상(Model Compensation), 센서 바이어스(Sensor Bias), 액추에이터 출력의 체계적인 차이를 식별할 수 있다.

동적 검증(Dynamic Validation)은 점진적으로 복잡도가 증가하는 제어된 운동을 도입한다. 스쿼트(Squatting), 몸통 회전(Torso Rotation), 팔 운동, 무게중심 이동, 보행 스텝(Stepping)은 정적 시험에서 보이지 않는 오류를 드러낼 수 있다. 추종 오차(Tracking Error), 명령 토크, 측정 토크, 접촉력, 운동량, 솔버 거동(Solver Behavior)을 동기화하여 기록하면 특정 동적 사건과 불일치를 연관시켜 분석할 수 있다.

접촉 거동(Contact Behavior)은 시뮬레이션과 실제 환경 사이의 차이를 발생시키는 가장 큰 원인 중 하나이다. 시뮬레이터는 선택된 강성(Stiffness), 감쇠(Damping), 마찰, 반발 계수(Restitution), 솔버 파라미터(Solver Parameters)를 가진 강체 또는 순응 수치 모델을 이용하여 접촉을 근사한다. 실제 접촉은 발바닥 재질, 바닥 특성, 구조적 유연성, 표면 불규칙성, 모델링되지 않은 변형에 영향을 받는다. 따라서 WBC 검증에서는 동일한 힘 프로파일을 가정하기보다 접촉 거동 자체를 조사해야 한다.

발 착지(Foot Touchdown)는 타이밍과 순응성 차이를 명확하게 드러내기 때문에 특히 중요한 검증 항목이다. 시뮬레이션된 발은 계획된 순간에 정확하게 접촉할 수 있지만 실제 로봇에서는 조기 접촉(Early Contact), 지연 접촉(Delayed Contact), 충격 진동(Impact Oscillation), 부분적인 발바닥 접촉(Partial Sole Contact)이 발생할 수 있다. 수직력 증가, 발 속도, 접촉 감지 타이밍(Contact Detection Timing), 충격 이후 운동을 비교하면 궤적, 감쇠 또는 접촉 전환 로직(Contact-Transition Logic)의 조정 필요성을 판단할 수 있다.

마찰 검증(Friction Validation)은 예상되는 접촉 한계에 접근하지만 의도적으로 이를 초과하지 않는 운동을 포함해야 한다. 횡방향 무게중심 이동, 제어된 밀기(Controlled Pushing), 비대칭 하중(Asymmetric Loading)을 통해 WBC가 가정한 마찰 계수가 실제적인지를 확인할 수 있다. 물리적 표면이 지지할 수 없는 힘을 허용하는 모델은 예상하지 못한 미끄러짐을 발생시킬 수 있으므로 일반적으로 낙관적인 마찰 가정보다 보수적인 마찰 추정(Conservative Friction Estimate)이 바람직하다.

토크 검증(Torque Validation)에서는 최적화기의 명령과 실제 액추에이터 거동을 비교해야 한다. 요청 토크, 추정 또는 측정 관절 토크, 모터 전류(Motor Current), 관절 속도, 온도를 기록하여 추종 정확도를 평가할 수 있다. 지속적인 차이는 마찰, 변속기 손실(Transmission Losses), 캘리브레이션 오차(Calibration Error), 포화(Saturation), 액추에이터 대역폭 한계(Actuator Bandwidth Limitations), 강체 모델에 포함되지 않은 동역학적 효과를 나타낼 수 있다.

주파수 응답(Frequency Response)은 제어기가 느린 운동에서는 정확하게 동작하지만 빠른 운동에서는 실제 시스템과 차이를 보일 수 있기 때문에 중요하다. 점진적으로 더 빠른 기준값을 적용하는 시험을 통해 위상 지연(Phase Lag), 감쇠(Attenuation), 구조 공진(Structural Resonance), 통신 지연을 확인할 수 있다. 목표는 시뮬레이션과 하드웨어의 궤적을 완전히 동일하게 만드는 것이 아니라 WBC를 위해 모델이 실제 거동을 충분히 정확하게 예측할 수 있는 대역폭을 파악하는 것이다.

지연(Latency)은 종단 간 특성(End-to-End Property)으로 측정해야 한다. 센서 획득(Sensor Acquisition), 상태 추정(State Estimation), WBC 계산, 네트워크 전송(Network Transmission), 액추에이터 처리, 기계적 응답이 각각 전체 지연에 기여한다. 시뮬레이션에서는 이러한 단계가 무시할 수 있을 정도로 빠르거나 완벽하게 결정론적인 타이밍으로 실행되는 경우가 많다. 실제 측정된 지연을 시뮬레이션에 도입하면 실제 제어 성능을 예측하는 능력을 크게 향상시킬 수 있다.

WBC는 완벽한 로봇 상태가 아니라 추정된 상태를 기반으로 동작하기 때문에 상태 추정 검증(State-Estimation Validation) 역시 중요하다. 따라서 시뮬레이션 시험에는 실제적인 잡음(Noise), 바이어스(Bias), 지연, 접촉 상태 불확실성을 포함해야 하며 실제 상태값(Ground-Truth States)에만 의존해서는 안 된다. 하드웨어 로그(Hardware Logs)를 통해 이러한 효과의 특성을 분석하고 분포를 얻은 후 이를 시뮬레이션에서 재현하여 더욱 현실적인 시험을 수행할 수 있다.

외란 제거 시험(Disturbance-Rejection Tests)은 제어기의 가정이 일시적으로 위반되는 상황에서도 안정성을 유지할 수 있는지를 평가한다. 외부 밀기(External Pushes), 페이로드 변화(Payload Changes), 기준값 교란(Reference Perturbations), 접촉력 외란(Contact-Force Disturbances)을 제어된 조건에서 적용할 수 있다. 비교 항목에는 복구 시간(Recovery Time), 무게중심 변위, 운동량 응답, 발 하중, 관절 토크 피크(Joint Torque Peaks), 복구 중 안전 제약 활성화 여부가 포함되어야 한다.

조작 검증(Manipulation Validation)은 상호작용 힘과 페이로드 불확실성(Payload Uncertainty)을 추가한다. 휴머노이드는 시뮬레이션과 실제 환경에서 질량을 알고 있는 물체를 들어 올릴 수 있으며, 이 과정에서 손 자세 오차, 손목 렌치(Wrist Wrench), 팔 토크, 몸통 보상(Torso Compensation), 발 하중을 비교할 수 있다. 페이로드를 점진적으로 증가시키면 제어기가 전신 부하를 올바르게 재분배하는지 또는 비현실적으로 정확한 모델 보상에 의존하는지를 확인할 수 있다.

양손 시험(Bimanual Tests)에서는 상대 손 오차(Relative Hand Error)와 내부 힘(Internal Force)을 추가로 평가해야 한다. 양손이 명령된 자세를 정확하게 추종하더라도 캘리브레이션 또는 물체 형상 불일치로 인해 과도한 반대 방향의 힘이 발생할 수 있다. 시뮬레이션과 실제 환경 사이에서 파지력 분배(Grasp-Force Distribution)와 물체 운동을 비교하면 양손 WBC에 사용되는 순응 작업 공식(Compliant Task Formulations)과 폐쇄 체인 제약(Closed-Chain Constraints)을 검증하는 데 도움이 된다.

보행-조작 검증(Locomotion-Manipulation Validation)에서는 상체와 하체 작업 사이의 결합을 평가해야 한다. 물체를 운반하면서 지지 자세를 변경하거나 보행하면 운동량, 페이로드, 접촉 모델의 오차가 동시에 나타날 수 있다. 유용한 지표에는 물체 자세 오차(Object-Pose Error), 발 배치(Foot Placement), 무게중심 추종, 지지 렌치(Support Wrench), 손 힘, 관절 토크 여유(Joint Torque Margin), 낮은 우선순위 목표를 완화해야 하는 빈도가 포함된다.

안전 제약(Safety Constraints)은 수동적인 관찰이 아니라 전용 검증을 필요로 한다. 시험에서는 위험한 물리적 경계를 넘지 않으면서 토크 제한, 관절 한계, 자기 충돌 여유(Self-Collision Margins), 마찰 경계(Friction Boundaries), 작업 공간 안전 영역(Task-Space Safety Regions)에 의도적으로 접근해야 한다. 기록된 결과를 통해 보호 경계에 도달하기 전에 WBC가 운동을 수정하는지, 그리고 의도한 대로 낮은 우선순위의 추종 오차가 증가하는지를 확인해야 한다.

자기 충돌 검증(Self-Collision Validation)은 하드웨어 위험 없이 어려운 자세를 탐색할 수 있는 시뮬레이션에서 먼저 수행할 수 있다. 조작 및 자세 작업이 서로 경쟁하는 동안 보호 대상 링크 쌍(Protected Link Pairs) 사이의 최소 거리를 감시해야 한다. 이후 하드웨어 시험에서는 더 큰 안전 여유와 낮은 운동 속도를 사용하여 기하학적 모델, 관절 캘리브레이션, 실제 구조 치수가 시뮬레이션의 안전 표현과 충분히 일치하는지 검증할 수 있다.

실시간 성능(Real-Time Performance) 역시 환경 간 비교 대상이 되어야 한다. 시뮬레이션은 충분한 계산 자원을 제공하거나 비실시간 방식으로 실행되어 계산 기한 문제(Deadline Problems)를 숨길 수 있다. 하드웨어 검증에서는 활성화된 충돌 제약을 포함하는 다중 접촉 양손 조작(Multi-Contact Bimanual Manipulation)과 같은 최악 조건 구성을 실행하면서 WBC 계산 시간, 솔버 반복 횟수(Solver Iterations), 수치 잔차(Numerical Residuals), 계산 기한 초과(Deadline Overruns), 통신 타이밍을 기록해야 한다.

유용한 검증 과정에서는 시험을 시작하기 전에 정량적 지표(Quantitative Metrics)를 정의한다. 데카르트 추종 오차(Cartesian Tracking Error), 방향 오차(Orientation Error), 관절 오차, 토크 오차, 접촉력 오차, 무게중심 편차, 운동량 오차, 최소 충돌 거리(Minimum Collision Distance), 솔버 시간(Solver Time), 제약 여유(Constraint Margin)를 시뮬레이션과 하드웨어에서 일관되게 평가할 수 있다. 반복 시험을 통해 하나의 성공적인 시연에 의존하는 대신 성능 분포를 얻을 수 있다.

직접적인 궤적 비교(Direct Trajectory Comparison)는 시간 정렬(Time Alignment)을 신중하게 수행해야 한다. 접촉 타이밍이나 통신 지연이 서로 다르면 물리적으로 유사한 두 응답도 타임스탬프만으로 비교할 경우 큰 차이가 있는 것처럼 보일 수 있다. 착지, 이륙(Lift-Off), 파지 형성(Grasp Establishment), 외란 시작(Disturbance Onset)을 이용한 이벤트 기반 정렬(Event-Based Alignment)은 타이밍 차이와 실제 운동 및 힘 응답의 차이를 분리하는 데 도움이 된다.

파라미터 식별(Parameter Identification)은 지속적으로 발생하는 시뮬레이션-실환경 불일치(Simulation-to-Real Mismatch)를 감소시킬 수 있다. 링크 관성 파라미터, 관절 마찰, 모터 상수(Motor Constants), 변속 효율(Transmission Efficiency), 액추에이터 지연, 강성, 감쇠, 접촉 특성을 전용 실험을 통해 추정할 수 있다. 갱신된 파라미터는 시뮬레이터를 하나의 특정 궤적에 과적합(Overfitting)시키지 않으면서 예측 정확도를 향상시켜야 한다.

정확한 파라미터 식별이 불가능한 경우 랜덤화(Randomization)는 이를 보완하는 전략을 제공한다. 시뮬레이션에서 마찰, 페이로드, 지연, 센서 잡음, 접촉 강성, 선택된 관성 파라미터를 현실적으로 가능한 범위에서 변화시킬 수 있다. 이러한 변화에서도 안정적으로 동작하는 WBC는 하나의 명목 모델(Nominal Model)에 대한 의존성이 낮으며 실제 하드웨어에서 발생하는 불확실성을 견딜 가능성이 높다.

검증은 시뮬레이션에서 즉시 동적인 전신 하드웨어 실험으로 이동하는 것이 아니라 통제된 단계(Controlled Gates)를 따라 진행되어야 한다. 오프라인 모델 검사(Offline Model Checks) 이후 시뮬레이션, 소프트웨어 인 더 루프 시험(Software-in-the-Loop Testing), 제한된 구동을 사용하는 하드웨어 인터페이스, 지지 장치 또는 테더(Tether)를 적용한 로봇 시험, 저에너지 실제 운동, 최종적으로 대표적인 운용 작업으로 확장할 수 있다. 각 단계에는 명시적인 승인 기준(Acceptance Criteria)이 필요하다.

제어기를 수정한 이후에는 회귀 시험(Regression Testing)이 필요하다. 작업 가중치(Task Weights), 계층 구조, 접촉 로직, 모델 파라미터, 솔버 설정, 안전 여유의 변경은 하나의 행동을 개선하면서 다른 행동을 저하시킬 수 있다. 표준화된 시뮬레이션 시험군(Standardized Simulation Suite)을 통해 새로운 소프트웨어를 하드웨어 시험에 승인하기 전에 직립, 보행, 조작, 양손 작업, 외란, 경계 조건 시나리오를 자동으로 반복 실행할 수 있다.

따라서 시뮬레이션-실환경 비교(Simulation-to-Real Comparison)의 목적은 시뮬레이션이 실제 로봇을 완벽하게 재현한다는 것을 증명하는 데 있지 않다. 그 목적은 모델이 일치하는 영역을 정량화하고, 불확실성이 중요한 영역을 식별하며, 이러한 차이가 존재하더라도 WBC가 안정성과 안전성을 유지하는지를 검증하고, 수치 모델과 실제 물리적 상호작용 사이에서 제어기 성능이 전이된다는 근거를 확립하는 데 있다.

성숙한 WBC 검증 프레임워크(Validation Framework)는 시뮬레이션과 하드웨어 사이에 폐루프(Closed Loop)를 형성한다. 실제 측정값은 모델 파라미터와 불확실성 범위를 개선하고, 갱신된 시뮬레이션은 제어기를 더욱 다양한 조건에 노출시키며, 실제 실험은 그 결과로 얻어진 강건성(Robustness)을 확인한다. 이러한 반복 과정은 시뮬레이션을 단순한 시연 환경에서 신뢰성 높은 휴머노이드 전신 제어를 개발하기 위한 공학적 도구(Engineering Instrument)로 전환한다.

전체 WBC 아키텍처에서 시뮬레이션-실환경 검증은 앞서 다룬 모든 구성 요소를 통합한다. 여기에는 작업 공간 계층 구조(Task-Space Hierarchy), 운용 공간 제어(Operational-Space Control), 계층적 이차 계획법(Hierarchical QP), 접촉 렌치 분배(Contact-Wrench Distribution), 동시 보행 및 조작(Simultaneous Locomotion and Manipulation), 순응 토크 제어(Compliant Torque Control), 양손 작업 실행(Bimanual Execution), 1kHz 최적화(1 kHz Optimization), 안전 제약(Safety Constraints)이 포함된다. 검증은 이러한 구성 요소들이 독립적인 수학적 공식에 머무르지 않고 하나의 일관된 물리 제어 시스템(Coherent Physical Control System)으로 함께 동작한다는 것을 확인한다.
