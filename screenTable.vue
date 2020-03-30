<template>
  <div ref="box" class="search-data">
    <!-- 非表格部分 -->
    <div ref="top" class="top">
      <div class="top-change">
        <div style="width:calc(100% - 100px);">
          <el-button size="mini" type="primary" @click="exportInfo">导出：</el-button>
          <span class="top-title">信息筛选：</span>
          <el-radio-group v-model="tableKey" @change="tabChange">
            <el-radio label="eleSalesInfo">电梯信息</el-radio>
            <el-radio label="eleInstInfo">安装信息</el-radio>
          </el-radio-group>
        </div>
        <div class="table-filter">
          <columnFilter ref="columnFilter" class="column-filter" @checkboxItem="checkboxItem($event)" />
        </div>
      </div>
    </div>
    <!-- 表格组件部分 -->
    <div class="bottom" :style="tableHeight">
      <div class="left">
        <div class="div-table">
          <baseTable
            ref="tables"
            :show-fixed-index="true"
            :show-index="false"
            :show-page="true"
            :local-page="true"
            :allow-move="true"
            :page-no="page"
            :page-size="pageSize"
            :total="total"
            class="baseTable"
            :columns="tableColumns"
            :data="tableData"
            @page-change="pageChange"
            @size-change="sizeChange"
            @row-click="rowClick"
            @filterMethod="filterMethod($event)"
          >
            <template slot="bismt" slot-scope="scope">
              <div class="options">
                <span
                  class="herfItem"
                  @click="intoDetail(scope.row.matnr)"
                >{{ scope.row.bismt }}</span>
              </div>
            </template>
            <template slot="nsmhth" slot-scope="scope">
              <div class="options">
                <span
                  class="herfItem"
                  @click="intoDetail(scope.row.matnr)"
                >{{ scope.row.nsmhth }}</span>
              </div>
            </template>
            <!-- 自定义交货期 -->
            <template
              slot="jhdat"
              slot-scope="scope"
            >
              <div class="options">
                <span>{{ scope.row.jhdat }}</span>
              </div>
            </template>
            <!-- 自定义交货期 -->
            <!-- 自定义业务员表头 -->
            <template
              slot="branc"
              slot-scope="scope"
            >
              <div class="options">
                {{ scope.row.branc }}
              </div>
            </template>
            <template
              slot="sales"
              slot-scope="scope"
            >
              <div class="options">
                {{ scope.row.sales }}
              </div>
            </template>
            <!-- 自定义表头 -->
            <template
              slot="azsta_z"
              slot-scope="scope"
            >
              <div class="options">
                {{ scope.row.azsta_z }}
              </div>
            </template>
          </baseTable>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
