<script setup>
import { ref, onMounted, computed } from 'vue'
import { useRoute, useRouter } from 'vue-router'
import { ElMessage } from 'element-plus'
import { capitalize } from 'lodash'

import { taskService } from '../services/task.service'

const taskToEdit = ref(taskService.getEmptyTask())
const route = useRoute()
const router = useRouter()
const taskId = route.params.id

onMounted(async () => {
  if (taskId) taskToEdit.value = await taskService.getTask(taskId)
})

const errorsOptions = computed(() => taskService.ERRORS)
const statusOptions = computed(() => Object.values(taskService.STATUS))

async function saveTask() {
  const action = taskId ? taskService.updateTask : taskService.addTask
  await action(taskToEdit.value)
  ElMessage.success('Task saved successfully!')
  router.push('/')
}
</script>

<template>

  <main class="task-edit">
    <h1>Task {{ taskId ? 'edit' : 'add' }}</h1>

    <el-form :model="taskToEdit" label-width="100">
      <el-form-item label="Title">
        <el-input v-model="taskToEdit.title" placeholder="Type a title.." />
      </el-form-item>

      <el-form-item label="Status">
        <el-select v-model="taskToEdit.status" placeholder="Choose a status">
          <el-option v-for="status in statusOptions" :key="status" :label="capitalize(status)" :value="status" />
        </el-select>
      </el-form-item>

      <el-form-item label="Description">
        <el-input v-model="taskToEdit.description" placeholder="Type a description" />
      </el-form-item>

      <el-form-item label="Importance">
        <el-input v-model="taskToEdit.importance" type="number" min="1" max="3" />
      </el-form-item>

      <el-form-item label="Tries count">
        <el-input v-model="taskToEdit.triesCount" type="number" min="1" max="5" />
      </el-form-item>

      <el-form-item label="Tries count">
        <el-select v-model="taskToEdit.errors" placeholder="Select errors" multiple>
          <el-option v-for="error in errorsOptions" :key="error" :label="error" :value="error" />
        </el-select>
      </el-form-item>

      <el-form-item>
        <el-button type="primary" @click="saveTask()">Save</el-button>
      </el-form-item>
    </el-form>
  </main>
</template>
