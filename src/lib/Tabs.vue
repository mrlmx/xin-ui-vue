<template>
    <div class="xin-tabs">
        <div class="xin-tabs-nav" ref="navWrapper">
            <div
                class="xin-tabs-nav-item"
                :class="{ 'xin-tabs-nav-active': active === tab.key }"
                v-for="tab of tabProps"
                :key="tab.key"
                @click="handleTabClick(tab.key)"
                :ref="
                    (el) => {
                        if (active === tab.key) activedNav = el;
                    }
                "
            >
                {{ tab.title }}
            </div>
            <div class="xin-tabs-nav-line" ref="navLine" />
        </div>
        <div class="xin-tabs-content">
            <component :is="tabComponent" :key="tabComponent.props.key" />
        </div>
    </div>
</template>

<script lang="ts">
import { computed, onMounted, onUpdated, ref, watchEffect } from "vue";
import Tab from "./Tab.vue";
export default {
    props: {
        active: String,
    },
    setup(props, context) {
        const activedNav = ref<HTMLDivElement>(null);
        const navWrapper = ref<HTMLDivElement>(null);
        const navLine = ref<HTMLDivElement>(null);
        const defaultSlot = context.slots.default();
        const tabProps = defaultSlot.map(({ type, props }) => {
            if (type !== Tab) {
                throw new Error("Tabs 子标签必须是 Tab");
            }
            return props;
        });
        const tabComponent = computed(() => {
            return defaultSlot.find((tab) => tab.props.key === props.active);
        });
        onMounted(() => {
            const { width, left } = activedNav.value.getBoundingClientRect();
            navLine.value.style.width = width + "px";
            const navWrapperPosition = navWrapper.value.getBoundingClientRect();
            navLine.value.style.left = left - navWrapperPosition.left + "px";
        });
        onUpdated(() => {
            const { width, left } = activedNav.value.getBoundingClientRect();
            navLine.value.style.width = width + "px";
            const navWrapperPosition = navWrapper.value.getBoundingClientRect();
            navLine.value.style.left = left - navWrapperPosition.left + "px";
        });
        // onMounted(() => {
        //     watchEffect(() => {
        //         const {
        //             width,
        //             left,
        //         } = activedNav.value.getBoundingClientRect();
        //         navLine.value.style.width = width + "px";
        //         const navWrapperPosition = navWrapper.value.getBoundingClientRect();
        //         navLine.value.style.left =
        //             left - navWrapperPosition.left + "px";
        //     });
        // });
        const handleTabClick = (key) => {
            context.emit("update:active", key);
        };
        return {
            defaultSlot,
            tabProps,
            // activeTab,
            handleTabClick,
            tabComponent,
            activedNav,
            navLine,
            navWrapper,
        };
    },
};
</script>

<style lang="scss">
@import "./constant";

.#{$class-prefix}-tabs {
    &-nav {
        position: relative;
        display: flex;
        border-bottom: 1px solid #f0f0f0;

        &-active {
            color: $primary-color;
        }

        &-item {
            padding: 10px 0;
            margin-right: 20px;
            cursor: pointer;

            &:hover {
                color: $primary-color-secondary;
            }
        }

        &-line {
            height: 2px;
            position: absolute;
            background: #1890ff;
            bottom: -1px;
            left: 0px;
            width: 0px;
            border: none;
            transition: all 0.3s;
        }
    }
}
</style>