<template>
    <view>
        <van-picker
            class="van-area__picker"
            active-class="active-class"
            toolbar-class="toolbar-class"
            column-class="column-class"
            show-toolbar
            value-key="name"
            :title="title"
            :loading="loading"
            :columns="displayColumns"
            :item-height="itemHeight"
            :visible-item-count="visibleItemCount"
            :cancel-button-text="cancelButtonText"
            :confirm-button-text="confirmButtonText"
            @change="onChange"
            @confirm="onConfirm"
            @cancel="onCancel"
        />
    </view>
</template>

<script>
'use strict';
import vanPicker from '../picker/index';

var __assign =
    (this && this.__assign) ||
    function () {
        __assign =
            Object.assign ||
            function (t) {
                for (var s, i = 1, n = arguments.length; i < n; i++) {
                    s = arguments[i];

                    for (var p in s) {
                        if (Object.prototype.hasOwnProperty.call(s, p)) {
                            t[p] = s[p];
                        }
                    }
                }

                return t;
            };

        return __assign.apply(this, arguments);
    };

Object.defineProperty(exports, '__esModule', {
    value: true
});

var component_1 = require('../common/component');

var shared_1 = require('../picker/shared');

var utils_1 = require('../common/utils');

var COLUMNSPLACEHOLDERCODE = '000000';
component_1.VantComponent({
    classes: ['active-class', 'toolbar-class', 'column-class'],
    props: __assign(__assign({}, shared_1.pickerProps), {
        value: {
            type: String,
            observer: function (value) {
                this.code = value;
                this.setValues();
            }
        },
        areaList: {
            type: Object,
            value: {},
            observer: 'setValues'
        },
        columnsNum: {
            type: null,
            value: 3,
            observer: function (value) {
                this.setData({
                    displayColumns: this.data.columns.slice(0, +value)
                });
            }
        },
        columnsPlaceholder: {
            type: Array,
            observer: function (val) {
                this.setData({
                    typeToColumnsPlaceholder: {
                        province: val[0] || '',
                        city: val[1] || '',
                        county: val[2] || ''
                    }
                });
            }
        }
    }),
    data: {
        columns: [
            {
                values: []
            },
            {
                values: []
            },
            {
                values: []
            }
        ],
        displayColumns: [
            {
                values: []
            },
            {
                values: []
            },
            {
                values: []
            }
        ],
        typeToColumnsPlaceholder: {}
    },
    mounted: function () {
        var that = this;
        utils_1.requestAnimationFrame(function () {
            that.setValues();
        });
    },
    methods: {
        getPicker: function () {
            if (this.picker == null) {
                this.picker = this.$mp.page.selectComponent('.van-area__picker');
            }

            return this.picker;
        },
        onCancel: function (event) {
            this.emit('cancel', event.detail);
        },
        onConfirm: function (event) {
            var index = event.detail.index;
            var value = event.detail.value;
            value = this.parseOutputValues(value);
            this.emit('confirm', {
                value: value,
                index: index
            });
        },
        emit: function (type, detail) {
            detail.values = detail.value;
            delete detail.value;
            this.$emit(type, detail);
        },
        // parse output columns data
        parseOutputValues: function (values) {
            var columnsPlaceholder = this.data.columnsPlaceholder;
            return values.map(function (value, index) {
                // save undefined value
                if (!value) {
                    return value;
                }

                value = JSON.parse(JSON.stringify(value));

                if (!value.code || value.name === columnsPlaceholder[index]) {
                    value.code = '';
                    value.name = '';
                }

                return value;
            });
        },
        onChange: function (event) {
            var that = this;
            var _a = event.detail;
            var index = _a.index;
            var picker = _a.picker;
            var value = _a.value;
            this.code = value[index].code;
            this.setValues().then(function () {
                that.$emit('change', {
                    picker: picker,
                    values: that.parseOutputValues(picker.getValues()),
                    index: index
                });
            });
        },
        getConfig: function (type) {
            var areaList = this.data.areaList;
            return (areaList && areaList[type + '_list']) || {};
        },
        getList: function (type, code) {
            var typeToColumnsPlaceholder = this.data.typeToColumnsPlaceholder;
            var result = [];

            if (type !== 'province' && !code) {
                return result;
            }

            var list = this.getConfig(type);
            result = Object.keys(list).map(function (code) {
                return {
                    code: code,
                    name: list[code]
                };
            });

            if (code) {
                // oversea code
                if (code[0] === '9' && type === 'city') {
                    code = '9';
                }

                result = result.filter(function (item) {
                    return item.code.indexOf(code) === 0;
                });
            }

            if (typeToColumnsPlaceholder[type] && result.length) {
                // set columns placeholder
                var codeFill = type === 'province' ? '' : type === 'city' ? COLUMNSPLACEHOLDERCODE.slice(2, 4) : COLUMNSPLACEHOLDERCODE.slice(4, 6);
                result.unshift({
                    code: '' + code + codeFill,
                    name: typeToColumnsPlaceholder[type]
                });
            }

            return result;
        },
        getIndex: function (type, code) {
            var compareNum = type === 'province' ? 2 : type === 'city' ? 4 : 6;
            var list = this.getList(type, code.slice(0, compareNum - 2)); // oversea code

            if (code[0] === '9' && type === 'province') {
                compareNum = 1;
            }

            code = code.slice(0, compareNum);

            for (var i = 0; i < list.length; i++) {
                if (list[i].code.slice(0, compareNum) === code) {
                    return i;
                }
            }

            return 0;
        },
        setValues: function () {
            var county = this.getConfig('county');
            var code = this.code;

            if (!code) {
                if (this.data.columnsPlaceholder.length) {
                    code = COLUMNSPLACEHOLDERCODE;
                } else if (Object.keys(county)[0]) {
                    code = Object.keys(county)[0];
                } else {
                    code = '';
                }
            }

            var province = this.getList('province');
            var city = this.getList('city', code.slice(0, 2));
            var picker = this.getPicker();

            if (!picker) {
                return;
            }

            var stack = [];
            var indexes = [];
            var columnsNum = this.data.columnsNum;

            if (columnsNum >= 1) {
                stack.push(picker.setColumnValues(0, province, false));
                indexes.push(this.getIndex('province', code));
            }

            if (columnsNum >= 2) {
                stack.push(picker.setColumnValues(1, city, false));
                indexes.push(this.getIndex('city', code));

                if (city.length && code.slice(2, 4) === '00') {
                    code = city[0].code;
                }
            }

            if (columnsNum === 3) {
                stack.push(picker.setColumnValues(2, this.getList('county', code.slice(0, 4)), false));
                indexes.push(this.getIndex('county', code));
            }

            return Promise.all(stack)
                .catch(function () {})
                .then(function () {
                    return picker.setIndexes(indexes);
                })
                .catch(function () {});
        },
        getValues: function () {
            var picker = this.getPicker();
            return picker
                ? picker.getValues().filter(function (value) {
                      return !!value;
                  })
                : [];
        },
        getDetail: function () {
            var values = this.getValues();
            var area = {
                code: '',
                country: '',
                province: '',
                city: '',
                county: ''
            };

            if (!values.length) {
                return area;
            }

            var names = values.map(function (item) {
                return item.name;
            });
            area.code = values[values.length - 1].code;

            if (area.code[0] === '9') {
                area.country = names[1] || '';
                area.province = names[2] || '';
            } else {
                area.province = names[0] || '';
                area.city = names[1] || '';
                area.county = names[2] || '';
            }

            return area;
        },
        reset: function (code) {
            this.code = code || '';
            return this.setValues();
        }
    }
});
</script>
<style>
@import '../common/index.css';
</style>
