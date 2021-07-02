<template>
    <label v-if="label" :for="label" :class="labelClasses">Plaats*</label>
    <div :class="widthClass">
        <input  
            :type="type" 
            v-bind="$attrs"
            :value="modelValue"
            :class="inputClasses" 
            class="w-full rounded-md py-4 shadow-sm"
            @input="$emit('update:modelValue', $event.target.value)" 
        >
        <div v-if="error" class="block text-sm italic text-red-500 text-center">{{ error }}</div>
    </div>
</template>

<script>
import { computed } from 'vue'
export default {
    inheritAttrs: false,
    props: {
        width: { default: 'full' },
        label: { String },
        labelClasses: { default: [] },
        error: { String },
        type: { default: 'text' },
        modelValue: { required: true }
    },

    setup({ width, error }) {
        let widthClass = computed(() => `w-${width}`)
        let inputClasses = computed(() => {
            if (error) {
                return ['border-red-500']
            }

            return ['bg-gray-300']
        })

        return {
            widthClass,
            inputClasses
        }
    }
}
</script>

<style>

</style>