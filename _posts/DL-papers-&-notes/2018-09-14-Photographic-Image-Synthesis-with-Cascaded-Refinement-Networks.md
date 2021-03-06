###文章亮点
使用VGG结构信息之差作为损失函数，解决了一对多生成时损失函数选择的问题

###工作亮点：
1.生成效果好（可以说是惊艳了）
2.不用GAN，结构简单，就基础的CNN组合
![感受一哈](https://upload-images.jianshu.io/upload_images/9165719-08ea7257c48f6807.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

###核心任务：
给定上图左边的masks，生成右边的图像，尽可能真实，可以理解为semantic segmentation的逆任务

在作者做实验的过程中，发现实现photorealism（照片写实主义）的关键点如下：
Photorealism<sub>照片写实主义：在文中你可以理解为，尽可能生成真实的图像</sub>
1.Global Coordination：有些图形结构不是局域性的，论文中说到，如果左边的车尾灯是红的，那么右边的车尾灯也应该是红色的才对，这就是对称性，是整张图片的信息，这种情况有很多，这种非局域性的特征使用了多分辨率精细化网络（multi-resolution refinement）来解决,从低分辨率一步步走向高分辨率（第一级网络只有4*8的分辨率），使得全局特征能够在精化过程中得到保留
2.High Resolution：如果你要使得图片足够真实，那么分辨率应该尽量高一些
3.Memory：你想要生成足够真实的图像，那么你的网络应该包含很多训练数据集的知识（任务是画图，神经网络当然要多记住几张图片）

###结构
结构就不多解释了，肯定是要看论文的
简单而言就是，第一层是4x8的分辨率做精细化，层数提升，分辨率x2（原文是doubled）

###训练：
给定数据集D{（I,L）},I 是图像，L是semantic layout包含很多masks
有趣的是，对于每一对（I,L），I只能作为参考，而不能作为ground truth，因为这是一个一对多的问题，给定L，应该可以生成很多不同的I，这就使得不能简单地用普通的损失函数去解决问题，作者给出的方法是，抓结构，VGG能够获得很多图片的结构信息，在艺术风格迁移上就有过相关的实践

损失函数由VGG某些选定层的feature map之差构成
再优化:一次生成一堆图片，选择Loss最少的图片作为输出
再再优化：一次生成一堆图片，选择每一类mask中loss最少的mask组合起来生成图片


