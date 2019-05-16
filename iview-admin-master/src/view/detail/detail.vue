<template>
    <div class="design-detail">
      <div class="left-menu">
        <Row>
          <Col span="8">
            <Menu class="Menu" :accordion="true" @on-open-change="getCatId" @on-select="getImg">
              <MenuGroup title="商品分类">
                <MenuItem name="1">
                  背景
                </MenuItem>
              </MenuGroup>
              <Submenu v-for="item in this.indoor_list.category" :key="item.cat_id"  :name="item.cat_id">
                <template slot="title">
                  {{item.cat_name}}
                </template>
                <MenuItem  v-for="(index,value) in leaveMenu" :key="value" :name="value">{{index.cat_name}}</MenuItem>
              </Submenu>
            </Menu>
          </Col>
        </Row>
      </div>
      <div>
        <p>图片</p>
        <ul class="editorImg" v-if="this.cat_id==0">
          <li v-for="item in this.indoor_list.background_imgs"
              :key="item.id" id="dragimg"
              draggable=”true”
              @dragover.prevent="handleDragOver($event, item)"
              @mousedown="mousedown($event)">
            <img :src="item.img_url" alt="">
          </li>
        </ul>
        <ul class="editorImg" v-else>
          <li v-for="item in imgGoods" :key="item.goods_id">
            <img :src="item.goods_thumb" alt="">
            <span>{{item.goods_name}}</span>
          </li>
        </ul>
      </div>
      <canvas id="myCanvas"
              width="1500"
              height="1000"
              draggable=”true”
              @dragover.prevent="handleDragOver($event)"
               ref="select_frame">
        您的浏览器不支持!!!
      </canvas>
      <Modal
        v-model="modal1"
        title="警告"
        @on-ok="ok"
        @on-cancel="cancel">
        <p>确定要替换背景图片吗</p>
      </Modal>
    </div>
</template>

<script>
import { getGoodsType, getGoodsType2 } from '@/api/detail-list'
export default {
  name: 'detail',
  data () {
    return {
      cat_id: 0,
      indoor_list: {},
      indoor_list2: {},
      leaveMenu: [],
      imgGoods: [],
      imgVal: 0,
      ImgUrl: '',
      x: 0,
      y: 0,
      modal1: false
    }
  },
  watch: {
    imgVal: function (value, oldValue) {
      console.log('value', value)
      console.log('oldValue', oldValue)
    }
  },
  mounted () {
    // this.handleGetGoodsType2(this.cat_id)
    this.handleGetGoodsType()
    this.$refs.select_frame.ondrop = (e) => {
      e.preventDefault() // 阻止离开时的浏览器默认行为
      this.ondropFun(e)
      console.log(e)
    }
  },
  updated () {
  },
  methods: {
    handleGetGoodsType2 (cat_id) {
      getGoodsType2(cat_id).then(res => {
        this.indoor_list2 = res.data.message
        this.leaveMenu = this.indoor_list2.category
        this.imgGoods = this.indoor_list2.category[this.imgVal].goods
        console.log('有参数', this.imgGoods)
      }).catch(err => {
        console.log(err)
      })
    },
    handleGetGoodsType () {
      getGoodsType().then(res => {
        this.indoor_list = res.data.message
        console.log('无参数', this.indoor_list.category)
      }).catch(err => {
        console.log(err)
      })
    },
    // 获取列表对应的cat_id
    getCatId (id) {
      this.cat_id = id
      if (this.cat_id.length !== 0) {
        this.handleGetGoodsType2(this.cat_id)
      }
      console.log('this.cat_id.length', this.cat_id.length)
    },
    // 获取列表id
    getImg (id) {
      this.imgVal = id
      console.log(this.imgVal)
    },
    ok () {
      this.$Message.info('Clicked ok')
    },
    cancel () {
      this.$Message.info('Clicked cancel')
    },
    handleDragOver (e) {
      e.dataTransfer.dropEffect = 'move'
      // e.dataTransfer.dropEffect="move";//在dragenter中针对放置目标来设置!
    },
    ondropFun (e) {
      // /*ondrop拖拽元素进入目标元素头上并且松开鼠标的时候的时候*/
      // 获取canvas元素
      var cvs = document.getElementById('myCanvas')
      var ctx = cvs.getContext('2d')
      // 创建image对象
      var img = new Image()
      img.src = this.ImgUrl
      // 待图片加载完后，将其显示在canvas上
      img.onload = function () {
        console.log('---------onload')
        ctx.setTransform(1, 0, 0, 1, 0, 0)
        ctx.clearRect(0, 0, cvs.width, cvs.height)
        ctx.drawImage(img, 0, 0) // this即是imgObj,保持图片的原始大小：470*480
        // ctx.drawImage(this, 0, 0,1024,768) // 改变图片的大小到1024*768
      }
    },
    // 鼠标按下
    mousedown (event) {
      this.ImgUrl = event.srcElement.currentSrc
      // console.log('鼠标按下', this.ImgUrl)
    }
  }
}
</script>

<style scoped>
  .design-detail{
    display: flex;
    overflow: auto;
  }
  .left-menu{
    min-width: 240px;
  }
  .editorImg{
    display: flex;
    flex-direction: row;
    flex-wrap: wrap;
    justify-content: left;
    max-width: 340px;
    background: rgba(205, 205, 205, 0.28);
  }
  .editorImg > li {
    margin-left: 10px;
    margin-top: 10px;
    list-style: none;
  }
  .editorImg > li> img{
    width: 100px;
    height: 100px;
  }
  .Menu{
    min-height: 400px;
    overflow: auto;
  }
  #myCanvas{
    width: 1500px;
    height: 1000px;
    background: rgba(79, 205, 73, 0.39);
  }
</style>
