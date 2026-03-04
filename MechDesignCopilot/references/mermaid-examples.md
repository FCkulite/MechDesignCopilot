# Mermaid图表示例 - 机械设计

**适用场景**: 机械设计、机电一体化、自动化系统
**更新日期**: 2024年

---

## 1. 流程图 (Flowchart)

### 1.1 设计流程图

```mermaid
flowchart TD
    Start([项目启动]) --> Req[需求收集<br/>功能/性能/约束]
    Req --> Concept[概念设计<br/>功能分解/原理方案]
    Concept --> Compare{方案对比<br/>评分矩阵}
    Compare --> Concept
    Compare --> Select[选定方案]

    Select --> Calc[详细计算<br/>强度/寿命/选型]
    Calc --> Verify{校核通过?}
    Verify -->|否| Calc
    Verify -->|是| Model[3D建模<br/>零件/装配]

    Model --> Check{干涉检查}
    Check -->|有干涉| Model
    Check -->|无干涉| Drawing[工程图出图]

    Drawing --> BOM[BOM编制<br/>外购/自制/标准件]
    BOM --> Proto[原型制造]
    Proto --> Test{测试验证}
    Test -->|不通过| Calc
    Test -->|通过| End([交付])

    style Start fill:#e1f5e1
    style End fill:#e1f5e1
    style Test fill:#ffe1e1
    style Verify fill:#fff4e1
```

### 1.2 工作流程图

```mermaid
flowchart LR
    A[上料] --> B[夹紧]
    B --> C[加工]
    C --> D{检测}
    D -->|合格| E[下料]
    D -->|不合格| F[返工]
    F --> C
    E --> G[包装]

    style A fill:#e3f2fd
    style B fill:#e3f2fd
    style C fill:#fff3e0
    style D fill:#f3e5f5
    style E fill:#e8f5e9
    style F fill:#ffebee
    style G fill:#e8f5e9
```

---

## 2. 状态图 (State Diagram)

### 2.1 系统状态机

```mermaid
stateDiagram-v2
    [*] --> PowerOff: 初始状态

    PowerOff --> Initializing: 上电
    Initializing --> Ready: 自检通过
    Initializing --> Fault: 自检失败

    Ready --> AutoMode: 自动模式
    Ready --> ManualMode: 手动模式
    Ready --> Fault: 传感器异常

    AutoMode --> Running: 启动
    Running --> Paused: 暂停
    Running --> AutoMode: 完成
    Running --> Fault: 故障

    Paused --> Running: 继续
    Paused --> Ready: 停止

    ManualMode --> Jogging: 点动
    Jogging --> ManualMode: 停止
    ManualMode --> Teaching: 示教
    Teaching --> ManualMode: 完成

    Fault --> Ready: 复位
    Fault --> PowerOff: 断电

    note right of AutoMode
        自动模式下：
        - 执行预设程序
        - 三轴联动
        - 循环作业
    end note

    note right of Fault
        故障类型：
        - 超程报警
        - 伺服报警
        - 急停触发
        - 通信丢失
    end note
```

### 2.2 工作模式切换

```mermaid
stateDiagram-v2
    [*] --> Idle: 系统就绪

    Idle --> Setup: 按下"设置"
    Setup --> Idle: 设置完成

    Idle --> Run: 按下"启动"
    Run --> Pause: 按下"暂停"
    Run --> Idle: 任务完成

    Pause --> Run: 按下"继续"
    Pause --> Idle: 按下"停止"

    Idle --> Error: 故障发生
    Run --> Error: 故障发生
    Error --> Idle: 故障清除

    note right of Run
        运行状态：
        - 执行运动指令
        - 监控传感器
        - 更新HMI显示
    end note
```

---

## 3. 时序图 (Sequence Diagram)

### 3.1 PLC与伺服通信时序

