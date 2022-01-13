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
      <el-table-column label="Mã ID" width="48" align="center">
        <template slot-scope="scope">
          <span>{{ scope.row.topic_id }}</span>
        </template>
      </el-table-column>
      <el-table-column label="Tiêu đề" width="150" align="center">
        <template slot-scope="scope">
          <span>{{ scope.row.title }}</span>
        </template>
      </el-table-column>
      <el-table-column label="Nội dung" align="center">
        <template slot-scope="scope">
          <span>{{ scope.row.content }}</span>
        </template>
      </el-table-column>
      <el-table-column label="Thể loại" width="150" align="center">
        <template slot-scope="scope">
          <span>{{ scope.row.category }}</span>
        </template>
      </el-table-column>
      <el-table-column label="Mã TV" width="48" align="center">
        <template slot-scope="scope">
          <span>{{ scope.row.user_id }}</span>
        </template>
      </el-table-column>
      <el-table-column label="Mã TL" width="48" align="center">
        <template slot-scope="scope">
          {{ scope.row.category_id }}
        </template>
      </el-table-column>
      <el-table-column align="center" prop="created_at" label="Ngày tạo" width="148">
        <template slot-scope="scope">
          <i class="el-icon-time" />
          <span>{{ ' ' + format_date(scope.row.created_at) }}</span>
        </template>
      </el-table-column>
      <el-table-column align="center" prop="updated_at" label="Ngày sửa" width="148">
        <template slot-scope="scope">
          <i class="el-icon-time" />
          <span>{{ ' ' + format_date(scope.row.updated_at) }}</span>
        </template>
      </el-table-column>
      <el-table-column label="Sửa" width="61" align="center">
        <template slot-scope="scope">
          <el-button type="info" icon="el-icon-s-unfold" circle @click="expand(scope.row.topic_id)" />

          <el-dialog title="Quản lý bình luận" :visible.sync="dialogTableVisible">
            <el-table
              v-loading="gridLoading"
              :data="gridData"
              element-loading-text="Đang tải"
            >
              <el-table-column property="comment.comment_id" label="Mã ID" width="48" />
              <el-table-column property="comment.topic_id" label="Mã BV" width="48" />
              <el-table-column property="comment.user_id" label="Mã TV" width="48" />
              <el-table-column property="user" label="Tên thành viên" width="150" />
              <el-table-column property="comment.comment" label="Bình luận" />
              <el-table-column property="comment.datetime" label="Ngày BL" width="151" />
              <el-table-column label="Xóa" width="61" align="center">
                <template slot-scope="scope">
                  <el-button type="danger" icon="el-icon-delete" circle @click="delcmt(scope.row.comment.comment_id), dialogTableVisible = true" />
                </template>
              </el-table-column>
            </el-table>
          </el-dialog>
        </template>
      </el-table-column>
      <el-table-column label="Xóa" width="61" align="center">
        <template slot-scope="scope">
          <el-button type="danger" icon="el-icon-delete" circle @click="del(scope.row.topic_id)" />
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
      gridLoading: true,
      gridData: null,
      dialogTableVisible: false
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
      fetch('http://tech4rum.test/api/get-all-topic')
        .then(res => res.json())
        .then(res => {
          this.list = res
          this.listLoading = false
        })
    },
    del(topic_id) {
      this.$confirm('Hành động này không thể hoàn tác. Chắc chứ?', 'Cảnh báo', {
        confirmButtonText: 'OK',
        cancelButtonText: 'Hủy',
        type: 'warning'
      }).then(() => {
        axios.delete('http://tech4rum.test/api/delete-topic/' + topic_id)
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
    expand(topic_id) {
      this.dialogTableVisible = true
      this.gridLoading = true
      fetch('http://tech4rum.test/api/get-comment-by-topic-id/' + topic_id)
        .then(res => res.json())
        .then(res => {
          this.gridData = res
          this.gridLoading = false
        })
    },
    delcmt(comment_id) {
      this.$confirm('Hành động này không thể hoàn tác. Chắc chứ?', 'Cảnh báo', {
        confirmButtonText: 'OK',
        cancelButtonText: 'Hủy',
        type: 'warning'
      }).then(() => {
        axios.delete('http://tech4rum.test/api/delete-comment/' + comment_id)
          .then(res => {
            if (res.data === 'Delete success') {
              this.$message({
                message: 'Xóa thành công!',
                type: 'success'
              })
              this.dialogTableVisible = false
            } else {
              this.$message({
                message: 'Có lỗi xảy ra vui lòng thử lại!',
                type: 'warning'
              })
              this.dialogTableVisible = false
            }
          })
      }).catch(() => {
        this.$message({
          type: 'info',
          message: 'Hủy thao tác'
        })
      })
    }
  }
}
</script>
