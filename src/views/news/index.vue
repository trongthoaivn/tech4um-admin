<template>
  <div class="app-container">
    <el-button type="primary" icon="el-icon-circle-plus-outline" round plain @click="newnews">Thêm tin tức mới</el-button>

    <el-dialog title="Thêm tin tức mới" :visible.sync="dialogFormVisible">
      <el-form :model="news">
        <el-form-item label="Mã quản trị" :label-width="formLabelWidth">
          <el-input v-model="news.admin_id" placeholder="Mã quản trị" autocomplete="off" :disabled="true" />
        </el-form-item>
        <el-form-item label="Tiêu đề" :label-width="formLabelWidth">
          <el-input v-model="news.title" type="textarea" :autosize="{ minRows: 2, maxRows: 4}" placeholder="Tiêu đề" autocomplete="off" />
        </el-form-item>
        <el-form-item label="Đầu đề" :label-width="formLabelWidth">
          <el-input v-model="news.header" type="textarea" :autosize="{ minRows: 2, maxRows: 4}" placeholder="Đầu đề" autocomplete="off" />
        </el-form-item>
        <el-form-item label="Hình ảnh" :label-width="formLabelWidth">
          <el-input v-model="news.img" placeholder="Hình ảnh" autocomplete="off" />
        </el-form-item>
        <el-form-item label="Nội dung" :label-width="formLabelWidth">
          <el-input v-model="news.content" type="textarea" :autosize="{ minRows: 2, maxRows: 4}" placeholder="Nội dung" autocomplete="off" />
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
      <el-table-column label="Mã ID" width="48" align="center">
        <template slot-scope="scope">
          <span>{{ scope.row.news.news_id }}</span>
        </template>
      </el-table-column>
      <el-table-column label="Mã QT" width="48" align="center">
        <template slot-scope="scope">
          <span>{{ scope.row.news.admin_id }}</span>
        </template>
      </el-table-column>
      <el-table-column label="Tiêu đề" width="100" align="center">
        <template slot-scope="scope">
          <span>{{ scope.row.news.title }}</span>
        </template>
      </el-table-column>
      <el-table-column label="Đầu đề" width="180" align="center">
        <template slot-scope="scope">
          {{ scope.row.news.header }}
        </template>
      </el-table-column>
      <el-table-column label="Hình ảnh" width="180" align="center">
        <template slot-scope="scope">
          {{ scope.row.news.img }}
        </template>
      </el-table-column>
      <el-table-column label="Nội dung" align="center">
        <template slot-scope="scope">
          {{ scope.row.news.content }}
        </template>
      </el-table-column>
      <el-table-column label="Sửa" width="61" align="center">
        <template slot-scope="scope">
          <el-button
            type="primary"
            icon="el-icon-edit"
            circle
            @click="formedit(
              news_id = scope.row.news.news_id,
              admin_id = scope.row.news.admin_id,
              title = scope.row.news.title,
              header = scope.row.news.header,
              img = scope.row.news.img,
              content = scope.row.news.content
            )"
          />
          <el-dialog title="Thêm tin tức mới" :visible.sync="dialogVisible">
            <el-form :model="news">
              <el-form-item label="Mã ID" :label-width="formLabelWidth">
                <el-input v-model="news.news_id" placeholder="Mã ID" autocomplete="off" :disabled="true" />
              </el-form-item>
              <el-form-item label="Mã quản trị" :label-width="formLabelWidth">
                <el-input v-model="news.admin_id" placeholder="Mã quản trị" autocomplete="off" :disabled="true" />
              </el-form-item>
              <el-form-item label="Tiêu đề" :label-width="formLabelWidth">
                <el-input v-model="news.title" type="textarea" :autosize="{ minRows: 2, maxRows: 4}" placeholder="Tiêu đề" autocomplete="off" />
              </el-form-item>
              <el-form-item label="Đầu đề" :label-width="formLabelWidth">
                <el-input v-model="news.header" type="textarea" :autosize="{ minRows: 2, maxRows: 4}" placeholder="Đầu đề" autocomplete="off" />
              </el-form-item>
              <el-form-item label="Hình ảnh" :label-width="formLabelWidth">
                <el-input v-model="news.img" placeholder="Hình ảnh" autocomplete="off" />
              </el-form-item>
              <el-form-item label="Nội dung" :label-width="formLabelWidth">
                <el-input v-model="news.content" type="textarea" :autosize="{ minRows: 2, maxRows: 4}" placeholder="Nội dung" autocomplete="off" />
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
          <el-button type="danger" icon="el-icon-delete" circle @click="del(scope.row.news.news_id)" />
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
      dialogFormVisible: false,
      dialogVisible: false,
      formLabelWidth: '150px',
      news: {
        news_id: '',
        title: '',
        content: '',
        admin_id: '',
        view: '',
        header: '',
        img: ''
      }
    }
  },
  created() {
    this.fetchData()
  },
  methods: {
    newnews() {
      this.dialogFormVisible = true
      this.news.news_id = null
      this.news.title = null
      this.news.content = null
      this.news.admin_id = '1'
      this.news.view = '0'
      this.news.header = null
      this.news.img = null
    },
    add() {
      this.dialogFormVisible = false
      axios.post('http://tech4rum.test/api/create-news', {
        title: this.news.title,
        content: this.news.content,
        admin_id: this.news.admin_id,
        view: this.news.view,
        header: this.news.header,
        img: this.news.img
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
    },
    fetchData() {
      this.listLoading = true
      fetch('http://tech4rum.test/api/get-all-news')
        .then(res => res.json())
        .then(res => {
          this.list = res
          this.listLoading = false
        })
    },
    formedit(news_id, admin_id, title, header, img, content) {
      this.dialogVisible = true
      this.news.news_id = news_id
      this.news.title = title
      this.news.content = content
      this.news.admin_id = admin_id
      this.news.view = '0'
      this.news.header = header
      this.news.img = img
    },
    edit() {
      this.dialogVisible = false
      axios.put('http://tech4rum.test/api/update-news/' + this.news.news_id, {
        title: this.news.title,
        content: this.news.content,
        admin_id: this.news.admin_id,
        view: this.news.view,
        header: this.news.header,
        img: this.news.img
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
    del(news_id) {
      this.$confirm('Hành động này không thể hoàn tác. Chắc chứ?', 'Cảnh báo', {
        confirmButtonText: 'OK',
        cancelButtonText: 'Hủy',
        type: 'warning'
      }).then(() => {
        axios.delete('http://tech4rum.test/api/delete-news/' + news_id)
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
    }
  }
}
</script>
