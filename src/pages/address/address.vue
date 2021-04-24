<!--
  @author: SM
  @desc: 地址选择器
-->
<template>
    <div id="address">
      <input @click="itemClickFun" :value="addressVal" />
      <!-- 地址选择器 -->
      <x-address-field :hotCity="hotCityArr" @close="addressCloseFun" :isShow="isShow"></x-address-field>
    </div>
</template>

<script>
  import { AddressField } from '@components'

  export default {
    components: {
      [ AddressField.name ]: AddressField
    },
    data(){
      return {
        // 默认不显示组件
        isShow: false,
        // 地址
        addressVal: '请选择',
        // 热门城市： 兰州、北京、上海、深圳、成都、武汉、长沙、重庆
        hotCityArr: [
          {parentValue: '450', name: '兰州', value: '861', active: true},
          {parentValue: '423', name: '北京', value: '560'},
          {parentValue: '431', name: '上海', value: '632'},
          {parentValue: '441', name: '深圳', value: '759'},
          {parentValue: '445', name: '成都', value: '798'},
          {parentValue: '439', name: '武汉', value: '729'},
          {parentValue: '440', name: '长沙', value: '743'},
          // {parentValue: '444', name: '重庆', value: '795'}
        ]
      }
    },
    methods: {
      itemClickFun(){
        this.isShow = !this.isShow
      },
      /**
       * 地址组件关闭回调
       */
      addressCloseFun(data){
        console.log('data == ', data);
        this.isShow = !this.isShow
        let addStr = '';
        data && data.forEach(item=>{
          // 表示有值
          if($Fw.isEmpty(item.activeCls)){
            addStr += item.name +' ';
          }
        })
        //
        this.addressVal = addStr || '请选择';
      }
    },
    created(){
      
    }
  }
</script>

<style lang="less">

</style>
