<template>
<div>
  <h3 class="vheader">客户统计</h3>
  <el-tabs type="card" value="tab1">
    <el-tab-pane label="新增客户统计" name="tab1">
      <Tab1></Tab1>
    </el-tab-pane>
    <el-tab-pane label="到期未续费客户统计" name="tab2">
      <Tab2></Tab2>
    </el-tab-pane>
    <el-tab-pane label="客户情况统计" name="tab4">
      <Tab4></Tab4>
    </el-tab-pane>
    <el-tab-pane label="新增客户环比" name="tab5">
      <Tab5></Tab5>
    </el-tab-pane>
  </el-tabs>
</div>
</template>
<script>
import StatisCustotaltocTab1 from '@/views/components/StatisCustotaltocTab1.vue'
import StatisCustotaltocTab2 from '@/views/components/StatisCustotaltocTab2.vue'
import StatisCustotaltocTab4 from '@/views/components/StatisCustotaltocTab4.vue'
import StatisCustotaltocTab5 from '@/views/components/StatisCustotaltocTab5.vue'

export default {
  name: 'StatisConversion',
  data: function () {
    return {}
  },
  methods: {

  },
  components: {
    Tab1: StatisCustotaltocTab1,
    Tab2: StatisCustotaltocTab2,
    Tab4: StatisCustotaltocTab4,
    Tab5: StatisCustotaltocTab5
  }
}
</script>
<style>

</style>
