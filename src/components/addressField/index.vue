<!--
  @author: SM
  @desc: 地址选择器
    infinite  循环
    fadeInUp 显示
    fadeOutDown 隐藏
-->
<template>
    <div @click="maskCloseFun" class="x-address-field" v-show="isDisplay">
      <div @click.stop class="x-address-field-box animated" :class="isDisplay ? 'fadeInUp': 'fadeOutDown'">
          <div class="x-address-field-header vux-1px-b">
            <h1 @click="addressClose">请选择<x-text class="x-address-close"></x-text></h1>
            <div class="x-address-field-header-result">
                <div>
                  <div @click="showTabView(index)" :class="[index==0 ? 'province' : index==1 ? 'city' : 'county', item.activeCls]" v-for="(item, index) in headerData" :key="index" class="x-address-header-item">{{item.name}}</div>
                </div>
            </div>
          </div> <!-- end header -->
         <!-- 地区列表选择begin -->
         <div class="x-address-content">
            <!-- 省 -->
            <div class="province" v-show="currentView == 1">
                <!-- 热门城市 -->
                <div v-if="defaultHotCity.length > 0" >
                    <div class="x-address-content-hot">热门城市</div>
                    <div class="x-address-content-hot-content"> 
                      <div class="x-flex-box" :gutter="11" v-for="(item, index) in Math.ceil(defaultHotCity.length / 4)" :key="index">
                          <div @click="hotItemClick(defaultHotCity[(index * 4 + n -1)])" :class="[!defaultHotCity[(index * 4 + n -1)] ? 'nohotitem' : '', defaultHotCity[(index * 4 + n -1)] && defaultHotCity[(index * 4 + n -1)].active ? 'hot-item-active' : '' ]" class="hot-item vux-1px x-flex-box-item" v-for="n in 4" :key="n">{{ defaultHotCity[(index * 4 + n -1)] ? defaultHotCity[(index * 4 + n -1)].name : '' }}</div>
                      </div>
                    </div>
                </div><!-- end hot city -->
                <div class="x-address-tips">请选择省份/地区</div>
                <div>
                  <!-- 选中 active -->
                  <div class="x-address-data-item" @click="selectClick(item, cData, 'province')" v-for="(item, index) in provinceData" :key="index">
                    <x-text class="x-adderss-first-name">{{ getName(item, 1, index, provinceData) }}</x-text>
                    <x-text class="x-address-full-name" :data-value="item.split('-')[3]">{{ getName(item, 2, index, provinceData) }}</x-text>
                  </div>
                 
                </div>
            </div>
            <!-- 市-->
            <div class="city" v-show="currentView == 2">
              <div class="x-address-tips">请选择城市</div>
              <div>

                <div class="x-address-data-item" @click="selectClick(item, cData2, 'city')"  v-for="(item, index) in cityData" :key="index">
                    <x-text class="x-adderss-first-name">{{ getName(item, 1, index, cityData) }}</x-text>
                    <x-text class="x-address-full-name" :data-value="item.split('-')[3]">{{ getName(item, 2, index, cityData) }}</x-text>
                </div>
                 
              </div> <!-- end city -->
            </div>
            <!-- 区-->
            <div class="county" v-show="currentView == 3">
              <div class="x-address-tips">请选择区/县</div>
              <div>
                
                  <div class="x-address-data-item" @click="selectClick(item, cData3, 'county')"  v-for="(item, index) in countyData" :key="index">
                    <x-text class="x-adderss-first-name">{{ getName(item, 1, index, countyData) }}</x-text>
                    <x-text class="x-address-full-name" :data-value="item.split('-')[3]">{{ getName(item, 2, index, countyData) }}</x-text>
                  </div>
     
              </div> <!-- end county -->
            </div>
         </div>
         <!-- 地区列表选择end -->
      </div><!-- box end -->
    </div>
</template>

