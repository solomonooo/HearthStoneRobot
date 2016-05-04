# HearthStoneRobot
炉石传说机器人
######准备写个炉石传说自动战斗AI,准备采用图像识别技术进行模拟
    一些问题：
    1.图像的采集
    2.图像的查找比较
    3.控制鼠标操作
	4.识别卡牌费用
test 1 图像的采集<br>
思路：<br>
1. 获取炉石传说程序的句柄<br>
2.通过句柄获取窗口<br>
3.获取窗口像素<br>
查找资料并实验得到有两种方法：<br>
**方法一：获取窗口后，使用bitblt函数进行窗口函数拷贝，缺点是窗口不能有遮挡**<br>
**方法二：使用printWindows 函数获取窗口图像，缺点是该函数是xp下专用，win10上对于有些窗口会无法获取图像，得到黑屏**<br>
经测试，炉石传说可以使用第二张方法，代码见`test/getWindowsImage.cpp`<br>

test 2 图像的查找比较
思路：<br>
1.获取当前画面<br>
2.读取待比较bmp<br>
3.比较<br>
已知方法：<br>
**方法一：逐像素，判断rgb差值，缺点慢，优点准确 代码见** `test/findSubImage.cpp`<br>
**方法二:采用相似图像比较分方法，比如pHash等，未测试**<br>

test 3 控制鼠标操作<br>
思路：<br>
1.获取目标窗口句柄<br>
2.给目标窗口发送消息<br>
3.获取鼠标在窗口的位置<br>
已知方法：<br>
**方法一：使用`sendMessage` 或者`postMessage`函数向炉石程序发送消息**<br>
**方法二：使用`mouse_event`模拟鼠标行动**<br>
经测试，炉石传说对方法一的消息不响应，即使设置窗口为`SetForegroundWindow`<br>
故采用第二种方式，第二种方式要求窗口在最前面，且固定位置，因此将窗口移到左上角，固定分辨率。代码见`controlMouse.cpp`<br>
test 4 识别卡牌费用<br>
有2种方法，代码见`test/HStest.cpp`：
方法一：使用图像查找比较的方法。经测试，有如下问题：    
        1.图片大小不好统一   
        2.图片背景因为有粒子的变化，变动较大       
        3.费用在某些条件下会更改并改变颜色     
方法二：使用数字识别的方法。     
有2个场景需要识别费用：      
场景一：发牌换牌阶段     
此阶段，背景为黑色，便于分离。流程：灰度化，阈值成二值图像，轮廓提取，测试得到卡牌的轮廓范围，近似矩形，得到卡牌数（得到先手还是后手），根据轮廓得到每个卡牌，对每个卡牌的左上角进行灰度处理，阈值化，轮廓提取，得到数字图像。使用数字识别的方法进行判断。    https://github.com/fztfztfzt/HearthStoneRobot/blob/master/image/scene1_getNum_1.bmp
结果：![提取卡牌](https://github.com/fztfztfzt/HearthStoneRobot/blob/master/image/scene1_getRect.bmp)   
![提取数字](https://github.com/fztfztfzt/HearthStoneRobot/blob/master/image/scene1_getNum_2.bmp)     
场景二：出牌场景     
此阶段，背景色彩多样，不能采用场景一的方式，经观察，发现数字部分为白色，且形状接近矩形（宽小于高），测试流程：     
遍历图像，白色部分不变，其他变为黑色，轮廓提取，去除过小的，近似矩形，去除宽大于等于高的，剩下部分均为数字。     
结果：![提取数字](https://github.com/fztfztfzt/HearthStoneRobot/blob/master/image/scene2_getNum.bmp)   