import { baseTable, columnFilter } from 'sl-components'
import '@/platform/platform'// 引入平台检验
export default {
  name: 'SearchData',
  components: { baseTable, columnFilter },
  data() {
    return {
      tableHeight: '',
      activeNames: [],
      activeName: 'first',
      oldTableData: [],
      cardsInfo: {},
      page: 1,
      pageSize: 20,
      total: 0,
      statue: false,
      tableColumns: [
        {
          prop: 'bismt',
          width: '150',
          label: '合同号梯号',
          fixed: 'left',
          cellTemplate: true
        },
        {
          prop: 'nsmhth',
          label: '合同号',
          width: '150',
          fixed: 'left',
          cellTemplate: true
        },
        {
          label: '日期',
          prop: 'jhdat',
          type: 'date',
          customTitle: true
        },
        {
          label: '姓名',
          prop: 'branc',
          type: 'input',
          customTitle: true
        }
      ], // 表格字段
      searchColumn: [], // 请求字段
      eleSalesInfoColumns: [
        {
          prop: 'bismt',
          label: '合同号梯号',
          fixed: 'left'
          // cellTemplate: true
        },
        {
          prop: 'nsmhth',
          label: '合同号',
          fixed: 'left'
          // cellTemplate: true
        },
        {
          prop: 'augru',
          label: '合同状态',
          width: '100'
        },
        {
          prop: 'prjnm',
          label: '项目名称'
        },
        {
          prop: 'namwe',
          label: '最终用户名称'
        },
        {
          prop: 'jhdat',
          label: '交货期',
          width: '100',
          type: 'date',
          customTitle: true
        },
        {
          prop: 'g30dat',
          label: '最终交货期',
          width: '100'
        },
        {
          prop: 'branc',
          label: '分公司',
          type: 'input',
          customTitle: true
        },
        {
          prop: 'sales',
          label: '业务员',
          type: 'input',
          customTitle: true
        }
      ], // 电梯销售表格column
      eleInstInfoColumns: [
        {
          prop: 'bismt',
          label: '合同号梯号',
          fixed: 'left',
          cellTemplate: true
        },
        {
          prop: 'nsmhth',
          label: '合同号',
          fixed: 'left',
          cellTemplate: true
        },
        {
          prop: 'traffic_order_no',
          label: '运单号'
        },
        {
          prop: 'augru',
          label: '当前所在地址',
          width: '150'
        },
        {
          prop: 'augru',
          label: '物流状态'
        }
      ], // 合同销售的物流信息字段表格column
      tableData: [],
      searchKey: 'eleInfo',
      tableKey: 'eleSalesInfo'
    }
  },
  created() {
    this.searchValue = this.$route.query.searchValue
    this.getData()
  },
  mounted() {
    const boxHeight = this.$refs.box.clientHeight
    const topHeight = this.$refs.top.clientHeight
    this.tableHeight = 'height:' + (boxHeight - topHeight - 32) + 'px'
  },
  // 在组件beforeDestory的时候销毁。
  beforeDestroy() {
  },
  methods: {
    handleChange(val) {
      this.activeNames = val
      this.scrollInit('scrollCenter')
      setTimeout(() => {
        this.scrollInit('scrollBottom')
      }, 100)
    },
    filterMethod(options) { // 过滤事件
      if (options.length === 0) {
        this.tableData = this.oldTableData
      }
      options.map((element, index) => {
        this.tableData = this.oldTableData.filter(item => item[element.key] === element[element.key])
      })
      this.total = this.tableData.length
      // console.log(this.tableData)
    },
    pageChange(e) {
      this.page = e
    },
    sizeChange(e) {
      this.pageSize = e
    },
    handleClick(e) {
      this.activeName = e.name
      if (e.name === 'second') {
        if (this.tableKey === 'contrSales') { // 判断如果是物流处理物流信息
          this.mapLine(this.currentLoc_address)
        }
      }
      this.scrollInit('scrollBottom')
    },
    backClick() {
      this.statue = false
    },
    rowClick(data) {
      this.activeName = 'first'
      this.currentLoc_address = data.loc_address
      this.statue = true
      this.getDetailData(data.matnr)
    },
    tabChange(e) {
      this.tableKey = e
      this.activeName = 'first'
      this.getData()
    },
    getData() {
      this.initTableType()
      this.oldTableData = []
      this.tableData = []
      this.page = 1
      this.pageSize = 20
      setTimeout(() => {
        this.$refs.tables.initFilterValue
        this.$refs.tables.setLoading(true)
      }, 0)
      switch (this.tableKey) {
        case 'eleSalesInfo':
          this.tableColumns = this.eleSalesInfoColumns
          this.searchColumn = this.eleSalesInfoArr
          break
        case 'eleInstInfo':
          this.tableColumns = this.eleInstInfoColumns
          this.searchColumn = this.eleInstInfoArr
          break
      }// 筛选表格请求参数以及表格column数据
      this.defaultTableColumns = this.tableColumns// 保存原始表头
      this.oldTableData = [
        {
          bismt: '1',
          nsmhth: '1',
          jhdat: '2016-05-02',
          branc: '王小虎1',
          address: '上海市普陀区金沙江路 100 弄',
          age: '17',
          nex: '男'
        },
        {
          bismt: '2',
          nsmhth: '2',
          jhdat: '2016-05-04',
          branc: '王小虎2',
          address: '上海市普陀区金沙江路 200 弄',
          age: '17',
          nex: '男'
        },
        {
          bismt: '3',
          nsmhth: '3',
          jhdat: '2016-05-01',
          branc: '王小虎3',
          address: '上海市普陀区金沙江路 300 弄',
          age: '17',
          nex: '男'
        },
        {
          bismt: '4',
          nsmhth: '4',
          jhdat: '2016-05-03',
          branc: '王小虎4',
          address: '上海市普陀区金沙江路 400 弄',
          age: '17',
          nex: '男'
        },
        {
          bismt: '1',
          nsmhth: '1',
          jhdat: '2016-05-02',
          branc: '王小虎1',
          address: '上海市普陀区金沙江路 100 弄',
          age: '17',
          nex: '男'
        },
        {
          bismt: '2',
          nsmhth: '2',
          jhdat: '2016-05-04',
          branc: '王小虎2',
          address: '上海市普陀区金沙江路 200 弄',
          age: '17',
          nex: '男'
        },
        {
          bismt: '3',
          nsmhth: '3',
          jhdat: '2016-05-01',
          branc: '王小虎3',
          address: '上海市普陀区金沙江路 300 弄',
          age: '17',
          nex: '男'
        },
        {
          bismt: '4',
          nsmhth: '4',
          jhdat: '2016-05-03',
          branc: '王小虎4',
          address: '上海市普陀区金沙江路 400 弄',
          age: '17',
          nex: '男'
        },
        {
          bismt: '1',
          nsmhth: '1',
          jhdat: '2016-05-02',
          branc: '王小虎1',
          address: '上海市普陀区金沙江路 100 弄',
          age: '17',
          nex: '男'
        },
        {
          bismt: '2',
          nsmhth: '2',
          jhdat: '2016-05-04',
          branc: '王小虎2',
          address: '上海市普陀区金沙江路 200 弄',
          age: '17',
          nex: '男'
        },
        {
          bismt: '3',
          nsmhth: '3',
          jhdat: '2016-05-01',
          branc: '王小虎3',
          address: '上海市普陀区金沙江路 300 弄',
          age: '17',
          nex: '男'
        },
        {
          bismt: '4',
          nsmhth: '4',
          jhdat: '2016-05-03',
          branc: '王小虎4',
          address: '上海市普陀区金沙江路 400 弄',
          age: '17',
          nex: '男'
        }
      ]
      this.tableData = this.oldTableData
      this.total = 12
      setTimeout(() => {
        this.$refs.tables.setLoading(false)
      }, 0)
      this.initColumnFilter()// 等待表格拿到数据后，调用自定义表头组件初始化数据
    },
    initColumnFilter() {
      console.log(this.tableColumns)
      const currentTableColumn = {
        // nextTableColumn: this[this.searchKey + this.tableKey], // 记录
        defaultCheckboxItem: this.tableColumns, // 默认的
        type: this.searchKey + this.tableKey// 表格的类型
      }
      this.$nextTick(() => {
        this.$refs.columnFilter.initColumnFilter(currentTableColumn)
      })
    }, // 表格自定义表头的初始化数据
    initTableType() { // 做数据区分
      setTimeout(() => {
        this.$refs.tables.initTableTypeValue(this.searchKey + this.tableKey)
      }, 0)
    }, // 区分表格
    checkboxItem(data) {
      var filterColumns = []
      for (var index = 0; index < data.length; index++) {
        this.defaultTableColumns.forEach((item) => {
          if (item.label === data[index]) {
            filterColumns.push(item)
          }
        })
      }
      this.tableColumns = filterColumns// 过滤后的表格数据
      const tableColumnType = this.searchKey + this.tableKey
      this[tableColumnType] = this.tableColumns// 保存当前表格的过滤后的表头
    }, // 表头的过滤的事件
    exportInfo() {
      import('@/vendor/Export2Excel').then(excel => {
        const tHeader = ['a', 'b', 'c', 'd', 'e', 'f', 'g', 'h']// 要导出后表头是什么，可以跟tableDate中的表头不一致
        const filterVal = ['a', 'b', 'c', 'd', 'e', 'f', 'g', 'h']
        const data = this.formatJson(filterVal, this.tableData)
        // const data = this.tableData;
        excel.export_json_to_excel({
          header: tHeader,
          data,
          filename: 'table-list' // 导出文件的名，自定义就好
        })
        // this.downloadLoading = false;
      })
    },
    // 下载方法中，需要用到的格式化json的方法
    formatJson(filterVal, jsonData) {
      return jsonData.map(v =>
        filterVal.map(j => {
          if (j === 'id') { // 此处如没有要格式化的列，可以不用此判断
            // return parseTime(v[j])
            return v[j]
          } else {
            return v[j]
          }
        })
      )
    },
    scrollInit(key) {
      // alert('计算')
      this.$nextTick(() => {
        setTimeout(() => {
          this.$refs[key].initScroll()
        }, 200)
      })
    },
    getDetailData() {

    }
  }
}
</script>
<style lang="less">
    .search-data{
        width: 100%;
        height: 100%;
        padding: 0 2rem;
        .top{
           width: 100%;
           padding:1rem;
           background: white;
           display: flex;
           align-items: center;
           .top-change{
             display: flex;
             justify-content: space-between;
             align-items: center;
             width: 70%;
            .top-title{
              font-size: 0.8rem;
              color:#606266;
              margin-left:2rem;
            }
            .el-radio-group{
              .el-radio__label{
                font-size:0.8rem;
              }
            }
          }
          .table-filter{
            width: 100px;
          }
        }
        .bottom{
          width: 100%;
          padding: 2rem 0px;
          padding-top: 0px;
          background: white;
          .left{
            width: 100%;
            height: 100%;
            padding: 0px 2rem;
            float: left;
            .div-table{
              width: 100%;
              height: 100%;
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
        .herfItem{
          color: #1890FF;
          cursor: pointer;
        }
        .table-title-icon{
          width:0.5rem;
          height:0.5rem;
          vertical-align: unset;
        }
    }
    .search-data .el-tabs__nav{
      width: 100%;
    }
   .search-data .el-tabs__item{
     width: 25%;
     font-size: 0.8rem;
     text-align: center;
    }
    .map{
      width:100%;
      height:100%;
      background: pink;
      .lookMapInfo{
        padding: 0.5rem 0.5rem 0 0.5rem;
        margin: 0;
        font-size: 14px;
        cursor: pointer;
      }
    }
</style>
