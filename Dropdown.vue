<template>
    <se-popper
        v-outside-click="onClickOutside"
        bem-full-width
        bem-with-offset
        :placement="position"
        :is-visible="isOpen"
        :class="bem.block"
        :anchor="anchor"
        @mouseleave.native="onMouseLeave">
        <se-button
            bem-navigation
            bem-icon-big
            :disabled="disabled"
            :icon="icon"
            @click.prevent="onClick"
            @mouseenter="onMouseEnter">
            <slot name="label" />
            <se-icon bem-icon-big :class="bem.element('icon').modifier('is-open', isOpen)" icon="angle-down" />
        </se-button>
        <template #content>
            <slot />
        </template>
    </se-popper>
</template>

<style lang="scss">
@use 'Variable/Palette';
@use 'Variable/Timing';

.se-dropdown {
    &__icon {
        width: 0.5rem;
        margin-left: 0.25rem;
        transition-timing-function: linear;
        transition-duration: 0.15s;

        &--is-open {
            transform: rotate(180deg);
        }
    }
}
</style>

<script lang="ts">
import { PropOptions } from 'vue';
import { createComponent } from '@Helper/ComponentFactory';
import { outsideClick } from '@Presentation/Directive/OutsideClick';
import SePopper from '@Component/SePopper.vue';
import SeIcon from '@Component/Base/SeIcon.vue';
import SeButton from '@Component/Base/SeButton.vue';

export const name = 'dropdown';

// @vue/component
export default createComponent(name)({
    components: {
        SePopper,
        SeIcon,
        SeButton,
    },

    directives: {
        outsideClick,
    },

    props: {
        anchor: {
            type: HTMLElement,
        } as PropOptions<HTMLElement>,

        position: {
            type: String,
            default: 'bottom-start',
        } as PropOptions<string>,

        disabled: {
            type: Boolean,
        } as PropOptions<boolean>,

        useClickOnly: {
            type: Boolean,
        } as PropOptions<boolean>,

        withClickOutside: {
            type: Boolean,
        } as PropOptions<boolean>,

        icon: {
            type: String,
            default: undefined,
        } as PropOptions<string | undefined>,
    },

    data() {
        return {
            isOpen: false,
        };
    },

    methods: {
        toggle() {
            this.isOpen = !this.isOpen;
        },

        close() {
            this.isOpen = false;
        },

        open() {
            this.isOpen = true;
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

        onClickOutside() {
            if (!this.withClickOutside) {
                return;
            }

            this.close();
        },

        onClick(): void {
            if (!this.useClickOnly) {
                return;
            }

            this.toggle();
            this.$emit('click');
        },
    },
});
</script>
