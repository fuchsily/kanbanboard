<template>
  <div class="card" @drop="onDrop($event)" @dragover.prevent @dragenter.prevent>
    <div class="card-header text-center" :class="titleClasses">
      <h4>{{ title }}</h4>
    </div>
    <div class="card-body">
      <Task
        v-for="task in tasks"
        :key="task.id"
        :task="task"
        :alertColor="alertColor"
        draggable="true"
        @dragstart="startDrag($event, task)"
      ></Task>
    </div>
    <div class="card-footer" v-if="newTasks">
      <NewTask @new-task="newTask"></NewTask>
    </div>
  </div>
</template>

<script>
import NewTask from "./NewTask.vue";
import Task from "./Task";
export default {
  components: {
    Task,
    NewTask,
  },
  props: {
    title: String,
    titleClasses: String,
    status: Number,
    newTasks: Boolean,
    tasks: Array,
  },
  emits: {
    "new-task": (task) => {
      if ("status" in task === false) {
        console.warn(
          "StatusCardComponent: Jede Aufgabe muss ein Status-Attribut haben"
        );
        return false;
      }
      return true;
    },
    "status-updated": (statusDO) => {
      if ("newStatus" in statusDO === false) {
        console.warn(
          "StatusCardComponent: Jede Aufgabe muss ein Status-Attribut haben"
        );
        return false;
      }
      return true;
    }
  },
  computed: {
    alertColor() {
      switch (this.status) {
        case 0:
          return "secondary";
        case 1:
          return "primary";
        case 2:
          return "success";
      }
      return "danger";
    },
  },
  methods: {
    newTask(task) {
        task.status = this.status;
        this.$emit("new-task", task);
     
    },
    startDrag(event, task) {
      event.dataTransfer.dropEffect = "move";
      event.dataTransfer.effectAllowed = "move";
      event.dataTransfer.setData("taskId", task.id);
    },
    onDrop(event) {
      const taskId = event.dataTransfer.getData("taskId");
      this.$emit("status-updated", {taskId: taskId, newStatus: this.status});
    },
  },
};
</script>

<style scoped></style>
