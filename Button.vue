<template>
    <a
        v-if="isLink"
        :disabled="loading"
        :class="bem.block.modifier('link').modifier('loading', loading).modifiers(bem.modifiers)"
        v-on="$listeners">
        <se-icon v-if="loading" icon="icon-loading" spin :class="bem.element('loader').modifiers(bem.modifiers)" />
        <se-icon
            v-if="icon && !loading"
            :icon="icon"
            :class="
                bem.element('icon').modifier('loading', loading).modifier('with-text', hasText).modifiers(bem.modifiers)
            " />
        <slot />
    </a>
    <button v-else :class="bem.block.modifier('loading', loading).modifiers(bem.modifiers)" v-on="$listeners">
        <se-icon v-if="loading" icon="icon-loading" spin :class="bem.element('loader').modifiers(bem.modifiers)" />
        <se-icon
            v-if="icon"
            :icon="icon"
            :class="
                bem.element('icon').modifier('loading', loading).modifier('with-text', hasText).modifiers(bem.modifiers)
            " />
        <slot />
    </button>
</template>

<style lang="scss">
@use 'Variable/Palette';
@use 'Variable/Font';
@use 'Variable/Timing';
@use 'Variable/Spacing';
@use 'Helper/Form';
@use 'Helper/Link';

$_transition-timing: ease-in-out;
$_space-between-text-and-icon: 0.5rem;

.se-button2 {
    position: relative;
    display: inline-flex;
    align-items: center;
    justify-content: center;
    font-size: Font.$size-small;
    text-align: center;
    text-decoration: none;
    line-height: 1;
    vertical-align: middle;
    white-space: nowrap;
    // user-agent [background-color: buttonface] is adding a gray background to all buttons.
    background-color: transparent;
    color: var(--cta-ghost-dark);
    cursor: pointer;
    will-change: box-shadow, border-color, background-color, color;
    transition: box-shadow Timing.$transition-default $_transition-timing,
        border-color Timing.$transition-default $_transition-timing,
        background-color Timing.$transition-default $_transition-timing,
        color Timing.$transition-default $_transition-timing, transform Timing.$transition-default $_transition-timing;

    &:not(&--unstyled) {
        font-weight: bold;

        &:active {
            transform: translateY(2px);
        }
    }

    &--full-width {
        width: 100%;
    }

    &--link {
        color: var(--ty-link);

        &:hover,
        &:focus,
        &:active {
            text-decoration: none;
            outline: none;
        }
    }

    &--unstyled,
    &--primary,
    &--primary-outline,
    &--secondary,
    &--secondary-outline,
    &--navigation {
        padding: 0.9rem 1rem;
        border: 1px solid transparent;
        border-radius: 0.25rem;
    }

    &--light-font {
        font-weight: normal;
    }

    &--primary {
        color: var(--cta-primary-light);
        background-color: var(--cta-primary-dark);
        border-color: var(--cta-primary-dark);

        &:hover,
        &:focus,
        &:active {
            color: var(--cta-primary-light);
            background-color: var(--cta-primary-hover);
            border-color: var(--cta-primary-hover);
        }
    }

    &--primary-outline {
        color: var(--cta-primary-outline-dark);
        background-color: var(--cta-primary-outline-light);
        border-color: var(--cta-primary-outline-border);

        &:hover,
        &:focus,
        &:active {
            color: var(--cta-primary-outline-dark);
            background-color: var(--cta-primary-outline-hover);
        }
    }

    &--secondary {
        color: var(--cta-secondary-light);
        background-color: var(--cta-secondary-dark);
        border-color: var(--cta-secondary-dark);

        &:hover,
        &:focus,
        &:active {
            color: var(--cta-secondary-light);
            background-color: var(--cta-secondary-hover);
        }
    }

    &--secondary-outline {
        color: var(--cta-secondary-outline-dark);
        background-color: var(--cta-secondary-outline-light);
        border-color: var(--cta-secondary-outline-border);

        &:hover,
        &:focus,
        &:active {
            color: var(--cta-secondary-outline-dark);
            background-color: var(--cta-secondary-outline-hover);
        }
    }

    &--text-danger {
        color: Palette.$orange-600;
    }

    &--icon-only {
        padding: 0;
    }

    &--has-focus {
        &:active,
        &:focus {
            background-color: Palette.$navy-030;
        }
    }

    &--small {
        padding-left: 0.75rem;
        padding-right: 0.75rem;
    }

    &--navigation {
        @include Link.link(var(--white), var(--white), false);
        padding: 0.75rem;
        background-color: var(--box-5);
        border-color: Palette.$navy-500;

        &:hover,
        &:active {
            background-color: var(--box-6);
            border-color: Palette.$navy-300;
            box-shadow: none;
        }
    }

    &--icon-big {
        padding-top: 0.5rem;
        padding-bottom: 0.5rem;
    }

    &__icon {
        display: inline;
        vertical-align: middle;
        color: inherit;
        will-change: opacity;
        transition: opacity Timing.$transition-default ease-in-out;

        &--with-text {
            margin-right: $_space-between-text-and-icon;
        }

        &--loading {
            opacity: 0;
        }

        &--icon-right {
            order: 1;
            margin-right: 0;
            margin-left: $_space-between-text-and-icon;
        }

        &--icon-only,
        &--styled-icon {
            margin: 0;
        }

        &--icon-big {
            width: 1.15rem;
            height: 1.15rem;
        }
    }

    &__loader {
        position: absolute;
        margin: auto;
        top: 0;
        bottom: 0;
        left: 0;
        right: 0;
        font-size: Font.$size-medium;
        color: var(--white);

        &--primary-outline {
            color: var(--cta-primary-outline-dark);
        }

        &--secondary-outline {
            color: var(--cta-secondary-outline-dark);
        }
    }

    &:disabled {
        &,
        &:hover,
        &:focus,
        &:active {
            color: var(--cta-disabled-dark);
            background-color: var(--cta-disabled-light);
            border-color: transparent;
            pointer-events: none;
        }
    }

    &--loading {
        &,
        &:hover,
        &:active,
        &:focus,
        &:disabled {
            color: transparent;
            pointer-events: none;
            opacity: 0.85;
        }
    }
}
</style>

<script lang="ts">
import { PropOptions } from 'vue';
import { createComponent } from '@Presentation/Helper/ComponentFactory';
import SeIcon from '@Component/Base/SeIcon.vue';

export const name = 'button2';

// @vue/component
export default createComponent(name)({
    props: {
        icon: {
            type: String,
            default: undefined,
        } as PropOptions<string | undefined>,

        isLink: {
            type: Boolean,
        } as PropOptions<boolean>,

        loading: {
            type: Boolean,
        } as PropOptions<boolean>,
    },

    computed: {
        hasText(): boolean {
            return Boolean(this.$slots.default) || Boolean(this.$scopedSlots.default);
        },
    },

    components: {
        SeIcon,
    },
});
</script>
