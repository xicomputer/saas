<template>
    <view>
        <script module="utils" lang="wxs" src="../wxs/utils.wxs"></script>
        <script module="computed" lang="wxs" src="./index.wxs"></script>

        <van-cell
            :size="size"
            :icon="leftIcon"
            :center="center"
            :border="border"
            :is-link="isLink"
            :required="required"
            :clickable="clickable"
            :title-width="titleWidth"
            title-style="margin-right: 12px;"
            :custom-style="customStyle"
            :arrow-direction="arrowDirection"
            custom-class="van-field"
            title-class="label-class"
        >
            <slot name="left-icon" slot="icon" />
            <view v-if="label" :class="utils.bem('field__label', { disabled })" slot="title">
                {{ label }}
            </view>
            <slot v-else name="label" slot="title" />
            <view :class="utils.bem('field__body', [type])">
                <textarea
                    v-if="type === 'textarea'"
                    :class="'input-class ' + utils.bem('field__input', [inputAlign, type, { disabled, error }])"
                    :fixed="fixed"
                    :focus="focus"
                    :cursor="cursor"
                    :value="innerValue"
                    :auto-focus="autoFocus"
                    :disabled="disabled || readonly"
                    :maxlength="maxlength"
                    :placeholder="placeholder"
                    :placeholder-style="placeholderStyle"
                    :placeholder-class="utils.bem('field__placeholder', { error, disabled })"
                    :auto-height="!!autosize"
                    :style="computed.inputStyle(autosize)"
                    :cursor-spacing="cursorSpacing"
                    :adjust-position="adjustPosition"
                    :show-confirm-bar="showConfirmBar"
                    :hold-keyboard="holdKeyboard"
                    :selection-end="selectionEnd"
                    :selection-start="selectionStart"
                    :disable-default-padding="disableDefaultPadding"
                    @input="onInput"
                    @blur="onBlur"
                    @focus="onFocus"
                    @confirm="onConfirm"
                    @linechange="onLineChange"
                    @keyboardheightchange="onKeyboardHeightChange"
                ></textarea>
                <input
                    wx:else
                    :class="'input-class ' + utils.bem('field__input', [inputAlign, { disabled, error }])"
                    :type="type"
                    :focus="focus"
                    :cursor="cursor"
                    :value="innerValue"
                    :auto-focus="autoFocus"
                    :disabled="disabled || readonly"
                    :maxlength="maxlength"
                    :placeholder="placeholder"
                    :placeholder-style="placeholderStyle"
                    :placeholder-class="utils.bem('field__placeholder', { error })"
                    :confirm-type="confirmType"
                    :confirm-hold="confirmHold"
                    :hold-keyboard="holdKeyboard"
                    :cursor-spacing="cursorSpacing"
                    :adjust-position="adjustPosition"
                    :selection-end="selectionEnd"
                    :selection-start="selectionStart"
                    :password="password || type === 'password'"
                    @input="onInput"
                    @blur="onBlur"
                    @focus="onFocus"
                    @confirm="onConfirm"
                    @keyboardheightchange="onKeyboardHeightChange"
                />
                <van-icon v-if="showClear" name="clear" class="van-field__clear-root van-field__icon-root" @touchstart.native.stop.prevent="onClear" />
                <view class="van-field__icon-container" @tap="onClickIcon">
                    <van-icon v-if="rightIcon || icon" :name="rightIcon || icon" :class="'van-field__icon-root ' + iconClass" custom-class="right-icon-class" />
                    <slot name="right-icon" />
                    <slot name="icon" />
                </view>
                <view class="van-field__button">
                    <slot name="button" />
                </view>
            </view>
            <view v-if="showWordLimit && maxlength" class="van-field__word-limit">
                <view :class="utils.bem('field__word-num', { full: value.length >= maxlength })">{{ value.length }}</view>
                /{{ maxlength }}
            </view>
            <view v-if="errorMessage" :class="utils.bem('field__error-message', [errorMessageAlign, { disabled, error }])">
                {{ errorMessage }}
            </view>
        </van-cell>
    </view>
</template>

<script>
'use strict';
import vanCell from '../cell/index';
import vanIcon from '../icon/index';

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

var props_1 = require('./props');

