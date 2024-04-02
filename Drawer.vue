<template>
    <!-- using v-if instead of v-show here so that OutsideClick directive
    will not immediately close the component when it is first opened -->
    <transition :name="bem.block.modifierOnly('fade-slide')" appear>
        <div v-if="isOpen" :class="bem.block">
            <div :class="bem.element('overlay')" />

            <div v-outside-click="onClose" :class="bem.element('wrapper')">
                <div :class="bem.element('header')">
                    <a :class="bem.element('link')" :href="headerHref" title="marketplace" class="gtm-header-logo">
                        <se-logo :class="bem.element('logo')" />
                    </a>
                    <span :class="bem.element('close-button')" @click="onClose">
                        <se-icon icon="times" :class="bem.element('close')" />
                    </span>
                </div>
                <div :class="bem.element('content')">
                    <slot />
                </div>
            </div>
        </div>
    </transition>
</template>

<style lang="scss">
@use 'Helper/Overlay';
@use 'Helper/Pixel';
@use 'Variable/Palette';
@use 'Variable/Breakpoint';
@use 'Variable/Timing';
@use 'Variable/ZIndex';
@use 'mq';

$_sidemenu-width: 20rem;

.se-drawer {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    z-index: ZIndex.$drawer;
    transition: Timing.$transition-default;

    &--fade-slide {
        &-enter,
        &-leave-to {
            opacity: 0;
            left: -$_sidemenu-width; // transform is not allowed here.
        }
    }

    &__overlay {
        @include Overlay.overlay();
    }

    &__wrapper {
        position: relative;
        width: $_sidemenu-width;
        height: 100%;
        background-color: var(--white);
        // [@]todo: remove non standard box-shadow
        box-shadow: 0.25rem 0.25rem 1rem 0 Palette.$gray-900, inset 0 1px 0 0 Palette.$gray-300;
        z-index: ZIndex.$drawer;
        overflow: scroll;

        @include mq.mq($until: Breakpoint.$mobile) {
            width: 100%;
        }
    }

    &__header {
        height: 4rem;
        background-color: var(--box-5);
        display: flex;
        justify-content: space-between;
        align-items: center;
        padding: 1rem;
        // [@]todo: remove non standard box-shadow
        box-shadow: inset 0 -1px 0 0 Palette.$gray-300;
    }

    &__logo {
        width: Pixel.toRem(138);
        height: 2rem;
    }

    &__close {
        color: var(--white);
        cursor: pointer;
    }
}
</style>

<script lang="ts">
import { createComponent } from '@Presentation/Helper/ComponentFactory';
import { outsideClick } from '@Presentation/Directive/OutsideClick';
import SeIcon from '@Component/Base/SeIcon.vue';
import SeLogo from '@Component/SeLogo.vue';

export const name = 'drawer';

// @vue/component
export default createComponent(name)({
    directives: { outsideClick },
    props: {
        isOpen: {
            type: Boolean,
        },

        headerHref: {
            type: String,
            default: '/',
        },
    },

    methods: {
        onClose() {
            this.$emit('close');
        },
    },

    components: {
        SeIcon,
        SeLogo,
    },
});
</script>
