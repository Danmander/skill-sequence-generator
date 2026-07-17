<template>
    <div class="sequence-item">
        <img
            v-if="image !== null"
            class="image"
            :src="image"
            @click="$refs.input.click()"
        >
        <div
            v-else
            class="image"
            @click="$refs.input.click()"
        />
        <input
            ref="input"
            class="d-none"
            type="file"
            accept="image/*"
            @change="onFileChange"
        >
    </div>
</template>

<script>
// import emptyImage from '@/assets/images/empty.png';

export default {
    emits: ["update:modelValue"],
    props: {
        modelValue: {
            type: File,
            default: null
        }
    },
    data() {
        return {
            image: this.modelValue,
        }
    },
    methods: {
        onFileChange(event) {
            const file = event.target.files[0] ?? null;
            if(file === null) {
                this.image = null;
                return;
            }

            const reader = new FileReader();
            reader.onload = () => {
                this.image = reader.result; // Base64 data url
                this.$emit("update:modelValue", reader.result);
            };
            reader.readAsDataURL(file);
        }
    },
    watch: {
        modelValue(newModelValue) {
            this.image = newModelValue;
        }
    }
}
</script>

<style scoped>
.sequence-item {
    line-height: 0;
}

.image {
    width: 128px;
    height: 128px;
    cursor: pointer;
    border-radius: 4px;
    background-color: rgb(var(--v-theme-surface));
}
</style>