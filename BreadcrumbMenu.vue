<template>
    <se-box v-if="hasItems" bem-bar :class="bem.block">
        <nav :class="bem.element('container')">
            <template v-for="(item, index) in items">
                <se-navigation-item
                    :key="item.label"
                    :item="item"
                    :tag="getTag(item, index)"
                    :disabled="isDisabled(item)"
                    :class="
                        bem
                            .element('step')
                            .modifier('current', isCurrent(item, index))
                            .modifier('before-current', isBeforeCurrent(index))
                            .modifier('disabled', isDisabled(item))
                    " />
                <se-icon
                    v-if="!isLast(index)"
                    :key="`${item.label}-icon`"
                    icon="angle-right"
                    :class="bem.element('icon').modifier('before-current', isBeforeCurrent(index))" />
            </template>
            <button v-if="showMobileMenuButton" :class="bem.element('menu-trigger')" @click="onMobileMenuTriggerClick">
                <se-icon icon="ellipsis-h" size="large" />
            </button>
        </nav>
    </se-box>
</template>

<style lang="scss">
@use 'Variable/Palette';
@use 'Helper/Layout';
@use 'Variable/Breakpoint';
@use 'mq';

.se-breadcrumb-menu {
    &__container {
        @include Layout.container();
    }

    &__step {
        font-weight: bold;
        color: var(--ty-link);
        display: inline-block;
        align-items: center;
        padding: 0 0.25rem;
        text-decoration: none;
        white-space: nowrap;
        text-transform: capitalize;

        &--current {
            color: var(--ty-secondary);
            cursor: default;
        }

        &--disabled {
            color: var(--ty-helper);
            cursor: default;
        }

        &:not(#{&}--current):not(#{&}--disabled):hover {
            text-decoration: underline;
        }
    }

    &__icon {
        display: inline-block;
        color: var(--ty-link);
        font-size: 0.7rem;
        vertical-align: middle;
    }

    &__menu-trigger {
        float: right;
    }

    @include mq.mq($until: Breakpoint.$desktop) {
        &__step {
            &:not(#{&}--current):not(#{&}--before-current) {
                display: none;
            }
        }

        &__icon {
            &:not(#{&}--before-current) {
                display: none;
            }
        }
    }

    @include mq.mq($from: Breakpoint.$desktop) {
        &__menu-trigger {
            display: none;
        }
    }
}
</style>

<script lang="ts">
import { PropOptions } from 'vue/types/umd';
import { Breadcrumb } from '@Data/Type/William/Navigation/Breadcrumb';
import { createComponent } from '@Presentation/Helper/ComponentFactory';
import SeNavigationItem from '@Presentation/Container/SeNavigationItem.vue';
import SeBox from '@Component/SeBox.vue';
import { isUndefined } from '@Shared/Helper/Identity';
import SeIcon from '@Component/Base/SeIcon.vue';

export const name = 'breadcrumb-menu';

// @vue/component
export default createComponent(name)({
    props: {
        items: {
            type: Array,
            default: () => [],
        } as PropOptions<Breadcrumb[]>,

        showMobileMenuButton: {
            type: Boolean,
        } as PropOptions<boolean>,
    },

    computed: {
        hasItems(): boolean {
            return this.items && this.items.length > 0;
        },
    },

    methods: {
        isFirst(index: number): boolean {
            return index === 0;
        },

        isLast(index: number): boolean {
            return index === this.items.length - 1;
        },

        isBeforeCurrent(index: number): boolean {
            const indexAfter = index + 1;

            if (indexAfter > this.items.length - 1) {
                return false;
            }

            const itemAfter = this.items[indexAfter];

            return this.isCurrent(itemAfter, indexAfter);
        },

        isCurrent(item: Breadcrumb, index: number): boolean {
            if (isUndefined(item.isActive)) {
                return this.isLast(index);
            }

            return item.isActive;
        },

        isDisabled(item: Breadcrumb): boolean {
            if (isUndefined(item.isLinkEnabled)) {
                return false;
            }

            return !item.isActive && !item.isLinkEnabled;
        },

        getTag(item: Breadcrumb, index: number): string {
            if (this.isCurrent(item, index)) {
                return 'strong';
            }

            if (this.isDisabled(item)) {
                return 'span';
            }

            return 'a';
        },

        onMobileMenuTriggerClick(): void {
            this.$emit('mobile-menu-button-click');
        },
    },

    components: {
        SeNavigationItem,
        SeBox,
        SeIcon,
    },
});
</script>
