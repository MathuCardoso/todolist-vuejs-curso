<script setup>
    const props = defineProps({
        type: {
            type: String,
            default: "button",
        },
        addClass: String,
    });

    const emit = defineEmits(["click"]);
</script>

<template>
    <div class="w-100">
        <button
            @click="emit('click')"
            :type="props.type"
            class="btn-solid"
            :class="props.addClass"
        >
            <slot></slot>
        </button>
    </div>
</template>

<style scoped></style>
