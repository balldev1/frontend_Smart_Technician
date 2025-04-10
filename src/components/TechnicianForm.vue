<template>
  <div class="">
    <div class="flex">
      <h1 class="badge px-2">เพิ่มช่าง</h1>
    </div>
    <form @submit.prevent="addTechnician" class="mt-4 flex gap-5">
      <input
        v-model="name"
        type="text"
        placeholder="ชื่อช่าง"
        required
        className="input input-neutral bg-white w-60 "
      />
      <input
        v-model="skills"
        type="text"
        placeholder="ทักษะ (คั่นด้วย ,)"
        required
        @blur="updateSkillsArray"
        className="input input-neutral bg-white  w-60"
      />
      <button
        type="submit"
        className="btn btn-neutral hover:cursor-pointer hover:opacity-90"
      >
        ยืนยัน
      </button>
    </form>
  </div>
</template>

<script setup>
import { ref } from "vue";
import axios from "axios";

const name = ref("");
const skills = ref("");

const updateSkillsArray = () => {
  // แปลง skills เป็นอาร์เรย์ก่อนส่ง
  skills.value = skills.value.split(",").map((skill) => skill.trim());
};

const addTechnician = async () => {
  try {
    await axios.post("/technicians", {
      name: name.value,
      skills: skills.value, // skills ส่งเป็นอาร์เรย์
    });
    name.value = "";
    skills.value = "";
  } catch (error) {
    console.error("เพิ่มช่างล้มเหลว", error);
  }
};
</script>
