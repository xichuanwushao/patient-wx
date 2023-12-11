<template>
    <view>
        <view class="top-container">
            <view class="step-container">
                <view class="icon-1"><text class="desc">个人基本信息</text></view>
                <view class="line"></view>
                <view class="icon-2"><text class="desc">疾病史与医保</text></view>
                <view class="line"></view>
                <view class="icon-3"><text class="desc">创建就诊卡</text></view>
            </view>
        </view>
        <view class="info-1-container" v-if="showInfoPanel == 1">
            <text class="title">填写个人基本信息</text>
            <text class="desc">填写如下信息，以帮助医生了解您的身体基本情况，方便问诊开药</text>
            <u-form
                labelPosition="top"
                :model="dataForm"
                :rules="rules"
                ref="form1"
                labelWidth="110"
                :labelStyle="labelStyle"
            >
                <u-form-item
                    label="本人姓名"
                    prop="name"
                    borderBottom
                    ref="name"
                    leftIcon="account"
                    :leftIconStyle="iconStyle"
                    required
                >
                    <u-input v-model="dataForm.name" border="none" placeholder="输入姓名" />
                </u-form-item>
                <u-form-item
                    label="性别"
                    prop="sex"
                    borderBottom
                    ref="sex"
                    @click="showSex = true"
                    leftIcon="man"
                    :leftIconStyle="iconStyle"
                    required
                >
                    <u-input
                        v-model="dataForm.sex"
                        disabled
                        disabledColor="#ffffff"
                        placeholder="请选择性别"
                        border="none"
                    ></u-input>
                    <u-icon slot="right" name="arrow-right"></u-icon>
                </u-form-item>
                <u-form-item
                    label="身份证号"
                    prop="pid"
                    borderBottom
                    ref="sex"
                    leftIcon="file-text"
                    :leftIconStyle="iconStyle"
                    required
                >
                    <u-input v-model="dataForm.pid" border="none" placeholder="输入身份证号码" />
                    <view class="ocr">
                        <ocr-navigator @onSuccess="scanIdcardFront" certificateType="idCard" :opposite="false">
                            <u-icon slot="right" size="26" :name="img['icon-1']"></u-icon>
                        </ocr-navigator>
                    </view>
                </u-form-item>
                <u-form-item
                    label="手机号码"
                    prop="tel"
                    borderBottom
                    ref="tel"
                    leftIcon="phone"
                    :leftIconStyle="iconStyle"
                    required
                >
                    <u-input v-model="dataForm.tel" border="none" placeholder="输入手机号码" />
                </u-form-item>
                <u-form-item
                    label="出生日期"
                    prop="birthday"
                    borderBottom
                    ref="birthday"
                    @click="showDatetime = true"
                    leftIcon="calendar"
                    :leftIconStyle="iconStyle"
                    required
                >
                    <u-input
                        v-model="dataForm.birthday"
                        disabled
                        disabledColor="#ffffff"
                        placeholder="请选择您的生日"
                        border="none"
                    ></u-input>
                    <u-icon slot="right" name="arrow-right"></u-icon>
                </u-form-item>
            </u-form>
            <view class="operate">
                <u-button type="primary" text="下一步" size="large" color="#2074fd" @click="nextHandle"></u-button>
            </view>
        </view>
        <view class="info-2-container" v-if="showInfoPanel == 2">
            <text class="title">填写疾病史与医保信息</text>
            <text class="desc">填写如下信息，以帮助医生了解您的身体基本情况，方便问诊开药</text>

            <view class="label">
                <u-icon name="info-circle-fill" size="20" color="#0764FD"></u-icon>
                <text>您是否患有或者患过以下疾病</text>
            </view>
            <view class="illness-tabs">
                <view
                    class="tab"
                    v-for="one in illnessList"
                    :class="dataForm.medicalHistory.includes(one) ? 'tab active' : 'tab'"
                    @tap="illnessHandle(one)"
                >
                    {{ one }}
                </view>
            </view>
            <view class="label">
                <u-icon name="info-circle-fill" size="20" color="#0764FD"></u-icon>
                <text>请选择您自己的医保类型</text>
            </view>
            <view class="insurance-tabs">
                <view
                    v-for="one in insuranceTypeList"
                    :class="dataForm.insuranceType == one ? 'tab active' : 'tab'"
                    @tap="insuranceTypeHandle(one)"
                >
                    {{ one }}
                </view>
            </view>
            <view class="operate">
                <u-button type="primary" text="提交信息" size="large" color="#2074fd" @click="submitHandle"></u-button>
            </view>
        </view>
        <u-action-sheet
            :show="showSex"
            :actions="sexList"
            title="请选择性别"
            @close="
                showSex = false;
                this.$refs.form1.validateField('sex');
            "
            @select="selectSex"
        ></u-action-sheet>
        <u-datetime-picker
            :show="showDatetime"
            mode="date"
            minDate="-2209017600000"
            @confirm="confirmBirthday"
            @cancel="
                showDatetime = false;
                this.$refs.form1.validateField('birthday');
            "
        ></u-datetime-picker>
        <image :src="bannerUrl" mode="widthFix" class="banner"></image>
    </view>
