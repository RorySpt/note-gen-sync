> [动画蓝图](./动画蓝图.md)

## 动画变形

> 需要开启 Animation Warping插件

开启该插件之后可以使用变形函数

### OrientationWraping

可以旋转下部肢体，使动画的方向匹配移动方向

![7007221d-143f-47da-a950-9a99f99357b7.png](https://raw.githubusercontent.com/RorySpt/note-gen-image-sync/main/7007221d-143f-47da-a950-9a99f99357b7.png)

### StrideWarping

可以使动画的脚步匹配移动速度，防止滑步

![image.png](https://raw.githubusercontent.com/RorySpt/note-gen-image-sync/main/d84c551c-06f4-4d18-952c-aed4aef02cc6.png)

## 转向时身体倾斜

使用叠加动画的方式将倾斜动画叠加到原有的动画上面，倾斜方向和倾斜角度使用混合动画的方式来控制

倾斜动画只需要一针，需要在附加设置中指定AdditiveAnimType为LocalSpace（或者MeshSpace，按需要），然后BasePoseType设置为SelectedAnimationFrame，BasePoseAnimation设置为倾斜动画的中间动画（即未倾斜的动画），用以计算叠加量
![image.png](https://raw.githubusercontent.com/RorySpt/note-gen-image-sync/main/9d81e024-32e8-497a-8997-74070bc451d9.png)
![image.png](https://raw.githubusercontent.com/RorySpt/note-gen-image-sync/main/d3fcd23c-ebc2-466d-b159-1e43f2e96398.png)

## 距离匹配

> 需要开启AnimationLocomotionLibrary，

### 动画准备

创建曲线压缩设置，修改为UniformIndexable

将需要距离匹配的动画（如移动停止动画）的压缩设置中的曲线压缩设置改为新建的曲线压缩设置

在动画数据修饰符选项卡中选择DistanceCurveModifier为动画添加距离曲线
![image.png](https://raw.githubusercontent.com/RorySpt/note-gen-image-sync/main/655e104a-caad-493e-930f-14b64d1e6ddd.png)

### 播放动画

要使用距离匹配需要使用SequenceEvaluator节点来驱动动画，它允许指定播放动画的当前帧

![image.png](https://raw.githubusercontent.com/RorySpt/note-gen-image-sync/main/2f939843-2630-4d3f-8d73-fa784fde1b6b.png)

使用PredictGroundMovementStopLocation函数节点计算停止距离

![image.png](https://raw.githubusercontent.com/RorySpt/note-gen-image-sync/main/3d4f6ed5-7817-447a-b65c-b57ce39a30c0.png)

将停止距离传入DistanceMatchToTarget节点，以根据当前到停止点的距离以播放正确的停止动画帧

![image.png](https://raw.githubusercontent.com/RorySpt/note-gen-image-sync/main/803298d3-31ef-4a24-abf7-559bd5cfb589.png)

