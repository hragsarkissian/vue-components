<template>
    <label :class="bem.block" v-bind="labelAttributes">
        <input
            ref="input"
            type="radio"
            :checked="checked"
            :class="bem.element('input').modifiers(bem.ifProvided('large'))"
            data-radio
            v-bind="inputAttributes"
            @change="onChange($event.target.checked)"
            v-on="listeners" />
        <slot :item="item">
            {{ item.label }}
        </slot>
    </label>
</template>

<style lang="scss">
@use 'Variable/Palette';
@use 'Helper/CheckoutPanel';

$_input-size: 1rem;
$_input-size-big: 1.6rem;

.se-radio {
    color: var(--ty-helper);
    display: flex;
    align-items: center;
    cursor: pointer;
    // Edge fix for :checked radio state
    accent-color: var(--ty-link);

    &__input {
        height: $_input-size !important;
        width: $_input-size !important;
        margin-right: 1rem;
        margin-top: 0;

        &--large {
            height: $_input-size-big !important;
            width: $_input-size-big !important;
        }
    }
}
</style>

<script lang="ts">
import { pick, omit } from 'ramda';
import { PropOptions } from 'vue';
import { SeCheckbox } from '@Data/Type/UI';
import { createComponent } from '@Presentation/Helper/ComponentFactory';

export const name = 'radio';

const INPUT_ATTRIBUTES = ['id', 'name', 'value', 'form', 'group'];

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
            type: Boolean,
        } as PropOptions<boolean>,
    },

    computed: {
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
