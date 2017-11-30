<template>
    <div class="image-preview-wrapper" v-if="showPreview" @scroll="fnScroll">
        <div class="preview-body">
            <div class="preview-image" :class="{'active': index == currIndex, 'image-prev': index < currIndex, 'image-next': index > currIndex }" v-for="(item, index) in imageList" :key="index" :style="'background-image: url('+item+')'"></div>
        </div>
        <div class="btn-preview preview-left" @click="changeIndex(currIndex-1)" v-show="imageList.length > 1">
            <i class="el-icon-arrow-left preview-icon"></i>
        </div>
        <div class="btn-preview preview-right" @click="changeIndex(currIndex+1)" v-show="imageList.length > 1">
            <i class="el-icon-arrow-right preview-icon"></i>
        </div>
        <div class="preview-tail">
            <div class="thumbnail-list clearfix" :style="'transform: translateX(-'+ 83*currIndex + 'px);'">
                <div class="thmub-unit" v-for="(item, index) in imageList" :key="index" :style="'background-image: url('+item+')'" @click.prevent.stop="changeIndex(index)"></div>
            </div>
            <div class="thumb-pointer" v-show="imageList.length > 0">
            </div>
        </div>
        <div class="preview-close" @click.prevent.stop="close">
            <i class="el-icon-close"></i>
        </div>
    </div>
</template>

<script>
    export default {
        components: {},
        data() {
            return {
                showPreview: false,
                currIndex: 0,
                imageList: [],
                lastTime: 0,
                timer: null
            };
        },
        watch: {
            showPreview(val){
                if(val){
                    document.documentElement.style.overflow = "hidden";
                }else{
                    document.documentElement.style.overflow = "auto";
                }
            }
        },
        methods: {
            show(imgList){
                if(imgList instanceof Array){
                    if(imgList.length <= 0){
                        this.$message({
                            type: 'warning',
                            message: '暂无可预览图片',
                            center: true
                        });
                        return ;
                    }
                    this.imageList = imgList;
                    this.showPreview = true;
                }else{
                    this.$message({
                        type: 'warning',
                        message: '图片格式不正确',
                        center: true
                    });
                }
                /*注册事件*/
                if(document.addEventListener){
                    document.addEventListener('DOMMouseScroll' , this.fnScroll, false);
                }//W3C
                window.onmousewheel=document.onmousewheel=this.fnScroll;//IE/Opera/Chrome/Safari
            },
            close(){
                this.showPreview = false;
                this.removeScrollListener();
            },
            removeScrollListener(){
                if(document.addEventListener){
                    document.removeEventListener('DOMMouseScroll' , this.fnScroll, false);
                }//W3C
                window.onmousewheel=document.onmousewheel = null;//IE/Opera/Chrome/Safari
            },
            changeIndex(index){
                let len = this.imageList.length;

                this.currIndex = (index+len)%len;
            },
            fnScroll(event) {
                let direct=0;
                let time = new Date().getTime();
                let e = event ? event : window.event;

                this.lastTime = time;
                if(e.wheelDelta){//IE/Opera/Chrome
                    direct=e.wheelDelta;
                }else if(e.detail){//Firefox
                    direct=e.detail;
                }
                console.log(direct);
                clearTimeout(this.timer);
                this.timer = setTimeout(() => {
                    console.log('success');
                    if(direct > 0){
                        this.changeIndex(this.currIndex - 1);
                    }else if(direct < -0){
                        this.changeIndex(this.currIndex + 1);
                    }
                }, 80);
            }
        },
        mounted() {
        },
        beforeDestroy() {
            document.documentElement.style.overflow = "auto";
            this.removeScrollListener();
        }
    };

</script>

