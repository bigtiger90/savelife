<template>
  <div style="width:400px">
    <img :src="image" width='750' height='530' class="img" alt="" />
    <canvas id="mycanvas" width='750' height='530' class="my-canvas"></canvas>
  </div>
</template>

<script lang="ts">
export default {
  data() {
    return {
      image: "",
      nane: '张三',
      zhengshu: '3333333'
    };
  },
  mounted() {
    console.log("mounted");
    this.drawPhoto();
  },
  methods: {
    drawPhoto() {
      let canvas = document.getElementById("mycanvas");//创建canvas
      let context = canvas.getContext("2d"); //创建画布
      let img = new Image(); //因为拿不到图片静态资源，所以创建图片标签
      img.setAttribute("crossOrigin", "anonymous"); //解决图片跨域问题,也要放到赋值前，否ze大部分浏览器会报错
      img.src = "/src/assets/test0.png"
      //加载图片
      img.onload = () => {
        canvas.setAttribute("width", img.width)
        canvas.setAttribute("height", img.height)
        //绘制图片
        console.log(img.width, img.height);
        context.drawImage(img, 0, 0, img.width, img.height);
        //字体大小
        context.font = "90px Arial";
        //字体文字，显示位置  图片上需要n个文字就写n个context.fillText(文字,上下,左右);
        context.fillText(this.nane, 420, 1150);
        context.fillText(this.zhengshu, 2700, 1020);
        //合成图片
      };
    },
  }
};
</script>

<style lang="scss" scoped>
.my-canvas{
    position: fixed;
    top: 0px;
    left: 0px;
    z-index: -99999999999;
    opacity: 0;
  }
.img{
  width: 100%;
  height: auto;
}
</style>