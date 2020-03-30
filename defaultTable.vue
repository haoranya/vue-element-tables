<template>
  <!-- 访问权限查询div -->
  <div ref="box" class="elevator-template-collocation">
    <!-- 非表格组件 -->
    <div ref="top" class="top">
      <searchsTable
        ref="searchs"
        :search-configs="searchConfigs"
        @do-search="doSearch"
      />
      <div class="header-message">
        <i class="el-icon-warning">共有{{ total }}项</i>
      </div>
    </div>
    <!-- 表格组件内容部分 -->
    <div class="content-div" :style="tableHeight">
      <!-- 表格div -->
      <div ref="tableDiv" class="table-div table-box">
        <!-- 表格组件 -->
        <baseTable
          ref="tables"
          show-selection
          show-page
          local-page
          :columns="tableColumns"
          :data="tableData"
          :page-no="pageNo"
          :page-size="pageSize"
          :total="total"
          class="baseTable"
          @select-change="selectChange"
          @page-change="pageChange"
          @size-change="sizeChange"
        >
          <!-- 操作列 -->
          <template slot="opt" slot-scope="scope">
            <div class="options">
              <span
                class="add-authority"
                @click="authorizeEdit([scope.row])"
              >变更模版</span>
            </div>
          </template>
        </baseTable>
      </div>
    </div>
  </div>
</template>
<script>
import { searchsTable, baseTable } from 'sl-components'
export default {
  name: 'ElevatorTemplateCollocation',
  components: { searchsTable, baseTable },
  data() {
    return {
      tableHeight: '',
      addPrincipalVisible: false,
      selectData: [], // 复选框选中的数据
      // 查询面板配置
      searchConfigs: [
        {
          title: '测试1',
          key: 'a',
          type: 'input'
        },
        {
          title: '测试2',
          key: 'b',
          type: 'input'
        },
        {
          title: '测试3',
          key: 'c',
          type: 'input'
        }
      ],
      // 表格列配置
      tableColumns: [
        {
          prop: 'a',
          label: '测试1',
          align: 'center'
        },
        {
          prop: 'b',
          label: '测试2',
          align: 'center'
        },
        {
          prop: 'c',
          label: '测试3',
          align: 'center'
        },
        {
          prop: 'd',
          label: '测试4',
          align: 'center'
        },
        {
          prop: 'opt',
          label: '操作',
          align: 'center',
          cellTemplate: true
        }
      ],
      // 表格数据
      tableData: [
        {
          a: '123456',
          b: '上海',
          c: 'true'
        },
        {
          a: '123456',
          b: '上海',
          c: 'true'
        },
        {
          a: '123456',
          b: '上海',
          c: 'true'
        },
        {
          a: '123456',
          b: '上海',
          c: 'true'
        },
        {
          a: '123456',
          b: '上海',
          c: 'true'
        },
        {
          a: '123456',
          b: '上海',
          c: 'true'
        },
        {
          a: '123456',
          b: '上海',
          c: 'true'
        },
        {
          a: '123456',
          b: '上海',
          c: 'true'
        },
        {
          a: '123456',
          b: '上海',
          c: 'true'
        },
        {
          a: '123456',
          b: '上海',
          c: 'true'
        },
        {
          a: '123456',
          b: '上海',
          c: 'true'
        },
        {
          a: '123456',
          b: '上海',
          c: 'true'
        },
      ],
      pageNo: 1, // 页数
      pageSize: 10, // 条数
      total: 12, // 总条数
      searchParams: {}, // 查询参数
      orderParams: {}, // 排序参数
      statusList: [], // 状态列表
      currentStatus: '', // 当前选择状态
      selectRows: [] // 勾选列表
    }
  },
  computed: {
  },
  created() {
    this.getData()// 初始化表格数据
  },
  mounted() {
    const boxHeight = this.$refs.box.clientHeight
    const topHeight = this.$refs.top.clientHeight
    this.tableHeight = 'height:' + (boxHeight - topHeight - 32) + 'px'
  },
  methods: {
    getData() {
      const params = Object.assign(
        this.searchParams
      )
      console.log(params)
    },
    sizeChange(data) {
      this.pageSize = data
      console.log(data)
    },
    pageChange(data) {
      this.pageNo = data
      console.log(data)
    },
    selectChange(data) {
      this.selectData = data
    },
    doSearch(data) {
      this.searchParams = data
      this.pageNo = 1
      this.$refs.tables.searchFlag = true
      this.getData()
    },
    btnClick(index) {
      this.btnArr.map((item) => { item.index = ''; return item })
      this.btnArr[index].index = index
      console.log(this.btnArr[index])
    }
  }
}
</script>
<style lang="less" scoped>
.elevator-template-collocation{
  height: calc(100% - 30px);
  margin: 1rem 2rem;
  background-color: #ffffff;
  display: flex;
  flex-flow: column;
  border-radius: 5px;
  // padding-bottom: 1rem;
  .header-message {
      height: 32px;
      line-height: 32px;
      margin-bottom: 5px;
      background-color: #e6f7ff;
      border: 1px solid #d6feff;
      border-radius: 3px;
      font-size: 14px;
      padding: 0 10px;
      color: #999999;
      i {
        color: #46a6ff;
        margin-right: 5px;
      }
    }
  .content-div {
    padding:0 10px;
    .status-list {
      font-size: 14px;
      padding: 5px 10px;
      border: 1px solid #e8e8e8;
      border-radius: 5px;
      margin-bottom: 5px;
      height: 40px;
      .el-button--mini {
        margin-left: 20px;
      }
    }
    .table-div {
        width: 100%;
        height:100%;
        .baseTable{
          height:100%!important;
        }
      .options{
        padding:10px 0;
        .delete-authority,
        .opt-operation,
        .add-authority,.authorize-authority {
          padding:10px 0;
          color: #46a6ff;
          margin: 0 2px;
          cursor: pointer;
        }
        .delete-authority{
            color: #ff4949;
        }
        .authorize-authority{
            color: #13ce66;
        }
      }
    }
  }
}
</style>
