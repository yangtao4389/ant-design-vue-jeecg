<template>
  <a-card :bordered="false">
    <!-- 查询区域 -->
    <div class="table-page-search-wrapper">
      <a-form layout="inline" @keyup.enter.native="searchQuery">
        <a-row :gutter="16">
          <a-col :xs="24" :sm="24" :md="10" :lg="10" :xl="8">
            <a-form-item label="日期"
                         >
              <j-date placeholder="开始日期" class="query-group-cust" v-model="queryParam.date_begin"></j-date>
              <span class="query-group-split-cust"></span>
              <j-date placeholder="结束日期" class="query-group-cust" v-model="queryParam.date_end"></j-date>
            </a-form-item>
          </a-col>
          <a-col :xs="24" :sm="24" :md="10" :lg="10" :xl="8">
            <a-form-item label="产品列表">
              <a-select placeholder="产品列表" v-model="queryParam.product_type" default-value="0">
                <a-select-option value="0">少儿</a-select-option>
                <a-select-option value="1">电竞</a-select-option>
                <a-select-option value="2">游戏</a-select-option>
              </a-select>
            </a-form-item>
          </a-col>
          <a-col :xs="24" :sm="24" :md="4" :lg="4" :xl="4" >
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
      <a-button type="primary" icon="download" @click="handleExportXls('scyd_day_reports_video')">导出</a-button>
    </div>

    <!-- table区域-begin
     :scroll="{ x: '150%' }"
        ref="table"
        size="middle"
        bordered  // 加边框

    -->
    <div>

      <a-tabs defaultActiveKey="1">
        <a-tab-pane key="1">
      <span slot="tab">
        <a-icon type="line-chart" />
        图形展示
      </span>
          <ve-line :data="chartData" :settings="chartSettings"></ve-line>
        </a-tab-pane>
        <a-tab-pane key="2">
      <span slot="tab">
        <a-icon type="ordered-list" />
        列表展示
      </span>
          <a-table
            bordered
            rowKey="date"
            :columns="columns"
            :dataSource="dataSource"
            :pagination="ipagination"
            :loading="loading"
            @change="handleTableChange">

          </a-table>
        </a-tab-pane>
      </a-tabs>


    </div>


  </a-card>
</template>

<script>

  import { JeecgListMixin } from '@/mixins/JeecgListMixin'
  import JDate from '@/components/jeecg/JDate.vue'
  import { filterObj,localDate } from '@/utils/util';
  import { getAction} from '@/api/manage'
  import ScydLineChartMultid from './modules/ScydLineChartMultid'
  export default {
    name: "ScydDayReportsVideoList",
    mixins:[JeecgListMixin],
    components: {
      JDate,
      ScydLineChartMultid,
    },
    data () {
      // this.chartSettings = {
      //   axisSite: { right: ['下单率'] },
      //   yAxisType: ['KMB', 'percent'],
      //   yAxisName: ['数值', '比率']
      // }
      return {
        description: 'scyd_day_reports_video管理页面',
        // 表头
        columns: [
          // {
          //   title: '#',
          //   dataIndex: '',
          //   key:'rowIndex',
          //   width:'10%',
          //   align:"center",
          //   customRender:function (t,r,index) {
          //     return parseInt(index)+1;
          //   }
          // },
          {
            // width:'10%',
            sorter: true, // 排序用？
            // fixed: 'left',
            title:'日期',
            align:"center",
            dataIndex: 'date',
            customRender:function (text) {
              return !text?"":(text.length>10?text.substr(0,10):text)
            }
          },
          {
            sorter: true, // 排序用
            // width:'10%',
            title:'uv',
            align:"center",
            dataIndex: 'uv'
          },
          {
            sorter: true, // 排序用
            // width:'10%',
            title:'pv',
            align:"center",
            dataIndex: 'pv'
          },
          {
            sorter: true, // 排序用
            // width:'10%',
            title:'订购量',
            align:"center",
            dataIndex: 'ordernum'
          },
          {
            sorter: true, // 排序用
            // width:'10%',
            title:'转化率',
            align:"center",
            dataIndex: 'percent'
          },
          // {
          //   width:100,
          //   title:'地区ID',
          //   align:"center",
          //   dataIndex: 'carrierid'
          // },
          // {
          //   width:100,
          //   title:'地区名',
          //   align:"center",
          //   dataIndex: 'carriername'
          // },
          {
            sorter: true, // 排序用
            title:'自订购数据',
            align:"center",
            dataIndex: 'fakeordernum'
          },

        ],
        url: {
          list: "/scyd/scydDayReportsVideo/list",
          exportXlsUrl: "/scyd/scydDayReportsVideo/exportXls",

        },
        dictOptions:{
        },
        /* 排序参数 */
        isorter:{
          column: '',
          order: '',
        },
        queryParam: {
          date_begin:localDate(),
          date_end:localDate(),
        },

        /*试一下直接传入数据*/
        chartSettings: {
          labelMap: {
            'uv': '访问用户',
            'ordernum': '下单用户',
            'percent':'转化率',
          },
          axisSite: { right: ['percent'] },
          yAxisType: ['KMB', 'percent'],
          yAxisName: ['人数', '比率']
        },
        chartData: {
          columns: ['date', 'uv','ordernum', 'percent'],
          rows: [

          ]
        },
      }
    },
    computed: {
      importExcelUrl: function(){
        return `${window._CONFIG['domianURL']}/${this.url.importExcelUrl}`;
      }
    },
    methods: {
      initDictConfig(){
      },
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
        // 判断是否有时间问题
        if(!params.date_begin || !params.date_end){
          this.$message.error("请选择查询日期")
          return false;
        }

        this.loading = true;
        getAction(this.url.list, params).then((res) => {
          if (res.success) {
            this.dataSource = res.result.records;
            this.ipagination.total = res.result.total;
            // 添加图表显示
            this.chartData.rows = res.result.records;
          }
          if(res.code===510){
            this.$message.warning(res.message)
          }
          this.loading = false;
        })
      },
      getQueryParams() {


        //获取查询条件
        let sqp = {}
        if(this.superQueryParams){
          sqp['superQueryParams']=encodeURI(this.superQueryParams)
        }
        var param = Object.assign(sqp, this.queryParam, this.isorter ,this.filters);
        param.field = this.getQueryField();
        // param.pageNo = this.ipagination.current;
        // param.pageSize = this.ipagination.pageSize;
        // param.pageSize = 100;
        return filterObj(param);
      },
      searchReset() {
        this.queryParam = {
          date_begin:localDate(),
          date_end:localDate(),
        }
        this.loadData(1);
      },
       
    }
  }
</script>
<style scoped>
  @import '~@assets/less/common.less'
</style>