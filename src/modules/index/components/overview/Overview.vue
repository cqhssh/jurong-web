<template>
  <div class="overview">
    <el-tabs v-model="activeName" @tab-click="switchTab">
      <el-tab-pane label="概况" name="survey">
        <common-graphic :data="graphic" type="city" :refresh="surveyRefresh"></common-graphic>
      </el-tab-pane>
      <el-tab-pane label="领导机构" name="organization">
        <div class="wrap">
          <div>
            <img :src="!showTable ? '/static/img/button_totable.png' : 'static/img/button_topic.png'" @click="showOrganizationTable">
          </div>
        </div>
        <div v-show="!showTable" id="chartWrap1" class="chart-wrap"></div>
        <div v-show="showTable">
          <CommonDialog ref="organizationDialog" :form-columns="organizationFormColumns" @submit="traggerBrotherEvent" :show-btn="true"></CommonDialog>
          <CommonTable ref="organizationTable" :columns="organizationColumns" :show-pagination="false"></CommonTable>
        </div>
      </el-tab-pane>
      <el-tab-pane label="办公室概况" name="office">
        <el-tabs v-model="officeName" @tab-click="switchTabOffice">
          <el-tab-pane v-for="(item,index) in officeTab" :key="index" :label="item.label" :name="item.name">
            <common-graphic :data="item" :refresh="item.refresh"></common-graphic>
          </el-tab-pane>
        </el-tabs>
      </el-tab-pane>
    </el-tabs>
  </div>
</template>

