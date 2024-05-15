## [Datasets](https://paperswithcode.com/datasets?task=3d-object-detection)

### Background Knowledge

#### Stereo
In 3D object detection, stereo refers to the use of multiple cameras or images to create a three-dimensional representation of a scene. This allows for the detection and localization of objects in a given environment. Stereo 3D object detection can be used in a variety of applications, such as robotics and autonomous vehicles, to navigate and interact with their surroundings.

Stereo似乎强调的是多个角度，类似于音响，但是在**更细的分类中stereo特指使用两个摄像头/传感器获取数据**。

#### Multiview
在更细的划分中，multiview应该指的是使用超过两个传感器来获取数据，因此比stereo的精度应该更高，但是比stereo计算更加耗时/

#### Flow
Flow 3D object detection is a method of identifying and locating objects in a three-dimensional space. It typically involves the use of computer vision algorithms and specialized hardware to detect and track objects in real time. The goal of flow 3D object detection is to enable machines and algorithms to understand and interpret the three-dimensional world in the same way that humans do. This technology has a wide range of applications, including robotics, surveillance, and driver assistance systems.

#### Stereo vs Flow
Stereo methods and flow methods are both techniques used in computer vision for object detection and recognition. The main difference between the two is the way they perceive and interpret the environment.

**Stereo** methods use **two cameras or image sensors** to capture images of a scene from slightly different angles. By comparing the images, a stereo algorithm can calculate the relative depth of objects in the scene and create a three-dimensional representation of the environment.

**Flow** methods, on the other hand, use **a single camera or image sensor** to capture images of a scene **over time**. By tracking the movement of objects in the scene from one frame to the next, a flow algorithm can calculate the speed and direction of each object and create a three-dimensional representation of the environment.

In general, stereo methods are more accurate and can provide higher-resolution 3D reconstructions, but they require specialized hardware and can be computationally intensive. Flow methods are less accurate but are simpler to implement and can be used with a single camera.

### Datasets Overview
| Datasets                                       | Stereo | Optical Flow | Scene Flow | Odometry | Depth | Obj. Det. | Tracking | Semantic |
| ---------------------------------------------- | ------ | ------------ | ---------- | --------------- | ----- | --------- | -------- | -------- |
| [KITTI](http://www.cvlibs.net/datasets/kitti/) | ✔      | ✔            | ✔          | ✔               | ✔     | ✔         | ✔        | ✔        | 
|                                                |        |              |            |                 |       |           |          |          |

一个简单的对比：[The Robust Vision Challenge 2022](http://www.robustvision.net/)


### [The KITTI Vision Benchmark Suite](https://www.cvlibs.net/datasets/kitti)

[Papers With Code上的介绍](https://paperswithcode.com/dataset/kitti)



笔记，我认为，人类首先识别一个物体的类别，然后再去分析这个物体的形态和方向，最后判断物体空间中的位置。

人必须要有一个物体特征的先验认知（大小，形状，颜色，材质）才能识别目标场景的具体情况。而先验特征肯定需要从3D场景中学习得到（即物体的特征表示）。通过将特征表示与待测图像进行比对，判断场景中是否有该物体。

在确定场景中存在一个物体之后，模型需要继续通过先验来确定该物体的空间位置，比如远近，大小等等信息，最终得到整个场景的所有分析。

而这理论上可以通过单目图像来解决。

双目图像理论上可以彻底分析出整个空间的位置信息，类似于人眼。