<template>
    <div class="sequance-item">
        <img
            v-if="imageSrc !== null"
            class="image"
            :src="imageSrc"
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
    computed: {
        imageSrc() {
            if(this.image === null) return null;
            return URL.createObjectURL(this.image);
        }
    },
    methods: {
        onFileChange(event) {
            this.image = event.target.files[0] ?? null;
            this.$emit("update:modelValue", this.image);
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
.sequance-item {
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