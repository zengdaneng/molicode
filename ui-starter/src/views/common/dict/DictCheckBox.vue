<template>
  <span>
    <CheckboxGroup v-model="model" @on-change="updateValue" :clearable="clearable">
      <Checkbox v-for="item in dictItems" :label="item.itemKey" :key="item.itemKey" :disabled="disabled">{{ item.itemName }}</Checkbox>
    </CheckboxGroup>
  </span>
</template>
<script>
    import {transferString, toStringArray, transferNumber} from '@/libs/renderUtil.js'
    import constants from '@/constants/constants'

    var _ = require('underscore')

    export default {
        name: 'DictCheckBox',
        props: {
            value: {
                type: [String, Number, Array, Boolean],
                default: []
            },
            number: {
                type: Boolean,
                default: false
            },
            kind: {
                type: String,
                default: ''
            },
            pid: {
                type: String,
                default: ''
            },
            clearable: {
                type: Boolean,
                default: false
            },
            disabled: {
                type: Boolean,
                default: false
            },
            multiple: {
                type: Boolean,
                default: false
            },
            transfer: {
                type: Boolean,
                default: false
            },
            localDictItems: {
                type: Array,
                default: null
            }
        },
        data() {
            return {
                model: toStringArray(this.value),
                dictItems: []
            }
        },
        mounted () {
            let _this = this;
            if (this.localDictItems !== null && this.localDictItems.length > 0) {
                _this.loadDict(this.localDictItems)
                return
            }
            if (this.kind === null || this.kind === '') {
                return
            }
            let key = this.kind
            if (this.pid !== '') {
                key = this.kind + '_' + this.pid
            }
            if (this.$store !== null && this.$store !== undefined) {
                let promise = this.$store.dispatch(constants.types.LOAD_DICT_INFO, {kind: key});
                promise.then((data) => {
                    _this.loadDict(data)
                });
            }
        },
        watch: {
            value() {
                let newVal = transferString(this.value)
                if (_.isArray(newVal)) {
                    if (_.isArray(this.model)) {
                        if (_.difference(newVal, this.model).length !== 0) {
                            this.model = newVal
                        }
                    } else {
                        this.model = newVal
                    }
                } else {
                    if (newVal !== this.model) {
                        this.model = newVal
                    }
                }
            }
        },
        methods: {
            updateValue () {
                let v = this.model
                if (this.number) {
                    v = transferNumber(v)
                }
                this.$emit('input', v)
            },
            loadDict (data) {
                this.dictItems = data
            }
        }
    }
</script>
