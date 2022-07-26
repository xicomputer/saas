<template>
    <script module="utils" lang="wxs" src="../wxs/utils.wxs"></script>
    <script module="computed" lang="wxs" src="./index.wxs"></script>

    <view class="van-checkbox custom-class">
        <view class="van-checkbox__icon-wrap" @tap="toggle">
            <slot v-if="useIconSlot" name="icon" />
            <van-icon
                wx:else
                name="success"
                size="0.8em"
                :class="utils.bem('checkbox__icon', [shape, { disabled: disabled || parentDisabled, checked: value }])"
                :style="computed.iconStyle(checkedColor, value, disabled, parentDisabled, iconSize)"
                custom-class="icon-class"
                custom-style="line-height: 1.25em;"
            />
        </view>
        <view :class="'label-class ' + utils.bem('checkbox__label', [labelPosition, { disabled: disabled || parentDisabled }])" @tap="onClickLabel">
            <slot />
        </view>
    </view>
</template>

<script>
'use strict';
import vanIcon from '../icon/index';

Object.defineProperty(exports, '__esModule', {
    value: true
});

var component_1 = require('../common/component');

function emit(target, value) {
    target.$emit('input', value);
    target.$emit('change', value);
}

component_1.VantComponent({
    field: true,
    relation: {
        name: 'checkbox-group',
        type: 'ancestor',
        current: 'checkbox'
    },
    classes: ['icon-class', 'label-class'],
    props: {
        value: Boolean,
        disabled: Boolean,
        useIconSlot: Boolean,
        checkedColor: String,
        labelPosition: String,
        labelDisabled: Boolean,
        shape: {
            type: String,
            value: 'round'
        },
        iconSize: {
            type: null,
            value: 20
        }
    },
    data: {
        parentDisabled: false
    },
    methods: {
        emitChange: function (value) {
            if (this.parent) {
                this.setParentValue(this.parent, value);
            } else {
                emit(this, value);
            }
        },
        toggle: function () {
            var _a = this.data;
            var parentDisabled = _a.parentDisabled;
            var disabled = _a.disabled;
            var value = _a.value;
            if (!disabled && !parentDisabled) {
                this.emitChange(!value);
            }
        },
        onClickLabel: function () {
            var _a = this.data;
            var labelDisabled = _a.labelDisabled;
            var parentDisabled = _a.parentDisabled;
            var disabled = _a.disabled;
            var value = _a.value;
            if (!disabled && !labelDisabled && !parentDisabled) {
                this.emitChange(!value);
            }
        },
        setParentValue: function (parent, value) {
            var parentValue = parent.data.value.slice();
            var name = this.data.name;
            var max = parent.data.max;

            if (value) {
                if (max && parentValue.length >= max) {
                    return;
                }

                if (parentValue.indexOf(name) === -1) {
                    parentValue.push(name);
                    emit(parent, parentValue);
                }
            } else {
                var index = parentValue.indexOf(name);

                if (index !== -1) {
                    parentValue.splice(index, 1);
                    emit(parent, parentValue);
                }
            }
        }
    }
});
</script>
<style>
@import '../common/index.css';
.van-checkbox {
    display: -webkit-flex;
    display: flex;
    -webkit-align-items: center;
    align-items: center;
    overflow: hidden;
    -webkit-user-select: none;
    user-select: none;
}
.van-checkbox__icon-wrap,
.van-checkbox__label {
    line-height: 20px;
    line-height: var(--checkbox-size, 20px);
}
.van-checkbox__icon-wrap {
    -webkit-flex: none;
    flex: none;
}
.van-checkbox__icon {
    display: -webkit-flex;
    display: flex;
    -webkit-align-items: center;
    align-items: center;
    -webkit-justify-content: center;
    justify-content: center;
    box-sizing: border-box;
    width: 1em;
    height: 1em;
    color: transparent;
    text-align: center;
    transition-property: color, border-color, background-color;
    font-size: 20px;
    font-size: var(--checkbox-size, 20px);
    border: 1px solid #c8c9cc;
    border: 1px solid var(--checkbox-border-color, #c8c9cc);
    transition-duration: 0.2s;
    transition-duration: var(--checkbox-transition-duration, 0.2s);
}
.van-checkbox__icon--round {
    border-radius: 100%;
}
.van-checkbox__icon--checked {
    color: #fff;
    color: var(--white, #fff);
    background-color: #1989fa;
    background-color: var(--checkbox-checked-icon-color, #1989fa);
    border-color: #1989fa;
    border-color: var(--checkbox-checked-icon-color, #1989fa);
}
.van-checkbox__icon--disabled {
    background-color: #ebedf0;
    background-color: var(--checkbox-disabled-background-color, #ebedf0);
    border-color: #c8c9cc;
    border-color: var(--checkbox-disabled-icon-color, #c8c9cc);
}
.van-checkbox__icon--disabled.van-checkbox__icon--checked {
    color: #c8c9cc;
    color: var(--checkbox-disabled-icon-color, #c8c9cc);
}
.van-checkbox__label {
    word-wrap: break-word;
    margin-left: 10px;
    margin-left: var(--checkbox-label-margin, 10px);
    color: #323233;
    color: var(--checkbox-label-color, #323233);
}
.van-checkbox__label--left {
    float: left;
    margin: 0 10px 0 0;
    margin: 0 var(--checkbox-label-margin, 10px) 0 0;
}
.van-checkbox__label--disabled {
    color: #c8c9cc;
    color: var(--checkbox-disabled-label-color, #c8c9cc);
}
.van-checkbox__label:empty {
    margin: 0;
}
</style>
