<template>
    <view>
        <script module="utils" lang="wxs" src="../wxs/utils.wxs"></script>

        <van-popup
            :show="show"
            position="bottom"
            :round="round"
            :z-index="zIndex"
            :overlay="overlay"
            custom-class="van-action-sheet"
            :safe-area-inset-bottom="safeAreaInsetBottom"
            :close-on-click-overlay="closeOnClickOverlay"
            @close="onClickOverlay"
        >
            <view v-if="title" class="van-hairline--bottom van-action-sheet__header">
                {{ title }}
                <van-icon name="close" custom-class="van-action-sheet__close" @click="onClose" />
            </view>
            <view v-if="description" class="van-action-sheet__description">
                {{ description }}
            </view>
            <view v-if="actions && actions.length">
                <!-- button外包一层view，防止actions动态变化，导致渲染时button被打散 -->
                <button
                    :open-type="item.openType"
                    :style="item.color ? 'color: ' + item.color : ''"
                    :class="utils.bem('action-sheet__item', { disabled: item.disabled || item.loading }) + ' van-hairline--top ' + (item.className || '')"
                    hover-class="van-action-sheet__item--hover"
                    :data-index="index"
                    @tap="onSelect"
                    @getuserinfo="bindGetUserInfo"
                    @contact="bindContact"
                    @getphonenumber="bindGetPhoneNumber"
                    @error="bindError"
                    @launchapp="bindLaunchApp"
                    @opensetting="bindOpenSetting"
                    :lang="lang"
                    :session-from="sessionFrom"
                    :send-message-title="sendMessageTitle"
                    :send-message-path="sendMessagePath"
                    :send-message-img="sendMessageImg"
                    :show-message-card="showMessageCard"
                    :app-parameter="appParameter"
                    v-for="(item, index) in actions"
                    :key="index"
                >
                    <block v-if="!item.loading">
                        {{ item.name }}
                        <text v-if="item.subname" class="van-action-sheet__subname">{{ item.subname }}</text>
                    </block>

                    <van-loading v-else custom-class="van-action-sheet__loading" size="20px" />
                </button>
            </view>
            <slot />
            <view v-if="cancelText" class="van-action-sheet__cancel" hover-class="van-action-sheet__cancel--hover" hover-stay-time="70" @tap="onCancel">
                {{ cancelText }}
            </view>
        </van-popup>
    </view>
</template>

<script>
'use strict';
import vanIcon from '../icon/index';
import vanPopup from '../popup/index';
import vanLoading from '../loading/index';

Object.defineProperty(exports, '__esModule', {
    value: true
});

var component_1 = require('../common/component');

var button_1 = require('../mixins/button');

var open_type_1 = require('../mixins/open-type');

component_1.VantComponent({
    mixins: [button_1.button, open_type_1.openType],
    props: {
        show: Boolean,
        title: String,
        cancelText: String,
        description: String,
        round: {
            type: Boolean,
            value: true
        },
        zIndex: {
            type: Number,
            value: 100
        },
        actions: {
            type: Array,
            value: []
        },
        overlay: {
            type: Boolean,
            value: true
        },
        closeOnClickOverlay: {
            type: Boolean,
            value: true
        },
        closeOnClickAction: {
            type: Boolean,
            value: true
        },
        safeAreaInsetBottom: {
            type: Boolean,
            value: true
        }
    },
    methods: {
        onSelect: function (event) {
            var index = event.currentTarget.dataset.index;
            var item = this.data.actions[index];

            if (item && !item.disabled && !item.loading) {
                this.$emit('select', item);

                if (this.data.closeOnClickAction) {
                    this.onClose();
                }
            }
        },
        onCancel: function () {
            this.$emit('cancel');
        },
        onClose: function () {
            this.$emit('close');
        },
        onClickOverlay: function () {
            this.$emit('click-overlay');
            this.onClose();
        }
    }
});
</script>
<style>
@import '../common/index.css';
.van-action-sheet {
    max-height: 90% !important;
    max-height: var(--action-sheet-max-height, 90%) !important;
    color: #323233;
    color: var(--action-sheet-item-text-color, #323233);
}
.van-action-sheet__cancel,
.van-action-sheet__item {
    text-align: center;
    font-size: 16px;
    font-size: var(--action-sheet-item-font-size, 16px);
    line-height: 50px;
    line-height: var(--action-sheet-item-height, 50px);
    background-color: #fff;
    background-color: var(--action-sheet-item-background, #fff);
}
.van-action-sheet__cancel--hover,
.van-action-sheet__item--hover {
    background-color: #f2f3f5;
    background-color: var(--active-color, #f2f3f5);
}
.van-action-sheet__cancel:before {
    display: block;
    content: ' ';
    height: 8px;
    height: var(--action-sheet-cancel-padding-top, 8px);
    background-color: #f7f8fa;
    background-color: var(--action-sheet-cancel-padding-color, #f7f8fa);
}
.van-action-sheet__item--disabled {
    color: #c8c9cc;
    color: var(--action-sheet-item-disabled-text-color, #c8c9cc);
}
.van-action-sheet__item--disabled.van-action-sheet__item--hover {
    background-color: #fff;
    background-color: var(--action-sheet-item-background, #fff);
}
.van-action-sheet__subname {
    margin-left: 4px;
    margin-left: var(--padding-base, 4px);
    font-size: 12px;
    font-size: var(--action-sheet-subname-font-size, 12px);
    color: #646566;
    color: var(--action-sheet-subname-color, #646566);
}
.van-action-sheet__header {
    text-align: center;
    font-weight: 500;
    font-weight: var(--font-weight-bold, 500);
    font-size: 16px;
    font-size: var(--action-sheet-header-font-size, 16px);
    line-height: 44px;
    line-height: var(--action-sheet-header-height, 44px);
}
.van-action-sheet__description {
    text-align: center;
    padding: 16px;
    padding: var(--padding-md, 16px);
    color: #646566;
    color: var(--action-sheet-description-color, #646566);
    font-size: 14px;
    font-size: var(--action-sheet-description-font-size, 14px);
    line-height: 20px;
    line-height: var(--action-sheet-description-line-height, 20px);
}
.van-action-sheet__close {
    position: absolute !important;
    top: 0;
    right: 0;
    line-height: inherit !important;
    padding: 0 12px;
    padding: var(--action-sheet-close-icon-padding, 0 12px);
    font-size: 18px !important;
    font-size: var(--action-sheet-close-icon-size, 18px) !important;
    color: #969799;
    color: var(--action-sheet-close-icon-color, #969799);
}
.van-action-sheet__loading {
    display: -webkit-flex !important;
    display: flex !important;
    height: 50px;
    height: var(--action-sheet-item-height, 50px);
}
</style>
