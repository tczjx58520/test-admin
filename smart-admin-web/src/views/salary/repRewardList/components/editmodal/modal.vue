<template>
  <Modal
    v-model="mymoadlStat"
    class="add"
    width="720"
    :closable="false"
    :mask-closable="false"
    :transfer="false"
  >
    <div slot="header" style="text-align: left; color: #fff">
      <span>{{ $t("xgzc") }}</span>
    </div>
    <div>
      <Card dis-hover>
        <div
          style="
            display: flex;
            align-items: center;
            border-bottom: 1px solid #e1e1e1;
            padding-bottom: 20px;
          "
        >
          <div
            style="
              width: 4px;
              height: 20px;
              background: #2d8cf0;
              margin-right: 15px;
            "
          ></div>
          <div>{{ $t("BaseData") }}</div>
        </div>
        <Divider />
        <Form
          ref="form"
          :model="addformbase"
          label-position="right"
          :label-width="100"
          :rules="ruleValidate"
          inline
        >
          <Row>
            <Col span="24">
              <FormItem :label="$t('shijianbiaoti')" style="width: 100%;height:60px" prop="title">
                <Input v-model="addformbase.title" />
              </FormItem>
            </Col>
            <Col span="12">
              <FormItem :label="$t('lianxiren')" style="width: 100%;height:60px" prop="contactName">
                <Input v-model="addformbase.contactName" @on-focus="handleSelectContact"/>
              </FormItem>
            </Col>
            <Col span="12">
              <FormItem :label="$t('shijianfang')" style="width: 100%;height:60px" >
                <Input v-model="addformbase.organizationName" readonly/>
              </FormItem>
            </Col>
            <Col span="12">
              <FormItem :label="$t('chuliren')" style="width: 100%;height:60px" prop="handPersonName">
                <Input v-model="addformbase.handPersonName" @on-focus="handleSelectEmp"/>
              </FormItem>
            </Col>
            <Col span="12">
              <FormItem :label="$t('shijianshijian')" style="width: 100%;height:60px">
                <DatePicker
                  v-model="addformbase.createtimeStr"
                  :options="options"
                  type="date"
                  placeholder="Select date"
                  style="width: 100%"
                  @on-change="selectDate"
                ></DatePicker>
              </FormItem>
            </Col>
            <Col span="24">
              <FormItem :label="$t('shijianneirong')" style="width: 100%;height:60px" prop="content">
                <Input type="textarea" v-model="addformbase.content" />
              </FormItem>
            </Col>
          </Row>
        </Form>
      </Card>
    </div>
    <div slot="footer">
      <ButtonGroup>
        <Button
          type="primary"
          size="large"
          :loading="modal_loading"
          @click="handsave"
          >{{ $t("Save") }}</Button
        >
        <Button type="error" size="large" @click="cancel">{{
          $t("Close")
        }}</Button>
      </ButtonGroup>
    </div>
  </Modal>
