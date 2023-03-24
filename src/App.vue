<template>
  <div id="app">
    <current-time class="col-4" />
    <task-input class="col-6" @add-task="addNewTask" />
    <div class="col-12">
      <div class="cardBox">
        <div class="container">
          <h2>My Tasks</h2>
          <div class="col-4">
            <input type="checkbox" v-model="hideDone" id="hideDone" />
            <label for="hideDone">Hide Done Tasks</label>
          </div>
          <div class="col-4">
            <input type="checkbox" v-model="reverse" id="reverse" />
            <label for="reverse"> Reverse Order</label>
          </div>
          <div class="col-4">
            <input type="checkbox" v-model="sortById" id="sortById" />
            <label for="sortById">Sort By Id</label>
          </div>
          <ul class="taskList">
            <li
              v-for="(taskItem, index) in displayList"
              :key="`${index}_${Math.random()}`"
            >
              <input
                type="checkbox"
                :checked="!!taskItem.finishedAt"
                @input="changeStatus(taskItem.id)"
              />
              #{{ taskItem.id }} - {{ taskItem.task }}
              <span v-if="taskItem.finishedAt">
                | Done at:
                {{ formatDate(taskItem.finishedAt) }}
              </span>
            </li>
          </ul>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import CurrentTime from "./components/CurrentTime.vue";
import TaskInput from "./components/TaskInput";
export default {
  name: "TodoApp",
  components: {
    CurrentTime,
    TaskInput,
  },
  data: () => ({
    taskList: [],
    hideDone: false,
    reverse: false,
    sortById: false,
  }),
  computed: {
    baseList() {
      return [...this.taskList].map((t, index) => ({
        ...t,
        id: index + 1,
      }));
    },
    filteredList() {
      return this.hideDone
        ? [...this.baseList].filter((t) => !t.finishedAt)
        : [...this.baseList];
    },
    sortedList() {
      return [...this.filteredList].sort((a, b) =>
        this.sortById ? b.id - a.id : (a.finishedAt || 0) - (b.finishedAt || 0)
      );
    },
    displayList() {
      const taskList = [...this.sortedList];
      return this.reverse ? taskList.reverse() : taskList;
    },
  },
  methods: {
    formatDate(value) {
      if (!value) return "";
      if (typeof value !== "number") return value;
      const browserLocale =
        navigator.languages && navigator.languages.length
          ? navigator.languages[0]
          : navigator.language;
      const intlDateTime = new Intl.DateTimeFormat(browserLocale, {
        year: "numeric",
        month: "numeric",
        day: "numeric",
        hour: "numeric",
        minute: "numeric",
      });
      return intlDateTime.format(new Date(value));
    },
    addNewTask(task) {
      this.taskList.push({
        task,
        createdAt: Date.now(),
        finishedAt: undefined,
      });
    },
    changeStatus(taskIndex) {
      const task = this.taskList[taskIndex - 1];
      if (task.finishedAt) {
        task.finishedAt = undefined;
      } else {
        task.finishedAt = Date.now();
      }
    },
  },
};
</script>
<style lang="scss">
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  display: flex;
  justify-content: center;
  align-items: center;
  flex-direction: row;
  flex-wrap: wrap;
  text-align: center;
  color: #2c3e50;
}
.taskList li {
  text-align: left;
}
.cardBox {
  margin: 20px auto !important;
}
</style>
