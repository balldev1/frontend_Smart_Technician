<template>
  <div>
    <div class="flex">
      <h1 class="badge px-2">เพิ่มงาน</h1>
    </div>
    <form @submit.prevent="addTask" class="flex gap-2 mt-5">
      <input
        v-model="title"
        type="text"
        placeholder="ชื่องาน"
        required
        className="input input-neutral bg-white w-60 "
      />
      <input
        v-model="required_skill"
        type="text"
        placeholder="ทักษะที่ต้องการ(มากกว่า 1 คั่นด้วย ,)"
        required
        className="input input-neutral bg-white w-60 "
      />
      <select v-model="urgency" className="select bg-white border-black">
        <option value="Low">Low</option>
        <option value="Medium">Medium</option>
        <option value="High">High</option>
      </select>
      <span class="flex items-center">ระยะเวลา</span>
      <input
        v-model="duration"
        type="number"
        min="1"
        max="3"
        placeholder="ระยะเวลา (1-3 วัน)"
        required
        className="input input-neutral bg-white w-20 "
      />
      <span class="flex items-center">จำนวนช่าง</span>
      <input
        v-model="required_technicians"
        type="number"
        min="1"
        max="3"
        className="input input-neutral bg-white w-20 "
        placeholder="จำนวนช่างที่ต้องการ"
        required
      />
      <button
        type="submit"
        className="btn btn-neutral hover:cursor-pointer hover:opacity-90"
      >
        เพิ่มงาน
      </button>
    </form>
  </div>
</template>

<script setup>
import { ref } from "vue";
import axios from "axios";

const title = ref("");
const required_skill = ref("");
const urgency = ref("Medium");
const duration = ref(1);
const required_technicians = ref(1);

const addTask = async () => {
  try {
    await axios.post("/tasks", {
      title: title.value,
      required_skill: required_skill.value,
      urgency: urgency.value,
      duration: duration.value,
      required_technicians: required_technicians.value,
    });
    title.value = "";
    required_skill.value = "";
    urgency.value = "Medium";
    duration.value = 1;
    required_technicians.value = 1;
  } catch (error) {
    console.error("เพิ่มงานล้มเหลว", error);
  }
};
</script>
