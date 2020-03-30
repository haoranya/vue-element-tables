<template>
  <div ref="innerTabelDiv" class="elevator-principal-page">
    <searchsTable
      :search-configs="searchConfigs"
      @do-search="doSearch"
    />
    <div class="header-btns">
      <el-button
        size="mini"
        type="primary"
        icon="el-icon-circle-plus"
        @click="addPricipal('add')"
      >添加</el-button>
      <el-button
        size="mini"
        type="primary"
        icon="el-icon-refresh"
        @click="addPricipal('update')"
      >更换</el-button>
    </div>
    <div class="header-message">
      <i class="el-icon-warning">共有{{ total }}项</i>
    </div>
    <div class="content-div">
      <div class="div-table">
        <nested-table
          ref="table"
          :columns="columns"
          :data="tableData"
          :height="height"
          :show-selection="true"
          :show-page="true"
          :local-page="true"
          :page-no="pageNo"
          :page-size="pageSize"
          :total="total"
          :loading="loading"
          class="nestedTable"
          @current-change="currentChange"
          @page-change="pageChange"
          @size-change="sizeChange"
        >
          <template slot="option" slot-scope="scope">
            <div class="option">
              <span
                v-if="scope.row.child"
                class="add-userName"
                @click="addPricipal('add', scope.row)"
              >添加</span>
              <span
                v-if="scope.row.child===undefined||scope.row.child.length===0"
                class="change-userName"
                @click="addPricipal('update', scope.row)"
              >更换</span>
              <span
                v-if="scope.row.child"
                class="opt-operation"
                @click="queryOperation(scope.row, scope.$index)"
              >日志</span>
            </div>
          </template>
        </nested-table>
      </div>
    </div>
  </div>
