<template>
    <se-popper
        v-outside-click="onClickOutside"
        bem-full-width
        bem-with-offset
        placement="bottom-start"
        :class="bem.block"
        :is-visible="isOpen"
        :anchor="anchor">
        <slot v-if="!anchor" />

        <template #content>
            <div ref="dropdown" :class="bem.element('dropdown')" tabindex="0">
                <se-icon v-if="isLoaderVisible" :class="bem.element('loader')" icon="circle-notch" baseline spin />
                <se-option
                    v-for="(option, index) in options"
                    v-else
                    :key="option.value"
                    :class="bem.element('option')"
                    v-bind="option"
                    :href="option.url"
                    :is-disabled="option.disabled || false"
                    data-qa-dropdown-option
                    tabindex="0"
                    @click="onSelect(option, $event)"
                    @keydown.arrow-down.prevent="onKeydown(index)"
                    @keydown.arrow-up.prevent="onKeydown(index, false)"
                    @keydown.enter.prevent="onSelect(option, $event)">
                    <slot name="option" :option="option">
                        {{ option.label }}
                    </slot>
                </se-option>
            </div>
        </template>
    </se-popper>
</template>

<style lang="scss">
@use 'Variable/Palette';
@use 'Variable/Font';
@use 'Helper/Pixel';

.se-dropdown-option-list {
    margin: 0;

    &__dropdown {
        padding: 0.5rem;
        min-width: 15rem;
        max-height: 21rem;
        overflow-y: auto;
    }

    &__loader {
        font-size: 1rem;
        margin-left: 0.25rem;
        color: Palette.$navy-100;
    }

    &__icon {
        vertical-align: middle;
    }

    &__option {
        display: flex;
        font-size: Font.$size-small;
    }
}
</style>

<script lang="ts">
import { PropOptions } from 'vue';
import { createComponent } from '@Presentation/Helper/ComponentFactory';
import SePopper from '@Presentation/Component/SePopper.vue';
import { outsideClick } from '@Presentation/Directive/OutsideClick';
import SeOption from '@Presentation/Component/SeOption.vue';
import SeIcon from '@Component/Base/SeIcon.vue';
import { Option } from '@Data/Type/UI';

export const name = 'dropdown-option-list';

// @vue/component
export default createComponent(name)({
    props: {
        options: {
            type: Array,
            default: () => [],
        } as PropOptions<Option[]>,

        isOpen: {
            type: Boolean,
            required: true,
        } as PropOptions<boolean>,

        anchor: {
            type: HTMLElement,
        } as PropOptions<HTMLElement>,
    },

    computed: {
        isLoaderVisible(): boolean {
            return this.isOpen && !this.options.length;
        },
    },

    methods: {
        onClickOutside(): void {
            this.$emit('click-outside');
        },

        onSelect(option, event: Event): void {
            this.$emit('select', option, event);
            option.action && option.action();
        },

        onKeydown(index: number, moveDown = true): void {
            const container = this.$refs?.dropdown as HTMLElement;

            if (!container) {
                return;
            }

            const childrenLength = container.children.length;

            if (childrenLength === 0) {
                return;
            }

            const currentIndex = moveDown
                ? (index + 1) % childrenLength
                : (index - 1 + childrenLength) % childrenLength;

            const el = container.children[currentIndex] as HTMLElement;

            el.focus();
        },
    },

    components: {
        SePopper,
        SeOption,
        SeIcon,
    },

    directives: {
        outsideClick,
    },
});
</script>
