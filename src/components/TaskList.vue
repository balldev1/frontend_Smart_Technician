<template>
  <div>
    <div class="flex">
      <h1 class="badge px-2">รายการงาน</h1>
    </div>
    <!-- ฟอร์มแก้ไขงาน (จะแสดงเมื่อผู้ใช้คลิกปุ่ม "แก้ไข") -->
    <div v-if="isEditing">
      <div class="flex mt-5">
        <h1 class="badge px-2">แก้ไขงาน</h1>
      </div>
      <form @submit.prevent="updateTask" class="mt-5 flex gap-2 items-center">
        <label for="title">ชื่องาน:</label>
        <input
          v-model="editedTask.title"
          type="text"
          id="title"
          required
          className="input input-sm input-neutral bg-white w-40 "
        />

        <label for="required_skill">ทักษะที่ต้องการ:</label>
        <input
          v-model="editedTask.required_skill"
          type="text"
          id="required_skill"
          required
          className="input input-sm input-neutral bg-white w-40 "
        />

        <label for="urgency">ความเร่งด่วน:</label>
        <select
          v-model="editedTask.urgency"
          id="urgency"
          required
          className="select w-20 bg-white border-black"
        >
          <option value="High">High</option>
          <option value="Medium">Medium</option>
          <option value="Low">Low</option>
        </select>

        <label for="duration">ระยะเวลา (วัน):</label>
        <input
          v-model="editedTask.duration"
          type="number"
          id="duration"
          min="1"
          max="3"
          required
          className="input input-sm input-neutral bg-white w-40 "
        />

        <label for="required_technicians ">จำนวนช่างที่ต้องการ:</label>
        <input
          v-model="editedTask.required_technicians"
          type="number"
          id="required_technicians"
          required
          className="input input-sm input-neutral bg-white w-40 "
        />

        <button
          type="submit"
          className="btn btn-neutral hover:cursor-pointer hover:opacity-90"
        >
          อัปเดตงาน
        </button>
        <button
          type="button"
          @click="cancelEdit"
          className="btn btn-neutral hover:cursor-pointer hover:opacity-90"
        >
          ยกเลิก
        </button>
      </form>
    </div>

    <!-- แสดงรายการงาน -->
    <!-- <ul>
      <li v-for="task in tasks" :key="task.id">
        {{ task.title }} - ทักษะที่ต้องการ: {{ task.required_skill }} -
        ระยะเวลา: {{ task.duration }} วัน ระยะเวลา: จำนวนช่างที่ต้องการ
        {{ task.required_technicians }} คน

        <button
          @click="editTask(task)"
          className="btn btn-neutral hover:cursor-pointer hover:opacity-90 mr-5"
        >
          แก้ไข
        </button>
        <button
          @click="deleteTask(task.id)"
          className="btn btn-neutral hover:cursor-pointer hover:opacity-90"
        >
          ลบ
        </button>
      </li>
    </ul> -->
    <table class="table-auto w-full border-collapse border mt-5">
      <thead>
        <tr>
          <th class="border p-2">ชื่องาน</th>
          <th class="border p-2">ทักษะที่ต้องการ</th>
          <th class="border p-2">ระยะเวลา</th>
          <th class="border p-2">จำนวนช่างที่ต้องการ</th>
          <th class="border p-2">การดำเนินการ</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="task in tasks" :key="task.id">
          <td class="border p-2">{{ task.title }}</td>
          <td class="border p-2">{{ task.required_skill }}</td>
          <td class="border p-2">{{ task.duration }} วัน</td>
          <td class="border p-2">{{ task.required_technicians }} คน</td>
          <td class="border p-2">
            <button
              @click="editTask(task)"
              class="btn btn-neutral hover:cursor-pointer hover:opacity-90 mr-5"
            >
              แก้ไข
            </button>
            <button
              @click="deleteTask(task.id)"
              class="btn btn-neutral hover:cursor-pointer hover:opacity-90"
            >
              ลบ
            </button>
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script setup>
import { ref, onMounted } from "vue";
import axios from "axios";

// State สำหรับรายการงาน และงานที่กำลังแก้ไข
const tasks = ref([]);
const isEditing = ref(false);
const editedTask = ref({
  id: null,
  title: "",
  required_skill: "",
  urgency: "",
  duration: 1,
  required_technicians: 1,
});

// ฟังก์ชันดึงข้อมูลงาน
const fetchTasks = async () => {
  try {
    const response = await axios.get("/tasks");
    tasks.value = response.data;
  } catch (error) {
    console.error("ไม่สามารถดึงข้อมูลงานได้", error);
  }
};

// ฟังก์ชันลบงาน
const deleteTask = async (id) => {
  try {
    await axios.delete(`/tasks/${id}`);
    tasks.value = tasks.value.filter((task) => task.id !== id);
  } catch (error) {
    console.error("ลบงานล้มเหลว", error);
  }
};

// ฟังก์ชันเริ่มต้นการแก้ไขงาน
const editTask = (task) => {
  isEditing.value = true;
  editedTask.value = { ...task }; // คัดลอกข้อมูลงานที่ต้องการแก้ไขไปยัง `editedTask`
};

// ฟังก์ชันอัปเดตงาน
const updateTask = async () => {
  try {
    const response = await axios.put(
      `/tasks/${editedTask.value.id}`,
      editedTask.value
    );
    const updatedTask = response.data;

    // อัปเดตงานในรายการ
    const index = tasks.value.findIndex((task) => task.id === updatedTask.id);
    if (index !== -1) {
      tasks.value[index] = updatedTask;
    }

    // ยกเลิกโหมดการแก้ไข
    isEditing.value = false;
  } catch (error) {
    console.error("อัปเดตงานล้มเหลว", error);
  }
};

// ฟังก์ชันยกเลิกการแก้ไข
const cancelEdit = () => {
  isEditing.value = false;
};

onMounted(fetchTasks);
</script>