```mermaid
sequenceDiagram
    participant HMI as 触摸屏HMI
    participant PLC as PLC控制器
    participant SVX as 伺服X
    participant SVY as 伺服Y
    participant SVZ as 伺服Z

    HMI->>PLC: 发送位置指令(X=100, Y=50, Z=20)
    PLC->>PLC: 轨迹规划<br/>插补计算

    Note over PLC,SVX: X轴运动
    PLC->>SVX: 发送脉冲+方向
    activate SVX
    SVX-->>PLC: 位置反馈
    deactivate SVX

    Note over PLC,SVY: Y轴运动
    PLC->>SVY: 发送脉冲+方向
    activate SVY
    SVY-->>PLC: 位置反馈
    deactivate SVY

    Note over PLC,SVZ: Z轴运动
    PLC->>SVZ: 发送脉冲+方向
    activate SVZ
    SVZ-->>PLC: 位置反馈
    deactivate SVZ

    PLC->>SVX: 停止脉冲
    PLC->>SVY: 停止脉冲
    PLC->>SVZ: 停止脉冲

    PLC-->>HMI: 运动完成
```

### 3.2 自动搬运流程时序

```mermaid
sequenceDiagram
    participant Sensor as 传感器
    participant PLC as PLC
    participant Servo as 伺服系统
    participant Gripper as 夹爪

    Sensor->>PLC: 检测到工件
    PLC->>PLC: 读取目标坐标

    PLC->>Servo: 移动到取料位
    Servo-->>PLC: 到达确认

    PLC->>Gripper: 夹爪闭合
    Gripper-->>PLC: 夹紧确认

    PLC->>Servo: 移动到放料位
    Servo-->>PLC: 到达确认

    PLC->>Gripper: 夹爪松开
    Gripper-->>PLC: 松开确认

    PLC->>Servo: 回到原点
    Servo-->>PLC: 完成

    PLC->>Sensor: 任务完成信号
```

---

## 4. 类图 (Class Diagram) - 零件装配结构

```mermaid
classDiagram
    class Assembly {
        +String name
        +int partCount
        +addPart()
        +checkInterference()
    }

    class Part {
        +String name
        +Material material
        +double weight
        +getVolume()
        +getCenterOfMass()
    }

    class StandardPart {
        +String standard
        +String supplier
        +getPrice()
    }

    class CustomPart {
        +String drawingNo
        +Process process
        +getCost()
    }

    class SubAssembly {
        +Assembly parent
        +explode()
    }

    Assembly <|-- SubAssembly : 继承
    Assembly "1" *-- "n" Part : 包含
    Part <|-- StandardPart : 继承
    Part <|-- CustomPart : 继承

    note for Part "基础零件类"
    note for StandardPart "外购标准件"
    note for CustomPart "自制非标件"
```

---

## 5. 实体关系图 (ER Diagram) - BOM数据结构

```mermaid
erDiagram
    PRODUCT ||--o{ ASSEMBLY : 包含
    ASSEMBLY ||--o{ SUB_ASSEMBLY : 分解
    ASSEMBLY ||--o{ PART : 包含
    PART ||--o{ STANDARD_PART : 继承
    PART ||--o{ CUSTOM_PART : 继承
    PART }o--|| MATERIAL : 使用
    PART }o--|| SUPPLIER : 采购

    PRODUCT {
        string product_id PK
        string name
        string version
        date created_date
    }

    ASSEMBLY {
        string assembly_id PK
        string product_id FK
        string name
        int quantity
    }

    PART {
        string part_id PK
        string assembly_id FK
        string name
        string drawing_no
        double weight
    }

    MATERIAL {
        string material_id PK
        string name
        string grade
        double density
    }

    SUPPLIER {
        string supplier_id PK
        string name
        string contact
        string phone
    }

    STANDARD_PART {
        string part_id PK,FK
        string standard
        string model
        double unit_price
    }

    CUSTOM_PART {
        string part_id PK,FK
        string process
        double cost
    }
```

---

## 6. 甘特图 (Gantt Chart) - 项目进度

```mermaid
gantt
    title 自动化设备开发项目进度
    dateFormat YYYY-MM-DD
    section 需求设计
    需求分析           :done,    des1, 2024-01-01, 5d
    方案设计           :done,    des2, after des1, 7d
    方案评审           :done,    des3, after des2, 2d

    section 详细设计
    机械设计           :active,  mech1, after des3, 15d
    电气设计           :         elec1, after des3, 12d
    设计评审           :         rev1, after mech1, 3d

    section 制造装配
    零件采购           :         purc1, after rev1, 10d
    零件加工           :         mach1, after rev1, 15d
    装配调试           :         asmb1, after mach1, 10d

    section 测试验收
    功能测试           :         test1, after asmb1, 5d
    可靠性测试         :         test2, after test1, 7d
    验收交付           :         crit,  del1, after test2, 3d
```