<script>
  import {baseUrl} from '@/common/constants/config';
  import reqType from '@/api/reqType';
  import CommonTable from '../common/CommonTable';
  import CommonDialog from '../common/CommonDialog';
  import CommonGraphic from '../common/CommonGraphic';
  const echarts = require('echarts/lib/echarts');
  require('echarts/lib/chart/bar');
  require('echarts/lib/chart/pie');
  require('echarts/lib/chart/line');
  require('echarts/lib/chart/tree');
  require('echarts/lib/component/tooltip');
  require('echarts/lib/component/legend');

  export default {
    name: 'Overview',
    components: {
      CommonGraphic,
      CommonTable,
      CommonDialog
    },
    props: [],
    data () {
      return {
        activeName: 'survey', // 默认tab
        graphic: {},
        organization: [{
          'name': '戴裔',
          'id': '4',
          'children': [
            {
              'name': '糜静娟',
              'id': '5',
              'children': [
                {
                  'name': '董爱梅',
                  'id': '6',
                  'children': [],
                  'depName': '人社'
                },
                {
                  'name': '徐圆',
                  'id': '7',
                  'children': [],
                  'depName': '计生'
                },
                {
                  'name': '朱瑞',
                  'id': '8',
                  'children': [],
                  'depName': '民政'
                },
                {
                  'name': '吴世玲',
                  'id': '9',
                  'children': [],
                  'depName': '农服'
                },
                {
                  'name': '张远华',
                  'id': '10',
                  'children': [],
                  'depName': '村镇'
                },
                {
                  'name': '金鑫',
                  'id': '11',
                  'children': [],
                  'depName': '司法'
                },
                {
                  'name': '巫君',
                  'id': '12',
                  'children': [],
                  'depName': '信访'
                },
                {
                  'name': '陈叶',
                  'id': '13',
                  'children': [],
                  'depName': '全科综合'
                }
              ],
              'depName': '为人民服务中心1'
            }
          ],
          'depName': '政府'
        }], // 模拟领导机构图数据
        organizationColumns: [
          {
            type: 'index',
            label: '序号'
          },
          {
            prop: 'name',
            label: '姓名'
          },
          {
            prop: 'sex',
            label: '性别'
          },
          {
            prop: 'positon',
            label: '职务'
          },
          {
            prop: 'remark',
            label: '备注'
          },
          {
            type: 'function',
            label: '操作',
            functionOpt: [
              {
                type: 'text',
                label: '编辑',
                func: this.edit
              },
              {
                type: 'text',
                label: '删除',
                func: this.delete
              }
            ]
          }
        ], // 模拟领导机构表格表头数据
        showTable: false, // 默认不显示表格，点击切换显示表格
        organizationFormColumns: [
          {
            type: 'text',
            key: 'name',
            label: '姓名'
          },
          {
            type: 'text',
            key: 'sex',
            label: '性别'
          },
          {
            type: 'text',
            key: 'positon',
            label: '职位'
          },
          {
            type: 'text',
            key: 'duty',
            label: '行政职务'
          },
          {
            type: 'text',
            key: 'remark',
            label: '备注'
          },
          {
            type: 'text',
            key: 'level',
            label: '层级'
          }
        ], // 组织机构弹框内容
        officeTab: [
          {
            id: 1,
            label: '办公室1',
            name: 'office1',
            img: [
              {path: '/static/img/test.jpeg', pathB: '/static/img/test.jpeg'},
              {path: '/static/img/bj.jpg', pathB: '2b'},
              {path: '/static/img/test.jpeg', pathB: '3b'},
              {path: '/static/img/bj.jpg', pathB: '4b'}
            ],
            text: '1111111111dadadadadadadada',
            refresh: false
          },
          {
            id: 2,
            label: '办公室2',
            name: 'office2',
            img: [
              {path: '/static/img/bj.jpg', pathB: '/static/img/test.jpeg'},
              {path: '/static/img/bj.jpg', pathB: '2b'},
              {path: '/static/img/bj.jpg', pathB: '3b'},
              {path: '/static/img/bj.jpg', pathB: '4b'}
            ],
            text: 'dadada12da',
            refresh: false
          },
          {
            id: 3,
            label: '办公室3',
            name: 'office3',
            img: [
              {path: '/static/img/bj.jpg', pathB: '/static/img/test.jpeg'},
              {path: '/static/img/bj.jpg', pathB: '2b'},
              {path: '/static/img/bj.jpg', pathB: '3b'},
              {path: '/static/img/bj.jpg', pathB: '4b'}
            ],
            text: 'dad33333dada',
            refresh: false
          }
        ],
        officeName: '',
        surveyRefresh: false
      };
    },
    created () {
      this.getSurveylist();
    },
    mounted () {
      this.drawChart1();
      this.officeName = this.officeTab && this.officeTab[0].name;
    },
    methods: {
      /**
       * 获取概况列表
       */
      getSurveylist () {
        this.$http(reqType.POST, `city/list`, false)
          .then(data => {
            this.graphic = this.initGraphic(data[0]);
          });
      },
      /**
       * 获取领导机构列表
       */
      getOrganizationlist () {
        this.$http(reqType.POST, `orgPerson/list`, {orgId: this.graphic.id, type: 'ORG_CITY'}, false)
          .then(data => {
            this.$refs.organizationTable.tableData = data;
          });
      },
      /**
       * 切换tab
       */
      switchTab (e) {
        let _index = parseInt(e.index);
        switch (_index) {
          case 0: // 概况 survey
            this.surveyRefresh = !this.surveyRefresh;
            return false;
          case 1: // 领导机构 organization
            this.showTable = false;
            this.getOrganizationlist();
            return false;
          case 2: // 办公室概况 office
            this.officeTab.forEach((v, i) => {
              let _flag = this.officeTab[i].refresh;
              this.officeTab[i].refresh = !_flag;
            });
            return false;
        }
      },
      switchTabOffice (e) {
        let _index = parseInt(e.index);
        let _flag = this.officeTab[_index].refresh;
        this.officeTab[_index].refresh = !_flag;
      },
      /**
       * 绘制领导机构图
       */
      drawChart1 () {
        let myChart = document.getElementById('chartWrap1');
        myChart.style.width = window.innerWidth - 500 + 'px';
        let option, chart;
        option = {
          tooltip: {
            trigger: 'item',
            triggerOn: 'mousemove'
          },
          series: [
            {
              type: 'tree',

              data: this.organization,

              left: '2%',
              right: '2%',
              top: '18%',
              bottom: '20%',
              symbolSize: [110, 50],
              symbol: '/static/img/jgbj.png',
              borderColor: 'black',
              orient: 'vertical',
              expandAndCollapse: true,
              label: {
                normal: {
                  position: 'inside',
                  rotate: 0,
                  verticalAlign: 'middle',
                  align: 'center',
                  fontSize: 16
                }
              },
              leaves: {
                label: {
                  normal: {
                    position: 'inside',
                    rotate: 0,
                    verticalAlign: 'middle',
                    align: 'center'
                  }
                }
              },

              animationDurationUpdate: 750
            }
          ]
        };
        chart = echarts.init(myChart);
        chart.setOption(option);
      },
      /**
       * 编辑某一个机构人员
       */
      edit (index, row) {
        if (this.$refs.organizationDialog) {
          this.$refs.organizationDialog.title = '编辑';
          this.$refs.organizationDialog.dialogVisible = true;
          this.$refs.organizationDialog.form = row;
        }
      },
      /**
       * 删除某一个机构人员
       */
      delete (index, row) {
        this.$http(reqType.DELETE, `orgPerson/${row.id}`, false)
          .then(data => {
            this.$alert('删除成功', '提示', {dangerouslyUseHTMLString: true});
            this.getOrganizationlist();
          });
      },
      /**
       * 点击显示table
       */
      showOrganizationTable () {
        this.showTable = !this.showTable;
        // if (this.showTable === true) {
        //   this.$refs.organizationTable.tableData = [{
        //     name: '张三',
        //     sex: '男',
        //     duty: '办公室主任',
        //     remark: '负责整个办公室的各项事务'
        //   }];
        //   this.$refs.organizationTable.pageable = {
        //     total: 1,
        //     currentPage: 1,
        //     pageSize: 10
        //   };
        // }
      },
      /**
       * 新增和编辑弹框
       */
      traggerBrotherEvent (form) {
        if (!form.id) {
          form.orgId = this.graphic.id;
          form.type = 'ORG_CITY';
          this.$http(reqType.POST, `orgPerson/`, form, false)
            .then(data => {
              this.getOrganizationlist();
            });
        }
        if (form.id) {
          form.orgId = this.graphic.id;
          form.type = 'ORG_CITY';
          this.$http(reqType.PUT, `orgPerson/${form.id}`, form, false)
            .then(data => {
              this.getOrganizationlist();
            });
        }
      },
      /**
       * 处理图文详情的数据
       */
      initGraphic (list) {
        if (list.jrResourceList) {
          list.img = [];
          list.imgB = [];
          list.jrResourceList.forEach((v, i) => {
            let _img = {};
            let _imgB = {};
            _img.url = `${baseUrl}${v.thumbnail}`;
            _imgB.url = `${baseUrl}${v.url}`;
            _imgB.id = v.id;
            _imgB.index = i;
            list.img.push(_img);
            list.imgB.push(_imgB);
          });
          return list;
        }
      }
    },
    watch: {}
  };
</script>

<style scoped lang="less">
  .overview {
    img {
      width: 45px;
      height: 45px;
    }
    padding: 0 15px 15px;
    .back {
      background: url("/static/img/jgbj.png");
    }
    .wrap {
      text-align: right;
      &>div{
        display: inline-flex;
        flex-direction: column;
      }
      img {
        width: 40px;
        height: 40px;
      }
    }
    .chart-wrap {
      width:100%;
      height:300px;
    }
  }
</style>
