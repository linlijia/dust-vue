<template>
  <el-tabs v-model="activeName" type="border-card" :style="tableStyle" @tab-click="handleClick">
    <el-tab-pane label="实时故障" name="first">
      <troubleList></troubleList>
    </el-tab-pane>
    <el-tab-pane label="我的消息" name="second">
      <messageList></messageList>
    </el-tab-pane>
  </el-tabs>
</template>

<script>
import troubleList from './trouble-list'
import messageList from './message-list'
export default {
  data () {
    return {
      activeName: 'first',
      tableStyle: 'height: 470px'
    }
  },
  components: {
    troubleList,
    messageList
  },
  methods: {
    handleClick (tab, event) {
      console.log(tab, event)
    }
  }
}
</script>

<style scoped>

</style>
