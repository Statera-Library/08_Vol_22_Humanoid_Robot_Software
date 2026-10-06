**Volume 22. Humanoid Robot Software**

# Chapter 05. Whole Body Control for Humanoids

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
