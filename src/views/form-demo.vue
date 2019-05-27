<template>
    <md-form :model="formData" :rules="rules" ref="form">
        <md-form-item label="手机号" prop="tel">
            <input type="tel" maxlength="11" v-model.trim="formData.tel" />
        </md-form-item>
        <md-form-item label="按钮">
            <a @click="handleSubmit">触发</a>
        </md-form-item>
    </md-form>
</template>

<script>
import { MdForm, MdFormItem } from './components/index'
export default {
    components: {
        MdForm,
        MdFormItem
    },
    data() {
        return {
            formData: {
                tel: ''
            },
            rules: {
                tel: [
                    { required: true, message: '您的手机号码未输入' },
                    {
                        pattern: /^1[34578]\d{9}$/,
                        message: '您的手机号码输入错误'
                    }
                ]
            }
        }
    },
    methods: {
        handleSubmit() {
            this.$refs.form.validate(errs => {
                console.log(errs)
            })
        }
    }
}
</script>
