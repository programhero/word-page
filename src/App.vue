<template>
  <div id="app">
    <el-form class="form" label-width="60px">
      <el-form-item class="form-item" label="单词:" prop="wordd">
        <el-input v-model="searcherForm.wordd" :clearable="true"></el-input>
      </el-form-item>
      <el-form-item class="form-item" label="类型:">
        <el-select v-model="searcherForm.wordType" :clearable="true">
          <el-option v-for="wordType in wordTypes" :key="wordType" :value="wordType"></el-option>
        </el-select>
      </el-form-item>
      <el-form-item class="form-item" label="主题:">
        <el-select v-model="searcherForm.theme" :clearable="true">
          <el-option v-for="theme in themes" :key="theme" :value="theme"></el-option>
        </el-select>
      </el-form-item>
      <el-form-item  class="form-item" label="词频">
        <el-input v-model="searcherForm.frequency" :clearable="true"></el-input>
      </el-form-item>
        <el-form-item  class="form-item" label-width="10px">
        <el-button type="primary" @click="search()" icon="el-icon-search">搜索</el-button>
        <el-button type="danger" @click="reset()" icon="el-icon-refresh-left">重置</el-button>
      </el-form-item>
    </el-form>
    <div style="text-align:left;margin-bottom:8px">
      <el-button type="success" @click="exportXsl()" icon="el-icon-download">导出</el-button>
      <el-button type="primary" @click="addRecord()" icon="el-icon-plus">添加</el-button>
    </div>
    <el-dialog :visible="formVisible" width="1200px" :before-close="() => formVisible = false">
      <el-form ref="form" :model="form" label-width="80px" :rules="formRules">
        <el-row>
          <el-col :span="6">      <el-form-item  label="单词:" prop="wordd">
        <el-input v-model="form.wordd" @change="worddChange"></el-input>
      </el-form-item></el-col>
          <el-col :span="6">
                  <el-form-item  label="类型:" prop="wordType">
        <el-select v-model="form.wordType">
          <el-option v-for="type in wordTypes" :key="type" :value="type"></el-option>
        </el-select>
      </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="主题:">
            <el-select v-model="form.theme">
              <el-option v-for="theme in themes" :key="theme" :value="theme"></el-option>
            </el-select>
          </el-form-item>
          </el-col>
          <el-col :span="6">
          <el-form-item  label="词频">
            <div style="height:29px; text-align:left;">{{ form.frequency }}</div>
          </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="动词变位:">
              <el-input v-model="form.conjugation"></el-input>
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item  label="固搭:">
              <el-input v-model="form.phrase"></el-input>
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="是否新增:">
                <el-checkbox v-model="isAdded"></el-checkbox>
            </el-form-item>
          </el-col>
          <el-col :span="24">
            <el-form-item label="例句:">
                <el-input v-model="form.example" type="textarea" size="4"></el-input>
            </el-form-item>
          </el-col>
          <el-col :span="24">
            <el-form-item label="备注:">
                <el-input v-model="form.remark" type="textarea" size="4"></el-input>
            </el-form-item>
          </el-col>
           <el-col :span="6">
            <el-form-item label="录入时间:">
                <span>{{ form.createdTime }}</span>
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="更新时间:">
                <span>{{ form.lastUpdatedTime }}</span>
            </el-form-item>
          </el-col>
        </el-row>
        <el-row>
          <el-col :span="24">
                        <div style="text-align:left;margin-bottom:8px">
      <el-button type="primary" @click="save" icon="el-icon-document">保存</el-button>
      <el-button type="danger" @click="resetForm" icon="el-icon-refresh-left">重置</el-button></div>
          </el-col>
        </el-row>

    
      </el-form>
    </el-dialog>
    <el-table
      :data="tableData"
      :stripe="true"
      :border="true"
      style="width: 100%;margin-bottom:20px">
      <el-table-column
        prop="wordd"
        label="单词"
        width="180">
      </el-table-column>
      <el-table-column
        prop="wordType"
        label="类型"
        width="180">
      </el-table-column>
      <el-table-column
        prop="theme"
        label="主题">
      </el-table-column>
      <el-table-column
        prop="frequency"
        label="频率">
      </el-table-column>
      <el-table-column
        prop="conjugation"
        label="动词变位">
      </el-table-column>
      <el-table-column
        prop="phrase"
        label="固搭">
      </el-table-column>
      <el-table-column
        prop="example"
        label="例句">
        <template slot-scope="scope">
          <show-message :msg="scope.row.example"></show-message>
        </template>
      </el-table-column>
      <el-table-column
        prop="remark"
        label="备注">
                <template slot-scope="scope">
          <show-message :msg="scope.row.remark"></show-message>
        </template>
      </el-table-column>
      <el-table-column width="140"
        prop="createdTime"
        label="创建时间">
      </el-table-column>
      <el-table-column width="140"
        prop="lastUpdatedTime"
        label="最后更新时间">
      </el-table-column>
      <el-table-column width="100" label="操作">
        <template slot-scope="scope">
          <a class="link-type" @click="del(scope.row.id)()">
              删除
          </a>
        </template>

      </el-table-column>
    </el-table>
    <el-pagination
      @size-change="handleSizeChange"
      @current-change="handleCurrentChange"
      :current-page="currentPage"
      :page-sizes="[10, 20, 30, 40]"
      :page-size="pageSize"
      layout="total, sizes, prev, pager, next, jumper"
      :total="total">
    </el-pagination>
  </div>
