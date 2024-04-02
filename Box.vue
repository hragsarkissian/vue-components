<template>
    <component
        :is="tag"
        :class="
            bem.block.modifiers(
                bem.ifProvided(
                    'bar',
                    'dark',
                    'darker',
                    'light',
                    'styled-above-desktop',
                    'big',
                    'no-padding',
                    'no-border',
                    'small'
                )
            )
        ">
        <div
            v-if="showHeader"
            :class="
                bem
                    .element('header')
                    .modifiers(
                        bem.ifProvided(
                            'wide-header',
                            'dark-header',
                            'borderless-header',
                            'no-padding',
                            'headline-x-t-border'
                        )
                    )
                    .modifier('header-padding', bem.hasModifier('no-padding'))
            ">
            <slot name="header">
                <component :is="titleTag">
                    <slot name="title" />
                </component>
                <div v-if="showFloatRight">
                    <slot name="float-right">
                        <a v-if="backToTop" href="#">
                            {{ $t('general.button.go.top') }}
                            <se-icon icon="long-arrow-alt-up" />
                        </a>
                    </slot>
                </div>
            </slot>
        </div>
        <slot />
    </component>
</template>

<style lang="scss">
@use 'Variable/Palette';
@use 'Variable/Font';
@use 'Variable/Breakpoint';
@use 'Variable/Spacing';
@use 'mq';

$_spacing: Spacing.$se-box-spacing;
$_spacing-small: Spacing.$se-box-spacing-small;
$_spacing-big: Spacing.$se-box-spacing-big;
$_border: 1px solid var(--box-border);

.se-box {
    padding: $_spacing;
    background-color: var(--white);
    border: $_border;

    /* modifiers ======================== */
    &--dark {
        background-color: var(--box-1);
    }

    &--darker {
        background-color: var(--box-4);
    }

    &--light {
        background-color: var(--box-2);
    }

    &--bar {
        border: none;
        border-bottom: $_border;
        padding-left: 0;
        padding-right: 0;
    }

    &--no-border {
        border: none;
    }

    &--small {
        padding: $_spacing-small;
    }

    &--big {
        padding: $_spacing-big;
    }

    &--no-padding {
        padding: 0;
    }

    &--styled-above-desktop {
        @include mq.mq($until: Breakpoint.$desktop) {
            padding: 0;
            border: 0; // initial doesnt work on IE 11
            background-color: transparent; // initial doesnt work on IE 11
        }
    }

    /*  elements ======================== */
    &__header {
        display: flex;
        align-content: center;
        align-items: center;
        justify-content: space-between;
        padding-bottom: $_spacing;
        margin-bottom: $_spacing;
        border-bottom: $_border;

        &--wide-header {
            margin-top: -$_spacing;
            margin-left: -$_spacing;
            margin-right: -$_spacing;
            padding: $_spacing;
        }

        &--dark-header {
            background-color: var(--box-1);
        }

        &--borderless-header {
            border-bottom: none;
        }

        &--header-padding {
            padding: $_spacing;
            margin-bottom: 0;
        }

        &--headline-x-t-border {
            border: $_border;
            border-bottom: none;
        }
    }
}
</style>

<script lang="ts">
import { PropOptions } from 'vue';
import { createComponent } from '@Presentation/Helper/ComponentFactory';
import SeIcon from '@Component/Base/SeIcon.vue';

export const name = 'box';

// @vue/component
export default createComponent(name)({
    components: {
        SeIcon,
    },

    props: {
        tag: {
            type: String,
            default: 'section',
        } as PropOptions<string>,

        titleTag: {
            type: String,
            default: 'h3',
        } as PropOptions<string>,

        backToTop: {
            type: Boolean,
        } as PropOptions<boolean>,
    },

    computed: {
        showTitle(): boolean {
            return Boolean(this.$slots.title);
        },

        showFloatRight(): boolean {
            return Boolean(this.$slots['float-right'] || this.backToTop);
        },

        showHeader(): boolean {
            return Boolean(this.$slots.header) || this.showTitle || this.showFloatRight;
        },
    },
});
</script>
