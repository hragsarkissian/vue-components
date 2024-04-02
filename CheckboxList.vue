<template>
    <div :class="bem.block">
        <se-checkbox
            v-for="(item, index) in items"
            :key="`checkbox-list-${item.value}-${index}`"
            v-bind="$attrs"
            :item="item"
            :checked="selectedValues.includes(item.value)"
            :class="bem.element('item')"
            @input="onInput($event)">
            {{ item.label }}
        </se-checkbox>
    </div>
</template>

<style lang="scss">
@use 'Variable/Palette';
@use 'Variable/Font';
@use 'Helper/Border';
@use 'Variable/Timing';

.se-checkbox-list {
    &__item {
        display: flex !important;
        margin-top: 0.75rem;
        margin-bottom: 0.75rem;
        white-space: normal;
    }
}
</style>

<script lang="ts">
import { uniq } from 'ramda';
import { PropOptions } from 'vue';
import { SeCheckbox as SeCheckboxType } from '@Data/Type/UI';
import { createComponent } from '@Helper/ComponentFactory';
import SeCheckbox from '@Component/Base/SeCheckbox.vue';

export const name = 'checkbox-list';

// @vue/component
export default createComponent(name)({
    inheritAttrs: false,
    components: {
        SeCheckbox,
    },

    props: {
        items: {
            type: Array,
            required: true,
        } as PropOptions<SeCheckboxType[]>,

        value: {
            type: Array,
            default: () => [],
        } as PropOptions<unknown[]>,
    },

    data() {
        return {
            selectedValues: this.value,
        };
    },

    methods: {
        onInput(e: Event): void {
            const target = e.target as HTMLInputElement;

            if (!target) {
                return;
            }

            const isChecked = target.checked;
            const value = target.value;

            this.updateValues(isChecked, value);
        },

        updateValues(isChecked: boolean, value: unknown): void {
            this.selectedValues = isChecked
                ? uniq([...this.selectedValues, value])
                : this.selectedValues.filter((selectedValue: unknown) => selectedValue !== value);

            this.$emit('input', this.selectedValues);
        },
    },
});
</script>
