<template>
        <Modal v-model="mymoadlStat" class="add" width="720" :closable="false" :mask-closable="false" :transfer="false" :styles="{top: '10px'}">
        <div slot="header" style="text-align:left;color:#fff;">
            <span>{{ $t('usermanage_view.adduser') }}</span>
        </div>
        <div>
            <Card dis-hover>
                <div style="display:flex; align-items: center; border-bottom: 1px solid #e1e1e1;padding-bottom: 20px;">
                <div style="width: 4px; height: 20px;background: #2d8cf0;margin-right: 15px"></div>
                <div>{{$t('BaseData')}}</div>
                </div>
                <Divider />
                <Form ref="form" :model="addformbase" label-position="right" :label-width="100" :rules="ruleValidate">
                    <FormItem :label="$t('welfare_view.title')" prop="title">
                        <Input v-model="addformbase.title"></Input>
                    </FormItem>
                    <FormItem :label="$t('salaryjudge_view.payDate')" prop="payDate">
                        <DatePicker type="month" v-model="addformbase.month" placeholder="Select date" style="width: 100%" @on-change="showmytime"></DatePicker>
                    </FormItem>
                    <FormItem :label="$t('welfare_view.suitType')" prop="middlename">
                        <RadioGroup v-model="addformbase.suitType">
                          <Radio label="1"><span>{{ $t('welfare_view.org') }}</span></Radio>
                          <Radio label="2"><span>{{ $t('welfare_view.personnel') }}</span></Radio>
                        </RadioGroup>
                    </FormItem>
                    <FormItem v-if="addformbase.suitType === '2'" :label="$t('welfare_view.personnel')" prop="personnel">
                        <Input v-model="addformbase.empList" readonly @click.native="showemp" style="width: 100%"></Input>
                    </FormItem>
                    <FormItem v-else :label="$t('welfare_view.org')" prop="organizationOa">
                        <Input v-model="addformbase.organizationOaName" readonly @click.native="isShowTree = !isShowTree" style="width: 100%">
                        <Icon slot="suffix" type="ios-arrow-down" v-if="!isShowTree" />
                              <Icon slot="suffix" type="ios-arrow-up" v-else />
                            </Input>
                            <div class="department-wrap" v-if="isShowTree">
                              <DepartmentEmployeeTree
                                :isDepartment="true"
                                @on-select="selectDepartmentOrEmployee"
                                @addmyorg = "addorg"
                                ref="departmentEmployeeTree"
                              ></DepartmentEmployeeTree>
                            </div>
                    </FormItem>
                </Form>
            </Card>

        </div>
        <div slot="footer">
            <ButtonGroup>
                <Button type="primary" size="large" :loading="modal_loading" @click="handsave">{{ $t('Save') }}</Button>
                <Button type="error" size="large"  @click="cancel">{{ $t('Close') }}</Button>
            </ButtonGroup>
        </div>
        <!-- 新建员工弹窗 -->
        <addemp :modalstat = "visiable_emp" :memberId="addformbase.empListIds" @updateStat = "updateStat_emp"></addemp>
        <!-- 新建结束============= -->
    </Modal>
