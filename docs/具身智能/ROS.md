# ROS

## 运行时结构

### Node (节点)

节点负责看摄像头、算路径、驱动电机等行为。一个节点通常只负责一件小事。

* 在**写代码时**，Node是一个文件（比如 `robot_control.py` 或 `node.cpp`）。
* 在**运行起来后**，Node就变成了操作系统里的一个**进程（Process）**，也就是在内存里运行的程序。

### Topic（话题）

ROS 2 Topic 的工作原理类似于广播电台：

* **Publisher (发布者)**：将数据（Message）发送到特定的 Topic 上。
* **Subscriber (订阅者)**：监听特定的 Topic 并接收数据。
* **Message (消息)**：Topic 传输的数据结构（如整数、字符串或复杂的传感器数据结构）。
* **多对多关系**：一个 Topic 可以有多个发布者和多个订阅者，系统是解耦的。

Topic有`/scan`，`/cmd_vel`等。斜杠（`/`）在 ROS2 里只是为了**分类和分层**。比如 `/camera/front/image_raw` 和 `/camera/back/image_raw`，它真正的名字叫**命名空间（Namespace）**。

Topic是一条在内存和网络中流淌的虚拟数据通道。

* **当发布（Publish）指令时：** 没有写任何文件，而是把数据打包，通过电脑的内存（共享内存）或者网络（UDP/TCP 协议）直接发射出去了。
* **底盘驱动节点：** 把频道调到了 `/cmd_vel`。只要一收到这个频道里的无线电信号，它就立刻把数据转成电信号去驱动电机。
* 机器人控制对**实时性**要求极高。如果是写文件、读文件，硬盘的读写速度太慢了，而且频繁擦写硬盘很快就会坏掉。所以 ROS2 全部走内存和网络通信。

#### /scan

`/scan` 是 ROS 中用于**传输激光雷达（LiDAR）扫描数据**的标准话题名称。它的消息类型通常是 `sensor_msgs/msg/LaserScan`。

**具体作用**：激光雷达节点会不断采集周围环境的距离信息，并将每一帧扫描结果（一组按角度排列的距离值）发布到 `/scan` 话题上。其他节点（如避障、建图、定位节点）通过订阅这个话题就能实时获取机器人周围的障碍物分布，从而做出相应决策。

一个典型的 `LaserScan` 消息包含以下核心字段：

- **`angle_min`**、**`angle_max`**：扫描的起始和结束角度（通常激光雷达扫描一个扇形区域，例如 -π/2 到 π/2）。
- **`angle_increment`**：相邻两条激光束之间的角度增量。
- **`ranges[]`**：浮点数数组，每个元素代表对应角度上检测到的障碍物距离（如果超出有效范围或没有检测到，通常设为 `inf` 或 `NaN`）。
- **`intensities[]`**（可选）：每个点的反射强度，可用于区分不同材质。

#### /odom

`/odom` 是 ROS / ROS2 中**机器人里程计信息**的标准话题名称。它发布的消息类型是 `nav_msgs/Odometry`，内容包含：

- **机器人位姿**（位置 x, y, z + 姿态四元数）相对于“里程计坐标系”（odom frame）  
- **机器人速度**（线速度 + 角速度）在机器人坐标系下  
- **协方差矩阵**（表示位姿和速度估计的不确定性）


### 你的那句话的含义
> “Gazebo odometry 转成 ROS2 `nav_msgs/Odometry`”

意思是：Gazebo 仿真器内部计算了机器人模型真实的里程数据（通常基于轮速或完美仿真模型），然后通过 ROS 接口，将数据封装成标准 `nav_msgs/Odometry` 消息，并发布到 `/odom` 话题。这样其他节点（如 `SequenceRunnerNode`）就可以像与真实机器人交互一样订阅 `/odom` 做闭环控制。

### 消息（Message / msgs）

聊天频道里规定的标准。ROS 规定了严格的数据格式。比如速度信息必须用 `geometry_msgs/Twist` 这种格式。

### 通信方式

| 通信方式 | 功能 |
| :--- | :--- |
| **Topic (话题)** | 发布者持续向话题发送数据，订阅者监听并接收。支持多对多通信，适用于传感器数据、状态更新等持续性数据流。 |
| **Service (服务)** | 客户端发送请求，服务端处理后返回响应。一请一答的同步通信，适用于配置变更、查询等短时任务。 |
| **Action (动作)** | 用于长时间运行的任务。客户端请求任务执行，服务端可返回执行反馈（Feedback），任务完成后返回最终结果。 |
| **Parameter (参数)** | 节点的可配置参数，支持运行时修改而无需重启或改代码。用于调整节点行为和配置。 |

### 基础查询

* **列出所有话题**
    ```bash
    ros2 topic list
    ```
    *加上 `-t` 可以同时查看话题的数据类型：* `ros2 topic list -t`

* **查看话题详情**
    *查看谁在发布、谁在订阅以及数据类型：*
    ```bash
    ros2 topic info /topic_name
    ```

