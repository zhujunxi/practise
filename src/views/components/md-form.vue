<template>
    <form class="md-form">
        <slot></slot>
    </form>
</template>

<script>
import AsyncValidator from 'async-validator'
export default {
    name: 'md-form',
    componentName: 'MdForm', // 通过 $options.componentName 来找 form 组件\
    props: {
        model: Object,
        rules: Object
    },
    data() {
        return {
            fields: [], // field: {prop, el}，保存FormItem的信息
            formError: {}
        }
    },
    created() {
        this.$on('form.addField', field => {
            if (field) {
                this.fields = [...this.fields, field]
            }
        })
        this.$on('form.removeField', field => {
            if (field) {
                this.fields = this.fields.filter(
                    ({ prop }) => prop !== field.prop
                )
            }
        })
    },
    computed: {
        formRules() {
            const descriptor = {}
            this.fields.forEach(({ prop }) => {
                if (!Array.isArray(this.rules[prop])) {
                    console.warn(
                        `prop 为 ${prop} 的 FormItem 校验规则不存在或者其值不是数组`
                    )
                    descriptor[prop] = [{ required: true }]
                    return
                }
                descriptor[prop] = this.rules[prop]
            })
            return descriptor
        },
        formValues() {
            return this.fields.reduce((data, { prop }) => {
                data[prop] = this.model[prop]
                return data
            }, {})
        }
    },
    methods: {
        validate(callback) {
            const validator = new AsyncValidator(this.formRules)
            validator.validate(this.formValues, errors => {
                let formError = {}
                if (errors && errors.length) {
                    errors.forEach(({ message, field }) => {
                        formError[field] = message
                    })
                } else {
                    formError = {}
                }
                this.formError = formError
                // 让错误信息的顺序与表单组件的顺序相同
                const errInfo = []
                this.fields.forEach(({ prop }) => {
                    if (formError[prop]) {
                        errInfo.push(formError[prop])
                    }
                })
                callback(errInfo)
            })
        }
    }
}
</script>

<style lang="less" scoped></style>
