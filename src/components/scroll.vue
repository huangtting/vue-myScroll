<template>
    <div class ="scroll-me" v-bind:style="scroll_me_style" v-on:mousewheel="goScroll" v-on:mousemove="runDragScroll" v-on:mouseup="stopDrag">
        <div class='scroll-content' ref='content' v-bind:style="{top:this.content_top+'px'}">
            <slot name="content" ></slot>
        </div>
        
        <div class="scroll_out" v-on:mousedown="jumpScroll" v-bind:class="{scroll_out_active:isActive}">
            <div class="scroll_in" v-bind:style="scroll_in_style" v-on:mousedown="dragScroll" ref="scroll_in" v-bind:class="{scroll_in_active:isActive}"></div>
        </div>
    </div>
</template>
<script>
export default{
    data(){
        return{
            isDrag:false,
            content_top:0,
            scroll_top:0,
            isBottom:false,
            Y:0,
            isActive:false
        }
    },
    create(){

    },
    mounted(){
      
    },
    props:[
        'containerHeight',
        'containerWidth',
        'speed'
    ],
    methods:{
       setScrollTop:function(scroll_top){
            if(scroll_top<0) scroll_top=0;
            console.log(scroll_top,this.containerHeight,this.scrollinHeigth)
            if(scroll_top>this.containerHeight-this.scrollinHeigth){
                scroll_top=this.containerHeight-this.scrollinHeigth;
            }

           this.scroll_top=scroll_top;
       },
       setContentTop:function(content_top){
            if(content_top>0) content_top=0;
            
            if(Math.abs(content_top)>this.max_content_top) 
            {
                content_top=0-this.max_content_top;
            }
            this.content_top=content_top;
       },
       goScroll:function(event){
            event=event||window.event;
            if(event.preventDefault) event.preventDefault();
            let content_top=this.content_top;
            let scroll_top=this.scroll_top;
            if(event.wheelDelta>0)//向上滚动
            {
                content_top+=this.content_per_scroll;
                scroll_top-=this.scrollin_per_scroll;

                this.setContentTop(content_top);
            }
            else//向下滚动
            {
               
                content_top-=this.content_per_scroll;
              
                scroll_top+=this.scrollin_per_scroll;

                this.setContentTop(content_top);
            }
            
            
            this.setScrollTop(scroll_top);
       },
       dragScroll:function(event){
            event=event||window.event;
            this.isActive=true;
            this.Y=event.clientY
            
            this.isDrag=true;
       },
       runDragScroll:function(event){
           if(this.isDrag)
            {
                let content_top=this.content_top;
                let scroll_top=this.scroll_top;

                event=event||window.event;
                scroll_top=this.scroll_top+(event.clientY-this.Y);

                content_top=content_top-(event.clientY-this.Y)*(this.content_per_scroll/this.scrollin_per_scroll);
                this.Y=event.clientY;
                this.setScrollTop(scroll_top);
                this.setContentTop(content_top);
            }
       },
       stopDrag:function()
       {
           this.isDrag=false;
           this.isActive=false;
       },
       jumpScroll:function(event){
            let content_top=this.content_top;
            let scroll_top=this.scroll_top;
            event=event|| window.event;//兼容IE事件

            if((event.target||event.srcElement)===this.$refs.scroll_in) return;

            scroll_top=(event.offsetY<scroll_top)?scroll_top-(this.scrollinHeigth*0.7):scroll_top+(this.scrollinHeigth*0.7);
            content_top=(event.offsetY<scroll_top)?content_top+(this.content_per_scroll*this.scrollinHeigth*0.7)/this.scrollin_per_scroll:content_top-(this.content_per_scroll*this.scrollinHeigth*0.7)/this.scrollin_per_scroll;

            this.setContentTop(content_top);
            this.setScrollTop(scroll_top)
       }

    },
    computed:{
       contentHeight:function(){
           return getComputedStyle(this.$refs.content).height.slice(0,-2);
       },
    //    滚动条可以滑动的px
       scroll_distance:function(){
           return this.contentHeight-this.containerHeight;
       },
    //    每次滚动滑动的px
       scrollin_per_scroll:function(){
           return (this.containerHeight-this.scrollinHeigth)/(this.scroll_distance/this.content_per_scroll);
       },
    //    滚动条的长度
       scrollinHeigth:function(){
           return this.containerHeight*0.1;
       },
       scroll_me_style:function(){
           return {
                width:this.containerWidth+"px",
                height:this.containerHeight+"px"
            }
       },
       scroll_in_style:function(){
           return {
               height:this.scrollinHeigth+'px',
               top:this.scroll_top+'px'
           }
       },
    //    每次滚动内容滑动的px
       content_per_scroll:function(){
           return this.speed*this.contentHeight;
       },
    //    content能够滑动的最大px
       max_content_top:function(){
           return this.contentHeight-this.containerHeight;
       }
      
    },
    watch:{

    }
}
</script>
<style scoped type='text/css'>
 .scroll-me{
            border: 1px solid #000;
            overflow-y: hidden;
            position: relative;
            margin: auto;
        }
        .scroll-content{
            position: relative;
            top:0px;
        }
        .scroll_out{
            position: absolute;
            z-index:0;
            right: 4px;
            top: 0;
            border-radius: 4px;
            width: 8px;
            height: 100%;
            background: #FFF;
            
        }
        .scroll_in{
            border-radius: 4px;
            width: 8px;
            position: absolute;
            z-index: 1;
            left: 0;
            top: 0;
            background: #000;
            opacity: 0.6;
        }
        .scroll_out_active{
            background-color: #ccc;
        }
        .scroll_in_active{
            opacity: 1;
        }
</style>