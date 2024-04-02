<template>
    <ul :class="bem.block.modifiers(bem.ifProvided('ui-blue'))">
        <li
            v-for="item in items"
            :key="item.value"
            :class="
                bem.element('list').modifier('selected', isItemSelected(item)).modifiers(bem.ifProvided('ui-blue'))
            ">
            <se-radio
                :item="item"
                :checked="isItemSelected(item)"
                :class="
                    bem.element('item').modifier('selected', isItemSelected(item)).modifiers(bem.ifProvided('ui-blue'))
                "
                @input="onInput(item)">
                <slot :name="`title(${item})`" />
            </se-radio>
            <slot :name="`option(${item.value})`" />
        </li>
    </ul>
</template>

<style lang="scss">
@use 'Variable/Palette';
@use 'Variable/Timing';
@use 'Helper/Panel';
@use 'Helper/CheckoutPanel';

.se-radio-list {
    list-style-type: none;
    margin-left: 0;

    &__list {
        &--ui-blue {
            background-color: var(--white);
            border: 1px solid var(--box-border);
            border-radius: 0.25rem;
            margin-bottom: 1rem;
            transition: border-color Timing.$transition-slow Timing.$transition-ease;
        }

        &--ui-blue &--selected {
            border: 2px solid Palette.$blue-300;
        }
    }

    &__item {
        &--ui-blue {
            font-weight: bold;
            padding: 1rem;
            transition: color Timing.$transition-slow Timing.$transition-ease;
        }

        &--selected {
            color: Palette.$blue-300;
        }
    }
}
</style>

<script lang="ts">
import { PropOptions } from 'vue';
import { createComponent } from '@Presentation/Helper/ComponentFactory';
import SeRadio from '@Component/Base/SeRadio.vue';
import { SeCheckbox } from '@Data/Type/UI';

export const name = 'radio-list';

// @vue/component
export default createComponent(name)({
    props: {
        items: {
            type: Array,
            default: () => [],
        } as PropOptions<SeCheckbox[]>,

        value: {
            type: [String, Number],
            default: undefined,
        } as PropOptions<string | number | undefined>,
    },

    methods: {
        onInput(item: SeCheckbox) {
            this.$emit('input', item.value);
        },

        isItemSelected(item: SeCheckbox) {
            return item.value === this.value;
        },
    },

    components: {
        SeRadio,
    },
});
</script>
