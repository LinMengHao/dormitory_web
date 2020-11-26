
<template>
  <div class="app-container">
    <div style="line-height: 40px; font-weight: 700; font-size: 22px; color: #a85a11; margin-left: 10px;">用户列表</div>
    <!--    查询条件表单-->

    <el-form :inline="true" class="demo-form-inline">
      <el-form-item label="用户名">
        <el-input v-model="userQuery.username" placeholder="用户名" />
      </el-form-item>
      <el-form-item label="姓名">
        <el-input v-model="userQuery.nickname" placeholder="姓名" />
      </el-form-item>
      <el-form-item label="电话号码">
        <el-input v-model="userQuery.phoneNumber" placeholder="电话号码" />
      </el-form-item>
      <el-form-item label="性别">
        <el-select v-model="userQuery.sex" placeholder="性别">
          <el-option label="男" value="0" />
          <el-option label="女" value="1" />
        </el-select>
      </el-form-item>
      <el-form-item label="状态">
        <el-select v-model="userQuery.status" placeholder="状态">
          <el-option label="已激活" value="1" />
          <el-option label="未激活" value="0" />
        </el-select>
      </el-form-item>
      <el-button plain="true" type="primary" icon="el-icon-search" @click="getList()">查询</el-button>
      <el-button plain="true" type="default" @click="resetData()">清空</el-button>
    </el-form>
    7
    <!--    表格-->

    <div style="width: 800px;margin: auto">
      <el-table
        v-loading="listLoading"
        element-loading-text="数据加载中"
        fit
        highlight-current-row
        m
        :data="list"
        border
        style="width: 100%"
      >
        <el-table-column fixed label="序号" width="50" align="center">
          <template slot-scope="scope">
            {{ (current - 1) * size + scope.$index + 1 }}
          </template>
        </el-table-column>
        <el-table-column prop="username" label="用户名" width="120" />
        <el-table-column prop="nickname" label="姓名" width="120" />
        <el-table-column prop="birth" label="出生日期" width="170" />
        <el-table-column label="性别" width="60">
          <template slot-scope="scope">
            <!-- ===判断类型和值 -->
            {{ scope.row.sex===1?'女':'男' }}
          </template>
        </el-table-column>
        <el-table-column prop="phoneNumber" label="电话号码" width="120" />
        <el-table-column prop="email" label="邮箱" width="150" />
        <el-table-column prop="createTime" label="创建时间" width="160" />
        <el-table-column label="状态" width="60">
          <template slot-scope="scope">
            <!-- ===判断类型和值 -->
            {{ scope.row.status===1?'激活':'未激活' }}
          </template>
        </el-table-column>
        <el-table-column fixed="right" label="操作" width="100">
          <template slot-scope="scope">
            <router-link :to="'/system/users/update/'+scope.row.id">
              <el-button type="primary" plain="true" size="mini" icon="el-icon-edit">修改</el-button>
            </router-link>
            <el-button type="danger" size="mini" plain="true" icon="el-icon-delete" @click="removeDataById(scope.row.id)">删除</el-button>
          </template>
        </el-table-column>
      </el-table>
    </div>
    <!--    分页-->
    <el-pagination
      :current-page="current"
      :page-size="size"
      :total="total"
      style="padding: 30px 0; text-align: center;"
      layout="total, prev, pager, next, jumper"
      @current-change="getList"
    />
  </div>
</template>
<script>
// 引入api下的user.js文件
import userApi from '@/api/users/users.js'

export default {
  // 写核心代码的位置
  data() { // 定义变量和初始值
    return {
      list: null, // 查询之后接口返回集合
      current: 1, // 当前页
      size: 10, // 每页记录数
      total: 0, // 总记录数
      userQuery: {} // 条件封装的对象 v-model双向绑定
    }
  },
  created() { // 页面渲染之前执行，调用method定义的方法
    // 调用
    this.getList()
  },
  methods: { // 创建具体的方法，调用user.js定义的方法
    getList(current = 1) { // 用户列表
      this.current = current
      userApi.getUser(this.current, this.size, this.userQuery)
        .then(response => { // 请求成功
          // response接口返回的数据
          // console.log(response)
          this.list = response.data.records
          this.total = response.data.total
        })
        .catch(error => { // 请求失败
          console.log(error)
        })
    },
    resetData() { // 清空按钮的方法
      // 清空已输入数据
      this.userQuery = {}
      // 查询所有  恢复未进行条件查询的状态
      this.getList()
    },
    removeDataById(id) { // 删除讲师按钮的方法
      this.$confirm('此操作将删除用户信息, 是否继续?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        userApi.delete(id)
          .then(response => { // 删除成功
            // 提示信息
            this.$message({
              type: 'success',
              message: '删除成功🤭'
            })
            // 回到列表页面
            this.getList()
          })
      })
      // 点取消执行catch方法  用户体验角度
      // 此处无需进行提示取消
      // 框架在./utils/request.js封装好了提示error的方法
    }
  }
}
</script>
