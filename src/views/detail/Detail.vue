<template>
  <div id="detail">
    <detail-nav-bar></detail-nav-bar>
    <div>{{ errorMsg }}</div>
  </div>
</template>
<script>
import DetailNavBar from './childComps/DetailNavBar.vue';
import { getDetailData } from 'network/details';

export default {
  name: "Detail",
  components: {
    DetailNavBar
  },
  data () {
    return {
      iid: null,
      errorMsg: ''
    };
  },
  created () {
    // 1.保存传入的iid
    this.iid = this.$route.params.iid

    // 2.根据iid请求详情数据
    this.getDetailData(this.iid)

    // 3.根据iid请求推荐数据
    // this.getRecommendData()
  },
  mounted () { },
  methods: {
    getDetailData () {
      getDetailData(this.iid).then(res => {
        console.log(res)
        this.errorMsg = res['错误信息']
      })
    }
  },
};
</script>
<style scoped></style>