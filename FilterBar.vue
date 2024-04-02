<template>
    <section :class="bem.block" data-qa-filter-bar data-qa-section>
        <div :class="bem.element('container')">
            <div :class="bem.element('form-group')">
                <div v-t="'product.list.filter-by'" :class="bem.element('title')" @click="onExpandClick" />
                <div :class="bem.element('fieldset').modifier('active', isActiveOnMobile)">
                    <div
                        v-for="filter in filters"
                        :key="filter.parameter_name"
                        :class="bem.element('field').modifier('checkbox', isCheckbox(filter))">
                        <se-checkbox
                            v-if="isCheckbox(filter)"
                            :checked="!!selectedValues[filter.parameter_name]"
                            :name="filter.parameter_name"
                            @change="(value) => onChange(filter.parameter_name, value, filter.type)">
                            <span :class="bem.element('checkbox-label')">
                                {{ getLabel(filter) }}
                            </span>
                        </se-checkbox>

                        <se-label v-if="isTextInput(filter)" bem-floating :text="getLabel(filter)">
                            <se-input
                                :value="selectedValues[filter.parameter_name]"
                                :type="filter.type"
                                :placeholder="filter.placeholder"
                                @input="(value) => onChange(filter.parameter_name, value, filter.type)" />
                        </se-label>

                        <se-label
                            v-if="!isTextInput(filter) && !isCheckbox(filter)"
                            bem-floating
                            :text="getLabel(filter)">
                            <se-select
                                :name="filter.parameter_name"
                                :value="selectedValues[filter.parameter_name]"
                                @change="(value) => onChange(filter.parameter_name, value, filter.type)">
                                <option v-if="filter.placeholder" value="">{{ filter.placeholder }}</option>
                                <option v-for="{ label, value } in filter.values" :key="value" :value="value">
                                    {{ label }}
                                </option>
                            </se-select>
                        </se-label>
                    </div>
                </div>
                <div :class="bem.element('mobile-icon')" @click="onExpandClick">
                    <se-icon :icon="mobileIcon" />
                </div>
            </div>
            <div :class="bem.element('button-container')">
                <se-button bem-secondary icon="filter" :class="bem.element('button')" @click="onClick">
                    {{ $t('filter.apply_filters') }}
                </se-button>

                <se-button
                    bem-secondary-outline
                    :class="bem.element('button')"
                    :disabled="!Object.keys(selectedValues).length"
                    @click="onResetClick">
                    {{ $t('filter.clear_applied_filters') }}
                </se-button>
            </div>
        </div>
    </section>
</template>

<style lang="scss">
@use 'Variable/Palette';
@use 'Variable/Breakpoint';
@use 'Helper/Panel';
@use 'mq';

.se-filter-bar {
    position: relative;

    &__mobile-icon {
        display: none;

        @include mq.mq($until: Breakpoint.$desktop) {
            display: block;
            align-self: flex-start;
            margin-left: 1rem;
        }
    }

    &__container {
        @include Panel.panel(Palette.$gray-100);
        padding: 0.75rem;

        @include mq.mq($from: Breakpoint.$desktop) {
            display: flex;
            align-items: center;
            justify-content: space-between;
        }
    }

    &__form-group {
        display: flex;
        align-items: center;
        justify-content: space-between;
    }

    &__title {
        font-weight: bold;
        min-width: 73px;
        margin-right: 1rem;
    }

    &__fieldset {
        width: 100%;
        display: inline-flex;
        flex-direction: row;
        flex-wrap: wrap;

        @include mq.mq($until: Breakpoint.$desktop) {
            display: none;
            &--active {
                flex-direction: column;
                align-items: flex-start;
                display: flex;
                flex-grow: 1;
            }
        }
    }

    &__button-container {
        display: flex;
    }

    &__field,
    &__button {
        @include mq.mq($until: Breakpoint.$desktop) {
            margin-top: 0.75rem;
        }

        @include mq.mq($from: Breakpoint.$desktop) {
            margin-right: 0.75rem;
        }
    }

    &__field {
        order: 1;
        min-width: 100%;
        white-space: nowrap;

        @include mq.mq($from: Breakpoint.$desktop) {
            min-width: 170px;
            width: 20%;
        }

        &--checkbox {
            order: 2;
            width: auto;
            min-width: auto;
            margin-top: 0.75rem;
        }
    }

    &__button {
        margin-right: 0.75rem;
        text-decoration: none;

        &:last-child {
            margin-right: 0;
        }
    }

    &__button-icon {
        color: var(--ty-helper);
        margin-right: 0.25rem;
    }
}
</style>

