<template>
  <div class="collision">
    <div class="container" ref="container"></div>
  </div>
</template>

<script setup lang="ts">
import { onMounted, ref } from 'vue'


interface BasicInfo {
  x: string,
  y: string,
  size: string
}

// 小球数据，初始位置，大小等
const Boxs: BasicInfo[]  = [
  {
    x: '300px',
    y: '200px',
    size: '100px'
  },
  {
    x: '900px',
    y: '300px',
    size: '100px'
  },
  {
    x: '47px',
    y: '390px',
    size: '100px'
  }

]

const maxWidth = window.innerWidth
const maxHeight =  window.innerHeight

// 小球初始化，随机生成方向与速度
class Ball {
  x:number 
  y:number 
  centerX: number 
  centerY: number 
  radio: number
  speed: number 
  movex: number
  movey: number
  dom: HTMLElement = document.createElement('div')
  
  constructor(basicInfo: BasicInfo){
    this.x = parseInt(basicInfo.x)
    this.y = parseInt(basicInfo.y)
    this.centerX = (this.x + parseInt(basicInfo.size)) / 2
    this.centerY = (this.y + parseInt(basicInfo.size)) / 2
    this.radio = parseInt(basicInfo.size) / 2
    this.speed = 2 + Math.floor(Math.random() * 4);//随机速度
    this.movex = Math.floor(Math.random() * 2);//x轴移动方向
    this.movey = Math.floor(Math.random() * 2);//y轴移动方
    
  }
// 碰撞检测
  crash(other: Ball) {
    if(this !== other) {
      //圆心距 求两个点之间的距离,开平方
      const distence = Math.sqrt((this.centerX - other.centerX)**2 + (this.centerY - other.centerY)**2 );
      // 两球相切时的球心距离
      const dima = this.radio + other.radio
      // 如果小球没有重合
      if(distence <= dima){
        if(this.centerX > other.centerX){
          this.movex = 1
          this.movey = this.centerY - other.centerY > 0 ? 1: 0
        }
        else if(this.centerX < other.centerX){
          this.movex = 0
          this.movey = this.centerY - other.centerY > 0 ? 1 : 0
        }
        else{
          this.movey = this.centerY - other.centerY > 0? 1: 0
        }
      }

    }
  }

  // 小球移动起来
  move() {
    if(this.movex === 1){
      this.x += this.speed;
      if (this.x + this.speed >= maxWidth) {//防止小球出界
        this.x = maxWidth;
        this.movex = 0;//小球运动方向发生改变
      }
    }
    else {
      this.x -= this.speed; // 1和0表示正反方向
      if (this.x - this.speed <= 0) {
        this.x = 0;
        this.movex = 1;
      }
    }
    if(this.movey === 1){
      this.y += this.speed;
      if (this.y + this.speed >= maxHeight) {
        this.y = maxHeight;
        this.movey = 0;
      }
    }
    else {
      this.y -= this.speed;
      if (this.y - this.speed <= 0) {
        this.y = 0;
        this.movey = 1;
      }
    }
    this.dom.style.left = this.x + 'px';//小球相对于屏幕的位置
    this.dom.style.top = this.y + 'px';
  }
}



const arrBall = ref<Ball[]>([])
const init =() => {
  Boxs.forEach(item => {
    arrBall.value.push(new Ball(item))
  })
}



onMounted(() => {
  const boxs = document.getElementsByClassName('box');
})

</script>

<style scoped lang="scss">
.collision {
  position: relative;
  display: block;
  width: 800px;
  height: 640px;
  border: 1px solid #333;
  background-color: #eee;

  .box {
    position: absolute;
    display: block;
    border: 2px solid #333;
    width: 50px;
    height: 50px;
  }
}
</style>