<template>
    <div class="song">
        <img class="thumb" :src="(song.img)" alt="Ardace">
        <div class="body">
            <h3 class="title">{{ song.name }}</h3>
            <p class="author">{{ song.singer }}</p>
        </div>
        <div class="option">
            <i class="fas fa-ellipsis-h"></i>
        </div>
    </div>
</template>
<script>
export default {
    props: {
        song: {
            type: Object,
            requred: true,
            default() {
                return {}
            },
        },
    },
}
</script>

