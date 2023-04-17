<template>
  <div>
    <el-container>
      <el-header>
        <div class="topClass">
          <div class="name">商品分类设置</div>
          <div class="function">
            <el-button type="primary" size="mini">搜索</el-button>
            <el-button
              type="primary"
              size="mini"
              @click="centerDialogVisible = true"
              >添加大分类</el-button
            >
            <el-button type="primary" size="mini" @click="DeleteAll"
              >删除</el-button
            >
          </div>
        </div>
      </el-header>
      <el-main>
        <!-- 主要表格 -->
        <el-table
          :data="tableData"
          style="width: 100%; margin-bottom: 20px"
          row-key="typeId"
          :border="true"
          default-expand-all
          :tree-props="{ children: 'children', hasChildren: 'hasChildren' }"
        >
          >
          <el-table-column fixed prop="date" label="选择" width="100" sortable>
            <input type="checkbox" value="true" />
          </el-table-column>
          <el-table-column prop="number" label="序号" width="100">
          </el-table-column>

          <el-table-column prop="typeId" label="分类id" width="100">
          </el-table-column>
          <el-table-column prop="typeName" label="分类名称" width="120">
          </el-table-column>
          <el-table-column prop="more" label="备注" width="180">
          </el-table-column>
          <el-table-column prop="status" label="状态" width="100">
            <template slot-scope="scope">
              <span v-if="scope.row.status == true">禁用</span>
              <span v-else>启用</span>
            </template>
          </el-table-column>
          <el-table-column prop="people" label="操作人" width="100">
          </el-table-column>
          <el-table-column prop="date" label="操作日期" width="100">
          </el-table-column>
          <el-table-column fixed="right" label="操作" width="200">
            <template slot-scope="scope">
              <el-button
                @click="modify(scope.row)"
                type="text"
                size="small"
                :disabled="scope.row.status == false"
                >修改</el-button
              >
              <el-button
                v-if="scope.row.hasChildren == 'true'"
                type="text"
                size="small"
                @click="centerDialogVisible1 = true"
                :disabled="scope.row.status == false"
                >添加子分类</el-button
              >
              <el-button type="text" size="small" @click="xuanzhuan(scope.row)"
                >禁用</el-button
              >
              <el-button
                type="text"
                size="small"
                @click="Delete(scope.row)"
                :disabled="scope.row.status == false"
                >删除</el-button
              >
            </template>
          </el-table-column>
        </el-table>
        <!-- 弹出框 -->
        <el-dialog
          title="商品新增分类"
          :visible.sync="centerDialogVisible"
          width="30%"
        >
          <el-form ref="bigData" :model="bigData" label-width="150px">
            <el-form-item label="大表单名称">
              <el-input v-model="bigData.typeName" clearable></el-input>
            </el-form-item>
            <el-form-item label="备注">
              <el-input v-model="bigData.more" clearable></el-input>
            </el-form-item>
          </el-form>
          <span slot="footer" class="dialog-footer">
            <el-button @click="centerDialogVisible = false">取 消</el-button>
            <el-button type="primary" @click="add">确 定</el-button>
          </span>
        </el-dialog>
        <el-dialog
          title="商品新增小分类"
          :visible.sync="centerDialogVisible1"
          width="30%"
        >
          <el-form ref="bigData" :model="smallData" label-width="150px">
            <el-form-item label="上级分类名称">
              <el-input :value="this.bigData.typeName" disabled></el-input>
            </el-form-item>
            <el-form-item label="分类名称">
              <el-input v-model="smallData.typeName" clearable></el-input>
            </el-form-item>
            <el-form-item label="备注">
              <el-input v-model="smallData.more" clearable></el-input>
            </el-form-item>
          </el-form>
          <span slot="footer" class="dialog-footer">
            <el-button @click="centerDialogVisible1 = false">取 消</el-button>
            <el-button type="primary" @click="add1">确 定</el-button>
          </span>
        </el-dialog>
        <el-dialog
          title="商品编辑"
          :visible.sync="centerDialogVisible2"
          width="30%"
        >
          <el-form ref="Data" v-model="Data" label-width="150px">
            <el-form-item label="分类名称">
              <el-input v-model="Data.typeName" clearable></el-input>
            </el-form-item>
            <el-form-item label="备注">
              <el-input v-model="Data.more" clearable></el-input>
            </el-form-item>
          </el-form>
          <span slot="footer" class="dialog-footer">
            <el-button @click="centerDialogVisible2 = false">取 消</el-button>
            <el-button type="primary" @click="centerDialogVisible2 = false"
              >确 定</el-button
            >
          </span>
        </el-dialog>
      </el-main>
    </el-container>
  </div>
