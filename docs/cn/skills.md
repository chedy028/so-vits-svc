# 技巧
如果前面的效果已经满意，或者没看明白下面在讲啥，那后面的内容都可以忽略，不影响模型使用。(这些可选项影响比较小，可能在某些特定数据上有点效果，但大部分情况似乎都感知不太明显)，
## 自动 f0 预测
4.0 模型训练过程会训练一个 f0 预测器，对于语音转换可以开启自动音高预测，如果效果不好也可以使用手动的，但转换歌声时请不要启用此功能！！！会严重跑调！！
+ 在 inference_main 中设置 auto_predict_f0 为 true 即可
## 聚类音色泄漏控制
介绍：聚类方案可以减小音色泄漏，使得模型训练出来更像目标的音色（但其实不是特别明显），但是单纯的聚类方案会降低模型的咬字（会口齿不清）（这个很明显），本模型采用了融合的方式，
可以线性控制聚类方案与非聚类方案的占比，也就是可以手动在 "像目标音色" 和 "咬字清晰" 之间调整比例，找到合适的折中点。

使用聚类前面的已有步骤不用进行任何的变动，只需要额外训练一个聚类模型，虽然效果比较有限，但训练成本也比较低
+ 训练过程：
  + 使用 cpu 性能较好的机器训练，据我的经验在腾讯云 6 核 cpu 训练每个 speaker 需要约 4 分钟即可完成训练
  + 执行 python cluster/train_cluster.py ，模型的输出会在 logs/44k/kmeans_10000.pt
+ 推理过程：
  + inference_main 中指定 cluster_model_path
  + inference_main 中指定 cluster_infer_ratio，0 为完全不使用聚类，1 为只使用聚类，通常设置 0.5 即可