component_1.VantComponent({
    field: true,
    classes: ['input-class', 'right-icon-class', 'label-class'],
    props: __assign(__assign(__assign(__assign({}, props_1.commonProps), props_1.inputProps), props_1.textareaProps), {
        size: String,
        icon: String,
        label: String,
        error: Boolean,
        center: Boolean,
        isLink: Boolean,
        leftIcon: String,
        rightIcon: String,
        autosize: [Boolean, Object],
        required: Boolean,
        iconClass: String,
        clickable: Boolean,
        inputAlign: String,
        customStyle: String,
        errorMessage: String,
        arrowDirection: String,
        showWordLimit: Boolean,
        errorMessageAlign: String,
        readonly: {
            type: Boolean,
            observer: 'setShowClear'
        },
        clearable: {
            type: Boolean,
            observer: 'setShowClear'
        },
        border: {
            type: Boolean,
            value: true
        },
        titleWidth: {
            type: String,
            value: '6.2em'
        }
    }),
    data: {
        focused: false,
        innerValue: '',
        showClear: false
    },
    created: function () {
        this.value = this.data.value;
        this.setData({
            innerValue: this.value
        });
    },
    methods: {
        onInput: function (event) {
            var _a = (event.detail || {}).value;
            var value = _a === void 0 ? '' : _a;
            this.value = value;
            this.setShowClear();
            this.emitChange();
        },
        onFocus: function (event) {
            this.focused = true;
            this.setShowClear();
            this.$emit('focus', event.detail);
        },
        onBlur: function (event) {
            this.focused = false;
            this.setShowClear();
            this.$emit('blur', event.detail);
        },
        onClickIcon: function () {
            this.$emit('click-icon');
        },
        onClear: function () {
            var that = this;
            this.setData({
                innerValue: ''
            });
            this.value = '';
            this.setShowClear();
            uni.nextTick(function () {
                that.emitChange();
                that.$emit('clear', '');
            });
        },
        onConfirm: function (event) {
            var _a = (event.detail || {}).value;
            var value = _a === void 0 ? '' : _a;
            this.value = value;
            this.setShowClear();
            this.$emit('confirm', value);
        },
        setValue: function (value) {
            this.value = value;
            this.setShowClear();

            if (value === '') {
                this.setData({
                    innerValue: ''
                });
            }

            this.emitChange();
        },
        onLineChange: function (event) {
            this.$emit('linechange', event.detail);
        },
        onKeyboardHeightChange: function (event) {
            this.$emit('keyboardheightchange', event.detail);
        },
        emitChange: function () {
            var that = this;
            this.setData({
                value: this.value
            });
            uni.nextTick(function () {
                that.$emit('input', that.value);
                that.$emit('change', that.value);
            });
        },
        setShowClear: function () {
            var _a = this.data;
            var clearable = _a.clearable;
            var readonly = _a.readonly;
            var that = this;
            var focused = that.focused;
            var value = that.value;
            this.setData({
                showClear: !!clearable && !!focused && !!value && !readonly
            });
        },
        noop: function () {}
    }
});
</script>
<style>
@import '../common/index.css';
.van-field {
    --cell-icon-size: 16px;
    --cell-icon-size: var(--field-icon-size, 16px);
}
.van-field__label {
    color: #646566;
    color: var(--field-label-color, #646566);
}
.van-field__label--disabled {
    color: #c8c9cc;
    color: var(--field-disabled-text-color, #c8c9cc);
}
.van-field__body {
    display: -webkit-flex;
    display: flex;
    -webkit-align-items: center;
    align-items: center;
}
.van-field__body--textarea {
    padding: 3.6px 0;
    line-height: 1.2em;
}
.van-field__body--textarea,
.van-field__input {
    box-sizing: border-box;
    min-height: 24px;
    min-height: var(--cell-line-height, 24px);
}
.van-field__input {
    position: relative;
    display: block;
    width: 100%;
    margin: 0;
    padding: 0;
    line-height: inherit;
    text-align: left;
    background-color: initial;
    border: 0;
    resize: none;
    color: #323233;
    color: var(--field-input-text-color, #323233);
    height: 24px;
    height: var(--cell-line-height, 24px);
}
.van-field__input--textarea {
    height: 18px;
    height: var(--field-text-area-min-height, 18px);
    min-height: 18px;
    min-height: var(--field-text-area-min-height, 18px);
}
.van-field__input--error {
    color: #ee0a24;
    color: var(--field-input-error-text-color, #ee0a24);
}
.van-field__input--disabled {
    background-color: initial;
    opacity: 1;
    color: #c8c9cc;
    color: var(--field-input-disabled-text-color, #c8c9cc);
}
.van-field__input--center {
    text-align: center;
}
.van-field__input--right {
    text-align: right;
}
.van-field__placeholder {
    position: absolute;
    top: 0;
    right: 0;
    left: 0;
    pointer-events: none;
    color: #c8c9cc;
    color: var(--field-placeholder-text-color, #c8c9cc);
}
.van-field__placeholder--error {
    color: #ee0a24;
    color: var(--field-error-message-color, #ee0a24);
}
.van-field__icon-root {
    display: -webkit-flex;
    display: flex;
    -webkit-align-items: center;
    align-items: center;
    min-height: 24px;
    min-height: var(--cell-line-height, 24px);
}
.van-field__clear-root,
.van-field__icon-container {
    line-height: inherit;
    vertical-align: middle;
    padding: 0 8px;
    padding: 0 var(--padding-xs, 8px);
    margin-right: -8px;
    margin-right: -var(--padding-xs, 8px);
}
.van-field__button,
.van-field__clear-root,
.van-field__icon-container {
    -webkit-flex-shrink: 0;
    flex-shrink: 0;
}
.van-field__clear-root {
    font-size: 16px;
    font-size: var(--field-clear-icon-size, 16px);
    color: #c8c9cc;
    color: var(--field-clear-icon-color, #c8c9cc);
}
.van-field__icon-container {
    font-size: 16px;
    font-size: var(--field-icon-size, 16px);
    color: #969799;
    color: var(--field-icon-container-color, #969799);
}
.van-field__icon-container:empty {
    display: none;
}
.van-field__button {
    padding-left: 8px;
    padding-left: var(--padding-xs, 8px);
}
.van-field__button:empty {
    display: none;
}
.van-field__error-message {
    text-align: left;
    font-size: 12px;
    font-size: var(--field-error-message-text-font-size, 12px);
    color: #ee0a24;
    color: var(--field-error-message-color, #ee0a24);
}
.van-field__error-message--center {
    text-align: center;
}
.van-field__error-message--right {
    text-align: right;
}
.van-field__word-limit {
    text-align: right;
    margin-top: 4px;
    margin-top: var(--padding-base, 4px);
    color: #646566;
    color: var(--field-word-limit-color, #646566);
    font-size: 12px;
    font-size: var(--field-word-limit-font-size, 12px);
    line-height: 16px;
    line-height: var(--field-word-limit-line-height, 16px);
}
.van-field__word-num {
    display: inline;
}
.van-field__word-num--full {
    color: #ee0a24;
    color: var(--field-word-num-full-color, #ee0a24);
}
</style>
