# draw-canvas
利用canvas实现移动端画图（只需要能用手指画轨迹线，可以放大缩小 ）

# 思路
1、利用canvas标签进行绘图，添加touchstart、touchmove两个移动端触摸事件    
2、初始化数据：ctx(content对象)、point（点）、distance（距离）、origin（缩放中心点）