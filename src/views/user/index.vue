<template>
  <div class="app-container">
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
          <span>{{ scope.row.user_id }}</span>
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
      <el-table-column label="Hình ảnh" width="150" align="center">
        <template slot-scope="scope">
          {{ scope.row.image }}
        </template>
      </el-table-column>
      <el-table-column label="Địa chỉ mail" width="110" align="center">
        <template slot-scope="scope">
          {{ scope.row.email }}
        </template>
      </el-table-column>
      <el-table-column label="Số điện thoại" width="120" align="center">
        <template slot-scope="scope">
          {{ scope.row.phone }}
        </template>
      </el-table-column>
      <el-table-column label="Điểm" width="56" align="center">
        <template slot-scope="scope">
          {{ scope.row.score }}
        </template>
      </el-table-column>
      <el-table-column label="Sửa" width="61" align="center">
        <template slot-scope="scope">
          <el-button
            type="primary"
            icon="el-icon-edit"
            circle
            @click="formedit(
              user_id = scope.row.user_id,
              fullname = scope.row.fullname,
              username = scope.row.username,
              password = scope.row.password,
              image = scope.row.image,
              email = scope.row.email,
              phone = scope.row.phone,
              score = scope.row.score
            )"
          />
          <el-dialog title="Sửa tài khoản thành viên" :visible.sync="dialogVisible">
            <el-form :model="user">
              <el-form-item label="Mã ID" :label-width="formLabelWidth">
                <el-input v-model="user.user_id" placeholder="Mã ID" autocomplete="off" :disabled="true" />
              </el-form-item>
              <el-form-item label="Tên đầy đủ" :label-width="formLabelWidth">
                <el-input v-model="user.fullname" placeholder="Tên đầy đủ" autocomplete="off" />
              </el-form-item>
              <el-form-item label="Tên tài khoản" :label-width="formLabelWidth">
                <el-input v-model="user.username" placeholder="Tên tài khoản" autocomplete="off" />
              </el-form-item>
              <el-form-item label="Mật khẩu" :label-width="formLabelWidth">
                <el-input v-model="user.password" placeholder="Mật khẩu" autocomplete="off" />
              </el-form-item>
              <el-form-item label="Hình ảnh" :label-width="formLabelWidth">
                <el-input v-model="user.image" placeholder="Hình ảnh" autocomplete="off" />
              </el-form-item>
              <el-form-item label="Địa chỉ mail" :label-width="formLabelWidth">
                <el-input v-model="user.email" placeholder="Địa chỉ mail" autocomplete="off" />
              </el-form-item>
              <el-form-item label="Số điện thoại" :label-width="formLabelWidth">
                <el-input v-model="user.phone" placeholder="Số điện thoại" autocomplete="off" />
              </el-form-item>
              <el-form-item label="Điểm" :label-width="formLabelWidth">
                <el-input v-model="user.score" placeholder="Điểm" autocomplete="off" />
              </el-form-item>
            </el-form>
            <span slot="footer" class="dialog-footer">
              <el-button @click="dialogVisible = false">Hủy</el-button>
              <el-button type="primary" @click="edit">Sửa</el-button>
            </span>
          </el-dialog>
        </template>
      </el-table-column>
      <el-table-column label="Xóa" width="61" align="center">
        <template slot-scope="scope">
          <el-button type="danger" icon="el-icon-delete" circle @click="del(scope.row.user_id)" />
        </template>
      </el-table-column>
    </el-table>
  </div>
</template>

<script>
import axios from 'axios'

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
      dialogVisible: false,
      formLabelWidth: '150px',
      user: {
        user_id: '',
        fullname: '',
        username: '',
        password: '',
        image: '',
        email: '',
        phone: '',
        score: ''
      }
    }
  },
  created() {
    this.fetchData()
  },
  methods: {
    fetchData() {
      this.listLoading = true
      fetch('http://tech4rum.test/api/get-all-user')
        .then(res => res.json())
        .then(res => {
          this.list = res
          this.listLoading = false
        })
    },
    del(user_id) {
      this.$confirm('Hành động này không thể hoàn tác. Chắc chứ?', 'Cảnh báo', {
        confirmButtonText: 'OK',
        cancelButtonText: 'Hủy',
        type: 'warning'
      }).then(() => {
        axios.delete('http://tech4rum.test/api/delete-user/' + user_id)
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
      }).catch(() => {
        this.$message({
          type: 'info',
          message: 'Hủy thao tác'
        })
      })
    },
    formedit(user_id, fullname, username, password, image, email, phone, score) {
      this.dialogVisible = true
      this.user.user_id = user_id
      this.user.fullname = fullname
      this.user.username = username
      this.user.password = password
      this.user.image = image
      this.user.email = email
      this.user.phone = phone
      this.user.score = score
    },
    edit() {
      this.dialogVisible = false
      axios.put('http://tech4rum.test/api/update-user/' + this.user.user_id, {
        fullname: this.user.fullname,
        username: this.user.username,
        password: this.user.password,
        image: this.user.image,
        email: this.user.email,
        phone: this.user.phone,
        score: this.user.score
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
    }
  }
}
</script>
