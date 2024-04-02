<template>
    <a :class="bem.block">
        <img :class="bem.element('img')" :src="imageSrc" width="51" height="48" />
        <span :class="bem.element('title')">
            <slot>{{ label }}</slot>
        </span>
    </a>
</template>

<style lang="scss">
@use 'Variable/Palette';
@use 'Variable/Font';
@use 'Variable/Breakpoint';
@use 'mq';

.se-card-nav {
    display: inline-flex;
    align-items: center;
    justify-content: center;
    flex-direction: column;
    text-align: center;
    width: 100%;
    min-height: 6rem;
    border-radius: 0.5rem;
    height: 8vw;
    text-decoration: none;
    // @todo standardize the shadow
    box-shadow: 0 0 2px 0 Palette.$gray-500, inset 0 1px 0 0 Palette.$gray-500, inset 0 -1px 0 0 Palette.$gray-500,
        inset 1px 0 0 0 Palette.$gray-500, inset -1px 0 0 0 Palette.$gray-500;
    background-color: var(--white);
    transition: 300ms ease;

    &:hover,
    &:focus {
        background: Palette.$gray-100;
        text-decoration: none;
    }

    &__title {
        font-family: Font.$family-heading;
        font-size: 0.8rem;
        font-weight: 600;
        text-align: center;
        color: Palette.$gray-700;

        @include mq.mq($from: Breakpoint.$tablet) {
            margin-top: 0.5rem;
        }
    }

    @include mq.mq($until: Breakpoint.$tablet) {
        width: 100%;
        min-height: 3rem;
        height: 3rem;
        justify-content: left;
        flex-direction: row;
        padding: 0.625rem;

        &__img {
            width: 2.125rem;
            height: 2rem;
        }

        &__title {
            margin-left: 0.625rem;
        }
    }
}
</style>

<script lang="ts">
import { PropOptions } from 'vue';
import { createComponent } from '@Presentation/Helper/ComponentFactory';

export const name = 'card-nav';

// @vue/component
export default createComponent(name)({
    props: {
        label: {
            type: String,
        } as PropOptions<string>,

        imageSrc: {
            type: String,
            required: true,
        } as PropOptions<string>,
    },
});
</script>