</template>

<script>
import ShowMessage from './components/ShowMessage'


const wordTypes = [
  '名词',
  '动词',
  '形容词',
  '副词',
  '连词',
  '其它'
]
const themes = [
  '社会问题-青少年',
  '社会问题-中学生',
  '社会问题-大学生',
  '社会问题-妇女',
  '社会问题-老年人',
  '环境+生态问题'
]
const searcherForm =  {
            wordd: '',
            wordType: '',
            theme: '',
            frequency: null,
            size: 10,
            page: 1
          }
const defaultForm = {
  frequency: 1
}
function urlParse(obj) {
  let paramPart = ''
  for(const k in obj) {
    if (obj[k]) {
     paramPart += k
     paramPart += '='
     paramPart += encodeURIComponent(obj[k])
     paramPart += '&'
    }
  }
  if  (paramPart) {
   paramPart.substring(0, paramPart.length)
  }
  return paramPart
}
export default {
  name: 'app',
  components: {
    ShowMessage
  },
  mounted() {
    this.search()
  },
  data() {
    return {
          pageSize: 10,
          currentPage: 1,
          total: 0,
          searcherForm: Object.assign({}, searcherForm),
          formVisible: false,
          form: { frequency: 1 },
          tableData: [],
          themes,
          wordTypes,
          formRules: {
            wordd: [{ required: true, message: '请输入单词', trigger: 'blur' }],
            wordType: [{ required: true, message: '请选择单词类型', trigger: 'blur' }]
          },
          isAdded: false
    }
  },
  methods: {
    del(id) {
      return () => {
        this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          window.fetch('/api/v1/word/'+id, {
            method: 'DELETE'
          }).then((resp) => {
            if (resp.ok) {
              this.$message({
                type: 'success',
                message: '删除成功!'
              });
              this.search()
            }
          })
        });
      }
    },
    handleSizeChange(pageSize) {
      this.searcherForm.size = pageSize
      this.pageSize = pageSize
      this.search()
    },
    handleCurrentChange(currentPage) {
      this.currentPage = currentPage
      this.searcherForm.page = currentPage
      this.search()
    },
    search() {
      window.fetch("/api/v1/word?" + urlParse(this.searcherForm) ).then(resp => {
        return resp.json()
      }).then((resp) => {
        this.tableData = resp.content
        this.total = resp.totalElements
      })
    },
    exportXsl() {
      window.open("/api/v1/word/export?" + urlParse(this.searcherForm),"_blank ")
    },
    addRecord() {
      this.formVisible = true
      this.isAdded = false
      this.form = Object.assign({}, defaultForm)
    },
    reset() {
      this.searcherForm = Object.assign({}, searcherForm)
    },
    worddChange(value) {
      window.fetch("/api/v1/word/search?word=" + value).then((resp) => {
        if (resp.status == 200) {
          return resp.json()
        } else {
          this.form.id  = null
          return Promise.reject(resp.status)
        }
      }).then(data => {
        this.form = data
      }).catch(err => {
        window.console.error(err)
      })
    },
    save() {
      this.$refs['form'].validate().then(() => {
        window.console.log(this.form)
        let body = Object.assign({}, this.form)
        if (this.isAdded) {
          body.id = null
          body.frequency = 1
        }
        fetch('/api/v1/word', {
          headers: {
            "Content-Type": "application/json"
          },
          body: JSON.stringify(body),
          method: 'POST'
        }).then(() => {
          this.search()
          this.formVisible = false
        })
      })
    },
    resetForm() {
      this.form = Object.assign({}, defaultForm)
    }
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
.form-item {
  display: inline-block;
  width: 200px;
}
.form {
  text-align: left;
}
.link-type {
  color: #337ab7;
  cursor: pointer;
}

.link-type:hover,.link-type:focus {
    color: rgb(32, 160, 255);
}
</style>
