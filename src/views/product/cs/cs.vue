<template>
  <div>
    <checkboxTable
      :tableData="tableData"
      @Search="Search"
      @editline="editline"
      @deleteline="deleteline"
      @pageChange="pageChange"
      @createInfor="createInfor"
    >
    </checkboxTable>
  </div>
</template>
<script>
import checkboxTable from "./checkboxTable.vue";
export default {
  components: {
    checkboxTable
    //引用组件
  },
  data: () => ({
    //向table传值
    tableData: {
      //是否显示组件
      isShow: true,
      //是否显示新建弹框
      dialogTableVisible: false,
      //表格行项目列表和总条目数
      lineItems: {
        items: [],
        totalCount: 0
      },
      //表格头部信息
      table: {
        header: {}
      },
      //存放搜索的key数组
      search: [],
      //分页器
      fenye: {
        pageIndex: 1,
        pageSize: 10
      }
    }
  }),
  methods: {
    //处理表格数据
    dealTableData(obj) {
      //赋值列表
      this.tableData.lineItems = obj;
      //创建表头名称
      this.tableData.table.header = {
        name: "姓名",
        sex: "性别",
        age: "年龄"
      };
      //搜索条件的key
      this.tableData.search = [
        { name: "姓名", id: "name", value: "" },
        { name: "年龄", id: "age", value: "" }
      ];
    },
    //新建条目
    createInfor(details) {
      this.$post("新建接口", details, {
        emulateJSON: true
      }).then(
        response => {
          if (response.code === "success") {
            this.$message({ message: "创建成功", type: "success" });
            //请求成功后将弹窗关闭
            this.tableData.dialogTableVisible = false;
            //重新请求列表接口，刷新数据
            this.getTableData();
          } else {
            this.$message({ message: "创建失败", type: "warning" });
          }
        },
        err => {
          console.log(err);
        }
      );
    },
    //请求基础表格数据
    getTableData(msg) {
      console.log(msg);
      this.$post("表格列表接口", {
        page: this.tableData.fenye.pageIndex,
        rows: this.tableData.fenye.pageSize
      }).then(
        response => {
          console.log(response);
          if (response.code === "success") {
            this.$message({ message: response.msg, type: "success" });
            //将数据传入处理函数
            this.dealTableData(response.data);
            return;
          } else {
            this.$message({ message: response.msg, type: "warning" });
          }
        },
        err => {
          console.log(err);
        }
      );
    }
  }
};
</script>
