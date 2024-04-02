<template>
    <transition :name="bem.block.modifierOnly('fade')">
        <section
            v-show="isOpen"
            data-qa-section
            :class="bem.block.modifier(type).modifier('hoverable', hoverable).modifier('closable', closable)">
            <span v-if="!noIcon" :class="bem.element('icon')">
                <se-icon baseline :spin="isLoadingType" :icon="computedIcon" />
            </span>

            <slot>Message</slot>
            <button v-if="closable" :aria-label="alertLabelTranslation" :class="bem.element('button')" @click="onClose">
                <se-icon icon="times" />
            </button>
        </section>
    </transition>
</template>

<style lang="scss">
@use 'Variable/AlertTheme';
@use 'Helper/Pixel';

// @todo: standardize border radius
$_borderRadius: Pixel.toRem(5);

.se-alert {
    $_self: &;
    position: relative;
    padding: 0.75rem;
    transition: background-color 300ms, filter 300ms;
    display: flex;
    font-weight: bold;
    padding-right: 2rem;

    @each $type, $map in AlertTheme.$colorMap {
        &--#{$type} {
            background-color: map-get($map, background);
            color: map-get($map, body);
            border: 1px solid map-get($map, border);
            border-radius: $_borderRadius;

            &#{$_self}--hoverable:hover {
                filter: brightness(1.1);
            }

            #{$_self}__button {
                &:hover {
                    background-color: map-get($map, background);
                    filter: brightness(1.1);
                }

                svg {
                    fill: map-get($map, body);
                    stroke: map-get($map, body);
                }
            }
        }
    }
    &--closable {
        padding-right: 2.5rem;
    }
    &__icon {
        margin-right: 0.5rem;
        font-size: 1rem;
        line-height: 0;
    }
    &__button {
        position: absolute;
        right: 0.65rem;
        top: 0.65rem;
        width: 1.5rem;
        height: 1.5rem;
        line-height: 0;
        transition: background-color 300ms;
        background-color: transparent;
        border-radius: $_borderRadius;
        display: flex;
        align-items: center;
        justify-content: center;
        cursor: pointer;

        & svg {
            top: 0;
        }
    }

    &--fade-enter-active,
    &--fade-leave-active {
        transition: opacity 250ms;
    }
    &--fade-enter,
    &--fade-leave-to {
        opacity: 0;
    }
}
</style>

<script lang="ts">
import { PropOptions } from 'vue';
import { createComponent } from '@Helper/ComponentFactory';
import { getDefaultIcon } from '@Helper/Notification';
import SeIcon from '@Component/Base/SeIcon.vue';
import { NotificationType } from '@Data/Type/Notification';

export const name = 'alert';

// @vue/component
export default createComponent(name)({
    components: {
        SeIcon,
    },

    props: {
        type: {
            type: String,
            default: NotificationType.Info,
        } as PropOptions<NotificationType>,

        hoverable: {
            type: Boolean,
        } as PropOptions<boolean>,

        closable: {
            type: Boolean,
        } as PropOptions<boolean>,

        icon: {
            type: String,
            default: '',
        } as PropOptions<string>,

        noIcon: {
            type: Boolean,
        } as PropOptions<boolean>,
    },

    data() {
        return {
            isOpen: true,
        };
    },

    computed: {
        computedIcon(): string {
            return this.icon || getDefaultIcon(this.type);
        },

        isLoadingType(): boolean {
            return this.type === NotificationType.Loading;
        },

        alertLabelTranslation(): string {
            return this.$t('product.alert') as string;
        },
    },

    methods: {
        onClose(): void {
            this.isOpen = false;
        },

        show(): void {
            this.isOpen = true;
        },
    },
});
</script>
