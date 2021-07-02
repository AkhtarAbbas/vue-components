<template>
   <dropdown ref="dropdownRef" align="right" class="w-full mt-1" width="w-full" :auto-close="false">
        <template #trigger={open}>
            <div class="dropdownTrigger p-4 border rounded-md shadow-sm" :class="{'shadow-3xl border-transparent' : open, 'border-gray-300' : !open}">
                <div class="flex items-center justify-between space-x-2">
                    <a href="#">
                        <span class="font-base text-md">{{ modelValue }}</span>
                    </a>
                   <svg class="w-5 h-5 mt-1 text-blueish transition duration-300 ease-in-out" :class="{'transform rotate-180' : open}" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 9l-7 7-7-7"></path></svg>
                </div>
            </div>
        </template>
        <template #content>
            <span v-for="option in options" :key="option">
                <slot name="item">
                    <dropdown-item
                        @click="select(option)"
                        :class="`${modelValue == option ? 'font-bold border-gray-400' : 'font-normal'}`" class=" text-lg leading-5 font-normal cursor-pointer py-6 text-black "
                    >
                        {{ option }}
                    </dropdown-item>
                </slot>
            </span>
        </template>
    </dropdown>
</template>

<script>
import { ref } from 'vue'
import Dropdown from './Dropdown.vue'
import DropdownItem from './DropdownItem.vue'

export default {
    props: {
        options: {required: true, type: Array},
        modelValue: {required: true},
    },

    emits: ['update:modelValue'],

    components: {
        Dropdown,
        DropdownItem
    },

    setup(_, { emit }) {
        const dropdownRef = ref(null)

        const select = (option) => {
            emit('update:modelValue', option)
            dropdownRef.value.open = false
        }
        return {
            select,
            dropdownRef
        }
    }
}
</script>
