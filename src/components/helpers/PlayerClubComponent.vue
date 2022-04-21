<script setup>
import { defineProps, /*onMounted,*/ /*defineExpose*/ } from "vue";
const props = defineProps({
    playerClubId: {}
})

// let clubs = {}

//emit data back?
</script>

<template>
    <p class="px-6 py-4" v-for="(club, key) in props.playerClubId" :key="key">
        {{ club.name }}
    </p>
</template>