</template>
<script>
import { publicEventsList } from '@/api/publicEventsList';
import { assetManage } from '@/api/assetManage';
import { classification } from '@/api/classification';
import { numSetting } from '@/api/numSetting';
import { utils } from '@/lib/util';
import {
  unitOfMeasure
} from '@/api/unitOfMeasure';
import {
  storage
} from '@/api/storageLocation';
export default {
  name: 'addModal',
  components: {
  },
  props: {
    modalstat: {
      type: Boolean,
      default: false
    },
    editinfo: null
  },
  created () {},
  mounted () {},
  data () {
    const validatePass2 = (rule, value, callback) => {
      if (
        this.addformbase.contactId === '' &&
        this.addformbase.contactId === null &&
        this.addformbase.contactId === undefined
      ) {
        callback(new Error('Please enter your contactName'));
      } else {
        callback();
      }
    };
    const validatePass3 = (rule, value, callback) => {
      if (
        this.addformbase.handPersonId === '' &&
        this.addformbase.handPersonId === null &&
        this.addformbase.handPersonId === undefined
      ) {
        callback(new Error('Please enter your handPersonName'));
      } else {
        callback();
      }
    };
    const validatePass4 = (rule, value, callback) => {
      if (value === '') {
        callback(new Error('Please enter'));
      } else {
        callback();
      }
    };
    const checkphone = (rule, value, callback) => {
      if (!value) {
        return callback(new Error('please enter'));
      }
      setTimeout(() => {
        console.log(String(value).length);
        if (String(value).length !== 11) {
          callback(new Error('please enter Correct phone'));
        } else {
          callback();
        }
      }, 1000);
    };
    return {
      isShowTree: false,
      modal_loading: false,
      mymoadlStat: this.modalstat,
      addformbase: {},
      ruleValidate: {
        title: [
          {
            required: true,
            message: 'The title cannot be empty',
            trigger: 'change'
          }
        ],
        contactName: [
          {
            required: true,
            validator: validatePass2,
            trigger: 'change'
          }
        ],
        handPersonName: [
          {
            required: true,
            trigger: 'change',
            validator: validatePass3
          }
        ],
        content: [
          {
            required: true,
            message: 'The content cannot be empty',
            trigger: 'change'
          }
        ]
      },
      // 新建员工弹窗
      visiable_emp: false,
      visiable_org: false,
      visiable_post: false,
      tranferValue: null,
      mytype: null,
      levelList: [],
      reposList: [],
      data_class: [],
      unitList: [],
      locationList: [],
      isrequireNum: false,
      options: {
        disabledDate (date) {
          return date && date.valueOf() <= Date.now() - 86400000;
        }
      }
    };
  },
  computed: {
  },
  watch: {
    modalstat () {
      this.mymoadlStat = this.modalstat;
      if (this.mymoadlStat) {
        this.getList();
        this.addformbase = Object.assign({}, this.editinfo);
        this.addformbase.handPersonName = this.addformbase.handlePersonName;
        this.addformbase.organizationName = this.addformbase.eventParty;
        const temp = new Date(this.addformbase.createtime);
        const date = utils.getDate(temp, 'YMDHM');
        this.addformbase.createtimeStr = date;
        console.log('this.addformbase=====================', this.addformbase);
      }
    }
  },
  methods: {
    handleSelectContact () {
      this.visiable = true;
    },
    handleSelectEmp () {
      this.visiable_emp = true;
    },
    selectDate (val) {
      this.addformbase.createtime = val;
    },
    // 选择人员部门岗位
    selectOrg (index) {
      this.mytype = index;
      this.visiable_org = true;
      switch (this.mytype) {
        case 1:
          this.tranferValue = this.addformbase.startOrganize;
          break;
        case 2:
          this.tranferValue = this.addformbase.forbidOrganize;
          break;
        case 3:
          this.tranferValue = this.addformbase.queryOrganize;
          break;
        case 4:
          this.tranferValue = this.addformbase.manageOrganize;
          break;
      }
    },
    selectPost (index) {
      this.mytype = index;
      this.visiable_post = true;
      switch (this.mytype) {
        case 1:
          this.tranferValue = this.addformbase.startPost;
          break;
        case 2:
          this.tranferValue = this.addformbase.forbidPost;
          break;
        case 3:
          this.tranferValue = this.addformbase.queryPost;
          break;
        case 4:
          this.tranferValue = this.addformbase.managePost;
          break;
      }
    },
    selectEmp (index) {
      this.mytype = index;
      this.showemp();
    },
    // ==============end========
    updateStat_emp (stat, row) {
      this.visiable_emp = stat;
      if (!row) {
        return false;
      }
      this.addformbase.manageEmpName = row.personName;
      this.addformbase.manageEmp = row.id;
    },
    updateStat_org (stat, orgList) {
      console.log(orgList);
      this.visiable_org = stat;
      if (!orgList) {
        return false;
      }
      switch (this.mytype) {
        case 1:
          this.addformbase.startOrganizeName = orgList.organizationOaName;
          this.addformbase.startOrganize = orgList.organizationOa;
          break;
        case 2:
          this.addformbase.forbidOrganizeName = orgList.organizationOaName;
          this.addformbase.forbidOrganize = orgList.organizationOa;
          break;
        case 3:
          this.addformbase.queryOrganizeName = orgList.organizationOaName;
          this.addformbase.queryOrganize = orgList.organizationOa;
          break;
        case 4:
          this.addformbase.manageOrganizeName = orgList.organizationOaName;
          this.addformbase.manageOrganize = orgList.organizationOa;
          break;
      }
    },
    getOrganizationData (val) {
      this.addformbase.organizationId = val.organizationOa;
      this.addformbase.manageOrganizeName = val.title;
      this.addformbase.manageOrganize = val.id;
    },
    updateStat_post (stat, postList) {
      this.visiable_post = stat;
      if (!postList) {
        return false;
      }
      switch (this.mytype) {
        case 1:
          this.addformbase.startPostName = postList.names;
          this.addformbase.startPost = postList.empIds;
          break;
        case 2:
          this.addformbase.forbidPostName = postList.names;
          this.addformbase.forbidPost = postList.empIds;
          break;
        case 3:
          this.addformbase.queryPostName = postList.names;
          this.addformbase.queryPost = postList.empIds;
          break;
        case 4:
          this.addformbase.managePostName = postList.names;
          this.addformbase.managePost = postList.empIds;
          break;
      }
    },
    showemp () {
      switch (this.mytype) {
        case 1:
          this.tranferValue = this.addformbase.startEmp;
          break;
        case 2:
          this.tranferValue = this.addformbase.forbidEmp;
          break;
        case 3:
          this.tranferValue = this.addformbase.queryEmp;
          break;
        case 4:
          this.tranferValue = this.addformbase.manageEmp;
          break;
      }
      // if (typeof (this.tranferValue) === 'string' && this.tranferValue !== null) {
      //   this.tranferValue.split(',');
      // }
      this.visiable_emp = true;
    },
    // 选择部门或者成员
    selectDepartmentOrEmployee (department) {
      console.log('department==============>', department);
    },
    cancel () {
      this.$emit('updateStat', false);
    },
    reset () {
      this.addformbase = {
        categoryName: '',
        remark: ''
      };
      this.$refs['form'].resetFields();
    },
    handsave () {
      this.$refs['form'].validate(valid => {
        if (valid) {
          publicEventsList.updatestorage(this.addformbase).then(res => {
            if (res.ret === 200) {
              this.$emit('updateStat', false);
              this.$Message.success(res.msg);
            }
          });
        } else {
          this.$Message.error('Fail!');
        }
      });
    },
    // 弹窗组件结束=========================》
    getList (val) {
      numSetting.getstorage().then(res => {
        this.isrequireNum = !res.data.numMethod;
      });
      const data3 = {};
      storage.getstorage(data3).then(res => {
        console.log(res);
        this.locationList = res.data;
      });
      const data2 = {};
      unitOfMeasure.getstorage(data2).then(res => {
        console.log(res);
        this.unitList = res.data;
      });
      const data = {
        parentId: val || 0
      };
      classification.getstorage(data).then((res) => {
        console.log('res=======', res.data);
        const data = res.data.map((item) => {
          return {
            loading: false,
            label: item.classifyName,
            value: item.id,
            parentId: 0,
            children: []
          };
        });
        this.data_class = data;
      });
    }
  }
};
</script>
<style lang="less" scoped>
.add /deep/ .ivu-modal-header {
  background-color: #2d8cf0;
}
.add /deep/ .ivu-modal-content {
  background-color: #eee;
}
.add /deep/ .ivu-modal-footer {
  border: none;
}
</style>
