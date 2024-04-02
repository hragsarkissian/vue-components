<template>
    <component
        :is="htmlElement"
        v-bind="$attrs"
        :class="bem.block.modifier(variant).modifier(size).modifiers(bem.ifProvided('auto-width'))"
        v-on="$listeners">
        <se-icon
            v-if="isLoading"
            :class="bem.element('loader').modifier('hidden', !isLoading)"
            icon="circle-notch"
            size="large"
            spin />

        <span :class="bem.element('value').modifier('hidden', isLoading).modifier(variant)">
            <slot>value</slot>
        </span>
    </component>
</template>

<style lang="scss">
@use 'Variable/Palette';
@use 'Variable/Font';
@use 'Variable/Timing';
@use 'Helper/Pseudo';
@use 'Helper/Pixel';

.se-button-base {
    display: inline-block;
    vertical-align: middle;
    font-family: Font.$family-default;
    text-align: center;
    cursor: pointer;
    padding: 0.75rem 1.25rem;
    border-radius: 0.25rem;
    border-width: 1px;
    border-style: solid;
    transition: all 300ms ease-in-out;
    font-size: Font.$size-small;
    text-decoration: none;
    white-space: nowrap;
    line-height: 1.1;
    width: 100%;
    font-weight: bold;
    position: relative;
    height: 2.5rem;

    @include Pseudo.hocus() {
        text-decoration: none;
    }

    &:focus {
        box-shadow: 0 0 0 Pixel.toRem(2) Palette.$navy-500;
    }

    &.no-focus {
        box-shadow: none;
        outline: none;
    }

    &[disabled] {
        cursor: default;
        pointer-events: none;
        opacity: 0.6;
    }

    //Button Types

    &--alternate {
        background-color: var(--box-5);
        color: var(--white);
        border-color: Palette.$navy-500;

        @include Pseudo.hocus() {
            background-color: var(--box-6);
            border-color: Palette.$navy-300;
            box-shadow: none;
        }
    }

    &--alternate-with-border {
        background-color: var(--box-5);
        color: var(--white);
        border-color: var(--white);

        @include Pseudo.hocus() {
            background-color: var(--box-6);
            border-color: var(--white);
        }
    }

    &--primary {
        background-color: Palette.$orange-500;
        color: var(--white);
        border-color: Palette.$orange-500;

        @include Pseudo.hocus() {
            background-color: Palette.$orange-300;
            border-color: Palette.$orange-300;
        }
    }

    &--transparent {
        color: Palette.$navy-900;
        border-color: Palette.$gray-700;
        background-color: transparent;
    }

    &--menu {
        text-align: left;
        color: var(--ty-secondary);
        border: none;
        background-color: transparent;
        font-family: Font.$family-default;
        &:hover,
        &.is-hover {
            background-color: var(--box-1);
        }
        &:focus {
            box-shadow: none;
        }
    }

    &--tertiary {
        color: Palette.$gray-700;
        border-color: var(--ty-helper);
        background-color: var(--white);
        @include Pseudo.hocus() {
            background-color: var(--box-1);
        }
    }

    &--tertiary-black {
        color: var(--ty-secondary);
        border-color: var(--ty-helper);
        background-color: var(--white);
        @include Pseudo.hocus() {
            background-color: var(--box-1);
        }
    }

    &--quarternary {
        color: Palette.$gray-700;
        border-color: var(--box-border);
        background-color: var(--box-1);
        @include Pseudo.hocus() {
            background-color: var(--box-4);
        }
    }

    &--secondary {
        background-color: var(--white);
        color: Palette.$orange-500;
        border-color: Palette.$orange-500;
        @include Pseudo.hocus() {
            background-color: var(--box-1);
        }
    }

    &--dropdown {
        background-color: var(--white);
        color: Palette.$gray-700;
        border-color: var(--box-border);
        &:focus {
            box-shadow: none;
        }
        &:disabled {
            background-color: var(--box-2);
        }
    }

    &--auto-width {
        width: auto;
    }

    &--small {
        padding: 0.5rem 0.5rem;
        min-width: 2rem;
        height: auto;
    }

    &--medium {
        padding: 0.75rem;
        min-width: 2.5rem;
    }

    &--large {
        padding: 0.75rem 1rem;
        min-width: 3rem;
    }

    &--link {
        color: var(--ty-link);
        border: none;
        background-color: transparent;
        font-family: Font.$family-default;
        height: 100%;
        width: fit-content;
        transition: none;

        &:focus {
            box-shadow: none;
        }
    }

    &__loader {
        margin: auto;
        position: absolute;
        top: 0;
        left: 0;
        bottom: 0;
        right: 0;
        opacity: 1;
        transition: opacity Timing.$transition-default;

        &--hidden {
            opacity: 0;
        }
    }

    &__value {
        display: flex;
        align-items: center;
        justify-content: center;
        transition: color Timing.$transition-default;

        &--hidden {
            color: transparent;
        }

        &--dropdown {
            justify-content: space-between;
        }
    }
}
</style>

<script lang="ts">
import { PropOptions } from 'vue';
import { createComponent } from '@Presentation/Helper/ComponentFactory';
import SeIcon from '@Component/Base/SeIcon.vue';

export const name = 'button-base';

// @vue/component
export default createComponent(name)({
    components: {
        SeIcon,
    },

    inheritAttrs: false,
    props: {
        size: {
            type: String,
        } as PropOptions<string>,

        htmlElement: {
            type: String,
            default: 'button',
        } as PropOptions<string>,

        variant: {
            type: String,
            default: '',
        } as PropOptions<string>,

        isLoading: {
            type: Boolean,
        } as PropOptions<boolean>,
    },
});
</script>