<style lang="less" scoped>
    .image-preview-wrapper{
        position: fixed;
        top: 0;
        bottom: 0;
        left: 0;
        right: 0;
        background-color: rgba(0 ,0, 0, .8);
        z-index: 100;
    }
    .preview-close{
        position: absolute;
        top: 0;
        right: 0;
        padding: 10px 10px 15px 15px;
        border-radius: 0 0 0 40px;
        display: inline-block;
        font-size: 18px;
        background-color: rgba(255,255,255,.8);
        cursor: pointer;
        &:hover{
            color: #fff;
        }
    }
    .preview-body{
        position: absolute;
        top: 5%;
        left: 5%;
        right: 5%;
        bottom: 10%;
        overflow: hidden;
    }
    .preview-image{
        position: absolute;
        top: 0;
        left: 0;
        width: 100%;
        height:100%;
        background-size: contain;
        background-position: 50%;
        background-repeat: no-repeat;
        transition: all .5s ease-in-out;
    }
    .image-prev{
        top: 0;
        left: -100%;
    }
    .image-next{
        top: 0;
        left: 100%;
    }

    .btn-preview{
        position: absolute;
        top: 0;
        bottom: 0;
        width: 15%;
        cursor: pointer;
        font-size: 100px;
        background-color: rgba(255,255,255,0);
        transition: all .3s ease-out;
        .preview-icon{
            position: absolute;
            top:45%;
            font-size: 80px;
            color: rgba( 255, 255, 255, .1);
            transition: all .3s ease-out;
            padding: 10px;
            border-radius: 100px;
            background: rgba( 255, 255, 255, 0);
        }
        &:hover{
            .preview-icon{
                color: rgba(255, 255, 255, .8);
                background: rgba(0 ,0, 0, .3);;
            }
        }
    }
    .preview-left{
        left: 0;
        // &:hover{
        //     background: -webkit-linear-gradient(left, rgba(255,255,255,.7) , rgba(255,255,255,0)); /* Safari 5.1 - 6.0 */
        //     background: -o-linear-gradient(right, rgba(255,255,255,.7) , rgba(255,255,255,0)); /* Opera 11.1 - 12.0 */
        //     background: -moz-linear-gradient(right, rgba(255,255,255,.7) , rgba(255,255,255,0)); /* Firefox 3.6 - 15 */
        //     background: linear-gradient(to right, rgba(255,255,255,.7) , rgba(255,255,255,0)); /* 标准的语法 */
        // }
        .preview-icon{
            left: 20%;
        }
    }
    .preview-right{
        right: 0;
        // &:hover{
        //     background: -webkit-linear-gradient(right, rgba(255,255,255,.7) , rgba(255,255,255,0)); /* Safari 5.1 - 6.0 */
        //     background: -o-linear-gradient(left, rgba(255,255,255,.7) , rgba(255,255,255,0)); /* Opera 11.1 - 12.0 */
        //     background: -moz-linear-gradient(left, rgba(255,255,255,.7) , rgba(255,255,255,0)); /* Firefox 3.6 - 15 */
        //     background: linear-gradient(to left, rgba(255,255,255,.7) , rgba(255,255,255,0)); /* 标准的语法 */
        // }
        .preview-icon{
            right: 20%;
        }
    }
    .preview-tail{
        position: absolute;
        bottom: 1%;
        width: 100%;
        height: 80px;
    }
    .thumb-pointer{
        position: absolute;
        top: 0;
        left: 50%;
        transform: translateX(-50%);
        border: 3px solid #00bbee;
        box-sizing: content-box;
        width: 80px;
        height: 80px;
        &:before{
            content: "";
            position: absolute;
            width: 0;
            height: 0;
            top: -13px;
            left: 50%;
            border-left: 5px solid transparent;
            border-right: 5px solid transparent;
            border-bottom: 10px solid #00bbee;
            transform: translateX(-50%);
        }
    }

    .thumbnail-list{
        width: auto;
        height: 86px;
        background: #fff;
        display: flex;
        position: absolute;
        left: calc(~'50% - 43px');
        transition: all .3s ease-in-out;
        top: 0;
    }

    .thmub-unit{
        flex-shrink: 0;
        flex-grow: 0;
        width: 80px;
        height: 80px;
        margin: 3px 0 0 3px;
        background-position: 50%;
        background-size: cover;
        background-repeat: no-repeat;
        cursor: pointer;
    }
</style>