---

## 7. 饼图 (Pie Chart) - 成本分析

```mermaid
pie title 成本构成分析
    "外购件（电机/驱动/传感器）" : 45
    "标准件（轴承/导轨/紧固件）" : 15
    "自制件（机架/支架）" : 20
    "加工成本（焊接/机加工）" : 12
    "其他（装配/测试/运输）" : 8
```

---

## 8. 旅程图 (Journey Map) - 用户使用流程

```mermaid
journey
    title 操作员使用设备旅程
    section 上班准备
      开机检查: 5: 操作员
      系统自检: 4: 系统
      回原点: 3: 操作员
    section 正常运行
      加载程序: 5: 操作员
      自动运行: 5: 系统
      监控状态: 3: 操作员
    section 异常处理
      故障报警: 1: 操作员
      排查问题: 2: 维护员
      恢复运行: 4: 维护员
    section 下班
      停机: 5: 操作员
      清洁保养: 4: 操作员
      关闭电源: 5: 操作员
```

---

## 9. 网络图 (Graph) - 系统拓扑

```mermaid
graph TB
    subgraph "控制层"
        PLC[PLC控制器]
        HMI[触摸屏HMI]
        IPC[工业PC]
    end

    subgraph "驱动层"
        SERVO_X[伺服驱动器X]
        SERVO_Y[伺服驱动器Y]
        SERVO_Z[伺服驱动器Z]
        VFD[变频器]
    end

    subgraph "执行层"
        M_X[电机X]
        M_Y[电机Y]
        M_Z[电机Z]
        M_CONV[输送带电机]
    end

    subgraph "传感层"
        ENC_X[编码器X]
        ENC_Y[编码器Y]
        ENC_Z[编码器Z]
        LS[限位开关]
        PS[光电传感器]
    end

    PLC -->|Ethernet| HMI
    PLC -->|Ethernet| IPC
    PLC -->|RS-485| SERVO_X
    PLC -->|RS-485| SERVO_Y
    PLC -->|RS-485| SERVO_Z
    PLC -->|Modbus| VFD

    SERVO_X -->|PWM| M_X
    SERVO_Y -->|PWM| M_Y
    SERVO_Z -->|PWM| M_Z
    VFD -->|3Phase| M_CONV

    ENC_X -->|A/B相| SERVO_X
    ENC_Y -->|A/B相| SERVO_Y
    ENC_Z -->|A/B相| SERVO_Z
    LS -->|24V| PLC
    PS -->|NPN| PLC

    style PLC fill:#e1f5fe
    style HMI fill:#e1f5fe
    style IPC fill:#e1f5fe
```

---

## 10. 思维导图 (Mindmap) - 知识体系

```mermaid
mindmap
  root((机械设计))
    基础知识
      力学
      材料科学
      制图标准
    设计方法
      概念设计
      详细设计
      优化设计
    传动系统
      齿轮传动
      带传动
      丝杠传动
    结构设计
      机架设计
      连接设计
      密封设计
    计算分析
      强度校核
      刚度校核
      寿命计算
    制造工艺
      铸造
      锻造
      焊接
      机加工
    电气控制
      传感器
      PLC
      伺服系统
```

---

**使用建议**:

1. **流程图**: 用于表达设计流程、工作流程、工艺流程
2. **状态图**: 用于表达系统状态、工作模式、故障处理
3. **时序图**: 用于表达通信时序、控制逻辑、操作步骤
4. **类图**: 用于表达零件装配结构、数据结构
5. **ER图**: 用于表达BOM数据结构、数据库设计
6. **甘特图**: 用于表达项目进度、计划安排
7. **饼图**: 用于表达成本构成、时间分配
8. **旅程图**: 用于表达用户使用体验
9. **网络图**: 用于表达系统拓扑、电气连接
10. **思维导图**: 用于表达知识体系、设计思路

根据具体设计文档的需要，选择合适的图表类型来清晰表达信息。