</template>
<script>
import $ from 'jquery';
import {
  salaryjudgeApi
} from '@/api/salaryjudge';
import DepartmentEmployeeTree from '../department-employee-tree/department-employee-tree';
import addemp from '../addemp/modal';
export default {
  name: 'editmodal',
  components: {
    DepartmentEmployeeTree,
    addemp
  },
  props: {
    modalstat: {
      type: Boolean,
      default: false
    },
    editinfo: null
  },
  created () {
    
  },
  mounted () {
  },
  data () {
    const validatePass = (rule, value, callback) => {
      if (value === '') {
        callback(new Error('Please enter'));
      } else {
        callback();
      }
    };
    const validatePass2 = (rule, value, callback) => {
      if (this.addformbase.month === '' && this.addformbase.month === null && this.addformbase.month === undefined) {
        callback(new Error('Please enter your month'));
      } else {
        callback();
      }
    };
    const validatePass3 = (rule, value, callback) => {
      if (this.addformbase.organizationOaName === '' && this.addformbase.organizationOaName === null && this.addformbase.organizationOaName === undefined) {
        callback(new Error('Please enter'));
      } else {
        callback();
      }
    };
    const validatePass4 = (rule, value, callback) => {
      if (this.addformbase.empList === '' && this.addformbase.empList === null && this.addformbase.empList === undefined) {
        callback(new Error('Please enter'));
      } else {
        callback();
      }
    };
    return {
      isShowTree: false,
      modal_loading: false,
      mymoadlStat: this.modalstat,
      addformbase: {
        suitType: '1',
        empListIds: null
      },
      ruleValidate: {
        title: [
          { required: true, message: 'The title cannot be empty', trigger: 'blur' }
        ],
        payDate: [
          { required: true, validator: validatePass2, trigger: 'blur' }
        ],
        organizationOa: [
          { required: true, validator: validatePass3, trigger: 'blur' }
        ],
        personnel: [
          { required: true, validator: validatePass4, trigger: 'blur' }
        ]
      },
      // 新建员工弹窗
      visiable_emp: false
    };
  },
  watch: {
    modalstat () {
      this.mymoadlStat = this.modalstat;
      this.addformbase = this.editinfo;
      this.addformbase.suitType = String(this.addformbase.suitType);
      this.addformbase.month = this.addformbase.salaryDate;
      this.addformbase.salaryDate = this.addformbase.salaryDate;
      console.log(this.addformbase);
      if (this.addformbase.suitType === '2') {
        this.addformbase.empListIds = this.addformbase.judgeTargetVos.map(item => { return item.targetId; }).join(',');
        this.addformbase.empList = this.addformbase.judgeTargetVos.map(item => { return item.targetName; }).join(',');
      } else {
        this.addformbase.organizationOaName = this.addformbase.judgeTargetVos.map(item => { return item.targetName; }).join(',');
        this.addformbase.organizationOa = this.addformbase.judgeTargetVos.map(item => { return item.targetId; }).join(',');
      }
      // this.addformbase.welfareTargetVos.map(item => { return item.targetId; }).join(',');
      // this.addformbase.welfareTargetVos.map(item => { return item.targetName; }).join(',');
      console.log('this.addformbase==========>', this.addformbase);
    }
  },
  methods: {
    showmytime (e) {
      console.log('e==============>', e);
      this.addformbase.salaryDate = e;
    },
    addorg (selection) {
      console.log('selection==========>', selection);
      console.log(selection.map(item => { return item.title; }).join(','));
      this.addformbase.organizationOaName = selection.map(item => { return item.title; }).join(',');
      this.addformbase.organizationOa = selection.map(item => { return item.id; }).join(',');
      console.log(this.addformbase.organizationOaName);
    },
    updateStat_emp (stat, empList) {
      this.visiable_emp = stat;
      this.addformbase.empList = empList.names;
      console.log('names========>', this.addformbase.empList);
      this.addformbase.empListIds = empList.empIds;
      console.log('empIds===============>', this.addformbase.empListIds);
      console.log('empList==================================>', empList);
    },
    showemp () {
      console.log('新建员工');
      console.log(typeof (this.addformbase.empListIds));
      if (typeof (this.addformbase.empListIds) === 'string' && this.addformbase.empListIds !== null) {
        this.addformbase.empListIds.split(',');
      }
      this.visiable_emp = true;
    },
    // 选择部门或者成员
    selectDepartmentOrEmployee (department) {
      console.log('department==============>', department);
      // this.addformbase.organizationOaName = department.name;
      // this.addformbase.organizationOa = department.id;
      // this.$set(this.formdata, 'organizeParent', department.id);
      // this.$set(this.formdata, 'organizeParent', department.name);
      // this.isShowTree = false;
      // $('.department-wrap').hide();
    },
    cancel () {
      this.$emit('updateStat', false);
    },
    reset () {
      this.addformbase = {
        organizationOaName: '',
        organizationOa: '',
        empList: '',
        empListIds: '',
        title: '',
        contnet: '',
        suitType: '1'
      };
      this.$refs['form'].resetFields();
    },
    handsave () {
      console.log(this.addformbase);
      this.addformbase.operatId = this.$store.state.user.userLoginInfo.userId;
      this.addformbase.judgeId = this.addformbase.id;
      if (this.addformbase.suitType === '2') {
        this.addformbase.suitTarget = this.addformbase.empListIds;
      } else {
        this.addformbase.suitTarget = this.addformbase.organizationOa;
      }
      this.$refs['form'].validate((valid) => {
        if (valid) {
          salaryjudgeApi.updatejudge(this.addformbase).then(res => {
            if (res.ret === 200) {
              this.$emit('updateStat', false);
              this.$Message.success(res.msg);
            }
          });
        } else {
          this.$Message.error('Fail!');
        }
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
