<template>
    <div class="battlestations__item battlestations__item--skeleton">
        <div class="battlestations__item-thumbnail"></div>
        <div class="battlestations__item-details">
            <div class="battlestations__item-title"></div>
            <div class="battlestations__item-text"></div>
            <div class="battlestations__item-upvotes"></div>
        </div>
    </div>
</template>

<style scoped>
.battlestations__item--skeleton {
    opacity: 0.5;
}

.battlestations__item-thumbnail {
    height: 100px;
    width: 100px;
    background-color: #ddd;
}

.battlestations__item-details {
    display: flex;
    flex-direction: column;
    margin-left: 1rem;
}

.battlestations__item-title {
    height: 1rem;
    width: 50%;
    margin-bottom: 0.5rem;
    background-color: #ddd;
}

.battlestations__item-text {
    height: 3rem;
    width: 100%;
    margin-bottom: 0.5rem;
    background-color: #ddd;
}

.battlestations__item-upvotes {
    height: 1rem;
    width: 30%;
    background-color: #ddd;
}
</style>