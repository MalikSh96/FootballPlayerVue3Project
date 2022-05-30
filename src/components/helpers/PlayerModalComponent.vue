<script setup>
import { defineProps } from "vue";
    const props = defineProps({
        showPlayer: {}
    })
</script>

<template>
    <div v-for="(player, key) in props.showPlayer" :key="key">
        <!-- Modal content -->
        <div class="flex justify-between items-start p-5 rounded-t border-b dark:border-[#0de358]">
            <h3 class="text-xl font-semibold text-gray-900 lg:text-2xl dark:text-white">
                Player details:
            </h3>
        </div>
        <!-- Modal body -->
        <div class="p-6 space-y-6">
            <p class="text-base leading-relaxed text-gray-500 dark:text-white">
                Name: {{ player.name }}
            </p>
            <p class="text-base leading-relaxed text-gray-500 dark:text-white">
                Age: {{ player.age }}
            </p>
            <p class="text-base leading-relaxed text-gray-500 dark:text-white">
                Birhtdate: {{ player.birth_date }}
            </p>
            <p class="text-base leading-relaxed text-gray-500 dark:text-white">
                Preferred foot: {{ player.foot }}
            </p>
            <p class="text-base leading-relaxed text-gray-500 dark:text-white">
                Position: {{ player.position }}
            </p>
            <p class="text-base leading-relaxed text-gray-500 dark:text-white">
                Fullname: {{ player.first_name ? player.first_name : 'Unknown'}} {{ player.last_name}}
            </p>
            <p class="text-base leading-relaxed text-gray-500 dark:text-white">
                Height: {{ player.height ? player.height : 'Unknown' }} cm
            </p>
            <p class="text-base leading-relaxed text-gray-500 dark:text-white">
                Weight: {{ player.weight ? player.weight : 'Unknown' }} kg
            </p>
        </div>
    </div>
</template>