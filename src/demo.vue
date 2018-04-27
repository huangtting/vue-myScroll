<template>
  <div>
      <scroll containerHeight=600 containerWidth=200 speed=0.05>
          <div slot='content'>
            <ul class="my-data3 scroll-list moved" id="scrollList2">
                    <li>
                        1
                    </li>
                    <li>
                        2
                    </li>
                    <li>
                        3
                    </li>
                    <li>
                        4
                    </li>
                    <li>
                        5
                    </li>
                </ul>
          </div>
      </scroll>
  </div>
</template>
<script>
import scroll from './components/scroll'
export default {
  components:{
      scroll
  }
}
</script>
<style scoped>
  ul {
    margin:0;
    padding:0;
            list-style: none;
            text-align: center;
        }
        li {
            line-height: 400px;
            font-size: 30px;
            font-weight: bold;
        }
</style>

