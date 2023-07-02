<template>
  <div>
    <header>
      <h1>Vue Todo with Typescript</h1>
    </header>
    <main>
      <TotoInput
        :item="todoText"
        @input="updateTodoText"
        @add="addTodoItem"
      ></TotoInput>
      <div>
        <ul>
          <TodoListItem
            v-for="(todoItem, index) in todoItems"
            :key="index"
            :index="index"
            :todoItem="todoItem"
            @toggle="toggleTodoItemComplete"
            @remove="removeTodoItem"
          ></TodoListItem>
          <!-- @delete="removeTodoItem" -->
          <!-- <li>아이템 1</li>
          <li>아이템 2</li>
          <li>아이템 3</li> -->
        </ul>
      </div>
    </main>
  </div>
</template>

<script lang="ts">
import Vue from "vue";
import TodoListItem from "./components/TodoListItem.vue";
import TotoInput from "./components/TotoInput.vue";

const STORAGE_KEY = "vue-todo-ts-vb1";
const storage = {
  save(todoItems: Todo[]) {
    const parsed = JSON.stringify(todoItems);
    localStorage.setItem(STORAGE_KEY, parsed);
  },
  fetch(): Todo[] {
    const todoItems = localStorage.getItem(STORAGE_KEY) || "[]";
    // debugger;
    const result = JSON.parse(todoItems);
    return result;
  },
};

export interface Todo {
  title: string;
  done: boolean;
}

export default Vue.extend({
  components: { TotoInput, TodoListItem },
  data() {
    return {
      todoText: "",
      todoItems: [] as Todo[],
    };
  },
  methods: {
    //읽어오는 역할
    updateTodoText(value: string) {
      this.todoText = value;
    },
    //저장하는 역할
    addTodoItem() {
      const value = this.todoText;
      const todo: Todo = {
        title: value,
        done: false,
      };
      this.todoItems.push(todo);
      storage.save(this.todoItems);
      // localStorage.setItem(value, value);
      this.initTodoText();
    },
    //초기화
    initTodoText() {
      this.todoText = "";
    },
    //정렬
    fetchTodoItems() {
      // axios.get("todos");
      this.todoItems = storage.fetch().sort((a, b) => {
        if (a.title < b.title) {
          return -1;
        }
        if (a.title > b.title) {
          return 1;
        }
        return 0;
      });
    },
    //삭제
    removeTodoItem(index: number) {
      console.log("remove", index);
      this.todoItems.splice(index, 1);
      storage.save(this.todoItems);
    },
    //수정
    toggleTodoItemComplete(todoItem: Todo, index: number) {
      this.todoItems.splice(index, 1, {
        ...todoItem,
        done: !todoItem.done,
      });
      storage.save(this.todoItems);
    },
  },
  //정렬 시점
  created() {
    this.fetchTodoItems();
  },
});
</script>

<style scoped></style>
