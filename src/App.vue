<template>
  <div class="flex flex-col bg-gray-200 text-black p-5 min-h-screen w-full">
    <div class="flex flex-col gap-5">
      <div class="flex flex-col gap-5">
        <div class="bg-white p-5 h-30 shadow-sm shadow-gray-500 rounded-md">
          <TechnicianForm />
        </div>
        <div class="bg-white p-5 shadow-sm shadow-gray-500 rounded-md">
          <TechnicianList />
        </div>
      </div>
      <div class="flex flex-col gap-5 h">
        <div class="bg-white p-5 shadow-sm shadow-gray-500 rounded-md">
          <TaskForm />
        </div>
        <div class="bg-white p-5 shadow-sm shadow-gray-500 rounded-md">
          <TaskList />
        </div>
      </div>
    </div>

    <MatchResult />
  </div>
</template>

<script setup>
import TechnicianForm from "./components/TechnicianForm.vue";
import TaskForm from "./components/TaskForm.vue";
import TechnicianList from "./components/TechnicianList.vue";
import TaskList from "./components/TaskList.vue";
import MatchResult from "./components/MatchResult.vue";
</script>
