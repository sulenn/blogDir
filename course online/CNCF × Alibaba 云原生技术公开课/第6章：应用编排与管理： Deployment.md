# 第六章：应用编排与管理： Deployment

**Deployment**:管理部署发布的控制器

1. 定义一组 Pod 的期望数量，controller 会维持 Pod 数量与期望数量一致

2. 配置 Pod 发布方式，controller 会按照给定策略更新 Pod，保证更新过程中不可用的 Pod 数量在限定范围内

3. 如果发布有问题，支持“一键”回滚

**Deployment语法**：

![image.png](https://ws1.sinaimg.cn/large/006alGmrgy1g8ruuz8ffrj30zd0kltj6.jpg)

**管理模式**：

![image.png](https://ws1.sinaimg.cn/large/006alGmrgy1g8rv1xc93vj30yd0hf791.jpg)

**扩容模拟**：

![image.png](https://ws1.sinaimg.cn/large/006alGmrgy1g8rv338uqnj31130idwlq.jpg)

**发布模拟**：

![image.png](https://ws1.sinaimg.cn/large/006alGmrgy1g8rv456c8jj312b0iqti9.jpg)

**回滚模拟**：

![image.png](https://ws1.sinaimg.cn/large/006alGmrgy1g8rv4kgehgj31300h3ait.jpg)