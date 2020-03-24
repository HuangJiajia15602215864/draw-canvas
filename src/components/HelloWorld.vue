<template>
  <div class="draw">
    <h2>canvas画图</h2>
    <canvas id="canvas" @touchstart="touchstart" @touchmove="touchmove"></canvas>
  </div>
</template>

<script>
  export default {
    data() {
      return {
        ctx: '',
        point: {
          x: 0,
          y: 0
        },
        distance: {
          start: 0,
          stop: 0
        },
        origin: {
          x:0,y:0
        },
        scale: 1,
        length: 0,
        isCanScale : false,
        vendors:{}
      }
    },
    mounted() {
      this.init();
      this.vendors = this.vendor();
    },
    methods: {
      // 初始化
      init() {
        const canvas = document.getElementById('canvas')
        const width = canvas.offsetWidth
        const height = canvas.offsetHeight
        this.ctx = canvas.getContext('2d'); // 创建 context 对象：
        canvas.width = width; // 创始化，重置宽高
        canvas.height = height
        this.ctx.strokeStyle = '#000'; // 设置画笔颜色
        this.ctx.lineWidth = 1; // 设置画线宽度
      },
      // 获取当前绘制的坐标
      absolutePoint(event) {
        const touch = event.targetTouches[0]
        const canvas = document.getElementById('canvas')
        const react = canvas.getBoundingClientRect(); //用于获取元素相对于视口的位置
        this.point = {
          x: touch.pageX - react.left,
          y: touch.pageY - react.top
        }
      },
      // 根据坐标进行绘制
      draw(event) {
        this.ctx.lineTo(this.point.x, this.point.y) // 定义坐标
        this.ctx.stroke() // 使用 stroke() 方法来绘制线条
      },
      // 开始触摸
      touchstart(event) {
        console.log('开始触摸')
        console.log(event.touches.length == '1')
        this.length = event.touches.length == '1'
        if (event.touches.length == '1') { // 绘制
          this.absolutePoint(event)
          this.ctx.moveTo(this.point.x, this.point.y); // 设置开始画图的起始坐标
        } else { // 缩放
          event.preventDefault();
          this.distance.start = this.getDistance({
            x: event.touches[0].screenX,
            y: event.touches[0].screenY
          }, {
            x: event.touches[1].screenX,
            y: event.touches[1].screenY
          });
        }
      },
      // 触摸移动
      touchmove(event) {
        if (event.touches.length == '1') { // 绘制
          this.absolutePoint(event)
          this.draw(event)
        } else { // 缩放
          event.preventDefault();
          this.origin = this.getOrigin({
            x: event.touches[0].pageX,
            y: event.touches[0].pageY
          }, {
            x: event.touches[1].pageX,
            y: event.touches[1].pageY
          });
          this.distance.stop = this.getDistance({
            x: event.touches[0].screenX,
            y: event.touches[0].screenY
          }, {
            x: event.touches[1].screenX,
            y: event.touches[1].screenY
          });
          this.scale =this.distance.stop /this.distance.start;
          this.isCanScale = true;
          this.setScaleAnimation(this.scale, true);
        }
      },
      // 获取中心点
      getOrigin(first, second) {
        return {
          x: (first.x + second.x) / 2,
          y: (first.y + second.y) / 2
        };
      },
      // 计算距离
      getDistance(start, stop) {
        return Math.sqrt(Math.pow((stop.x - start.x), 2) + Math.pow((stop.y - start.y), 2));
      },
      // 缩放动画
      setScaleAnimation(scale, animation) {
        console.log('进入缩放动画')
        console.log(scale)
        var transition_animation = '';
        var x, y;
        if (!this.isCanScale) {
          return;
        }
        this.isCanScale = false;
        if (animation) {
          transition_animation = 'none';
        } else {
          transition_animation = this.vendors.TRANSFORM_PROPERTY + ' 0.3s ease-out';
        }
        const canvas = document.getElementById('canvas')
        canvas.style[this.vendors.TRANSITION] = transition_animation;
        x = this.origin.x + (-this.origin.x) * scale;
        y = this.origin.y + (-this.origin.y) * scale;
        console.log(x,y)
        canvas.style[this.vendors.TRANSFORM] = 'matrix(' + scale + ', 0, 0, ' + scale + ', ' + x + ', ' + y + ')';
      },
      vendor() {
			var TRANSITION = 'transition';
			var TRANSITION_END = 'transitionend';
			var TRANSFORM = 'transform';
			var TRANSFORM_PROPERTY = 'transform';
			var TRANSITION_PROPERTY = 'transition';			
			if (typeof document.body.style.webkitTransform !== undefined) {
				TRANSFORM = 'webkitTransform';
				TRANSITION = 'webkitTransition';
				TRANSITION_END = 'webkitTransitionEnd';
				TRANSFORM_PROPERTY = '-webkit-transform';
				TRANSITION_PROPERTY = '-webkit-transition';
			}
			return {
				TRANSFORM: TRANSFORM,
				TRANSITION: TRANSITION,
				TRANSITION_END: TRANSITION_END,
				TRANSFORM_PROPERTY: TRANSFORM_PROPERTY,
				TRANSITION_PROPERTY: TRANSITION_PROPERTY
			};
		}

    }
  }
</script>
<style scoped>
  #canvas {
    height: 300px;
    width: 80%;
    border: 1px solid #ddd;
    box-shadow: 2px 2px 2px 2px #eee;
  }

</style>
