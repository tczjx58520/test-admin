<template>
  <div>
    <!-- 查询结束 -->
    <Card class="warp-card" dis-hover style="height: calc(100vh)">
      <div style="margin-bottom:20px;">
        <Button style="margin-right:15px;" @click="reset" icon="md-refresh" type="default">{{ $t('Reflash') }}</Button>
        <Button v-privilege="['19-25-1']" style="margin-right:15px;" @click="created" icon="md-add" type="warning">{{ $t('Create') }}</Button>
        <Button v-privilege="['19-25-3']" style="margin-right:15px;" @click="clear" icon="md-close" type="error">{{ $t('Delete') }}</Button>
      </div>
      <Table :columns="columns" :data="data" :loading="loading" max-heigth="calc(80vh)" @on-selection-change="selectwelfare" @on-row-dblclick="Edit"></Table>
      <Page :current="searchform.pageNum" :page-size="searchform.pageSize" :page-size-opts="[10, 20, 30, 50, 100]"
        :total="pageTotal" @on-change="changePage" @on-page-size-change="changePageSize" show-elevator show-sizer
        show-total style="margin:24px 0;text-align:right;"></Page>
    </Card>
    <!-- 新建弹窗 -->
        <addModal :modalstat = "visiable" @updateStat = "updateStat"></addModal>
    <!-- 新建结束============= -->
        <editModal :modalstat = "visiable_edit" :editinfo="editinfo" @updateStat = "updateStat_edit"></editModal>
  </div>
</template>

<script>
import {
  welfareApi
} from '@/api/welfare';
import addModal from './components/addmodal/modal';
import editModal from './components/editmodal/modal';
import addemp from './components/addemp/modal';
export default {
  name: 'welfare',
  components: {
    addModal,
    addemp,
    editModal
  },
  props: {},
  data () {
    return {
      visiable_edit: false,
      editinfo: {},
      searchform: {
        pageNum: 1,
        pageSize: 10
      },
      loading: false,
      pageTotal: 0,
      // table表头
      columns: [
        {
          type: 'selection',
          width: 50,
          align: 'center'
        },
        {
          title: this.$t('welfare_view.title'),
          key: 'title'
        },
        {
          title: this.$t('welfare_view.creatTime'),
          key: 'createDate'
        },
        {
          title: this.$t('welfare_view.creatPerson'),
          key: 'createName'
        },
        {
          title: this.$t('welfare_view.suitable'),
          key: 'userName',
          render: (h, params) => {
            if (params.row.suitType === 2) {
              return h('span', this.$t('welfare_view.personnel'));
            } else {
              return h('span', this.$t('welfare_view.org'));
            }
          }
        },
        {
          title: this.$t('welfare_view.content'),
          key: 'content'
        },
        {
          title: this.$t('usermanage_view.action'),
          key: 'action',
          width: 200,
          align: 'center',
          render: (h, params) => {
            return h('div', [
              h('Button', {
                props: {
                  type: 'info',
                  size: 'small'
                },
                directives: [
                  {
                    name: 'privilege',
                    value: ['19-25-2']
                  }
                ],
                on: {
                  click: () => {
                    this.Edit(params.row);
                  }
                }
              }, this.$t('Edit'))
            ]);
          }
        }
        //   {
        //     title: '操作',
        //     key: 'action',
        //     align: 'center',
        //     className: 'action-hide',
        //     render: (h, param) => {
        //       return this.$tableAction(h, [{
        //         title: '删除',
        //         directives: [{
        //           name: 'privilege',
        //           value: 'user-login-log-delete'
        //         }],
        //         action: () => {
        //           this.$Modal.confirm({
        //             title: '友情提醒',
        //             content: '确定要删除吗？',
        //             onOk: () => {
        //               this.deleteLog(param.row.id);
        //             }
        //           });
        //         }
        //       }]);
        //     }
        //   }
      ],
      // table数据
      data: [],
      // 新建弹窗
      visiable: false,
      moreWelfare: []
    };
  },
  computed: {},
  watch: {},
  filters: {},
  created () {},
  mounted () {
    this.getwelfareList();
  },
  methods: {
    Edit (row) {
      if (this.$judge(['1-4-2'])) {
        this.editinfo = row;
        this.visiable_edit = true;
      } else {
        console.log('needroles');
      }
    },
    selectwelfare (selection) {
      this.moreWelfare = selection;
    },
    clear () {
      console.log('this.moreWelfare', this.moreWelfare);
      for (const i in this.moreWelfare) {
        let data = {};
        data.welfareSetId = this.moreWelfare[i].id;
        data.operatId = this.$store.state.user.userLoginInfo.userId;
        welfareApi.delwelfare(data).then(res => {
          if (res.ret === 200) {
            this.$Message.success(res.msg);
            this.reset();
          } else {
            this.$Message.error(res.msg);
          }
        });
      }
    },
    // 查询用户登录日志
    async getwelfareList () {
      try {
        this.loading = true;
        let result = await welfareApi.getwelfareList(this.searchform);
        this.loading = false;
        this.data = result.data.content.list;
        this.pageTotal = result.data.content.totalCount;
      } catch (e) {
        // TODO zhuoda sentry
        console.error(e);
        this.loading = false;
      }
    },
    // 翻页
    changePage (pageNum) {
      this.searchform.pageNum = pageNum;
      this.getUserLoginLogPage();
    },
    // 改变一页展示数
    changePageSize (pageSize) {
      this.searchform.pageNum = 1;
      this.searchform.pageSize = pageSize;
      this.getUserLoginLogPage();
    },
    // 搜索
    search () {
      this.searchform.pageNum = 1;
      this.getwelfareList();
    },
    // 重置
    reset () {
      this.searchform.pageNum = 1;
      this.searchform.pageSize = 10;
      this.search();
    },
    // 删除日志(暂无)
    async deleteLog () {
      this.$Spin.show();
      this.$Spin.hide();
      this.$Message.success('删除成功');
      this.getUserLoginLogPage();
    },
    // 弹窗组件
    created () {
      this.visiable = true;
    },
    updateStat (stat) {
      this.visiable = stat;
      this.getwelfareList();
    },
    updateStat_edit (stat) {
      this.visiable_edit = stat;
      this.getwelfareList();
    }
    // 弹窗组件结束=========================》
  }
};

</script>
<style lang="less" scoped>
    .ivu-form-item {
        margin-bottom: 0;
    }
</style>