<script lang="ts">
import { TranslateResult } from 'vue-i18n';
import { PropOptions } from 'vue/types/umd';
import { isEqual } from '@Shared/Helper/Identity';
import { createComponent } from '@Helper/ComponentFactory';
import { SeFilter } from '@Data/Type/UI';
import { DEFAULT_LIMIT, DEFAULT_OFFSET, PaginationConfig } from '@Data/Endpoint/Pagination';
import SeCheckbox from '@Component/Base/SeCheckbox.vue';
import SeInput from '@Component/Base/SeInput.vue';
import SeLabel from '@Component/Base/SeLabel.vue';
import SeSelect from '@Component/Base/SeSelect.vue';
import SeButton from '@Component/Base/SeButton.vue';
import SeIcon from '@Component/Base/SeIcon.vue';

const CHECKBOX = 'checkbox';
const TEXT_INPUT = 'text';

export const name = 'filter-bar';

// @vue/component
export default createComponent(name)({
    props: {
        filters: {
            type: Array,
            default: () => [],
        } as PropOptions<SeFilter[]>,

        isSpa: {
            type: Boolean,
        } as PropOptions<boolean>,
    },

    data() {
        return {
            selectedValues: {}, // cannot track updates here with computed
            isActiveOnMobile: false,
        };
    },

    mounted(): void {
        window.queueMicrotask(() => {
            this.selectedValues = this.getSelectedFiltersFromUrlQuery();
        });
    },

    computed: {
        component() {
            if (this.isSpa) {
                return 'router-link';
            }

            return 'div';
        },

        mobileIcon(): string {
            return this.isActiveOnMobile ? 'chevron-up' : 'chevron-down';
        },

        paginationQuery(): PaginationConfig {
            const params = new URL(window.location.href).searchParams;

            return {
                limit: params.get('limit') || DEFAULT_LIMIT,
                offset: params.get('offset') || DEFAULT_OFFSET,
            };
        },
    },

    methods: {
        getLabel(filter: SeFilter): string | TranslateResult {
            return filter.label;
        },

        onChange(name: string, value: string, type: string) {
            this.setFilter(name, value, type);
        },

        isCheckbox(filter: SeFilter): boolean {
            return filter.type === CHECKBOX;
        },

        isTextInput(filter: SeFilter): boolean {
            return filter.type === TEXT_INPUT;
        },

        isOptionDisabled(filterOption): boolean {
            return filterOption.totalMatches === 0;
        },

        onExpandClick(): void {
            this.isActiveOnMobile = !this.isActiveOnMobile;
        },

        isSelected(paramName: string): boolean {
            return !!this.selectedValues[paramName];
        },

        setFilter(name: string, value: string, type: string): void {
            if (!value) {
                delete this.selectedValues[name];

                return;
            }

            if (type !== CHECKBOX) {
                this.selectedValues[name] = value;

                return;
            }

            this.selectedValues[name] = value ? '1' : '0';
        },

        onClick(): void {
            if (!this.isSpa) {
                this.$emit('filter', this.selectedValues);

                return;
            }

            const query = this.generateQueryFromFilters();

            // because router.push should be invoked only if there is a new filter, otherwise we will get console errors.
            if (isEqual(query, this.$route.query)) {
                return;
            }

            this.$router.push({
                query,
            });
        },

        onResetClick(): void {
            const { limit, offset } = this.paginationQuery;

            if (!this.isSpa) {
                this.selectedValues = {};
                this.$emit('filter', this.selectedValues);

                return;
            }

            this.$router.push({
                query: {
                    limit: limit as string,
                    offset: offset as string,
                },
            });
        },

        generateQueryFromFilters() {
            const query = this.selectedValues;
            const queryKeys = Object.keys(this.$route.query);

            this.filters.forEach((filter) => {
                if (queryKeys.includes(filter.parameter_name) && !this.selectedValues[filter.parameter_name]) {
                    delete query[filter.parameter_name];
                }
            });

            return query;
        },

        getSelectedFiltersFromUrlQuery() {
            const queryParams = new URL(window.location.href).searchParams;

            const activeFilters = this.filters.reduce((result, key: SeFilter) => {
                if (!queryParams.has(key.parameter_name)) {
                    return result;
                }

                result[key.parameter_name] = queryParams.get(key.parameter_name);

                return result;
            }, {});

            return activeFilters;
        },
    },

    components: {
        SeCheckbox,
        SeSelect,
        SeInput,
        SeLabel,
        SeButton,
        SeIcon,
    },
});
</script>
