<template>
  <div>
    <div style="margin-bottom: 10px">
      <el-input v-model="params.name" style="width: 200px" placeholder="根据姓名查找"></el-input>
      <el-input v-model="params.phone" style="width: 200px; margin-left: 5px" placeholder="根据电话查找"></el-input>
      <el-button round type="warning" style="margin-left: 10px" @click="findBySearch()">查找联系人</el-button>
      <el-button round type="warning" style="margin-left: 10px" @click="reset()">清空查询栏</el-button>
      <el-button round type="primary" style="margin-left: 10px" @click="add()" icon="el-icon-plus">新增联系人</el-button>
    </div>
    <div>
      <el-table :data="tableData" style="width: 100%">
        <el-table-column prop="name" label="姓名" width="90"></el-table-column>
        <el-table-column prop="sex" label="性别" width="90"></el-table-column>
        <el-table-column prop="age" label="年龄" width="90"></el-table-column>
        <el-table-column prop="phone" label="电话"></el-table-column>
        <el-table-column prop="address" label="地址"></el-table-column>
        <el-table-column label="操作">
          <template slot-scope="scope">
            <el-button type="primary" @click="edit(scope.row)" icon="el-icon-edit">修改联系人信息</el-button>
            <el-popconfirm title="确定删除该联系人吗？" @confirm="del(scope.row.id)">
              <el-button slot="reference" type="danger" style="margin-left: 5px" icon="el-icon-delete">删除该联系人</el-button>
            </el-popconfirm>
          </template>
        </el-table-column>
      </el-table>
    </div>
    <div style="margin-top: 10px">
        <el-pagination
          background
          @size-change="handleSizeChange"
          @current-change="handleCurrentChange"
          :current-page="params.pageNum"
          :page-sizes="[5, 10]"
          :page-size="params.pageSize"
          layout="total, sizes, prev, pager, next, jumper"
          :total="total">
        </el-pagination>
    </div>
    <div>
      <el-dialog title="请填写联系人信息" :visible.sync="dialogFormVisible" width="30%">
          <el-form :model="form">
            <el-form-item label="姓名" label-width="15%">
              <el-input v-model="form.name" autocomplete="off" style="width: 90%"></el-input>
            </el-form-item>
            <el-form-item label="性别" label-width="15%">
              <el-radio v-model="form.sex" label="男">男</el-radio>
              <el-radio v-model="form.sex" label="女">女</el-radio>
            </el-form-item>
            <el-form-item label="年龄" label-width="15%">
              <el-input v-model="form.age" autocomplete="off" style="width: 90%"></el-input>
            </el-form-item>
            <el-form-item label="电话" label-width="15%">
              <el-input v-model="form.phone" autocomplete="off" style="width: 90%"></el-input>
            </el-form-item>
            <el-form-item label="地址" label-width="15%">
              <el-input v-model="form.address" autocomplete="off" style="width: 90%"></el-input>
            </el-form-item>
          </el-form>
          <div slot="footer" class="dialog-footer">
            <el-button @click="dialogFormVisible = false">取 消</el-button>
            <el-button type="primary" @click="submit()">确 定</el-button>
          </div>
      </el-dialog>
    </div>
  </div>
</template>

<script>
import request from "@/utils/request";
  export default {
    name: "AdminView",
    data() {
      return {
        params: {
          name: '',
          phone: '',
          pageNum: 1,
          pageSize: 5
        },
        tableData: [],
        total: 0,
        dialogFormVisible: false,
        form: {}
      }
    },
    created() {
      this.load();
      this.findBySearch();
    },
    methods: {
      load() {
        request.get("/admin").then(res => {
          if (res.code === '0') {
            this.tableData = res.data;
          }
        })
      },
      findBySearch() {
        request.get("/admin/search", {
          params: this.params
        }).then(res => {
          if (res.code === '0') {
            this.tableData = res.data.list;
            this.total = res.data.total;
          } else {
            this.$message({
              message: res.msg,
              type: 'success'
          });
          }
        })
      },
      reset(){
        this.params = {
            pageNum: 1,
            pageSize: 5,
            name: '',
            phone: ''
        }
        this.findBySearch();
      },
      handleSizeChange(pageSize) {
        this.params.pageSize = pageSize;
        this.findBySearch();
      },
      handleCurrentChange(pageNum) {
        this.params.pageNum = pageNum;
        this.findBySearch();
      },
      add(){
        this.form = {};
        this.dialogFormVisible = true;
      },
      edit(obj){
        this.form = obj;
        this.dialogFormVisible = true;
      },
      submit(){
        request.post("/admin", this.form).then(res => {
          if(res.code === '0'){
            this.$message({
              message: '操作成功!',
              type: 'success'
            });
            this.dialogFormVisible = false;
            this.findBySearch();
          }else{
            this.$message({
              message: res.msg,
              type: 'success'
            });
          }
        })
      },
      del(id) {
        request.delete("/admin/" + id).then(res => {
          if (res.code === '0') {
            this.$message({
              message: '删除成功!',
              type: 'success'
            });
          this.findBySearch();
          } else {
          this.$message({
            message: res.msg,
            type: 'success'
            });
          }
        })
      }
    }
  }
</script>