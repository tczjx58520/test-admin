<template>
  <div>
    <Card dis-hover>
      <div style="display: flex;align-items: center;">
        <div style="margin-right: 7px">{{ $t("planTitle") }}</div>
        <div>
          <Input v-model="listQuery.title" />
        </div>

        <div style="margin-left: 50px;margin-right: 7px">{{ $t("createTime") }}</div>
        <div>
          <DatePicker type="daterange"
                      placement="bottom-end"
                      @on-change="changeDate"
                      placeholder="Select date"
                      style="width: 200px"></DatePicker>
        </div>

        <div style="margin-left: 50px;margin-right: 7px">{{ $t("state") }}</div>
        <div>
          <Select v-model="listQuery.planStatus"
                  style="width:200px">
            <Option v-for="item in selectList"
                    :value="item.value"
                    :key="item.value">{{ item.label }}</Option>
          </Select>
        </div>

        <div style="margin-left: 50px;margin-right: 7px">{{ $t("planType") }}</div>
        <div>
          <Select v-model="listQuery.type"
                  style="width:200px">
            <Option :value="0">日计划</Option>
            <Option :value="1">周计划</Option>
            <Option :value="2">月计划</Option>
            <Option :value="3">年计划</Option>
          </Select>
        </div>
        <Button type="primary"
                style="margin-left: 15px"
                @click="handleSelect">{{ $t("Search") }}</Button>
      </div>
    </Card>
    <Card class="warp-card"
          dis-hover>
      <div>

        <Button style="margin-right:15px;"
                icon="md-refresh"
                @click="refresh">{{ $t('Reflash') }}</Button>

        <RadioGroup v-model="listQuery.category"
                    type="button"
                    @on-change="changeType">
          <Radio label="">所有</Radio>
          <Radio label="0">个人计划</Radio>
          <Radio label="1">组织计划</Radio>
          <Radio label="2">工作汇报</Radio>
          <Radio label="3">工作总结</Radio>
        </RadioGroup>
        <!-- <Button style="margin-right:15px;"
                icon="md-refresh"
                @click="refresh"
                type="default">{{ $t('Reflash') }}</Button>
        <Button style="margin-right:15px;"
                v-privilege="['10-15-1']"
                @click="findAll"
                type="warning">{{ $t('All') }}</Button>
        <Button style="margin-right:15px;"
                v-privilege="['10-15-1']"
                @click="personalPlan"
                type="error">{{ $t('personalPlan') }}</Button>
        <Button style="margin-right:15px;"
                v-privilege="['10-15-1']"
                @click="organizationPlan"
                type="error">{{ $t('organizationPlan') }}</Button>
        <Button style="margin-right:15px;"
                v-privilege="['10-15-1']"
                @click="workreport"
                type="error">{{ $t('workreport') }}</Button>
        <Button style="margin-right:15px;"
                v-privilege="['10-15-1']"
                @click="worksummary"
                type="error">{{ $t('worksummary') }}</Button> -->
      </div>
    </Card>

    <Card style="margin-top:10px">
      <Table max-height="500px"
             :columns="tablecolumns"
             :data="tableData"
             @on-selection-change="changeTable">
        <template slot-scope="{ row, index }"
                  slot="action">
          <Button type="primary"
                  size="small"
                  style="margin-right: 5px"
                  @click="show(row)">查看</Button>
        </template>
      </Table>
      <Page :current="listQuery.pageNum"
            :page-size="listQuery.pageSize"
            :page-size-opts="[10, 20, 30, 50, 100]"
            :total="total"
            @on-change="changePageNum"
            @on-page-size-change="changePageSize"
            show-elevator
            show-sizer
            show-total
            style="margin:24px 0;text-align:right;"></Page>
    </Card>

  </div>
</template>
<script>
import { planManage } from '@/api/planManage';
const defaultListQuery = {
  pageNum: 1,
  pageSize: 10,
  employeeId: null,
  flag: 2
};
export default {
  data () {
    return {
      tablecolumns: [
        {
          type: 'selection',
          width: 60,
          align: 'center'
        },
        {
          title: this.$t('title'),
          key: 'title'
        },
        {
          title: this.$t('startTime'),
          key: 'startTime'
        },
        {
          title: this.$t('endTime'),
          key: 'endTime'
        },
        {
          title: this.$t('planState'),
          key: 'planStatus',
          render: (h, params) => {
            if (params.row.planStatus === 0) {
              return h('span', '未开始');
            }
            if (params.row.planStatus === 1) {
              return h('span', '进行中');
            }
            if (params.row.planStatus === 1) {
              return h('span', '已完成');
            }
          }
        },
        {
          title: this.$t('updateTime'),
          key: 'createTime'
        },
        {
          title: this.$t('planType'),
          key: 'type',
          render: (h, params) => {
            if (params.row.type === 0) {
              return h('span', '日');
            }
            if (params.row.type === 1) {
              return h('span', '周');
            }
            if (params.row.type === 2) {
              return h('span', '月');
            }
            if (params.row.type === 3) {
              return h('span', '年');
            }
          }
        },
        {
          title: this.$t('action'),
          key: 'position',
          slot: 'action'
        }
      ],
      tableData: [],
      total: 0,
      listQuery: Object.assign({}, defaultListQuery),
      selectList: [
        {
          value: '0',
          label: '未开始'
        },
        {
          value: '1',
          label: '进行中'
        },
        {
          value: '2',
          label: '已完成'
        }
      ],
      selectedData: []
    };
  },
  created () {
    this.getList();
  },
  methods: {
    changeType () {
      this.getList();
    },
    getList () {
      this.listQuery.employeeId = this.$store.state.user.userLoginInfo.userId;
      planManage.findPlan(this.listQuery).then(res => {
        this.tableData = res.data.list;
        this.total = res.data.total;
        console.log(111111111, this.tableData);
      });
    },
    changeDate (val) {
      this.listQuery.createStartTime = val[0];
      this.listQuery.createEndTime = val[1];
      console.log(val);
    },
    changePageNum (val) {
      this.listQuery.pageNum = val;
    },
    changePageSize (val) {
      this.listQuery.pageSize = val;
    },
    changeTable (val) {
      this.selectedData = val;
    },
    refresh () {
      this.listQuery.category = null;
      this.getList();
    },
    findAll () {
      this.listQuery.category = null;
      this.getList();
    },
    personalPlan () {
      this.listQuery.category = 0;
      this.getList();
    },
    organizationPlan () {
      this.listQuery.category = 1;
      this.getList();
    },
    workreport () {
      this.listQuery.category = 2;
      this.getList();
    },
    worksummary () {
      this.listQuery.category = 3;
      this.getList();
    },
    handleSelect () {
      this.getList();
    },
    show (row) {
      console.log('show', row);
      const planInfomation = Object.assign({}, row);
      const nameList = [];
      planInfomation.planShareFors.forEach(element => {
        nameList.push(element.shareForPersonName);
      });
      planInfomation.userName = nameList.join(',');
      this.$router.push({ path: '/planManagement/viewPlan', query: { planInfo: planInfomation } });
    }
  }
};
</script>
