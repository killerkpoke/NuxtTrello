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

function createColumn() {
    const column: Column = {
        id: nanoid(),
        title: "New Column",
        tasks: []
    }
    columns.value.unshift(column);
    nextTick(() => {
        const colInpHeader = document.querySelector(".column:first-of-type .title-input") as HTMLInputElement;
        colInpHeader.focus();
    });
    
}
// TODO - 
function deleteColumn(column: Column, columns: Column[]) {
    column.title === '' ? columns = columns.filter(c => c.id !== column.id): null
}

</script>

<template>
    <div class="flex items-start overflow-x-auto rounded-t-lg gap-4 bg-cyan-800 p-4">
         <button
            @click="createColumn"
            class="bg-gray-200 whitespace-nowrap p-2 rounded opacity-50"
        >
        <font-awesome-icon icon="fa-solid fa-plus" />    
        Add Another Column
        </button>
    </div>
    <div class="flex items-start overflow-x-auto rounded-b-lg gap-4 bg-cyan-800 p-4">
        
        <draggable
            v-model="columns"
            group="columns"
            :animation="250"
            handle=".drag-handle"
            item-key="id"
            class="flex gap-4 items-start"
        >
            <template #item="{ element: column }: { element: Column }">
                <div class="bg-gray-200 column p-5 rounded-lg min-w-[250px]">
                    <header class="font-bold mb-4">
                        <DragHandle />
                        <input
                            class="title-input bg-transparent focus:bg-white rounded px-1 mx-2 w-4/5"
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
    </div>
</template>