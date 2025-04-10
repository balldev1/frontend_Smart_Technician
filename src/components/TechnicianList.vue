<template>
  <div class="">
    <div class="flex">
      <h1 class="badge px-2">รายชื่อช่าง</h1>
    </div>

    <!-- ฟอร์มแก้ไขช่าง (จะแสดงเมื่อผู้ใช้คลิกปุ่ม "แก้ไข") -->
    <div v-if="isEditing">
      <div class="flex mt-4">
        <h1 class="badge px-2">แก้ไขข้อมูลช่าง</h1>
      </div>
      <form @submit.prevent="updateTechnician" class="mt-4">
        <label for="name">ชื่อช่าง:</label>
        <input
          v-model="editedTechnician.name"
          type="text"
          id="name"
          required
          className="input input-neutral bg-white  w-60 "
        />
        <label for="skills">ทักษะ (คั่นด้วย ,):</label>
        <input
          v-model="editedTechnician.skills"
          type="text"
          id="skills"
          required
          @blur="convertSkillsToArray"
          className="input input-neutral bg-white  w-60"
        />

        <button
          type="submit"
          className="btn btn-neutral hover:cursor-pointer hover:opacity-90 mt-5"
        >
          อัปเดตช่าง
        </button>
        <button
          type="button"
          className="btn btn-neutral hover:cursor-pointer hover:opacity-90 mt-5 ml-5"
          @click="cancelEdit"
        >
          ยกเลิก
        </button>
      </form>
    </div>

    <!-- แสดงรายการช่าง -->
    <ul class="mt-5 flex flex-col">
      <li v-for="technician in technicians" :key="technician.id" class="mt-2">
        {{ technician.name }} - {{ technician.skills.join(", ") }}
        <button
          @click="editTechnician(technician)"
          className="btn btn-sm btn-neutral  hover:cursor-pointer hover:opacity-90  ml-5"
        >
          แก้ไข
        </button>
        <button
          @click="deleteTechnician(technician.id)"
          className="btn btn-sm btn-neutral hover:cursor-pointer hover:opacity-90  ml-5"
        >
          ลบ
        </button>
      </li>
    </ul>
  </div>
</template>

<script setup>
import { ref, onMounted } from "vue";
import axios from "axios";

// State สำหรับรายการช่าง และช่างที่กำลังแก้ไข
const technicians = ref([]);
const isEditing = ref(false);
const editedTechnician = ref({
  id: null,
  name: "",
  skills: "",
});

// ฟังก์ชันดึงข้อมูลช่าง
const fetchTechnicians = async () => {
  try {
    const response = await axios.get("/technicians");
    technicians.value = response.data;
  } catch (error) {
    console.error("ไม่สามารถดึงข้อมูลช่างได้", error);
  }
};

// ฟังก์ชันลบช่าง
const deleteTechnician = async (id) => {
  try {
    await axios.delete(`/technicians/${id}`);
    technicians.value = technicians.value.filter(
      (technician) => technician.id !== id
    );
  } catch (error) {
    console.error("ลบช่างล้มเหลว", error);
  }
};

// ฟังก์ชันเริ่มต้นการแก้ไขช่าง
const editTechnician = (technician) => {
  isEditing.value = true;
  editedTechnician.value = { ...technician };

  // ถ้าทักษะเป็น string ให้แปลงเป็น array ทันที
  if (typeof technician.skills === "string") {
    editedTechnician.value.skills = technician.skills
      .split(",")
      .map((skill) => skill.trim());
  } else {
    editedTechnician.value.skills = technician.skills;
  }

  editedTechnician.value.skillsString =
    editedTechnician.value.skills.join(", ");
};

// ฟังก์ชันอัปเดตช่าง
const updateTechnician = async () => {
  try {
    // แปลง skillsString ให้เป็น array ทันทีถ้าเป็น string
    if (typeof editedTechnician.value.skills === "string") {
      editedTechnician.value.skills = editedTechnician.value.skills
        .split(",")
        .map((skill) => skill.trim());
    }

    const response = await axios.put(
      `/technicians/${editedTechnician.value.id}`,
      {
        ...editedTechnician.value,
        skills: editedTechnician.value.skills, // ส่ง skills เป็น array
      }
    );
    const updatedTechnician = response.data;

    // อัปเดตช่างในรายการ
    const index = technicians.value.findIndex(
      (technician) => technician.id === updatedTechnician.id
    );
    if (index !== -1) {
      technicians.value[index] = updatedTechnician;
    }

    // ยกเลิกโหมดการแก้ไข
    isEditing.value = false;
  } catch (error) {
    console.error("อัปเดตช่างล้มเหลว", error);
  }
};

// ฟังก์ชันแปลงสตริงที่คั่นด้วยคอมม่าเป็นอาร์เรย์
const convertSkillsToArray = () => {
  if (typeof editedTechnician.value.skillsString === "string") {
    editedTechnician.value.skills = editedTechnician.value.skillsString
      .split(",")
      .map((skill) => skill.trim());
  }
};

// ฟังก์ชันยกเลิกการแก้ไข
const cancelEdit = () => {
  isEditing.value = false;
};

onMounted(fetchTechnicians);
</script>
