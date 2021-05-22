# 使用DQN训练AI玩合成大西瓜
DS&BA项目课程，决策与优化
使用强化学习中的DQN方法训练Agent玩合成大西瓜，神经网络使用Resnet
训练速度较慢，目前仍在训练中……

非常感谢Sharpiless，主要代码基于其修改：https://github.com/Sharpiless/play-daxigua-using-Reinforcement-Learning

## 版本需求
Python 3.7


PaddlePaddle 1.8
* Windows：
  * GPU版本：pip install paddlepaddle-gpu==1.8.5.post107 -f https://paddlepaddle.org.cn/whl/stable.html
  * CPU版本：pip install paddlepaddle==1.8.5 -i https://mirror.baidu.com/pypi/simple
* 其他版本网址：https://www.paddlepaddle.org.cn/install/old?docurl=/documentation/docs/zh/install/pip/windows-pip.html


Parl 1.3.1 

## 文件说明
包含文件与文件夹：
* res 文件夹 水果贴图
* Fruit.py 生成水果及水果贴图
* Game.py 游戏相关基础设置，包括生成水果，水果碰撞等处理
* State.py 主要是游戏进行时next的控制
* Main.py 自己玩合成大西瓜 可以调整FPS
* resnet.py 定义的Resnet
* train_paddle.py 使用paddle训练


训练后生成文件与文件夹：
* learn：保存的学习网络模型
* predict：保存的预测网络模型
* model：保存模型的其他参数
* rpm.pkl：保存经验池（20000大小的经验池约为5.77GB**请注意，该文件只会自动读取rpm.pkl，如要读取之后存下的rpm版本，需手动设置**）
* score.csv：训练时得分记录
* test_scores.csv：测试时得分记录

## 部分参数说明
训练相关参数（均位于train_paddle.py）：
* 训练频率：LEARN_FREQ
* 经验池大小：MEMORY_SIZE
* 预热经验池大小：MEMORY_WARMUP_SIZE（至少大于BatchSize）
* 批次经验数量：BATCH_SIZE
* 学习率：LEARNING_RATE
* 折扣因子：GAMMA
* 最大回合数：max_episode（几乎不会用到）
  每训练25个回合做一次测试，一次测试包含5个回合
  
游戏速度相关参数（用于加速训练，随意调整可能会出现穿模等奇怪的Bug）：
* FPS（Game.py）：控制FPS，提升FPS会降低速度，但是也会降低穿模的可能（文件中训练时设置为80）
* gravity（State.py）：控制**训练时**下落加速度，增大可以提升速度，也会增加穿模可能（提升不明显，文件中训练时设置为8000）
* gravity（Main.py）：控制**自行游玩**下落加速度，增大可以提升速度，也会增加穿模可能
* create_time（State.py）：控制**训练时**创造水果的时间，实际所需帧数为FPS\*create_time（文件中训练时设置为0.1）
* create_time（Main.py）：控制**自行游玩**创造水果的时间，实际所需帧数为FPS*create_time
* next函数（State.py）：此处控制每隔多久返回一个next，由self.FPS * k来控制

## 代码方面工作
游戏差异：
	* 宽长从400 800 改为568 875
	* 水果生成位置改为0.1 Height
	* 死线改为0.15 Height
	* FPS 80 放置一次后等待一秒 重力加大为12800
	* 生成水果包括猕猴桃
	* 生成位置在死线下
  
bug修复：
	* 修复了多个相同水果碰撞全部升级的bug
	* 修复了训练输入维度不一致的bug
	* 修复了虽然会reset但是永远是alive，run_episode函数不会跳出的bug
	* 修复了水果以中心位置触线计算死亡的bug，改为考虑半径来计算死亡
	* 修复了第一下丢水果一定会丢在边上的bug
 * 动作位置会出界

进行了游戏速度，超参数的调整，以及增加两个网络参数的保存与读取函数，自动化读取模型等操作
