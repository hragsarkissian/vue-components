<template>
    <select
        v-model="internalValue"
        :size="size"
        :data-invalid="invalid"
        :class="bem.block.modifier('has-multiple-visible-options', hasSize)"
        v-on="listeners">
        <slot />
    </select>
</template>

<style lang="scss">
@use 'Helper/Form';
@use 'Variable/Palette';

.se-select {
    @include Form.input();
    @include Form.select();

    &--has-multiple-visible-options {
        appearance: none;
        background-image: none;
        padding: 0;
        overflow-y: auto;

        & > option {
            padding: 0.5rem;

            &:checked {
                background-color: Palette.$blue-010;
            }
        }
    }
}
</style>

<script lang="ts">
import { PropOptions } from 'vue';
import { createComponent } from '@Presentation/Helper/ComponentFactory';

export const name = 'select';

// @vue/component
export default createComponent(name)({
    model: {
        prop: 'value',
        event: 'change',
    },

    props: {
        invalid: {
            type: Boolean,
        } as PropOptions<boolean | undefined>,

        size: {
            type: [String, Number],
        } as PropOptions<string | number>,

        value: {
            type: [String, Number, Array],
            default: '',
        } as PropOptions<string | number | Array<string | number>>,
    },

    data() {
        return {
            internalValue: this.value,
        };
    },

    computed: {
        hasSize(): boolean {
            const size = parseInt(`${this.size}`);

            return size > 1;
        },

        listeners(): Record<string, unknown> {
            return {
                ...this.$listeners,

                change: (event: Event) => {
                    const element = event?.target as HTMLSelectElement;

                    this.$emit('change', element.value || '');
                },
            };
        },
    },

    methods: {
        onChange(value: string | number): void {
            this.$emit('change', value);
        },
    },

    watch: {
        value(): void {
            this.internalValue = this.value;
        },
    },
});
</script>
