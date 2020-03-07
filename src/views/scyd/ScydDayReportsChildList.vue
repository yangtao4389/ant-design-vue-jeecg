<template>
  <a-card :bordered="false">
    <!-- 查询区域 -->
    <div class="table-page-search-wrapper">
      <a-form layout="inline" >
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
              <a-select placeholder="产品列表" default-value="0">
                <a-select-option value="0">少儿</a-select-option>
                <a-select-option value="1">电竞</a-select-option>
                <a-select-option value="2">游戏</a-select-option>
              </a-select>
            </a-form-item>
          </a-col>
          <a-col :md="8" :sm="24">
            <span class="table-page-search-submitButtons">
              <a-button type="primary">查询</a-button>
              <a-button style="margin-left: 8px">重置</a-button>
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
    <s-table :columns="columns" :data="loadData" >

    </s-table>
    <!--<div-->
      <!--slot="expandedRowRender"-->
      <!--slot-scope="record"-->
      <!--style="margin: 0">-->
      <!--<a-row-->
        <!--:gutter="24"-->
        <!--:style="{ marginBottom: '12px' }">-->
        <!--<a-col :span="12" v-for="(role, index) in record.permissions" :key="index" :style="{ marginBottom: '12px' }">-->
          <!--<a-col :lg="4" :md="24">-->
            <!--<span>{{ role.permissionName }}：</span>-->
          <!--</a-col>-->
          <!--<a-col :lg="20" :md="24" v-if="role.actionEntitySet.length > 0">-->
            <!--<a-tag color="cyan" v-for="(action, k) in role.actionEntitySet" :key="k">{{ action.describe }}</a-tag>-->
          <!--</a-col>-->
          <!--<a-col :span="20" v-else>-</a-col>-->
        <!--</a-col>-->
      <!--</a-row>-->
    <!--</div>-->

    <!--<div>-->
      <!--<div class="ant-alert ant-alert-info" style="margin-bottom: 16px;">-->
        <!--<i class="anticon anticon-info-circle ant-alert-icon"></i> 已选择 <a style="font-weight: 600">{{ selectedRowKeys.length }}</a>项-->
        <!--<a style="margin-left: 24px" @click="onClearSelected">清空</a>-->
      <!--</div>-->

      <!--<a-table-->
        <!--ref="table"-->
        <!--size="middle"-->
        <!--bordered-->
        <!--rowKey="id"-->
        <!--:columns="columns"-->
        <!--:dataSource="dataSource"-->
        <!--:pagination="ipagination"-->
        <!--:loading="loading"-->
        <!--:rowSelection="{fixed:true,selectedRowKeys: selectedRowKeys, onChange: onSelectChange}"-->
        <!--:scroll="tableScroll"-->
        <!--@change="handleTableChange">-->

        <!--<template slot="htmlSlot" slot-scope="text">-->
          <!--<div v-html="text"></div>-->
        <!--</template>-->
        <!--<template slot="imgSlot" slot-scope="text">-->
          <!--<span v-if="!text" style="font-size: 12px;font-style: italic;">无此图片</span>-->
          <!--<img v-else :src="getImgView(text)" height="25px" alt="图片不存在" style="max-width:80px;font-size: 12px;font-style: italic;"/>-->
        <!--</template>-->
        <!--<template slot="fileSlot" slot-scope="text">-->
          <!--<span v-if="!text" style="font-size: 12px;font-style: italic;">无此文件</span>-->
          <!--<a-button-->
            <!--v-else-->
            <!--:ghost="true"-->
            <!--type="primary"-->
            <!--icon="download"-->
            <!--size="small"-->
            <!--@click="uploadFile(text)">-->
            <!--下载-->
          <!--</a-button>-->
        <!--</template>-->

        <!--<span slot="action" slot-scope="text, record">-->
          <!--<a @click="handleEdit(record)">编辑</a>-->

          <!--<a-divider type="vertical" />-->
          <!--<a-dropdown>-->
            <!--<a class="ant-dropdown-link">更多 <a-icon type="down" /></a>-->
            <!--<a-menu slot="overlay">-->
              <!--<a-menu-item>-->
                <!--<a-popconfirm title="确定删除吗?" @confirm="() => handleDelete(record.id)">-->
                  <!--<a>删除</a>-->
                <!--</a-popconfirm>-->
              <!--</a-menu-item>-->
            <!--</a-menu>-->
          <!--</a-dropdown>-->
        <!--</span>-->

      <!--</a-table>-->
    <!--</div>-->


  </a-card>
</template>

<script>
  import STable from '@/components/table/'
  // import { JeecgListMixin } from '@/mixins/JeecgListMixin'
  // import ScydDayReportsChildModal from './modules/ScydDayReportsChildModal'
  import JDate from '@/components/jeecg/JDate.vue'

  export default {
    name: "ScydDayReportsChildList",
    // mixins:[JeecgListMixin],
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
        loadData: parameter => {
          return this.$http.get('/scyd/scydDayReportsChild/list', {
            params: Object.assign(parameter, this.queryParam)
          }).then(res => {
            /*
            data
            * pageSize: 10
pageNo: 0
totalPage: 1
totalCount: 5
            *
            * */
            let result = res.result
            result.data = result.records
            result.pageSize = result.pages
            result.pageNo = result.current
            result.totalPage = 1
            result.totalCount =5
            return result
            // let result = res.result
            // result.data.map(permission => {
            //   permission.actionList = JSON.parse(permission.actionData)
            //   return permission
            // })
            // return result
          })
        },
        selectedRowKeys: [],
        selectedRows: []
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
       
    }
  }
</script>
<style scoped>
  @import '~@assets/less/common.less'
</style>