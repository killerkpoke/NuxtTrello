<script setup lang="ts">
import type { Column, Task } from '~~/types';
import draggable from "vuedraggable";
import { nanoid } from "nanoid";
const columns = useLocalStorage<Column[]>("trelloBoard", [
    {
        id: nanoid(),
        title: "Backlog",
        tasks: [
            {
                id: nanoid(),
                title: "Create marketing landing page",
                created_at: new Date()
            },
            {
                id: nanoid(),
                title: "Fix some bugs",
                created_at: new Date()
            },
        ]
    },
    {
        id: nanoid(),
        title: "For Dev",
        tasks: []
    },
    {
        id: nanoid(),
        title: "In Proggress",
        tasks: []
    },
    {
        id: nanoid(),
        title: "QA",
        tasks: []
    },
    {
        id: nanoid(),
        title: "Complete",
        tasks: []
    }
]);

const alt = useKeyModifier("Alt")


// watch(columns, () => {
    // ajax request
// }, {
//     deep: true,
// })

function createColumn() {
    const column: Column = {
        id: nanoid(),
        title: "New Column",
        tasks: []
    }
    columns.value.push(column);
    nextTick(() => {
        const colInpHeader = document.querySelector(".column:last-of-type .title-input") as HTMLInputElement;
        colInpHeader.focus();
    });
    
}
// TODO - 
function deleteColumn(column: Column, columns: Column[]) {
    column.title === '' ? columns = columns.filter(c => c.id !== column.id): null
}

</script>

<template>
    <div class="flex items-start overflow-x-auto gap-4">
        <draggable
            v-model="columns"
            group="columns"
            :animation="250"
            handle=".drag-handle"
            item-key="id"
            class="flex gap-4  items-start"
        >
            <template #item="{ element: column }: { element: Column }">
                <div class="bg-gray-200 column p-5 rounded min-w-[250px]">
                    <header class="font-bold mb-4">
                        <DragHandle />
                        <input
                            class="title-input bg-transparent focus:bg-white rounded px-1 w-4/5"
                            @keyup.enter="($event.target as HTMLInputElement).blur()"
                            @keydown.backspace="
                                column.title === '' ? columns = columns.filter(c => c.id !== column.id): null"
                            type="text"
                            v-model="column.title"
                        />
                    </header>
                    <draggable
                        v-model="column.tasks"
                        :group="{ name: 'tasks', pull: alt ? 'clone': true }"
                        handle=".drag-handle"
                        :animation="250"
                        item-key="id"
                    >
                        <template #item="{ element: task }: { element: Task }">
                            <div>
                                <TrelloBoardTask 
                                    :task="task"
                                    @delete="column.tasks = column.tasks.filter((t)=> t.id !== $event)"/>
                            </div>
                        </template>
                    
                    </draggable>
                       
                    <footer>
                        <NewTask @add="column.tasks.push($event)"/>
                    </footer>
                </div>
            </template>
        </draggable>
        <button
            @click="createColumn"
            class="bg-gray-200 whitespace-nowrap p-2 rounded opacity-50"
        >
            + Add Another Column
        </button>
    </div>
</template>