<script>
// 导入城市数据
const cityData = require('./city.json');
// 倒入拼音
require('./Pinyin')
//
export default {
  // 组件名称
  name: 'x-address-field',
  //
  data(){
    return {
      // 省份
      provinceData: [],
      // 城市
      cityData: [],
      // 区县
      countyData: [],
      // 当前省数据
      cData: cityData,
      // 当前市数据
      cData2: [],
      // 当前区县数据
      cData3: [],
      // header数据
      headerData: [],
      // 当前显示数据面包
      currentView: 1,
      // 是否显示组件
      isDisplay: this.isShow,
      // 是否显示热门城市
      showHotCity: false,
      // 接受外部数组对象
      defaultHotCity: this.hotCity
    }
  },
  watch: {
    isShow(value){
      this.isDisplay = value;
    },
    hotCity(value){
      this.defaultHotCity = value;
      this.initHotCity();
    }
  },
  // 外部接受的值
  props: {
    // 是否显示
    isShow: {
      type: Boolean,
      default: false
    },
    // 点击遮照层是否关闭
    maskClose: {
      type: Boolean,
      default: false
    },
    // 热门城市
    hotCity: {
      type: Array,
      default: []
    }
  },
  // 方法
  methods: {
    /**
     * 点击关闭图标
     */
    addressClose(){
      // 关闭
      this.isDisplay = false;
      // 抛出事件
      this.$emit('close', this.headerData, this.isDisplay)
    },
    /**
     * 热门城市点击
     */
    hotItemClick(item){
      //
      if(item){
        // 设置选中城市
        this.hotSetCity(item);

        // 设置选中样式
        let newArray = []
        this.defaultHotCity && this.defaultHotCity.forEach(cItem=>{
          cItem.active = false;
          if(cItem.value == item.value){
            cItem.active = true;
          }
          newArray.push(cItem);
        })
        this.defaultHotCity = newArray;
      }
    },
    /**
     * 点击热门城市出发选中
     */
    hotSetCity(item){
      // 点击的省
      let pId = item.parentValue;
      // 点击的城市
      let id = item.value;
      // 1、根据parentValue找出对应的 省份
      this.provinceData && this.provinceData.forEach(item=>{
        let ppId = item.split('-')[3];
        if(pId == ppId) {
          this.selectClick(item, this.cData, 'province')
          return;
        }
      })
      // 2、根据找出的省份找出对应的城市
      this.$nextTick(function(){
        this.cityData && this.cityData.forEach(item=>{
          let iId = item.split('-')[3];
          if(id == iId){
            this.selectClick(item, this.cData2, 'city')
            return;
          }
        })
      })
    },
    /**
     * 遮照点击
     */
    maskCloseFun(){
      if(this.maskClose){
        this.addressClose();
      }
    },
    /**
     * 显示tab
     */
    showTabView(index){
      this.currentView = (index + 1)
    },
    /**
     * 返回对应数据
     */
    getName(item, type, index, currendData){
      //
      let newItem = item.split('-');
      // 当前首字母
      let firstName = newItem[0];
      // 当前地区
      let fullName = newItem[2];
      //
      if(type == 1) {
        // 下一个首字母
        if(index == 0){
          return firstName;
        }
        // 判断上一个是否和当前一样
        let prevName = currendData[index - 1].split('-')[0];
        // 当前的字母和上一个一样
        if(prevName != firstName){
          //
          return firstName;
        }
        //
        return '';
      }
      // type = 2
      return fullName;
    },
    /**
     * 移除选中样式
     */
    removeActive(cName){
      let classArr = cName.split('-');
      classArr && classArr.forEach(item => {
          let pClass = document.querySelector('.'+item+' .x-address-full-name.active');
          if(pClass){
            pClass.className = pClass.className.replace('active', '')
          }
      })
    },
    /**
     * 当前数据点击
     */
    selectClick(item, currendData, flag){
      //
      let newItem = item.split('-');
      // 1、取下标
      let index = newItem[1];
      // 2、根据下标获取下一个节点数据
      let record = currendData[index];
      // 当前点击的值
      let cValue = record.value;
      // 3、追加到选中数组中
      if(flag == 'province'){
        this.headerData = [];
        this.cityData = [];
        this.countyData = [];
        this.cData2 = record.sub;
        // 排序城市数据
        this.handleData(record.sub, this.cityData);
        // 移除选中样式（省市区）
        this.removeActive('province-city-county');
        // 记录当前数据
        this.$set(this.headerData, '0', record)
        this.$set(this.headerData, '1', {name:'请选择城市', activeCls: 'nonactive'})
        this.currentView = 2;
      }else if(flag == 'city'){
        this.countyData = []
        this.cData3 = record.sub;
        // 排序城市数据
        this.handleData(record.sub, this.countyData);
        // 移除选中样式（市区）
        this.removeActive('city-county');
        // 记录当前数据
        this.$set(this.headerData, '1', record)
        this.$set(this.headerData, '2', { name:'请选择区县', activeCls: 'nonactive' })
        this.currentView = 3;
      }else{      
        // 移除选中样式（市区）
        this.removeActive('county');
        // 记录当前数据
        this.$set(this.headerData, '2', record)
        // 调用关闭方法
        this.addressClose();
      }
      // 设置选中样式
      let cEl = document.querySelector("[data-value='"+cValue+"']");
      let cClassName = cEl.className;
      cEl.className = cClassName + ' active';
    },
    /**
     * 根据名字返回首拼音
     */
    convertName(name, isFirst){
      // isFirst是否返回首字母
      return isFirst ? Pinyin.getFirstLetter(name).slice(0, 1) : Pinyin.getFirstLetter(name);
    },
    /**
     * 排序数据
     */
    sortData(data){
      data =  data && data.sort();
    },
    /**
     * 过滤数据(省市区),追加拼音
     */
    handleData(cData, targetData){
      cData && cData.forEach((item, index) => {
        // 首字母 + 数组序列 + 名字
        targetData.push(this.convertName(item.name, true) + '-' + index + '-' + item.name + '-' + item.value);
      });
      // 排序
      this.sortData(targetData);
    },
    /**
     * 初始化城市热门
     */
    initHotCity(){
      this.$nextTick(function(){
        let currentItem = {}
        this.defaultHotCity && this.defaultHotCity.forEach(item=>{
          if(item.active){
            currentItem = item;
            return;
          }
        })
        //
        if(currentItem){
          //
          this.hotItemClick(currentItem);
        }
      })
    }
  },
  created(){
    // 排序省份数据
    this.handleData(cityData, this.provinceData);
    // 判断一开始传入的热门城市是否有选中项目
    this.initHotCity();
  }
}
</script>
<style lang="less">
@import  "./animate.less";
/*
  地址选择器
*/
@theme-color: #04BE02;
@color-999999: #999999;
@color-222222: #222222;
.x-address-field {
  position: fixed;
  left: 0;
  top: 0;
  right: 0;
  bottom: 0;
  background: rgba(0, 0, 0, .5);
  .x-address-field-box {
    background-color: #FFFFFF;
    position: absolute;
    bottom: 0px;
    border-radius: 16px 16px 0 0;
    height: 480px;  // 总高度
    width: 100%;
    overflow: hidden;
    display: flex;
    flex-direction: column;
    // 内容区域
    .x-address-content {
      padding: 0 20px;
      overflow-y: auto;
      flex: 1;
      .x-address-tips {
        font-size: 12px;
        color: @color-999999;
        margin-bottom: 20px;
        margin-top: 20px;
      }
      .x-address-data-item {
        margin-bottom: 20px;
        position: relative;
      }
      .x-adderss-first-name {
        font-size: 14px;
        color: @color-999999;
        width: 25px;
        display: inline-block;
      }
      .x-address-full-name {
        font-size: 14px;
        color: @color-999999;
        &.active {
          color: @theme-color;

          &::after {
            content: '';
            position: absolute;
            width: 18px;
            height: 10px;
            right: 0;
            border: 4px solid @theme-color;
            border-top: none;
            border-right: none;
            background: transparent;
            transform: scale(0.6) rotate(-45deg);
          }
        }
        
      }
      .vudiv:first-child {
        margin-bottom: 12px;
      }
      .x-address-content-hot {
        font-size: 14px;
        color: @color-222222;
        margin-top: 20px;
      }
      .x-address-content-hot-content {
        margin-top: 12px;
        .hot-item {
          height: 26px; /*no*/
          font-size: 13px;
          line-height: 27px; /*no*/
          text-align: center;
          &.nohotitem {
            &::before {
              content: '';
              border: none;
            }
          }

          &.vux-1px:before {
           border-radius: 8px;
          }
          &.hot-item-active {
           background: @theme-color;
           color: #FFFFFF;
           border-radius: 4px;
           &.vux-1px:before {
             border-color: @theme-color;
             background: @theme-color;
             z-index: -1;
           }
          }
        }
      }
    }
    // 头部区域
    .x-address-field-header {
        // min-height: 153px; // 头部高度
        .x-address-field-header-result{
          //  min-height: 84px;
          // 选择后条目
          .x-address-header-item {
            height: 44px;
            line-height: 44px;
            padding-left: 46px;
            position: relative;
            font-size: 14px;
            color: @theme-color;
            // 圆圈
            &::before{
              content: '';
              position: absolute;
              top: 18px;
              left: 20px;
              width: 7px;
              height: 7px;
              border-radius: 50%;
              background: @theme-color;
            }
            // 线
            &::after {
              content: '';
              position: absolute;
              height: 100%;
              left: 23px;
              background: @theme-color;
              width: 1px;
              transform: scaleX(.5);
            }
            // 第一个一半
            &:first-child {
               &::after {
                  height: 50%;
                  top: 50%;
               }
             
            }
            // 最后一个一半
            &:last-child {
               &::after {
                  // height: 50%;
                  height: 19px;
               }
            }
            // 未选择列表
            &.nonactive {
              color: @color-222222;
              &::before{
                content: '';
                border: 1px solid @theme-color;
                width: 5px;
                height: 5px;
                border-radius: 50%;
                background: transparent;
              }
            }
          }// end item
        }
      h1 {
        font-size: 18px;
        color: @color-222222;
        text-align: center;
        overflow: hidden;
        height: 69px;
        line-height: 69px;
        position: relative;
        .x-address-close {
          position:absolute;
          // background: #999999;
          width: 15px;
          height: 15px;
          right: 20px;
          top: 27px;
          transform: rotate(45deg);
          // 画出关闭按钮
          &::after {
            content: '';
            background: @color-999999;
            height: 1px;
            right: -1px;
            top: 5px;
            position: absolute;
            width: 15px;
          }
          &::before {
            content: '';
            background: @color-999999;
            height: 1px;
            position: absolute;
            width: 15px;
            left: 1px;
            top: 5px;
            transform: rotate(90deg);
          }
        }
      }
    }
    .x-flex-box {
      display: flex;
      :first-child  {
        margin-left: 0px;
      }
    }
    .x-flex-box-item {
      flex: 1;
      margin-left: 11px;
      margin-bottom: 11px;
    }
  }
  // 1px
  .setLine(@c: #C7C7C7) {
    content: " ";
    position: absolute;
    left: 0;
    top: 0;
    width: 200%;
    border: 1px solid @c;
    color: @c;
    height: 200%;
    transform-origin: left top;
    transform: scale(0.5);
  }
  /*
  vux-1px 全边框*/
  .vux-1px {
    position: relative;
  }

  .vux-1px {
    &:before {
      .setLine();
    }
  }
}
</style>