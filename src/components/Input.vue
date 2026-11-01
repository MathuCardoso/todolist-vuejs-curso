<script setup>
    const props = defineProps({
        type: {
            type: String,
            default: "text",
        },
        placeholder: String,
        label: String,
        id: String,
        error: String,
        modelValue: String,
    });
    const emit = defineEmits(["update:modelValue"]);
</script>

<template>
    <div class="form-field">
        <label v-if="props.label" :for="props.id" class="form-label">
            {{ props.label }}
        </label>
        <input
            :type="props.type"
            :placeholder="props.placeholder"
            :id="props.id"
            class="form-control"
            :value="props.modelValue"
            @input="emit('update:modelValue', $event.target.value)"
        />
        <span v-if="props.error" class="fg-danger fw-bold">{{ props.error }}</span>
    </div>
</template>

<style scoped></style>
