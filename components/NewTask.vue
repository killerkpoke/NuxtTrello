<script setup lang="ts">
import type { Task } from '@/types';
import { nanoid } from "nanoid";

const emit = defineEmits<{
    (e: "add", payload: Task): void;
}>();

const focused = ref(false);
const title = ref("");

function createTask(e: Event) {
    if (title.value.trim()) {
        e.preventDefault();
        emit("add", {
            id: nanoid(),
            title: title.value.trim(),
            created_at: new Date(),
        } as Task);
    }
    title.value = "";
}
</script>

<template>
    <div>
        <textarea
            v-model="title"
            @keydown.tab="createTask"
            @keydown.enter="createTask"
            class="focus:bg-white focus:shadow resize-none rounded w-full border-r-2"
            :class="{
                'h-7': !focused,
                'h-20': focused
            }"
            style="outline: none !important;"
            @focus="focused = true"
            @blur="focused = false"
            :placeholder="!focused ? '+ Add A a Card': 'Enter a title for this card'"
        >

        </textarea>
    </div>

</template>