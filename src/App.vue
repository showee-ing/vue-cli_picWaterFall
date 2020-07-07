<template>
  <div class="v-waterfall-content" id="v-waterfall">
    <div v-for="(img,index) in waterfallList"
         :key="index"
         class="v-waterfall-item"
         :style="{top:img.top+'px',left:img.left+'px',width:ImgWidth+'px',height:img.height}">
      <img :src="img.src" alt="">
    </div>
  </div>
</template>

<script>
    export default {
        name: "pic-waterfall",
        data(){
            return {
                waterfallList:[],
                imgArr:[
                    require('./assets/images/1 (13).jpeg'),
                    require('./assets/images/1 (12).jpeg'),
                    require('./assets/images/1 (11).jpeg'),
                    require('./assets/images/1 (10).jpeg'),
                    require('./assets/images/1 (9).jpeg'),
                    require('./assets/images/1 (8).jpeg'),
                    require('./assets/images/1 (7).jpeg'),
                    require('./assets/images/1 (6).jpeg'),
                    require('./assets/images/1 (5).jpeg'),
                    require('./assets/images/1 (4).jpeg'),
                    require('./assets/images/1 (3).jpeg'),
                    require('./assets/images/1 (2).jpeg'),
                    require('./assets/images/1 (1).jpeg'),
                    require('./assets/images/1 (1).gif'),
                    require('./assets/images/1.jpg'),
                    require('./assets/images/2.jpg'),
                    require('./assets/images/3.jpg'),
                    require('./assets/images/4.jpg'),
                    require('./assets/images/5.jpg'),
                    require('./assets/images/6.jpg'),
                    require('./assets/images/7.jpg'),
                    require('./assets/images/8.jpg'),
                    require('./assets/images/9.jpg'),
                    require('./assets/images/10.jpg'),
                    require('./assets/images/11.jpg'),
                    require('./assets/images/12.jpg'),
                    require('./assets/images/13.jpg'),
                    require('./assets/images/14.jpg'),
                    require('./assets/images/15.jpg'),
                    require('./assets/images/16.jpg'),
                    require('./assets/images/17.jpg'),
                    require('./assets/images/18.jpg'),
                    require('./assets/images/19.jpg'),
                    require('./assets/images/20.jpg'),
                ],
                ImgWidth:'',
                ImgCol:5,
                ImgRight:10,
                ImgBottom:10,
                deviationHeight:[],
                imgList:[],
                screenWidth:document.body.clientWidth,
            }
        },
        created() {
            for (let i = 0;i < this.imgArr.length;i++){
                this.imgList.push(this.imgArr[i]);
            }
        },
        mounted(){
            this.calculationWidth();
            const that = this;
            window.onresize = () => {
                return (() => {
                    window.screenWidth = document.body.clientWidth
                    that.screenWidth = window.screenWidth
                })()
            }
        },
        watch:{
            screenWidth(val){
                // 为了避免频繁触发resize函数导致页面卡顿，使用定时器
                if(!this.timer){
                    // 一旦监听到的screenWidth值改变，就将其重新赋给data里的screenWidth
                    this.screenWidth = val
                    this.timer = true
                    let that = this
                    setTimeout(function(){
                        that.calculationWidth();
                        that.timer = false
                    },400)
                }
            },
        },
        methods:{
            //计算每个图片的宽度
            calculationWidth(){
                this.ImgWidth = (this.screenWidth-this.ImgRight*this.ImgCol)/this.ImgCol;
                //初始化偏移高度数组
                this.deviationHeight = new Array(this.ImgCol);
                for (let i = 0;i < this.deviationHeight.length;i++){
                    this.deviationHeight[i] = 0;
                }
                this.imgPreloading()
            },
            //图片预加载
            imgPreloading(){
                this.waterfallList=[];
                for (let i = 0;i < this.imgList.length;i++){
                    let aImg = new Image();
                    aImg.src = this.imgList[i];
                    aImg.onload = aImg.onerror = ()=>{
                        let imgData = {};
                        //按比例计算图片高度
                        imgData.height = this.ImgWidth/aImg.width*aImg.height;
                        imgData.src = this.imgList[i];
                        this.waterfallList.push(imgData);
                        this.rankImg(imgData);
                    }
                }
            },
            //瀑布流布局
            rankImg(imgData){
                let {ImgWidth,ImgRight,ImgBottom,deviationHeight} = this;
                let minIndex = this.filterMin();
                imgData.top = deviationHeight[minIndex];
                imgData.left = minIndex*(ImgRight+ImgWidth);
                deviationHeight[minIndex] += imgData.height + ImgBottom;
            },
            //找到最短的列并返回下标
            filterMin(){
                const min = Math.min.apply(null, this.deviationHeight);
                return this.deviationHeight.indexOf(min);
            },
        }
    }
</script>