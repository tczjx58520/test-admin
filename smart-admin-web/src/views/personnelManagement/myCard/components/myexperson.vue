<template>
  <div>
    <TabPane :label="$t('grjl')" name="name5">
      <div style="display: flex; align-items: center">
        <div
          style="
            width: 4px;
            height: 20px;
            background: #2d8cf0;
            margin-right: 15px;
          "
        ></div>
        <div>{{ $t("gongzuojinli") }}</div>
      </div>
      <Divider />
      <Table stripe :columns="columns1" :data="data1">
        <template slot-scope="{ row }" slot="companyName">
          <strong>{{ row.companyName }}</strong>
        </template>
        <template slot-scope="{ row }" slot="postName">
          <strong>{{ row.postName }}</strong>
        </template>
        <template slot-scope="{ row }" slot="beginTime">
          <strong>{{ row.beginTime }}</strong>
        </template>
        <template slot-scope="{ row }" slot="endTime">
          <strong>{{ row.endTime }}</strong>
        </template>
        <template slot-scope="{ row }" slot="leaveReason">
          <strong>{{ row.leaveReason }}</strong>
        </template>
        <template slot-scope="{ row }" slot="describution">
          <strong>{{ row.describution }}</strong>
        </template>
      </Table>
      <div style="display: flex; align-items: center">
        <div
          style="
            width: 4px;
            height: 20px;
            background: #2d8cf0;
            margin-right: 15px;
          "
        ></div>
        <div>{{ $t("jianyujinli") }}</div>
      </div>
      <Divider />
      <Table stripe :columns="columns2" :data="data2">
        <template slot-scope="{ row }" slot="schoolName">
          <strong>{{ row.schoolName }}</strong>
        </template>
        <template slot-scope="{ row }" slot="major">
          <strong>{{ row.major }}</strong>
        </template>
        <template slot-scope="{ row }" slot="beginTime">
          <strong>{{ row.beginTime }}</strong>
        </template>
        <template slot-scope="{ row }" slot="endTime">
          <strong>{{ row.endTime }}</strong>
        </template>
        <template slot-scope="{ row }" slot="education">
          <strong>{{ row.education }}</strong>
        </template>
      </Table>

      <div style="display: flex; align-items: center">
        <div
          style="
            width: 4px;
            height: 20px;
            background: #2d8cf0;
            margin-right: 15px;
          "
        ></div>
        <div>{{ $t("peixunjinli") }}</div>
      </div>
      <Divider />
      <Table stripe :columns="columns3" :data="data3">
        <template slot-scope="{ row }" slot="trainingSchoolName">
          <strong>{{ row.trainingSchoolName }}</strong>
        </template>
        <template slot-scope="{ row }" slot="trainingName">
          <strong>{{ row.trainingName }}</strong>
        </template>
        <template slot-scope="{ row }" slot="beginTime">
          <strong>{{ row.beginTime }}</strong>
        </template>
        <template slot-scope="{ row }" slot="endTime">
          <strong>{{ row.endTime }}</strong>
        </template>
      </Table>

      <div style="display: flex; align-items: center">
        <div
          style="
            width: 4px;
            height: 20px;
            background: #2d8cf0;
            margin-right: 15px;
          "
        ></div>
        <div>{{ $t("zigezhenshu") }}</div>
      </div>
      <Divider />
      <Table stripe :columns="columns4" :data="data4">
        <template slot-scope="{ row }" slot="qualifications">
          <strong>{{ row.qualifications }}</strong>
        </template>
        <template slot-scope="{ row }" slot="beginTime">
          <strong>{{ row.beginTime }}</strong>
        </template>
        <template slot-scope="{ row }" slot="endTime">
          <strong>{{ row.endTime }}</strong>
        </template>
        <template slot-scope="{ row }" slot="issuingUnit">
          <strong>{{ row.issuingUnit }}</strong>
        </template>
        <template slot-scope="{ row }" slot="certificateNumber">
          <strong>{{ row.certificateNumber }}</strong>
        </template>
      </Table>

      <div style="display: flex; align-items: center">
        <div
          style="
            width: 4px;
            height: 20px;
            background: #2d8cf0;
            margin-right: 15px;
          "
        ></div>
        <div>{{ $t("yuyannengli") }}</div>
      </div>
      <Divider />
      <Table stripe :columns="columns5" :data="data5">
        <template slot-scope="{ row }" slot="language">
          <strong>{{ row.language }}</strong>
        </template>
        <template slot-scope="{ row }" slot="level">
          <strong>{{ row.level }}</strong>
        </template>
      </Table>
    </TabPane>
  </div>
