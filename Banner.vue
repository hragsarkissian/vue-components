<template>
    <li :class="bem.block">
        <se-tooltip v-if="hasTooltip" placement="bottom-start">
            <se-alert :type="notification.type" :icon="notification.icon" :closable="notification.closable" hoverable>
                {{ notification.content }}
            </se-alert>
            <template #content>
                {{ notification.tooltip }}
            </template>
        </se-tooltip>
        <se-alert v-else :type="notification.type" :icon="notification.icon" :closable="notification.closable">
            {{ notification.content }}
        </se-alert>
    </li>
</template>

<style lang="scss">
.se-banner {
    list-style: none;
}
</style>

<script lang="ts">
import { PropOptions } from 'vue';
import SeAlert from '@Component/SeAlert.vue';
import { BannerNotification } from '@Data/Type/Notification';
import { createComponent } from '@Presentation/Helper/ComponentFactory';

const SeTooltip = () => import('@Component/SeTooltip.vue').then((m) => m.default);
const name = 'se-banner';

// @vue/component
export default createComponent(name)({
    components: {
        SeAlert,
        SeTooltip,
    },

    props: {
        notification: {
            type: Object,
            required: true,
        } as PropOptions<BannerNotification>,
    },

    computed: {
        hasTooltip(): boolean {
            return Boolean(this.notification.tooltip);
        },
    },
});
</script>
