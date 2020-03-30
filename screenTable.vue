<template>
  <div ref="box" class="search-data">
    <!-- 非表格部分 -->
    <div ref="top" class="top">
      <div class="top-change">
        <div style="width:calc(100% - 100px);">
          <span class="top-title">信息筛选：</span>
          <el-radio-group v-model="tableKey" @change="tabChange">
            <el-radio label="a">信息1</el-radio>
            <el-radio label="b">信息2</el-radio>
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
            <template slot="a" slot-scope="scope">
              <div class="options">
                <span
                  class="herfItem"
                  @click="intoDetail()"
                >{{ scope.row.a }}</span>
              </div>
            </template>
            <template slot="b" slot-scope="scope">
              <div class="options">
                <span
                  class="herfItem"
                  @click="intoDetail()"
                >{{ scope.row.b }}</span>
              </div>
            </template>
            <!-- 自定义 -->
            <template
              slot="f"
              slot-scope="scope"
            >
              <div class="options">
                <span>{{ scope.row.f }}</span>
              </div>
            </template>
            <!-- 自定义表头 -->
            <template
              slot="h"
              slot-scope="scope"
            >
              <div class="options">
                {{ scope.row.h }}
              </div>
            </template>
            <!-- 自定义表头 -->
            <template
              slot="i"
              slot-scope="scope"
            >
              <div class="options">
                {{ scope.row.i }}
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
      aColumns: [
        {
          prop: 'a',
          label: '测试1',
          fixed: 'left'
          // cellTemplate: true
        },
        {
          prop: 'b',
          label: '测试2',
          fixed: 'left'
          // cellTemplate: true
        },
        {
          prop: 'c',
          label: '测试3',
          width: '100'
        },
        {
          prop: 'd',
          label: '测试4'
        },
        {
          prop: 'e',
          label: '测试5'
        },
        {
          prop: 'f',
          label: '测试6',
          width: '100',
          type: 'date',
          customTitle: true
        },
        {
          prop: 'g',
          label: '测试7',
          width: '100'
        },
        {
          prop: 'h',
          label: '测试8',
          type: 'input',
          customTitle: true
        },
        {
          prop: 'i',
          label: '测试9',
          type: 'input',
          customTitle: true
        }
      ], // a表格column
      bColumns: [
        {
          prop: 'a',
          label: '测试1',
          fixed: 'left',
          cellTemplate: true
        },
        {
          prop: 'b',
          label: '测试2',
          fixed: 'left',
          cellTemplate: true
        },
        {
          prop: 'c',
          label: '测试3'
        },
        {
          prop: 'd',
          label: '测试4',
          width: '150'
        },
        {
          prop: 'e',
          label: '测试4'
        }
      ], // b表格column
      tableData: [],
      searchKey: 'test',
      tableKey: 'a'
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
        case 'a':
          this.tableColumns = this.aColumns
          this.searchColumn = this.aArr
          break
        case 'b':
          this.tableColumns = this.bColumns
          this.searchColumn = this.bArr
          break
      }// 筛选表格请求参数以及表格column数据
      this.defaultTableColumns = this.tableColumns// 保存原始表头
      this.oldTableData = [
        {
          "a": '1',
          "b": '1',
          "c": '2016-05-02',
          "d": '王小虎1',
          "e": '上海市普陀区金沙江路 100 弄',
          "f": '17',
          "g": '男'
        },
        {
          "a": '1',
          "b": '1',
          "c": '2016-05-02',
          "d": '王小虎1',
          "e": '上海市普陀区金沙江路 100 弄',
          "f": '17',
          "g": '男'
        },
        {
          "a": '1',
          "b": '1',
          "c": '2016-05-02',
          "d": '王小虎1',
          "e": '上海市普陀区金沙江路 100 弄',
          "f": '17',
          "g": '男'
        },
        {
          "a": '1',
          "b": '1',
          "c": '2016-05-02',
          "d": '王小虎1',
          "e": '上海市普陀区金沙江路 100 弄',
          "f": '17',
          "g": '男'
        },
        {
          "a": '1',
          "b": '1',
          "c": '2016-05-02',
          "d": '王小虎1',
          "e": '上海市普陀区金沙江路 100 弄',
          "f": '17',
          "g": '男'
        },
        {
          "a": '1',
          "b": '1',
          "c": '2016-05-02',
          "d": '王小虎1',
          "e": '上海市普陀区金沙江路 100 弄',
          "f": '17',
          "g": '男'
        },
        {
          "a": '1',
          "b": '1',
          "c": '2016-05-02',
          "d": '王小虎1',
          "e": '上海市普陀区金沙江路 100 弄',
          "f": '17',
          "g": '男'
        },
        {
          "a": '1',
          "b": '1',
          "c": '2016-05-02',
          "d": '王小虎1',
          "e": '上海市普陀区金沙江路 100 弄',
          "f": '17',
          "g": '男'
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
