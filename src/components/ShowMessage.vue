<template>
  <div style="text-align:left">
    <div @click="show" style="white-space:nowrap;overflow:hidden;text-overflow:ellipsis;">
      <i class="el-icon-view" ></i>{{msg}}</div>
    <el-dialog title="查看" :visible="visible" v-bind="$attrs" v-on="$listeners" :before-close="close">
      <pre>{{msg}}</pre>
    </el-dialog>
  </div>
</template>

<script>
export default {
  name: 'ShowMessage',
  props: {
    msg: String
  },
  data(){
    return {
      visible: false
    }
  },
  methods: {
    close(event) {
      window.console.log(event)
      this.visible = false
    },
    show() {
      this.visible = true
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
</style>
