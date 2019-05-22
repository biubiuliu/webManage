<template>
    <div>
      <Card :bordered="false" :dis-hover="true">
        <p slot="title">设计列表</p>
        <p>
          <Button type="primary" @click="getDetail">新建设计</Button>
        </p>
        <p style="margin-top: 20px">
          <Button type="primary" @click="getDetail2">新建设计2</Button>
        </p>
      </Card>
      <Card :bordered="false" :dis-hover="true">
        <p>
          没有记录
        </p>
      </Card>
    </div>
</template>

<script>
export default {
  name: 'design_manage',
  data () {
    return {
      msg: '设计管理'
    }
  },
  methods: {
    getDetail () {
      let routeData = this.$router.resolve(
        {
          path: '/detail'
        }
      )
      window.open(routeData.href, '_blank')
    },
    getDetail2 () {
      let routeData = this.$router.resolve(
        {
          path: '/details'
        }
      )
      window.open(routeData.href, '_blank')
    }
  }
}
</script>

<style scoped>

</style>
