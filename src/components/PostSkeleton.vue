<template>
    <div class="battlestations__item battlestations__item--skeleton">
        <div alt="Post image" class="battlestations__item-image"></div>
        <h2 class="battlestations__item-title"></h2>
        <p class="battlestations__item-upvotes"></p>
    </div>
</template>

<style scoped>
.battlestations__item--skeleton {
    --skeleton-background: hsl(200, 20%, 90%);
    --skeleton-animation: skeleton-loading 1s linear infinite alternate;
}

.battlestations__item-image {
    animation: var(--skeleton-animation);
    background: var(--skeleton-background);
    border-radius: var(--item-radius);

}


.battlestations__item-title {
    animation: var(--skeleton-animation);
    background: var(--skeleton-background);
    border-radius: var(--item-radius);
    height: 2.5rem;
    width: 90%;
}

.battlestations__item-upvotes {
    animation: var(--skeleton-animation);
    background: var(--skeleton-background);
    border-radius: var(--item-radius);
    height: 2rem;
    width: 45%;
}

@keyframes skeleton-loading {
    0% {
        background: var(--skeleton-background);
    }

    100% {
        background: hsl(200, 20%, 95%);
    }
}
</style>