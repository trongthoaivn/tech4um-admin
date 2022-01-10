<template>
  <div class="app-container">
    <el-row>
      <el-button type="primary" icon="el-icon-circle-plus-outline" round plain @click="newadmin">Tài khoản quản trị mới</el-button>
    </el-row>
    <el-dialog title="Tài khoản quản trị mới" :visible.sync="dialogFormVisible">
      <el-form :model="admin">
        <el-form-item label="Tên đầy đủ" :label-width="formLabelWidth">
          <el-input v-model="admin.fullname" placeholder="Tên đầy đủ" autocomplete="off" />
        </el-form-item>
        <el-form-item label="Tên tài khoản" :label-width="formLabelWidth">
          <el-input v-model="admin.username" placeholder="Tên tài khoản" autocomplete="off" />
        </el-form-item>
        <el-form-item label="Mật khẩu" :label-width="formLabelWidth">
          <el-input v-model="admin.password" placeholder="Mật khẩu" autocomplete="off" />
        </el-form-item>
      </el-form>
      <span slot="footer" class="dialog-footer">
        <el-button @click="dialogFormVisible = false">Hủy</el-button>
        <el-button type="primary" @click="add">Thêm</el-button>
      </span>
    </el-dialog>
    <el-table
      v-loading="listLoading"
      :data="list"
      element-loading-text="Đang tải"
      border
      fit
      highlight-current-row
    >
      <el-table-column align="center" label="STT" width="48">
        <template slot-scope="scope">
          {{ scope.$index+1 }}
        </template>
      </el-table-column>
      <el-table-column label="Mã ID" width="59" align="center">
        <template slot-scope="scope">
          <span>{{ scope.row.admin_id }}</span>
        </template>
      </el-table-column>
      <el-table-column label="Tên đầy đủ" align="center">
        <template slot-scope="scope">
          <span>{{ scope.row.fullname }}</span>
        </template>
      </el-table-column>
      <el-table-column label="Tên đăng nhập" width="150" align="center">
        <template slot-scope="scope">
          <span>{{ scope.row.username }}</span>
        </template>
      </el-table-column>
      <el-table-column label="Mật khẩu" width="150" align="center">
        <template slot-scope="scope">
          <span>{{ scope.row.password }}</span>
        </template>
      </el-table-column>
      <el-table-column align="center" prop="created_at" label="Ngày tạo" width="148">
        <template slot-scope="scope">
          <i class="el-icon-time" />
          <span>{{ ' ' + format_date(scope.row.created_at) }}</span>
        </template>
      </el-table-column>
      <el-table-column align="center" prop="created_at" label="Ngày sửa" width="148">
        <template slot-scope="scope">
          <i class="el-icon-time" />
          <span>{{ ' ' + format_date(scope.row.updated_at) }}</span>
        </template>
      </el-table-column>
      <el-table-column label="Sửa" width="61" align="center">
        <template slot-scope="scope">
          <el-button type="primary" icon="el-icon-edit" circle @click="newedit(admin_id = scope.row.admin_id, fullname = scope.row.fullname, username = scope.row.username, password = scope.row.password)" />
          <el-dialog title="Sửa tài khoản quản trị" :visible.sync="dialogVisible">
            <el-form :model="admin">
              <el-form-item label="Mã ID" :label-width="formLabelWidth">
                <el-input v-model="admin.admin_id" placeholder="Mã ID" autocomplete="off" :disabled="true" />
              </el-form-item>
              <el-form-item label="Tên đầy đủ" :label-width="formLabelWidth">
                <el-input v-model="admin.fullname" placeholder="Tên đầy đủ" autocomplete="off" />
              </el-form-item>
              <el-form-item label="Tên tài khoản" :label-width="formLabelWidth">
                <el-input v-model="admin.username" placeholder="Tên tài khoản" autocomplete="off" />
              </el-form-item>
              <el-form-item label="Mật khẩu" :label-width="formLabelWidth">
                <el-input v-model="admin.password" placeholder="Mật khẩu" autocomplete="off" />
              </el-form-item>
            </el-form>
            <span slot="footer" class="dialog-footer">
              <el-button @click="dialogVisible = false">Hủy</el-button>
              <el-button type="primary" @click="edit(scope.row.admin_id)">Sửa</el-button>
            </span>
          </el-dialog>
        </template>
      </el-table-column>
      <el-table-column label="Xóa" width="61" align="center">
        <template slot-scope="scope">
          <el-button type="danger" icon="el-icon-delete" circle @click="del(scope.row.admin_id)" />
        </template>
      </el-table-column>
    </el-table>
  </div>
