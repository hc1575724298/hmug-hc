<template>
  <view>
    <van-card :thumb-link="`/subpkg/goods_detail/goods_detail?id=${item.goods_id}`" v-for="item in goods"
      :key="item.goods_id" num="2" :price="item.goods_price | toFixed" :title="item.goods_name"
      :thumb="item.goods_small_logo || defaultPic " />
  </view>
</template>

<script>
  import {
    getGoodsList
  } from '@/api/goods.js'
  import toast from '@/utils/toast.js'
  export default {
    data() {
      return {
        queryData: {
          query: '',
          cid: '',
          pagenum: 1,
          pagesize: 10
        },
        goods: [],
        total: 0,
        // 默认的空图片
        defaultPic: 'https://img3.doubanio.com/f/movie/8dd0c794499fe925ae2ae89ee30cd225750457b4/pics/movie/celebrity-default-medium.png',
        isLoading: false
      }
    },
    methods: {
      async getGoodsList(stopPullDown) {
        this.isLoading = true
        const res = await getGoodsList(this.queryData)
        console.log(res);
        this.total = res.total
        this.goods = [...this.goods, ...res.goods]
        this.isLoading = false
        stopPullDown && stopPullDown()
      }

    },


    onLoad({
      query
    }) {
      this.queryData.query = query
      this.getGoodsList()
    },
    //监听下拉刷新
    onPullDownRefresh() {
      this.queryData = {
        query: '',
        cid: '',
        pagenum: 1,
        pagesize: 10
      }
      this.goods = []
      this.total = 0
      this.getGoodsList(() => {
        uni.stopPullDownRefresh()
      })
    },
    // 监听上啦触底
    onReachBottom() {
      if (this.queryData.pagenum * this.queryData.pagesize > this.total) return toast('没有更多数据了')
      if (this.isLoading) return
      this.queryData.pagenum++
      this.getGoodsList()
    }

  }
</script>

<style lang="scss">

</style>
