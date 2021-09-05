<template>
  <AddTask v-show="showAddTask" @add-task="addTask" />
  <Tasks
    @toggle-reminder="toggleReminder"
    @delete-task="deleteTask"
    :tasks="tasks"
  />
</template>

<script>
import Tasks from '../components/Tasks.vue'
import AddTask from '../components/AddTask.vue'

export default {
    name: 'Home',
    props: {
        showAddTask: Boolean,
    },
    components: {
        Tasks,
        AddTask,
    },
    data() {
        return {
            tasks: [],
        }
    },
    methods: {
        async addTask(task) {
            const res = await fetch('api/tasks', {
                method: 'POST',
                headers: {
                    'Content-type': 'application/json',
                },
                body: JSON.stringify(task),
            })
            const data = await response.json()
            this.tasks = [...this.tasks, data]
        },
        async deleteTask(id) {
            const response = await fetch(`api/tasks/${id}`, {
            method: 'DELETE',
            })
            response.status === 200 ? (this.tasks = this.tasks.filter((task) => task.id !== id)) : alert('Error deleting task')
        },
        async toggleReminder(id) {
            const taskToToggle = await this.fetchTask(id)
            const updateTask = { ...taskToToggle, reminder: !taskToToggle.reminder }
            const response = await fetch(`api/tasks/${id}`, {
                method: 'PUT',
                headers: {
                'Content-type': 'application/json',
                },
                body: JSON.stringify(updTask),
            })
            const data = await response.json()
            this.tasks = this.tasks.map((task) => task.id === id ? { ...task, reminder: data.reminder } : task)
            /* mapping through the entire array, returning the array of updated tasks: for each task check if task.id is equal to id that is passed in,
            if it is equal, return an array of objects where the reminder of the initial task is changed to the opposite of the current data reminder:
            true turns to false and opposite; else we just return the initial task */
        },
        async fetchTasks() {
            const response = await fetch('api/tasks')
            const data = await response.json()
            return data
        },
        async fetchTask(id) {
            const response = await fetch(`api/tasks/${id}`)
            const data = await response.json()
            return data
        },
    },
    async created() { /* when created() runs we are filling the array */
        this.tasks = await this.fetchTasks()
    },
}
</script>