</template>
<script>
import { searchsTable, nestedTable } from 'sl-components'
export default {
  name: 'ElevatorPrincipalPage',
  components: { nestedTable, searchsTable },
  data() {
    return {
      tableData: [
      ],
      columns: [
        {
          prop: 'a',
          label: '测试1'
        },
        {
          prop: 'b',
          label: '测试2'
        },
        {
          prop: 'c',
          label: '操作时间'
        },
        {
          prop: 'option',
          label: '操作',
          cellTemplate: true

        }
      ],
      searchConfigs: [
        {
          title: '测试1',
          key: 'a',
          type: 'input'
        },
        {
          title: '测试2',
          key: 'b',
          type: 'select',
          options: [
            {
              value: 'Y',
              label: '是'
            },
            { value: 'N',
              label: '否'
            }
          ]
        },
        {
          title: '操作时间',
          key: 'c',
          type: 'daterange',
          cols: 2
        }
      ],
      loading: false, // 表格预加载
      pageNo: 1, // 页数
      pageSize: 10, // 条数
      total: 12, // 总条数
      partentSelection: [], // 表格父节点的选中
      principalType: '', // 负责人弹窗的类型
      searchParams: {}, // 搜索条件
      childSelection: [], // 表格子节点的选中
      height: this.screenHeight
    }
  },
  computed: {
    /**
     * @description 屏幕高度
     */
    screenHeight() {
      return this.$store.state.app.screenHeight
    }
  },
  watch: {
    /**
     * @description 监听屏幕高度变化
     */
    screenHeight(height) {
      this.handleTableHeight()
    }
  },
  mounted() {
    this.getData()// 初始化表格数据
    // this.handleTableHeight()
  },
  methods: {
    /**
     * @description 设置表格滚动高度
     */
    handleTableHeight() {
      this.height = this.$refs.innerTabelDiv.clientHeight - this.$refs.pageTop.clientHeight - 64
    },
    getData() {
      this.tableData = [
        {
          a: 1,
          b: 2,
          c: 3,
          number: 2,
          personLiable: '111',
          child: [
            {
              a: 1,
              b: 2,
              c: 3
            },
            {
              a: 1,
              b: 2,
              c: 3
            }
          ]

        },
        {
          a: 1,
          b: 2,
          c: 3,
          number: 2,
          personLiable: '111',
          child: [
            {
              a: 1,
              b: 2,
              c: 3
            },
            {
              a: 1,
              b: 2,
              c: 3
            }
          ]

        },
        {
          a: 1,
          b: 2,
          c: 3,
          number: 2,
          personLiable: '111',
          child: [
            {
              a: 1,
              b: 2,
              c: 3
            },
            {
              a: 1,
              b: 2,
              c: 3
            }
          ]

        },
        {
          a: 1,
          b: 2,
          c: 3,
          number: 2,
          personLiable: '111',
          child: [
            {
              a: 1,
              b: 2,
              c: 3
            },
            {
              a: 1,
              b: 2,
              c: 3
            }
          ]

        },
        {
          a: 1,
          b: 2,
          c: 3,
          number: 2,
          personLiable: '111',
          child: [
            {
              a: 1,
              b: 2,
              c: 3
            },
            {
              a: 1,
              b: 2,
              c: 3
            }
          ]

        },
        {
          a: 1,
          b: 2,
          c: 3,
          number: 2,
          personLiable: '111',
          child: [
            {
              a: 1,
              b: 2,
              c: 3
            },
            {
              a: 1,
              b: 2,
              c: 3
            }
          ]

        },
        {
          a: 1,
          b: 2,
          c: 3,
          number: 2,
          personLiable: '111',
          child: [
            {
              a: 1,
              b: 2,
              c: 3
            },
            {
              a: 1,
              b: 2,
              c: 3
            }
          ]

        },
        {
          a: 1,
          b: 2,
          c: 3,
          number: 2,
          personLiable: '111',
          child: [
            {
              a: 1,
              b: 2,
              c: 3
            },
            {
              a: 1,
              b: 2,
              c: 3
            }
          ]

        },
        {
          a: 1,
          b: 2,
          c: 3,
          number: 2,
          personLiable: '111',
          child: [
            {
              a: 1,
              b: 2,
              c: 3
            },
            {
              a: 1,
              b: 2,
              c: 3
            }
          ]

        },
        {
          a: 1,
          b: 2,
          c: 3,
          number: 2,
          personLiable: '111',
          child: [
            {
              a: 1,
              b: 2,
              c: 3
            },
            {
              a: 1,
              b: 2,
              c: 3
            }
          ]

        },
        {
          a: 1,
          b: 2,
          c: 3,
          number: 2,
          personLiable: '111',
          child: [
            {
              a: 1,
              b: 2,
              c: 3
            },
            {
              a: 1,
              b: 2,
              c: 3
            }
          ]

        },
        {
          a: 1,
          b: 2,
          c: 3,
          number: 2,
          personLiable: '111',
          child: [
            {
              a: 1,
              b: 2,
              c: 3
            },
            {
              a: 1,
              b: 2,
              c: 3
            }
          ]

        }
      ]
      //   const params = Object.assign(
      //     {
      //       queryNum: 100
      //     },
      //     this.searchParams,
      //   )// 对象的合并
      //   this.loading = true// 表格预加载
      //   QueryPrincipalList(params).then(resp => {
      //     if (resp.code === '2000') {
      //       this.loading = false// 表格预加载
      //       this.tableData = this.nestedData(resp.table ? resp.table:[])
      //       this.total = this.tableData.length// 由于是可折叠，数据处理完毕，总数是处理完毕的
      //     }
      //   }).catch(() => {

    //   })
    },
    // 表格可折叠数据处理
    nestedData(data) {
      this.tableArr = []
      data.forEach((item, index) => {
        if (this.tableArr.length === 0) {
          item.number = 1
          item.personLiable = item.userName
          item.child = [{ ...item }]
          item.personLiable = item.number + '个负责任人'
          this.tableArr.push(item)
          return
        }// 第一次循环
        for (var i = 0; i < this.tableArr.length; i++) {
          if (this.tableArr[i].nmatnr === item.nmatnr && this.tableArr[i].property === item.property) {
            this.tableArr[i].number++
            this.tableArr[i].personLiable = this.tableArr[i].number + '个负责任人'
            item.personLiable = item.userName
            item.number = 1
            this.tableArr[i].child.push(item)
            break
          } else if (i === this.tableArr.length - 1) {
            item.number = 1
            item.child = [{ ...item }]
            item.personLiable = item.number + '个负责任人'
            this.tableArr.push(item)
            break
          }
        }
      })
      // console.log(this.tableArr)
      return this.tableArr
    },
    // // 选中父节点
    // selectChangePartent(val) {
    //   this.partentSelection = val
    //   console.log(val)
    // },
    // // 选中子节点
    // selectChangeChild(val) {
    //   this.childSelection = val
    //   console.log(val)
    // },
    // 行点击
    currentChange(val) {
      console.log(val)
    },
    // 换页
    pageChange(val) {
      this.loading = true
      setTimeout(() => {
        this.loading = false
      }, 1000)
      console.log(val)
    },
    // 每页条数
    sizeChange(val) {
      console.log(val)
    },
    // 搜索接口
    doSearch(val) {
      this.searchParams = val
      this.pageNo = 1
      this.getData()
      this.$refs.table.searchData()
    }
  }
}
</script>
<style lang="less" scoped>
.elevator-principal-page{
    width:calc(100% - 4rem);
    height: calc(100% - 2rem);
    margin: 1rem 2rem;
    background-color: #ffffff;
    display: flex;
    flex-flow: column;
    border-radius: 5px;
    .header-btns {
        // height:5%;
        height: 32px;
        margin: 5px;
    }
    .header-message {
        height: 32px;
        line-height: 32px;
        margin:0 5px 5px 5px;
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
      height: calc(100% - 210px);
      .div-table{
          width: 100%;
          padding: 0 5px;
          height: 100%;
          .nested-table{
            height: 100%!important;
          }
          .option{
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
    .partentTable .el-table__body .el-table__expanded-cell>div .el-table__body-wrapper .el-table__body tr td:last-child {
        padding-left: unset;
    }
}
</style>
