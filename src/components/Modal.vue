<!-- src/components/Modal.vue -->
<template>
    <div v-if="show" class="modal-overlay">
        <div class="modal-container">
            <div class="modal-header">
                <span class="modal-title">{{ title }}</span>
                <button @click="closeModal" class="close-button">Закрити</button>
            </div>
            <div class="modal-content">
                <slot></slot>
            </div>
        </div>
    </div>
</template>

<script>
export default {
    props: {
        show: Boolean,
        title: String,
    },
    methods: {
        closeModal() {
            this.$emit('close');
        },
    },
};
</script>

<style scoped>
.modal-overlay {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background: rgba(0, 0, 0, 0.5);
    display: flex;
    justify-content: center;
    align-items: center;
}

.modal-container {
    background: #fff;
    padding: 20px;
    border-radius: 8px;
    box-shadow: 0px 0px 10px rgba(0, 0, 0, 0.5);
    width: 400px;
}

.modal-header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-bottom: 10px;
}

.modal-title {
    font-size: 18px;
    font-weight: bold;
}

.close-button {
    background: #e74c3c;
    color: #fff;
    border: none;
    padding: 8px 12px;
    cursor: pointer;
    border-radius: 4px;
}

.close-button:hover {
    background: #912317;
}

</style>