</template>

<script>
import axios from 'axios'
import moment from 'moment'

export default {
  filters: {
    statusFilter(status) {
      const statusMap = {
        published: 'success',
        draft: 'gray',
        deleted: 'danger'
      }
      return statusMap[status]
    }
  },
  data() {
    return {
      list: null,
      listLoading: true,
      dialogFormVisible: false,
      dialogVisible: false,
      formLabelWidth: '150px',
      admin: {
        admin_id: '',
        fullname: '',
        username: '',
        password: ''
      }
    }
  },
  created() {
    this.fetchData()
  },
  methods: {
    format_date(value) {
      if (value) {
        return moment(String(value)).format('HH:MM DD/MM/YYYY')
      }
    },
    fetchData() {
      this.listLoading = true
      fetch('http://tech4rum.test/api/get-all-admin')
        .then(res => res.json())
        .then(res => {
          this.list = res
          this.listLoading = false
          return res
        })
    },
    del(admin_id) {
      this.$confirm('Hành động này không thể hoàn tác. Chắc chứ?', 'Cảnh báo', {
        confirmButtonText: 'OK',
        cancelButtonText: 'Hủy',
        type: 'warning'
      }).then(() => {
        axios.delete('http://tech4rum.test/api/delete-admin/' + admin_id)
          .then(res => {
            if (res.data === 'Delete success') {
              this.$message({
                message: 'Xóa thành công!',
                type: 'success'
              })
              setTimeout(this.fetchData, 0)
            } else {
              this.$message({
                message: 'Có lỗi xảy ra vui lòng thử lại!',
                type: 'warning'
              })
              setTimeout(this.fetchData, 0)
            }
          })
      })
    },
    newedit(admin_id, fullname, username, password) {
      this.dialogVisible = true
      this.admin.admin_id = admin_id
      this.admin.fullname = fullname
      this.admin.username = username
      this.admin.password = password
    },
    edit() {
      this.dialogVisible = false
      axios.put('http://tech4rum.test/api/update-admin/' + this.admin.admin_id, {
        fullname: this.admin.fullname,
        username: this.admin.username,
        password: this.admin.password
      })
        .then(res => {
          if (res.data === 'Update success') {
            this.$message({
              message: 'Sửa thành công!',
              type: 'success'
            })
            setTimeout(this.fetchData, 0)
          } else {
            this.$message({
              message: 'Có lỗi xảy ra vui lòng thử lại!',
              type: 'warning'
            })
            setTimeout(this.fetchData, 0)
          }
        })
    },
    newadmin() {
      this.dialogFormVisible = true
      this.admin.fullname = null
      this.admin.username = null
      this.admin.password = null
    },
    add() {
      this.dialogFormVisible = false
      axios.post('http://tech4rum.test/api/create-admin', {
        fullname: this.admin.fullname,
        username: this.admin.username,
        password: this.admin.password
      })
        .then(res => {
          if (res.data === 'Create success') {
            this.$message({
              message: 'Thêm thành công!',
              type: 'success'
            })
            setTimeout(this.fetchData, 0)
          } else {
            this.$message({
              message: 'Có lỗi xảy ra vui lòng thử lại!',
              type: 'warning'
            })
            setTimeout(this.fetchData, 0)
          }
        })
    }
  }
}
</script>
