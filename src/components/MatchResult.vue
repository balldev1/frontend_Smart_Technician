<template>
  <div>
    <div class="flex mt-5">
      <h1 class="badge px-2">ผลการจับคู่งาน</h1>
    </div>
    <button
      @click="matchTasks"
      className="btn btn-neutral hover:cursor-pointer hover:opacity-90 mt-5"
    >
      จับคู่งาน
    </button>

    <!-- แสดงข้อมูลการจับคู่งาน -->
    <!-- <div v-if="matches.length">
      <table>
        <thead>
          <tr>
            <th>งาน</th>
            <th>ช่าง</th>
            <th>วันที่</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(match, index) in matches" :key="index">
            <td>{{ match.task.title }}</td>
            ตรวจสอบว่า match.technicians มีข้อมูลหรือไม่ -->
    <!-- <td>
              <div v-if="match.technicians.length > 0">
                <ul>
                  <li
                    v-for="technician in match.technicians"
                    :key="technician.id"
                  >
                    {{ technician.name }} (ทักษะ:
                    {{ technician.skills.join(", ") }})
                  </li>
                </ul>
              </div>
              <div v-else>
                <p>ไม่สามารถจับคู่ช่างได้: {{ match.message }}</p>
              </div>
            </td> -->
    <!-- ตรวจสอบว่า match.start_date หรือ match.end_date มีข้อมูลหรือไม่ -->
    <!-- <td>
              <div v-if="match.start_date && match.end_date">
                <p>{{ match.start_date }} ถึง {{ match.end_date }}</p>
              </div>
              <div v-else>
                <p>ไม่มีการจับคู่ได้</p>
              </div>
            </td>
          </tr>
        </tbody>
      </table>
    </div> -->
    <!-- แสดงข้อมูลการจับคู่งาน -->
    <div v-if="matches.length" class="mt-5 bg-white">
      <table class="table-auto w-full border-collapse border">
        <thead>
          <tr>
            <th class="border p-2">งาน</th>
            <th class="border p-2">ช่าง</th>
            <th class="border p-2">วันที่</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(match, index) in matches" :key="index">
            <td class="border p-2">{{ match.task.title }}</td>

            <!-- ตรวจสอบว่า match.technicians มีข้อมูลหรือไม่ -->
            <td class="border p-2">
              <div v-if="match.technicians.length > 0">
                <ul>
                  <li
                    v-for="technician in match.technicians"
                    :key="technician.id"
                  >
                    {{ technician.name }} (ทักษะ:
                    {{ technician.skills.join(", ") }})
                  </li>
                </ul>
              </div>
              <div v-else>
                <p>ไม่สามารถจับคู่ช่างได้: {{ match.message }}</p>
              </div>
            </td>

            <!-- ตรวจสอบว่า match.start_date หรือ match.end_date มีข้อมูลหรือไม่ -->
            <td class="border p-2">
              <div v-if="match.start_date && match.end_date">
                <!-- <p>{{ match.start_date }} ถึง {{ match.end_date }}</p> -->
                <p>
                  {{ formatDate(match.start_date) }} -
                  {{ formatDate(match.end_date) }}
                </p>
              </div>
              <div v-else>
                <p>ไม่มีการจับคู่ได้</p>
              </div>
            </td>
          </tr>
        </tbody>
      </table>
    </div>

    <!-- แสดงผลกรณีไม่มีข้อมูลใน matches -->
    <div v-else class="flex mt-5">
      <p class="badge px-2">ยังไม่มีผลการจับคู่งาน</p>
    </div>

    <div v-if="totalDuration !== undefined" class="flex mt-5">
      <p lass="badge px-2">
        จำนวนวันที่ใช้ในการจัดตารางงานทั้งหมด: {{ totalDuration }} วัน
      </p>
    </div>
  </div>
</template>

<script setup>
import { ref } from "vue";
import axios from "axios";

const matches = ref([]);
const totalDuration = ref(0);

const formatDate = (dateString) => {
  const monthsInThai = [
    "มกราคม",
    "กุมภาพันธ์",
    "มีนาคม",
    "เมษายน",
    "พฤษภาคม",
    "มิถุนายน",
    "กรกฎาคม",
    "สิงหาคม",
    "กันยายน",
    "ตุลาคม",
    "พฤศจิกายน",
    "ธันวาคม",
  ];

  const date = new Date(dateString);
  const day = date.getDate().toString().padStart(2, "0");
  const month = monthsInThai[date.getMonth()];
  const year = date.getFullYear() + 543; // Convert to Thai Buddhist Era (BE)

  return `${day} ${month} ${year}`;
};

const matchTasks = async () => {
  try {
    const response = await axios.post("/match");
    console.log("API Response: ", response);

    // กำหนดข้อมูลที่ได้จาก API ให้กับ matches
    matches.value = response.data.matches;
    totalDuration.value = response.data.total_duration;
  } catch (error) {
    console.error("จับคู่งานล้มเหลว", error);
  }
};
</script>