### 数据交互

* **打印话题数据内容**
    *实时查看话题里到底传的是什么：*
    ```bash
    ros2 topic echo /topic_name
    ```

* **手动发布消息**
    *常用于调试，模拟传感器或给机器人发指令：*
    ```bash
    ros2 topic pub /topic_name msg_type "{data: value}"
    ```
    *示例（以 1Hz 频率发布字符串）：*
    `ros2 topic pub -r 1 /chatter std_msgs/msg/String "{data: 'Hello ROS2'}"`

### 性能与结构分析

* **查看发布频率**
    *检查传感器数据是否达到预期的刷新率：*
    ```bash
    ros2 topic hz /topic_name
    ```

* **查看消息类型定义**
    *想知道这个话题的数据结构具体长什么样：*
    ```bash
    ros2 interface show <type_name>
    # 例如：ros2 interface show geometry_msgs/msg/Twist
    ```

## 软件结构

### Workspace (工作空间)

通常叫 `dev_ws`。它是最外层的容器，里面包含 `src`（源码）、`build`（中间产物）、`install`（安装后的可执行文件）。

### Package (功能包)

`ros2 pkg create` 是 ROS 2 中的一个命令行工具，用于**自动创建一个新的功能包（Package）的目录结构和基础模板代码**。

在 ROS 2 中，所有的代码（节点、库、配置等）都必须组织在“功能包”里。手动创建这些文件夹和编译文件非常麻烦且容易出错，所以我们使用这个命令：

```bash
ros2 pkg create --build-type <编译类型> <功能包名称>
```

* **`--build-type`**: 指定编译系统。
    * `ament_cmake`: 如果你打算用 **C++** 开发。
    * `ament_python`: 如果你打算用 **Python** 开发。
* **`<package_name>`**: 你给功能包起的名字（建议使用小写字母和下划线）。

当你运行这个命令后，它会自动生成一套标准功能包。

#### 如果是 C++ (`ament_cmake`)

它会为你生成：
* **`CMakeLists.txt`**: 告诉编译器如何构建你的代码。
* **`package.xml`**: 包含功能包的元数据（作者、许可证、依赖项）。
* **`include/`**: 存放头文件的文件夹。
* **`src/`**: 存放源代码的文件夹。

#### 如果是 Python (`ament_python`) 

它会为你生成：
* **`setup.py`**: 描述如何安装和运行 Python 脚本。
* **`setup.cfg`**: 配置文件。
* **`package.xml`**: 依赖管理。
* **`resource/`**: 资源文件占位。
* **`与包同名的文件夹/`**: 存放 Python 脚本的地方。

### 创建节点

把节点代码放在 `src/`（C++）或与包同名的文件夹（Python）里。每个节点都是一个独立的可执行程序。

### 登记（entry point）

在功能包的配置文件里（`CMakeLists.txt` 或 `setup.py`），你需要告诉系统你的节点叫什么名字，以及它是哪个源文件编译出来的。

例如，在setup.py里，有类似这样的代码：

```python
entry_points={
    'console_scripts': [
        'track_node = follow_track.track_node:main'
    ],
}
```

这行代码的意思是：当运行 `track_node` 这个命令时，去调用 `follow_track` 包下的 `track_node.py` 里的 `main` 函数。

## 编译 

### 进入工作空间

在执行构建命令之前，你必须处于工作空间的**根目录**（通常叫 `dev_ws` 或 `ros2_ws`）。

```bash
cd ~/ros2_ws
```

### 执行构建命令

使用 `colcon build` 来编译整个工作空间：
```bash
colcon build
```

参数：

* **`--symlink-install`**：
    使用软链接安装。这样当你修改 **Python 脚本**或**配置文件**（如 launch 文件）时，不需要重新 build 就能直接运行新代码。
* **`--packages-select <package_name>`**：
    只编译特定的包。如果你的工作空间很大，这能节省大量时间。
* **`--continue-on-error`**：
    即便某个包报错，也继续编译其他不相关的包。

### 环境生效 (Source)

```bash
# 在当前工作空间下
source install/setup.bash
```

## 运行

运行节点：
```bash
ros2 run <package_name> <executable_name>
```

## 核心机制

### DDS (Data Distribution Service)

这是 ROS 2 区别于 ROS 1 的最大特点。ROS 2 不再自己写底层的网络传输，而是利用了工业级的 **DDS 标准**。
* **作用**：它负责在复杂的网络环境下（甚至是多台电脑之间）寻找节点、保证数据不丢失、处理延时。
* **Discovery (发现机制)**：当你启动一个节点，DDS 会自动在局域网内广播：“我叫 A，我有一个名叫 /chatter 的话题”。其他节点会自动匹配上。

### The RMW (ROS Middleware Interface)

为了不被某一家 DDS 厂商绑死，ROS 2 加了一层“适配器”。无论底层用的是 eProsima Fast DDS 还是 Cyclone DDS，对你写的业务代码来说都是透明的。