</template>
<script>
import { empCard } from '@/api/empCard';
export default {
  name: 'userInfo_tab',
  components: {},
  props: {},
  data () {
    return {
      columns1: [
        {
          title: this.$t('gongsimingchen'),
          slot: 'companyName'
        },
        {
          title: this.$t('gangweimingchen'),
          slot: 'postName'
        },
        {
          title: this.$t('kaishishijian'),
          slot: 'beginTime'
        },
        {
          title: this.$t('jieshushijian'),
          slot: 'endTime'
        },
        {
          title: this.$t('lizhiyuanyin'),
          slot: 'leaveReason'
        },
        {
          title: this.$t('miaoshu'),
          slot: 'describution'
        }
      ],
      data1: [
        {
          companyName: '阿里巴巴',
          postName: '技术总监',
          beginTime: '2020-12-19 02:18:00',
          endTime: '2020-8-19 02:18:00',
          leaveReason: '个人原因',
          describution: '好'
        }
      ],
      columns2: [
        {
          title: this.$t('xuexiaomingchen'),
          slot: 'schoolName'
        },
        {
          title: this.$t('zhuanye'),
          slot: 'major'
        },
        {
          title: this.$t('kaishishijian'),
          slot: 'beginTime'
        },
        {
          title: this.$t('jieshushijian'),
          slot: 'endTime'
        },
        {
          title: this.$t('xueli'),
          slot: 'education'
        }
      ],
      data2: [
        {
          schoolName: '哈佛大学',
          major: '计算机',
          beginTime: '2020-12-19 02:18:00',
          endTime: '2020-8-19 02:18:00',
          education: '本科'
        }
      ],
      columns3: [
        {
          title: this.$t('peixunjigoumingchen'),
          slot: 'trainingSchoolName'
        },
        {
          title: this.$t('peixundemingchen'),
          slot: 'trainingName'
        },
        {
          title: this.$t('kaishishijian'),
          slot: 'beginTime'
        },
        {
          title: this.$t('jieshushijian'),
          slot: 'endTime'
        }
      ],
      data3: [
        {
          trainingSchoolName: '新东方',
          trainingName: '英语',
          beginTime: '2020-12-19 02:18:00',
          endTime: '2020-8-19 02:18:00'
        }
      ],
      columns4: [
        {
          title: this.$t('zigezhengshumingchen'),
          slot: 'qualifications'
        },
        {
          title: this.$t('kaishishijian'),
          slot: 'beginTime'
        },
        {
          title: this.$t('jieshushijian'),
          slot: 'endTime'
        },
        {
          title: this.$t('banfadanwei'),
          slot: 'issuingUnit'
        },
        {
          title: this.$t('zhengshubianhao'),
          slot: 'certificateNumber'
        }
      ],
      data4: [
        {
          qualifications: '南京五好青年',
          beginTime: '2020-12-19 02:18:00',
          endTime: '2020-8-19 02:18:00',
          issuingUnit: '南京市政府',
          certificateNumber: '32554448822255'
        }
      ],
      columns5: [
        {
          title: this.$t('yuyan'),
          slot: 'language'
        },
        {
          title: this.$t('shuipin'),
          slot: 'level'
        }
      ],
      data5: [
        {
          language: '英语',
          level: '四级'
        },
        {
          language: '菲律宾语',
          level: '四级'
        }
      ]
    };
  },
  computed: {},
  watch: {},
  filters: {},
  created () {},
  mounted () {
    this.getInfo();
  },
  methods: {
    getInfo () {
      const data = {
        employeeId: this.$store.state.user.userLoginInfo.userId
        // employeeId: 75568
      };
      empCard.getPersonExpress(data).then(res => {
        this.showformBase = Object.assign({}, res.data);
        this.data5 = res.data.languageList;
        this.data1 = res.data.workExperienceList;
        this.data2 = res.data.eduExperienceList;
        this.data3 = res.data.trainningExperienceList;
        this.data4 = res.data.certificateList;
      });
    }
  }
};
</script>
<style lang="less" scoped></style>