</template>

<script>
import axios from "axios";
export default {
  name: "HomeView",
  data() {
    return {
      tableData: [],
      centerDialogVisible: false,
      centerDialogVisible1: false,
      centerDialogVisible2: false,
      disabled: false,
      bigData: {
        children: [],
        date: "2023-4-14",
        more: "本草纲目",
        number: 10,
        people: "苏军",
        status: "false",
        typeId: 100,
        typeName: "苏",
        hasChildren: "true",
      },
      smallData: {
        date: "2023-4-14",
        more: "",
        number: 10,
        people: "苏军",
        status: "false",
        typeId: 100,
        typeName: "",
        father: "false",
      },
      Data: {
        date: "2023-4-14",
        more: "",
        number: 10,
        people: "苏军",
        status: "false",
        typeId: 100,
        typeName: "",
        father: "false",
      },
      info: [],
      hai: 0,
    };
  },
  created() {
    this.show();
  },
  methods: {
    show() {
      axios
        .get("http://127.0.0.1:4523/m1/2589202-0-default/getMessage")
        .then((res) => {
          this.tableData = res.data.info;

          // res.data.info.forEach((item) => {
          //   if (item.typeName) {
          //     this.father.push(item.typeName);
          //   }
          // });
          // console.log(this.father);
          // localStorage.setItem("info", JSON.stringify(res.data.info));
          this.info = res.data.info;
          console.log(res);
        })
        .catch((err) => {
          console.log(err);
        });
    },
    xuanzhuan(row) {
      console.log(row.status);

      for (let index = 0; index < this.tableData.length; index++) {
        if (row.typeId == this.info[index].typeId) {
          this.tableData[index].status = !row.status;
        }
      }
      this.hai += 1;
      let obj = this.tableData;
      this.tableData = obj;
      console.log(this.tableData);
    },
    add() {
      var obj = this.info;
      obj.push(this.bigData);
      // console.log(this.bigData);
      this.$nextTick(() => {
        this.tableData = obj;
      });

      this.bigData.number += 1;
      this.bigData.typeId += 1;
      this.centerDialogVisible = false;
    },
    handleClick(row) {
      console.log(row);
    },
    select(select, row) {
      console.log(select, row, "21312");
    },
    add1(row) {
      console.log(row, "123123");
      // this.smallData.typeName = row.typeName;
      // this.smallData.more = row.more;
      var obj = this.info;
      obj.push(this.smallData);
      // console.log(this.bigData);
      this.tableData = obj;
      this.smallData.number += 1;
      this.smallData.typeId += 1;
      this.centerDialogVisible1 = false;
    },
    modify(row) {
      // console.log(row, "12321");
      // this.Data.more = row.more;
      // this.Data.typeName = row.typeName;
      var obj = this.info;
      let new_array = obj.filter((obj) => obj.typeId != row.typeId);
      this.Data.more = row.more;
      this.Data.typeName = row.typeName;
      this.Data.number = row.number;
      this.Data.people = row.people;
      this.Data.status = row.status;
      this.Data.typeId = row.typeId;
      this.Data.father = "false";
      new_array.unshift(this.Data);
      this.tableData = new_array;
      this.centerDialogVisible2 = true;
    },
    Delete(row) {
      var obj = this.info;
      let new_array = obj.filter((obj) => obj.typeId != row.typeId);
      this.info = new_array;
      this.tableData = new_array;
    },
    DeleteAll() {
      var checkboxes = document.querySelectorAll(
        'input[type="checkbox"]:checked'
      );

      console.log(checkboxes);
      for (let index = 0; index < checkboxes.length; index++) {
        this.info.pop();
      }

      this.tableData = this.info;
    },
  },
  destroyed() {},
};
</script>

<style>
.topClass {
  display: flex;
  justify-content: space-between;
}
</style>