</template>

<script>
const dayjs = require('dayjs');

export default {
    data() {
        return {
            img: {
                'icon-1': `${this.patientUrl}/page/user/fill_user_info/icon-1.png`
            },
            flag: 'insert',
            showInfoPanel: 1,
            labelStyle: {
                color: '#2074fd',
                'font-weight': 'bold'
            },
            iconStyle: {
                color: '#0764FD',
                'font-size': '34rpx',
                'margin-top': '3rpx'
            },
            showSex: false,
            showDatetime: false,
            sexList: [
                {
                    name: '男'
                },
                {
                    name: '女'
                }
            ],
            illnessList: [
                '高血压',
                '糖尿病',
                '心脏病',
                '脑梗',
                '脑出血',
                '脑中风',
                '白血病',
                '癫痫',
                '肾病',
                '其他',
                '无'
            ],
            insuranceTypeList: [
                '社会基本医疗保险',
                '商业医疗保险',
                '新型农村合作医疗',
                '大病统筹',
                '公费医疗',
                '城镇居民医疗保险',
                '其他',
                '无'
            ],
            validate: true,
            rules: {
                name: [
                    {
                        required: true,
                        message: '请输入姓名',
                        trigger: ['change', 'blur']
                    },
                    {
                        type: 'string',
                        pattern: '^[\\u4e00-\\u9fa5]{2,15}$',
                        required: true,
                        message: '姓名不正确',
                        trigger: ['blur', 'change']
                    }
                ],
                sex: {
                    type: 'string',
                    max: 1,
                    required: true,
                    message: '请选择男或女',
                    trigger: ['blur', 'change']
                },
                pid: [
                    {
                        required: true,
                        message: '请输入身份证号码',
                        trigger: ['change', 'blur']
                    },
                    {
                        type: 'string',
                        pattern:
                            '^[1-9]\\d{5}(18|19|([23]\\d))\\d{2}((0[1-9])|(10|11|12))(([0-2][1-9])|10|20|30|31)\\d{3}[0-9Xx]$',
                        required: true,
                        message: '身份证号码不正确',
                        trigger: ['blur', 'change']
                    }
                ],
                tel: [
                    {
                        required: true,
                        message: '请输入手机号码',
                        trigger: ['change', 'blur']
                    },
                    {
                        type: 'string',
                        pattern: '^1[0-9]{10}$',
                        required: true,
                        message: '手机号码不正确',
                        trigger: ['blur', 'change']
                    }
                ],
                birthday: {
                    type: 'string',
                    required: true,
                    message: '请选择出生日期',
                    trigger: ['blur', 'change']
                }
            },
            dataForm: {
                id: null,
                name: null,
                sex: null,
                pid: null,
                tel: null,
                birthday: null,
                medicalHistory: ['无'],
                insuranceType: '无'
            },
            bannerUrl: `${this.patientUrl}/banner/banner-4.jpg`
        };
    },
    methods: {
        selectSex: function(e) {
            this.dataForm.sex = e.name;
        },
        confirmBirthday: function(e) {
            this.showDatetime = false;
            let date = new Date();
            date.setTime(e.value);
            this.dataForm.birthday = dayjs(date).format('YYYY-MM-DD');
            this.$refs.form1.validateField('birthday');
        },
        nextHandle: function() {
            let that = this;
            that.validate = true;
            that.$refs.form1.validateField('name', function(errors) {
                that.validateFieldHandle(that, errors);
            });
            that.$refs.form1.validateField('pid', function(errors) {
                that.validateFieldHandle(that, errors);
            });
            that.$refs.form1.validateField('tel', function(errors) {
                that.validateFieldHandle(that, errors);
            });
            that.$refs.form1.validateField('sex', function(errors) {
                that.validateFieldHandle(that, errors);
            });
            that.$refs.form1.validateField('birthday', function(errors) {
                that.validateFieldHandle(that, errors);
            });
            //以上验证是异步的，所以需要定时器预估时间
            setTimeout(function() {
                if (!that.validate) {
                    uni.showToast({
                        icon: 'error',
                        title: '数据不正确'
                    });
                    return;
                }
                that.showInfoPanel = 2;
            }, 500);
        },
        validateFieldHandle: function(ref, errors) {
            if (errors.length > 0) {
                ref.validate = false;
            }
        },
    },
    onLoad: function() {

    }
};
</script>

<style lang="less">
@import url(fill_user_info.less);
</style>
