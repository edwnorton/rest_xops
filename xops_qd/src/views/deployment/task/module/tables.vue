<template>
  <div>
    <!--表格渲染-->
    <el-table v-loading="sup_this.loading" :data="sup_this.data" size="small" border style="width: 100%;">
      <el-table-column prop="name" label="名称"/>
      <el-table-column label="环境" width="200">
        <template slot-scope="scope">
          <span>{{ scope.row.environment == 'dev' ? '开发环境': scope.row.environment == 'test' ? '测试环境':scope.row.environment == 'prod' ? '生产环境':scope.row.environment }}</span>
        </template>
      </el-table-column>
      <el-table-column label="最近状态" width="150">
        <template slot-scope="scope">
          <span v-if="scope.row.status == 'Succeed'" style="color:#00CC00">运行中</span>
          <span v-else-if="scope.row.status == 'Stop'" style="color:#ED7D31">已停止</span>
          <span v-else style="color:red">失败</span>
        </template>
      </el-table-column>
      <el-table-column label="操作" width="350px" align="center">
        <template slot-scope="scope">
          <div style="display: inline-block;margin: 0px 1px;">
            <el-button v-if="checkPermission(['admin','project_all','project_edit'])" :loading="initLoading" size="mini" type="success" icon="el-icon-caret-right" @click="toInit(scope.row.id)">检查</el-button>
          </div>
          <div style="display: inline-block;margin: 0px 1px;">
            <el-button v-if="checkPermission(['admin','deploy_all','deploy_excu'])" size="mini" type="primary" icon="el-icon-plus" @click="toTools(scope.row.id)">工具</el-button>
          </div>
        </template>
      </el-table-column>
    </el-table>
    <el-pagination
      :total="sup_this.total"
      style="margin-top: 8px;"
      layout="total, prev, pager, next, sizes"
      @size-change="sup_this.sizeChange"
      @current-change="sup_this.pageChange"/>
  </div>
</template>

<script>
import checkPermission from '@/utils/permission' // 权限判断函数
import { DeployExcu } from '@/api/deploy'
import { parseTime } from '@/utils/index'
export default {
  props: {
    sup_this: {
      type: Object,
      required: true
    }
  },
  data() {
    return {
      delLoading: false
    }
  },
  methods: {
    parseTime,
    checkPermission,
    // 检查服务运行状态
    toInit(id) {
      this.initLoading = true
      DeployExcu({
        'excu': 'init',
        'id': id
      }).then(res => {
        this.initLoading = false
        this.$message({
          showClose: true,
          type: 'success',
          message: '初始化成功!',
          duration: 2500
        })
        this.sup_this.init()
      }).catch(err => {
        this.initLoading = false
        console.log(err)
      })
    },
    // 工具箱
    toTools(id) {
      this.$router.push({
        path: 'tasks/tools',
        query: { id: id }
      })
    }
  }
}
</script>

<style scoped>

</style>
