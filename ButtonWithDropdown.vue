<template>
    <se-dropdown-option-list
        :options="options"
        :is-open="isOpen"
        :class="bem.block"
        @click-outside="onClickOutside"
        @select="onSelect"
        @mouseleave.native="onMouseLeave">
        <se-button
            data-qa-dropdown-button
            type="button"
            v-bind="$attrs"
            :class="bem.element('button')"
            :icon="icon"
            @click.prevent="onClick"
            @mouseenter.native="onMouseEnter">
            <slot />
            <se-icon
                v-if="!hideDownIcon"
                :class="bem.element('dropdown-icon').modifier('is-open', isOpen)"
                icon="angle-down" />
        </se-button>
    </se-dropdown-option-list>
</template>

<style lang="scss">
@use 'Variable/Palette';
@use 'Variable/Font';
@use 'Helper/Pseudo';
@use 'Variable/Breakpoint';
@use 'mq';

.se-button-with-dropdown {
    display: inline-flex;

    &__button {
        display: inline-flex;
        align-items: center;
    }

    &__dropdown-icon {
        width: 0.5rem;
        margin-left: 0.25rem;
        font-size: 1rem;
        transition-timing-function: linear;
        transition-duration: 0.15s;
        transform: translate3d(0, 0, 0) scale3d(1, 1, 1) rotateX(0) rotateY(0) rotateZ(0) skew(0, 0);
        transform-style: preserve-3d;

        &--is-open {
            transform: translate3d(0, 0, 0) scale3d(1, 1, 1) rotateX(0) rotateY(0) rotateZ(180deg) skew(0, 0);
        }
    }
}
</style>

<script lang="ts">
import { PropOptions } from 'vue';
import { Option } from '@Data/Type/UI';
import { createComponent } from '@Presentation/Helper/ComponentFactory';
import SeDropdownOptionList from '@Component/SeDropdownOptionList.vue';
import SeIcon from '@Component/Base/SeIcon.vue';
import SeButton from '@Presentation/Component/Base/SeButton.vue';

export const name = 'button-with-dropdown';

// @vue/component
export default createComponent(name)({
    inheritAttrs: false,

    props: {
        options: {
            type: Array,
            default: () => [],
        } as PropOptions<Option[]>,

        icon: {
            type: String,
            default: '',
        },

        iconSize: {
            type: String,
            default: '1rem',
        },

        hideDownIcon: {
            type: Boolean,
        } as PropOptions<boolean>,

        useClickOnly: {
            type: Boolean,
        } as PropOptions<boolean>,

        withClickOutside: {
            type: Boolean,
        } as PropOptions<boolean>,
    },

    data() {
        return {
            isOpen: false,
        };
    },

    methods: {
        onClickOutside() {
            if (!this.withClickOutside) {
                return;
            }

            this.close();
        },

        onSelect(option, event): void {
            this.$emit('select', option, event);
            this.close();
        },

        onClick(): void {
            if (!this.useClickOnly) {
                return;
            }

            this.toggle();
            this.$emit('click');
        },

        onMouseEnter(): void {
            if (this.useClickOnly) {
                return;
            }

            this.open();
            this.$emit('mouseenter');
        },

        onMouseLeave(): void {
            if (this.useClickOnly) {
                return;
            }

            this.close();
            this.$emit('mouseleave');
        },

        toggle() {
            this.isOpen = !this.isOpen;
        },

        close() {
            this.isOpen = false;
        },

        open() {
            this.isOpen = true;
        },
    },

    components: {
        SeDropdownOptionList,
        SeButton,
        SeIcon,
    },
});
</script>
