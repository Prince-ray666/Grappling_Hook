 抓钩 / Grappling Hook 物理系统（Unity 3D）

一套完整的 3D 抓钩（钩爪）物理系统，覆盖「必达门槛」与全部「进阶挑战」，并提供一键搭建演示场景的 Editor 脚本。

## 操作按键

| 输入       | 动作             |
| -------- | -------------- |
| 鼠标移动     | 转动视角 / 瞄准      |
| WASD     | 移动（Shift 冲刺）   |
| Space    | 跳跃             |
| **鼠标左键** | 发射抓钩（起锚）       |
| **鼠标右键** | 断开抓钩、恢复自由态     |
| **E**    | 收绳（缩短绳长，向锚点拉近） |
| **Q**    | 放绳（放长绳长）       |
| 鼠标滚轮     | 快速收 / 放绳       |
| Esc      | 切换鼠标锁定         |

Keys 均可在 `GrapplingGun` Inspectors 中修改（`Input` 折叠区）。

## 需求覆盖对照

### 必达门槛

- **发射与命中**：`GrapplingGun.Fire()` 从相机沿鼠标指针方向 `Physics.Raycast`，命中墙壁/锚点/可抓物体后创建绳索与约束；`Flying` 状态播放钩爪甩出动画。
- **拉力（把玩家拉向锚点）**：命中后挂载 `SpringJoint`，持续把玩家拉向锚点；收绳时额外施加 `reelPullForce`，附着瞬间有 `attachPullImpulse`。
- **断开**：按释放键（右键）销毁关节、释放锚点，`State` 回到 `Idle` 恢复自由态。

### 进阶挑战

| 挑战                | 实现位置                              | 说明                                           |
| ----------------- | --------------------------------- | -------------------------------------------- |
| 摆动物理（锚点为圆心、绳长为半径） | `GrapplingGun` 的 SpringJoint + 重力 | 弹簧关节把玩家约束在绳长圆弧上，重力自然形成摆                      |
| 收绳 / 放绳           | `GrapplingGun.ReelIn/ReelOut`     | 实时改 `SpringJoint.maxDistance`，动态调整绳长         |
| 绳索与障碍碰撞（弯曲/断开）    | `GrapplingRope`                   | Verlet + 障碍推挤实现绕障弯曲；过度缠绕时 `ShouldBreak` 触发断开 |
| 轨迹预览线             | `TrajectoryPreview`               | 空闲时显示飞行路径与命中点，命中可抓物体变橙色                      |
| 抓取可移动物体           | `GrapplingGun` + `Grabbable`      | 轻物体被拉向玩家、重物体作摆荡锚点、也可拖拽到指定位置                  |

## 关键参数说明

- `GrapplingGun`：`springStrength/springDamper` 决定拉力和摆动手感；`minRopeLength/maxRopeLength/reelInSpeed/reelOutSpeed` 决定收放绳范围与速度；`grabMode` 可选 `Auto / SwingFromObject / PullObjectToPlayer / DragObject`。
- `GrapplingRope`：`segments` 绳索分段、`gravityScale` 下垂量、`obstacleMask` 障碍层、`breakWrapFactor` 缠绕断开阈值。
- `Grabbable`：物体质量相对玩家越小，越容易被「拉向玩家」（`Auto` 模式按质量比自动决定）。

