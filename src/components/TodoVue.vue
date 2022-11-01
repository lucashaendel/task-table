<script setup>
import { onMounted, ref, reactive } from "vue";
import InputNew from "./InputNew.vue";
const count = ref(0);
let boards = reactive([
  {
    id: crypto.randomUUID(),
    name: "board-1",
    items: [{ id: crypto.randomUUID(), title: "Buen dia Plataforma5" }],
  },
  {
    id: crypto.randomUUID(),
    name: "board-2",
    items: [{ id: crypto.randomUUID(), title: "AGUANTE EL GRUPO C!!" }],
  },
]);
function startDrag(evt, boardId, itemId) {
  // console.log(boardId, itemId);
  evt.dataTransfer.dropEffect = "move";
  evt.dataTransfer.effectAllowed = "move";
  evt.dataTransfer.setData("item", JSON.stringify({ boardId, itemId }));
}
function onDrop(evt, dest) {
  const { boardId, itemId } = JSON.parse(evt.dataTransfer.getData("item"));
  // console.log({ boardId, itemId });
  const board = boards.find((board) => board.id === boardId);
  const item = board.items.find((item) => item.id === itemId);
  board.items = board.items.filter((i) => i.id !== item.id);
  dest.items.push({ ...item });
}
function handleNewItem(text, board) {
  // console.log(text.value);
  board.items.push({ id: crypto.randomUUID(), title: text.value });
}
function createNewBoard() {
  const name = prompt("Name of board");
  if (name) {
    const board = {
      id: crypto.randomUUID(),
      name: name,
      items: [],
    };
    boards.push(board);
  }
}
</script>

<template>
  <div>
    <nav>
      <ul>
        <li>
          <a href="#" @click="createNewBoard">Create list</a>
        </li>
      </ul>
    </nav>

    <div class="boards-container">
      <div class="boards">
        <div
          class="board"
          @drop="onDrop($event, board)"
          @dragover.prevent
          @dragenter.prevent
          v-for="board in boards"
          :key="board.id"
        >
          {{ board.name }}
          <div class="input">
            <InputNew @on-new-item="(text) => handleNewItem(text, board)" />
          </div>
          <div
            class="item drag-el"
            draggable="true"
            @dragstart="startDrag($event, board.id, item.id)"
            v-for="item in board.items"
            :key="item.id"
          >
            {{ item.title }}
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
nav {
  width: 100vw;
  height: 10vh;
  background-color: #2f2f2f;
  margin-bottom: 5vh;
  display: flex;
  align-items: center;
}
nav li {
  list-style: none;
}
nav li a {
  text-decoration: none;
  font-size: 1.5em;
  color: white;
  text-transform: uppercase;
  transition: 0.3s ease-in-out all;
}

nav li a:hover {
  color: red;
  text-shadow: 0px 0px 15px red;
  transition: 0.3s ease-in-out all;
}

.drag-el {
  background-color: #fff;
  margin-bottom: 10px;
  padding: 5px;
}
.boards {
  display: flex;
  margin-left: 5vw;
}
.board {
  margin: 0 10px;
  background: #ccc;
  padding: 10px;
}

.items {
  display: flex;
  flex-direction: column;
}

.item {
  margin-top: 10px;
  background-color: white;
  padding: 10px;
  box-sizing: border-box;
}
</style>
