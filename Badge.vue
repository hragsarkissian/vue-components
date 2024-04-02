<template>
    <span :class="bem.block">
        <slot>Value</slot>
    </span>
</template>

<style lang="scss">
@use 'Variable/Palette';
@use 'Helper/Pixel';
@use 'Variable/Legacy/Font';

.se-badge {
    background-color: Palette.$orange-500;
    border-radius: 0.25rem;
    font-size: Font.$font-size-smaller;
    padding: 0.25rem;
    font-weight: bold;
    line-height: Font.$font-size-small;
    display: inline-flex;
    line-height: 0;
    align-items: center;
    justify-content: center;
    min-width: 1.25rem;
    height: Pixel.toRem(14);
    text-align: center;
    color: var(--white);
}
</style>

<script lang="ts">
import { createComponent } from '@Presentation/Helper/ComponentFactory';

export const name = 'badge';

// @vue/component
export default createComponent(name)({});
</script>
