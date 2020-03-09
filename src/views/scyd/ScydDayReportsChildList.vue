<template>
  <a-card :bordered="false">
    <!-- 查询区域 -->
    <div class="table-page-search-wrapper">
      <a-form layout="inline" @keyup.enter.native="searchQuery">
        <a-row :gutter="48">
          <a-col :md="8" :sm="24">
            <a-form-item label="日期">
              <j-date placeholder="开始日期" class="query-group-cust" v-model="queryParam.date_begin"></j-date>
              <span class="query-group-split-cust"></span>
              <j-date placeholder="结束日期" class="query-group-cust" v-model="queryParam.date_end"></j-date>
            </a-form-item>
          </a-col>
          <a-col :md="8" :sm="24" >
            <a-form-item label="产品列表">
              <a-select placeholder="产品列表" v-model="queryParam.product_type" default-value="0">
                <a-select-option value="0">少儿</a-select-option>
                <a-select-option value="1">电竞</a-select-option>
                <a-select-option value="2">游戏</a-select-option>
              </a-select>
            </a-form-item>
          </a-col>
          <a-col :md="8" :sm="24">
            <span class="table-page-search-submitButtons">
              <a-button type="primary" @click="searchQuery">查询</a-button>
              <a-button style="margin-left: 8px"  @click="searchReset" >重置</a-button>
            </span>
          </a-col>

        </a-row>
      </a-form>
    </div>
    <!-- 查询区域-END -->
    
    <!-- 操作按钮区域 -->
    <div class="table-operator">
      <a-button type="primary" icon="download" @click="handleExportXls('scyd_day_reports_child')">导出</a-button>

    </div>

    <!-- table区域-begin -->
    <s-table ref="table" rowKey="date" :columns="columns"  :dataSource="dataSource" :pagination="ipagination" :data="loadData" >

    </s-table>


  </a-card>
</template>

<script>
  import STable from '@/components/table/'
  import { JeecgListMixin } from '@/mixins/JeecgListMixin'
  // import ScydDayReportsChildModal from './modules/ScydDayReportsChildModal'
  import JDate from '@/components/jeecg/JDate.vue'

  export default {
    name: "ScydDayReportsChildList",
    mixins:[JeecgListMixin],
    components: {
      STable,
      JDate,
      // ScydDayReportsChildModal
    },
    data () {
      return {
        description: 'scyd_day_reports_child管理页面',
        visible: false,
        labelCol: {
          xs: { span: 24 },
          sm: { span: 5 },
        },
        wrapperCol: {
          xs: { span: 24 },
          sm: { span: 16 },
        },
        form: null,
        mdl: {},
        // 高级搜索 展开/关闭
        advanced: false,
        // 查询参数
        queryParam: {},

        // 表头
        columns: [
          {
            title:'日期',
            dataIndex: 'date',
          },
          {
            title:'uv',
            dataIndex: 'uv'
          },
          {
            title:'pv',
            dataIndex: 'pv'
          },
          {
            title:'订购数量',
            dataIndex: 'orderNum'
          },
          {
            title:'自订购数量',
            dataIndex: 'fakeOrderNum'
          },
          {
            title:'转化率',
            dataIndex: 'percent'
          }
        ],
        // 向后端拉取可以用的操作列表
        permissionList: null,
        // 加载数据方法 必须为 Promise 对象
        // loadData: parameter => {
        //   return this.$http.get('/scyd/scydDayReportsChild/list', {
        //     params: Object.assign(parameter, this.queryParam)
        //   }).then(res => {
            /*
            data
            * pageSize: 10
pageNo: 0
totalPage: 1
totalCount: 5
            *
            * */
            // let result = res.result
            // result.data = result.records
            // result.pageSize = result.pages
            // result.pageNo = result.current
            // result.totalPage = 1
            // result.totalCount =5
            // return result
            // let result = res.result
            // result.data.map(permission => {
            //   permission.actionList = JSON.parse(permission.actionData)
            //   return permission
            // })
            // return result
        //   })
        // },
        selectedRowKeys: [],
        selectedRows: [],
        url: {
          list: "/scyd/scydDayReportsChild/list",
        },
        /* 排序参数 */
        isorter:{
          column: 'date',
          order: 'date',
        },

      }
    },
    computed: {
      importExcelUrl: function(){
        return `${window._CONFIG['domianURL']}/${this.url.importExcelUrl}`;
      }
    },
    // created () {
    //   this.loadPermissionList()
    // },
    methods: {
      loadPermissionList () {
        // permissionList
        new Promise((resolve => {
          const data = [
            { label: '新增', value: 'add', defaultChecked: false },
            { label: '查询', value: 'get', defaultChecked: false },
            { label: '修改', value: 'update', defaultChecked: false },
            { label: '列表', value: 'query', defaultChecked: false },
            { label: '删除', value: 'delete', defaultChecked: false },
            { label: '导入', value: 'import', defaultChecked: false },
            { label: '导出', value: 'export', defaultChecked: false }
          ]
          setTimeout(resolve(data), 1500)
        })).then(res => {
          this.permissionList = res
        })
      },
      // 重置
      searchReset(){
        var that = this;
        // var logType = that.queryParam.logType;
        that.queryParam = {}; //清空查询区域参数
        // that.queryParam.logType = logType;
        that.loadData();
      },

      // getQueryParams(){
      //   console.log(this.queryParam.createTimeRange)
      //   var param = Object.assign({}, this.queryParam,this.isorter);
      //   param.field = this.getQueryField();
      //   param.pageNo = this.ipagination.current;
      //   param.pageSize = this.ipagination.pageSize;
      //   delete param.createTimeRange; // 时间参数不传递后台
      //   // return filterObj(param);
      //   return param;
      // },
      loadData(arg) {
        if(!this.url.list){
          this.$message.error("请设置url.list属性!")
          return
        }
        //加载数据 若传入参数1则加载第一页的内容
        if (arg === 1) {
          this.ipagination.current = 1;
        }
        var params = this.getQueryParams();//查询条件
        this.loading = true;
        getAction(this.url.list, params).then((res) => {
          if (res.success) {
            this.dataSource = res.result.records;
            this.ipagination.total = res.result.total;
          }
          if(res.code===510){
            this.$message.warning(res.message)
          }
          this.loading = false;
        })
      },
    }
  }
</script>
<style scoped>
  @import '~@assets/less/common.less'
</style>