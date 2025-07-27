<template>
  <div class="container">
    <h1>📋 Live Attendance Updates</h1>
    <div class="attendance-list">
      <div
        v-for="(entry, index) in attendanceUpdates"
        :key="index"
        class="attendance-card"
        :class="{
          'status-present': entry.status === 'PRESENT',
          'status-absent': entry.status === 'ABSENT',
          'status-late': entry.status === 'LATE',
        }"
      >
        <p><strong>Name:</strong> {{ entry.student_name }}</p>
        <p><strong>Subject:</strong> {{ entry.period_name }}</p>
        <p><strong>Class:</strong> {{ entry.class_name }}</p>
        <p><strong>Class:</strong> {{ entry.period_time }}</p>
        <p><strong>Date:</strong> {{ entry.date }}</p>
        <p><strong>Update At:</strong> {{ entry.update_time }}</p>
        <p>
          <strong>Status:</strong>
          <span
            :class="{
              'status-badge-present': entry.status === 'PRESENT',
              'status-badge-absent': entry.status === 'ABSENT',
              'status-badge-late': entry.status === 'LATE',
            }"
          >
            {{ getStatusEmoji(entry.status) }} {{ entry.status.toUpperCase() }}
          </span>
        </p>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue';
import { io } from 'socket.io-client';

const attendanceUpdates = ref([]);

const getStatusEmoji = (status) => {
  const emojis = {
    PRESENT: '✅',
    ABSENT: '❌',
    LATE: '⏰',
  };
  return emojis[status] || '';
};

onMounted(() => {
  const socket = io('http://localhost:3000');
  socket.on('attendanceUpdate', (data) => {
    attendanceUpdates.value.unshift(data);
    console.log('attendanceUpdates.value:::', attendanceUpdates.value);
  });
});
</script>

<style scoped>
body {
  margin: 0;
  font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
  background-color: #f2f2f2;
}

.container {
  max-width: 800px;
  margin: 40px auto;
  background: #ffffff;
  padding: 30px;
  border-radius: 10px;
  box-shadow: 0 4px 20px rgba(0, 0, 0, 0.1);
  color: #222;
}

h1 {
  text-align: center;
  margin-bottom: 30px;
  color: #333;
}

.attendance-list {
  display: flex;
  flex-direction: column;
  gap: 20px;
}

.attendance-card {
  background: #ffffff;
  padding: 20px;
  border-radius: 8px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.05);
  transition: transform 0.2s ease;
  color: #333;
  border-left: 6px solid #4caf50; /* Default for present */
}

.attendance-card:hover {
  transform: scale(1.01);
}

/* Status border colors */
.status-present {
  border-left-color: #4caf50;
}
.status-absent {
  border-left-color: #f44336;
}
.status-late {
  border-left-color: #ff9800;
}

/* Status badge styles */
.status-badge-present {
  color: #2e7d32;
  font-weight: bold;
}
.status-badge-absent {
  color: #c62828;
  font-weight: bold;
}
.status-badge-late {
  color: #e65100;
  font-weight: bold;
}
</style>
