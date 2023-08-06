<template>
    <div class="checkbox-round-wrapper">
        <div class="inline-content-wrapper">
            <!-- Checkbox -->
            <div class="input-container">
                <!-- Not-selected icon -->
                <icon-unchecked
                v-if="!selected"
                class="icon-unchecked"/>
                <!-- Selected Icon -->
                <icon-checked
                v-else
                class="icon-checked"/>
                <!-- Checkbox input behind to trigger behavior -->
                <input
                type="checkbox"
                :id="`checkbox-${id}`"
                :checked="selected"
                @click="$emit('clicked', id)">
            </div>
            <!-- Text -->
            <label
            :for="`checkbox-${id}`"
            :class="{ 'checked': selected }"
            v-html="text"/>
        </div>
        <slot></slot>
    </div>
</template>

<script setup>
import iconUnchecked from '../../assets/icons/circle.svg'
import iconChecked from '../../assets/icons/status-check.svg'

/* Emmitters */
const emit = defineEmits(['clicked'])

/** Props received
@id { String, Number } - checkbox id
@text { String } - text
@selected { Boolean } - checkbox is checked or not
*/
const props = defineProps({
    id: String || Number,
    text: String,
    selected: Boolean
})
</script>

<style lang="scss" scoped>
.checkbox-round-wrapper {
    width: 100%;
    display: flex;
    flex-direction: column;

    .inline-content-wrapper {
        display: flex;
        align-items: center;
        gap: 8px;

        .input-container {
            display: flex;
            justify-content: center;
            align-items: center;

            position: relative;

            .icon-unchecked {
                width: 18px;
                height: 18px;

                position: absolute;
                pointer-events: none;

                :deep(.change-color) {
                    stroke: rgb(141, 141, 238);
                }
            }

            .icon-checked {
                width: 24px;
                height: 24px;

                position: absolute;
                pointer-events: none;

                :deep(.change-color-fill) {
                    stroke: blueviolet;
                    fill: blueviolet;
                }
            }
        }

        label {
            font-size: 16px;
            font-weight: 500;
            line-height: 1.31;
            color: rgb(141, 141, 238);
            cursor: pointer;

            &.checked {
                color: blueviolet;
            }
        }

        input {
            width: 18px;
            height: 18px;
            flex-shrink: 0;

            position: relative;
            appearance: none;

            cursor: pointer;
        }
    }
}
</style>
