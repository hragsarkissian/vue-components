<template>
    <label :class="bem.block.modifiers(bem.ifProvided('required'))" data-checkbox-label v-bind="labelAttributes">
        <input
            ref="input"
            type="checkbox"
            data-checkbox
            v-bind="inputAttributes"
            :class="bem.element('input')"
            :checked="isChecked"
            v-on="listeners" />
        <span :class="bem.element('checkbox')" />
        <slot />
    </label>
</template>

<style lang="scss">
@use 'Variable/Palette';
@use 'Variable/Font';
@use 'Variable/Timing';
@use 'Helper/Border';

$_size: 1rem;

.se-checkbox {
    display: inline-flex;
    align-items: center;
    cursor: pointer;

    &--required::after {
        content: ' *';
    }

    &__input {
        position: absolute;
        margin: 0;
        visibility: hidden;
        pointer-events: none;
        z-index: -1;

        // for ie11
        &::-ms-check {
            display: none;
        }
    }

    &__checkbox {
        position: relative;
        min-width: $_size;
        width: $_size;
        height: $_size;
        margin: 0 0.5rem 0 0;
        background: var(--white);
        border: 1px solid var(--box-border);
        border-radius: 0.25rem;
        z-index: 0;

        &::before {
            content: url('@Presentation/Static/Image/checkmark.svg');
            visibility: hidden;
            opacity: 0;
            transition: opacity Timing.$transition-default;
        }
    }

    &__input:checked ~ &__checkbox:before {
        visibility: visible;
        opacity: 1;
    }
}
</style>

<script lang="ts">
import { pick, omit } from 'ramda';
import { PropOptions } from 'vue';
import { SeCheckbox } from '@Data/Type/UI';
import { createComponent } from '@Presentation/Helper/ComponentFactory';

export const name = 'checkbox';

const INPUT_ATTRIBUTES = ['id', 'name', 'value', 'form', 'checked', 'autocomplete'];

// @vue/component
export default createComponent(name)({
    inheritAttrs: false,

    model: {
        prop: 'checked',
        event: 'change',
    },

    props: {
        item: {
            type: Object,
            default: undefined,
        } as PropOptions<SeCheckbox | undefined>,

        checked: {
            type: [Boolean, Number],
            default: false,
        } as PropOptions<boolean | 0 | 1>,
    },

    computed: {
        isChecked(): boolean {
            return Boolean(this.checked);
        },

        listeners(): Record<string, unknown> {
            return {
                ...this.$listeners,
                change: (e: Event) => this.onChange(e),
            };
        },

        attributes(): Record<string, unknown> {
            if (!this.item) {
                return this.$attrs;
            }

            return {
                ...this.item,
                ...this.$attrs,
            };
        },

        labelAttributes(): Record<string, unknown> {
            return omit(INPUT_ATTRIBUTES, this.attributes);
        },

        inputAttributes(): Record<string, unknown> {
            return pick(INPUT_ATTRIBUTES, this.attributes);
        },
    },

    methods: {
        onChange(e: Event): void {
            const target = e.target as HTMLInputElement;

            if (!target) {
                return;
            }

            const isChecked = Boolean(target.checked);

            this.$emit('change', isChecked);
        },
    },
});
</script>
