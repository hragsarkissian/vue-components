<template>
    <textarea v-if="multiline" :class="bem.block" :value="value" :data-invalid="invalid" v-on="listeners" />
    <input
        v-else
        :class="bem.block.modifiers(bem.ifProvided('extra-space-left'))"
        :value="value"
        :data-invalid="invalid"
        v-on="listeners" />
</template>

<style lang="scss">
@use 'Variable/Palette';
@use 'Variable/Input';
@use 'Helper/Form';

.se-input {
    @include Form.input();

    &--extra-space-left {
        padding-left: Input.$default-padding + Input.$extra-padding;
    }
}
</style>

<script lang="ts">
import { createComponent } from '@Presentation/Helper/ComponentFactory';
import { PropOptions } from 'vue';

export const name = 'input';

// @vue/component
export default createComponent(name)({
    props: {
        multiline: {
            type: Boolean,
        } as PropOptions<boolean | undefined>,

        value: {
            type: [String, Number],
            default: undefined,
        } as PropOptions<string | number | undefined>,

        invalid: {
            type: Boolean,
        } as PropOptions<boolean | undefined>,
    },

    computed: {
        listeners(): Record<string, unknown> {
            return {
                ...this.$listeners,

                input: (event: InputEvent) => {
                    const element = event?.target as HTMLInputElement;

                    this.$emit('input', element.value || '');
                },
            };
        },
    },
});
</script>
